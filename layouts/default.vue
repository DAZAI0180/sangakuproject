<template>
  <v-app>

    <v-toolbar :fixed="fixed" app >
      <v-spacer />
        <v-text-field
        hide-details
        prepend-icon="search"
        single-line
        placeholder="何をお探しですか？"
         />
        <!--<v-toolbar-title v-text="title" />-->
        <v-spacer />
    </v-toolbar>
    
    <v-content>
      <v-container>
        <nuxt />
      </v-container>
    </v-content>

<v-footer>
 <v-bottom-nav :value="true" :active.sync="bottomNav"
 :fixed=true >
  <v-btn color="teal" flat value="recent" to = "/" >
    <span>ホーム</span>
    <v-icon>home</v-icon>
  </v-btn>

      <v-btn
        color="teal"
        flat
        value="favorites"
      >
        <span>出品</span>
        <v-icon>photo_camera</v-icon>
      </v-btn>

      <v-btn
        color="teal"
        flat
        value="unko"
        to = "/mypage"
      >
        <span>マイページ</span>
        <v-icon>accessibility</v-icon>
      </v-btn>
     
          </v-bottom-nav>
</v-footer>
     

  </v-app>
  
</template>

<script>
import firebase from '~/plugins/firebase'
import { mapActions, mapState, mapGetters } from 'vuex'
export default {
  // データをこのコンポーネントにセットする必要がないため fetch() を使う

 data() {
    return {
      clipped: false,
      drawer: false,
      fixed: false,
      absolute:false,
      value:true,
      items: [
        {
          icon: 'apps',
          title: 'Welcome',
          to: '/'
        },
        {
          icon: 'bubble_chart',
          title: 'ホーム',
          to: '/home'
        },
        {
          icon: 'bubble_chart',
          title: '出品した商品',
          to: '/'
        },
        {
          icon: 'bubble_chart',
          title: 'せがんご3',
          to: '/inspire'
        },
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: '産学連携',
      bottomNav: "",
      color: '#FFF'
    }
  },
    created() {
    firebase.auth().onAuthStateChanged(user => {
      if (user) {
        // User is signed in.
        this.user = user ? user : {}
        console.log(this.user.uid)
      } else {
        // No user is signed in.
        this.$router.push("/login")
      }
    })
  }
}
</script>
