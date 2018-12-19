<template>
  <v-ons-page>
    <custom-toolbar :back-label="'TOP'">カテゴリページ</custom-toolbar>
    <p style="text-align: center">
      AWSクラウドサービス別問題
    </p>
    <v-ons-list>
      <v-ons-list-item modifier="chevron" tappable @click="push(category)" v-for="category in allCategory">{{category}}</v-ons-list-item>
    </v-ons-list>
  </v-ons-page>
</template>

<script>
  import customToolbar from './CustomToolbar';
  import categoryQuestionPage from './CategoryQuestionPage';
  import questionString from 'raw-loader!./question';
  var STORAGE_KEY = 'userInfo';
  
  var getAllCategory = function(){
    var hash = JSON.parse(questionString)
    // JSはsetの順番も保持される
    var set = new Set()
    for (var key in hash) {
        set.add(hash[key].category);
    }
    return Array.from(set);
  }

  export default {
    data() {
      return {
        allCategory: getAllCategory()
      };
    },
    methods: {
      pop(){
        this.pageStack.pop();
      },
      push(category) {
        this.pageStack.push({
          extends: categoryQuestionPage,
          data() {
            return {
              categoryId: category,
            };
          }
        });
      },
    },
    props: ['pageStack'],
    components: { customToolbar },
    key: 'key_categoryPage',
  }
</script>
