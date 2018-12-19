<template>
  <v-ons-page>
    <custom-toolbar :back-label="'TOP'">マーク問題</custom-toolbar>
    <p style="text-align: center">
      全{{getMarkedQuestion().length}}問
    </p>
    <v-ons-list>
      <v-ons-list-item modifier="chevron" tappable @click="push(question)" v-for="question in getMarkedQuestion()">{{question.title}}</v-ons-list-item>
    </v-ons-list>
  </v-ons-page>

</template>

<script>
  import customToolbar from './CustomToolbar';
  import questionPage from './QuestionPage';
  import questionString from 'raw-loader!./question';
  var STORAGE_KEY = 'userInfo';
  export default {
    data() {
      return {
        allUserInfo: JSON.parse(localStorage.getItem(STORAGE_KEY) || "{}")
      };
    },
    methods: {
      pop(){
        this.pageStack.pop();
      },
      getMarkedQuestion(){
        var hash = JSON.parse(questionString);
        console.log(hash);
        var allUserInfo = JSON.parse(localStorage.getItem(STORAGE_KEY) || "{}");
        // JSはsetの順番も保持される
        var questions = [];
      
        for (var key in hash) {
          if(allUserInfo[key] != null && allUserInfo[key].isBookmarked){
            questions.push(hash[key]);
          }
        }
        return questions;
      },
      push(question) {
        var markedQuestion = this.getMarkedQuestion();
        this.pageStack.push({
          extends: questionPage,
          data() {
            var allUserInfo = JSON.parse(localStorage.getItem(STORAGE_KEY) || "{}");
            console.log("a");

            var keys = [];
            markedQuestion.forEach(function(e){
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
    key: 'key_bookmarkQuestionPage',
  }
</script>
