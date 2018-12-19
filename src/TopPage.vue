<template>
  <v-ons-page>
    <custom-toolbar>TOP</custom-toolbar>
    <p style="text-align: center">
      AWS試験対策問題集
    </p>
      <v-ons-button modifier="large--cta" @click="pushCategoryPage" style="background-color: red; color: blue">カテゴリ別</v-ons-button><br />
      <v-ons-button modifier="large--cta" @click="pushWeakPage">苦手な問題</v-ons-button><br />
      <v-ons-button modifier="large--cta" @click="pushBookmarkPage">マーク済問題</v-ons-button>
    
  </v-ons-page>
</template>

<script>
  import customToolbar from './CustomToolbar';
  import categoryPage from './CategoryPage';
  import bookmarkQuestionPage from './BookmarkQuestionPage';
  import weakQuestionPage from './WeakQuestionPage';

  export default {
     methods: {
       pop(){
         this.pageStack.pop();
       },
       pushCategoryPage() {
        this.pageStack.push(categoryPage);
      },
      pushBookmarkPage(){
        this.pageStack.push(bookmarkQuestionPage);
      },
      pushWeakPage(){
        this.pageStack.push(weakQuestionPage);
      },
     },
     props: ['pageStack'],
     components: { customToolbar },
     key: 'key_topPage',
  }
</script>
