<template>
  <v-ons-page>
    <custom-toolbar :back-label="'TOP'">未回答／苦手問題</custom-toolbar>
    <p style="text-align: center">
      全{{getWeakQuestion().length}}問
    </p>
    <v-ons-list>
      <v-ons-list-item modifier="chevron" tappable @click="push(q)" v-for="q in getWeakQuestion()">{{q.title}}</v-ons-list-item>
    </v-ons-list>
  </v-ons-page>
</template>

<script>
  import customToolbar from './CustomToolbar';
  import questionPage from './QuestionPage';
  import questionString from 'raw-loader!./question';
  var STORAGE_KEY = 'userInfo';
  var WEAK_RATE=75;

  function isWeakQuestion(allUserInfo, key){
    var question = allUserInfo[key];
    if(question == null || question.history.length == 0){return true;}
    var correctCount = 0;
    question.history.forEach(function(v){
      if(v){correctCount++;}
    });
    return Math.round((correctCount/question.history.length)*100) < WEAK_RATE;
  }

  export default {
    data() {
      return {
        allUserInfo: JSON.parse(localStorage.getItem(STORAGE_KEY) || "{}"),
        getWeakQuestion: function () {
        var hash = JSON.parse(questionString);
        var allUserInfo = JSON.parse(localStorage.getItem(STORAGE_KEY) || "{}");
        // JSはsetの順番も保持される
        var questions = [];
      
        for (var key in hash) {
          if(isWeakQuestion(allUserInfo,key)){
            questions.push(hash[key]);
          }
        }
        console.log(questions)
        return questions;
      }
      };
    },
    computed: {
    },
    methods: {
      pop(){
        this.pageStack.pop();
      },
      push(question) {
        var targetQuestion = this.getWeakQuestion();
        this.pageStack.push({
          extends: questionPage,
          data() {
            var allUserInfo = JSON.parse(localStorage.getItem(STORAGE_KEY) || "{}");

            var keys = [];
            targetQuestion.forEach(function(e){
              keys.push(e.id);
            });
            // 選択した問題以前をkeyから削除
            keys.splice(0,keys.indexOf(question.id));
            console.log(keys);
            return {
              questionKey: "key_"+question.id,
              targetQuestionKeys: keys,
              question: JSON.parse(questionString)["key_"+question.id],
              allUserInfo: allUserInfo,
              userInfo: allUserInfo["key_"+question.id] || {"history":[],"isBookmarked":false}
            };
          }
        });
      },
    },
    props: ['pageStack'],
    components: { customToolbar },
    key: 'key_weakQuestionPage',
  }
</script>
