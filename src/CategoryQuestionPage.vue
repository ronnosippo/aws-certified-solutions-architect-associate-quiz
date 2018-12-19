<template>
  <v-ons-page>
    <custom-toolbar :back-label="'カテゴリ'">{{categoryId}}</custom-toolbar>
    <p style="text-align: center">
      {{categoryId}}に関する問題 全{{getCategoryQuestion(categoryId).length}}問
    </p>
    <v-ons-list>
      <v-ons-list-item modifier="chevron" tappable @click="push(question)" v-for="question in getCategoryQuestion(categoryId)">
      {{question.title}}</v-ons-list-item>
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
      console.log(JSON.parse(localStorage.getItem(STORAGE_KEY) || "{}"));
      return {
        allUserInfo: JSON.parse(localStorage.getItem(STORAGE_KEY) || "{}")
      };
    },
    methods: {
      pop(){
        this.pageStack.pop();
      },
      getCategoryQuestion(category){
        var hash = JSON.parse(questionString)
        // JSはsetの順番も保持される
        var questions = [];
        for (var key in hash) {
          if(hash[key].category === category){
            questions.push(hash[key]);
          }
        }
        return questions;
      },
      push(question) {
        this.pageStack.push({
          extends: questionPage,
          data() {
            var allUserInfo = JSON.parse(localStorage.getItem(STORAGE_KEY) || "{}");
            var keys = Object.keys(JSON.parse(questionString));
            // 選択した問題以前をkeyから削除
            keys.splice(0,keys.indexOf("key_"+question.id));
            //keys.splice(keys.indexOf(question.id)-1,1);
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
    key: 'key_categoryQuestionPage',
  }
</script>
