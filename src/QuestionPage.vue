<template>
  <v-ons-page id="question-page">
    <custom-toolbar :back-label="'戻る'">{{question.title}}</custom-toolbar>
    <span>正解率：{{getHistoryRate()}}</span>

    <v-ons-card>
    {{question.body}}
    </v-ons-card>

    <div>
      <v-ons-list-item v-for="(choice, $index) in getQuestion" >
        <label class="left">
          <v-ons-checkbox :input-id="'checkbox-' + $index" 
          :value="choice.id" v-model="checkList" :disabled="isAnswered"
          :class="{ isAnswered: hogehoge }" >
          </v-ons-checkbox>
        </label>
        <label class="center" :for="'checkbox-' + $index">
          {{ choice.body }}
        </label>
      </v-ons-list-item>
    </div>
    <transition>
      <v-ons-card v-if="isAnswered">
            <div >
        Lorem ipsum dolor sit amet
      </div>
        <span>回答：{{getAnswer()}}</span>

        <span>解説</span>
        <span v-html="question.description"></span>
        <v-ons-button modifier="large" style="margin: 6px 0" @click="push">次へ</v-ons-button>
        <br>
        <v-ons-button v-if="!userInfo.isBookmarked" modifier="large--cta" style="margin: 6px 0" @click="toggleBookmark" >マークを付ける</v-ons-button>
        <v-ons-button v-if="userInfo.isBookmarked" modifier="large--quiet" style="margin: 6px 0" @click="toggleBookmark" >マークを取り消す</v-ons-button>
      </v-ons-card>
    </transition>

  </v-ons-page>
</template>

<script>
import app from './App';
import topPage from './TopPage';
import questionPage from './QuestionPage';
import categoryPage from './CategoryPage';
import customToolbar from './CustomToolbar';
import questionString from 'raw-loader!./question';
var STORAGE_KEY = 'userInfo';

export default{
  data() {
    return {
      isAnswered: false,
      isCorrect: false,
      checkList: [],
      shuffledChoice: []
    };
  },
  computed: {
    getQuestion: function () {
      var array = this.question.choices;
      for (var i = array.length - 1; i >= 0; i--){
        var rand = Math.floor( Math.random() * ( i + 1 ) );
        [array[i], array[rand]] = [array[rand], array[i]]
      }
      return array;
      //this.shuffledChoice = array;
      //console.log(this.shuffledChoice);
    }
  },
  methods: {
    pop() {
      this.pageStack.pop();
    } ,
    getHistoryRate(){
      if(this.userInfo.history.length == 0){return "0%";}
      var correctCount = 0;
      this.userInfo.history.forEach(function(v){
        if(v){correctCount++;}
      });
      return Math.round((correctCount/this.userInfo.history.length)*100) + "%";
    },
    getAnswer(){
      this.shuffledChoice = [];
      var answerArr = [];
      for(var i=0; i<this.question.answer.length; i++) {
        var answer = this.question.answer[i];
        for(var j=0; j<this.question.choices.length; j++) {
          var choice = this.question.choices[j];
          if(choice.id === answer){answerArr.push(choice)}
        }
      }
      return answerArr;
    },
    toggleBookmark() {
      this.userInfo.isBookmarked = !this.userInfo.isBookmarked;
      this.allUserInfo[this.questionKey] = this.userInfo ;
      localStorage.setItem(STORAGE_KEY, JSON.stringify(this.allUserInfo));
    },
    push() {
      var allUserInfo = this.allUserInfo;
      this.targetQuestionKeys.shift();
      // 問題が存在しなければTOPページに遷移
      if(this.targetQuestionKeys.length == 0){
        this.pageStack.length=0;
        this.pageStack.push(topPage);
      }else{
        var nextKey = this.targetQuestionKeys[0];
        var targetKeys = this.targetQuestionKeys;
        this.pageStack.pop();
        this.pageStack.push({
          extends: questionPage,
          data() {
            return {
              questionKey: nextKey,
              targetQuestionKeys: targetKeys,
              question: JSON.parse(questionString)[nextKey],
              allUserInfo: allUserInfo,
              userInfo: allUserInfo[nextKey] || {"history":[],"isBookmarked":false}
            };
          }
        });
      }
    },
  },
  watch: {
    checkList: {
      handler(val) {
        var modelAnswerArr = this.question.answer;
        if(modelAnswerArr.length != this.checkList.length ){
          return;
        }
        this.isAnswered = true;
        // 正解チェック
        this.isCorrect = true;
        for(var i=0; i<this.checkList.length; i++) {
          if(!modelAnswerArr.includes(this.checkList[i])){
            this.isCorrect = false;
            break;
          }
        }
        // 履歴に追加
        this.userInfo.history.push(this.isCorrect);
        this.allUserInfo[this.questionKey] = this.userInfo
        localStorage.setItem(STORAGE_KEY, JSON.stringify(this.allUserInfo));
      },
      deep: true
    },
  },
  props: ['pageStack'],
  components: { customToolbar },
  key: 'key_questionPage',
};
</script>