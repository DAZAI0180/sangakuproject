<template>
  <v-layout>

    <v-flex >
      <form action @submit.prevent="sendMessage" class="form">
      <p style="width:100%; margin:10px 0 13px 0; background-color:gray; color:white;line-height:200%">
        &emsp;商品名を入力してください
      </p>
      <v-flex xs12 sm6 md3>
        <v-text-field
          label=""
          solo
          v-model="title"
        ></v-text-field>
      </v-flex>

      <p style="width:100%; margin:10px 0 13px 0; background-color:gray; color:white;line-height:200%">
        &emsp;画像を追加してください
      </p>
      <img
        width=33%
        v-show="flag"
        class="preview-item-file1"
        :src="uploadedImage[0]"
      /><img
        width=33%
        v-show="flag"
        class="preview-item-file2"
        :src="uploadedImage[1]"
      /><img
        width=33%
        v-show="flag"
        class="preview-item-file3"
        :src="uploadedImage[2]"
      />
        <v-layout justify-center>
          <v-btn
            @click="pickFile"
            v-model="imageName"
            fab
          >
            <v-icon>photo_camera</v-icon>
          </v-btn>
        </v-layout>
      <input
        type="file"
        style="display: none"
        ref="image"
        accept="image/*"
        @change="onFilePicked"
      />

      <p style="width:100%; margin:10px 0 13px 0; background-color:gray; color:white;line-height:200%">
        &emsp;商品の説明を入力してください
      </p>
      <v-flex xs12>
        <v-textarea
          solo
          name="input-7-4"
          v-model="input"
          label=""
          value=""
          height=150
        ></v-textarea>
      </v-flex>
      <p style="width:100%; margin:10px 0 13px 0; background-color:gray; color:white;line-height:200%">
        &emsp;商品のカテゴリを追加してください
      </p>
      <v-layout row>
        <v-flex xs10>
          <v-text-field
            v-model="category"
          ></v-text-field>
        </v-flex>
        <v-flex  xs2>
          <v-layout justify-center>
            <v-btn color="primary" fab small dark>
              <v-icon>edit</v-icon>
            </v-btn>
          </v-layout>
        </v-flex>
      </v-layout>
      
      <v-flex xs12>
          <v-btn color="red" round large type="submit" style="width:44%">出品する</v-btn>
          <v-btn color="blue" round large style="width:44%">キャンセル</v-btn>
      </v-flex>
      </form>
    </v-flex>
  </v-layout>
</template>

<script src="/******/v-preview-input/v-preview-input.js"></script>
<script>
import createPersistedState from 'vuex-persistedstate'
import firebase from '~/plugins/firebase'
import { mapActions, mapState, mapGetters } from 'vuex'

  export default {
  
      fetch ({ store, route,redirect }) {
      console.log("今からリダイレクト分岐");
    if (!store.state.user.user) {
      //console.log("リダイレクトなんだよなぁ")
      if(route.name != "/login"){
      return redirect('/login')
      }else{
       return redirect('/mypage')
      }
    }
    
  },
    data() {
    return {
      user: {},  // ユーザー情報
      text: [],  // 取得したメッセージを入れる配列
      input: '',  // 入力したメッセージ
      category: '',
      title: '',
      photo: null,
      photo_url: null,
      dialog: false,
      imageName: [],
      imageUrl: "",
      imageFile: [],
      uploadedImage: [],
      flag: "",
    }
    //console.log(user);
  },
    methods : {
      ...mapActions(['setUser']), 
      sendMessage(){
        firebase.auth().onAuthStateChanged(user => {
            this.user = user ? user : {}
            //console.log(this.user.uid)
            const db = firebase.firestore()
            // ストレージオブジェクト作成
            var storageRef = firebase.storage().ref();
            for(var i=0;i <= 3;i++){
            // ファイルのパスを設定
            var mountainsRef = storageRef.child(`images/${this.imageName[i]}`);
            // ファイルを適用してファイルアップロード開始
            mountainsRef.put(this.imageFile[i]).then(snapshot => {
            });
            }
            snapshot.ref.getDownloadURL().then(downloadURL => {
                //db.collection("images").add({ downloadURL });
                    this.uploadedImage.push(downloadURL);
                    var d = new Date();
                    var year  = d.getFullYear();
                    var month = d.getMonth() + 1;
                    var day   = d.getDate();
                    var hour  = ( d.getHours()   < 10 ) ? '0' + d.getHours()   : d.getHours();
                    var min   = ( d.getMinutes() < 10 ) ? '0' + d.getMinutes() : d.getMinutes();
                    var sec   = ( d.getSeconds() < 10 ) ? '0' + d.getSeconds() : d.getSeconds();
                    var time = ( year + '-' + month + '-' + day + ' ' + hour + ':' + min + ':' + sec );
                    var data = {
                    id: user.uid,
                    name: user.displayName,
                    title:this.title,
                    text: this.input,
                    category: this.category,
                    url1: this.uploadedImage[0],
                    url2: this.uploadedImage[1],
                    url3: this.uploadedImage[2],
                    created_at:time,
                  };
                  // console.log(this.imageUrl);
                  var setDoc = db.collection('item').doc().set(data);

                              this.input = "";
            this.imageName= "",
            this.imageUrl = "",
            this.imageFile = "",
            this.title = ""
                  //console.log(setDoc);
              });
            // console.log(this.imageUrl);
            //var setDoc = db.collection('item').doc().set(data);
             // フォームを空にする

        })
      },
      pickFile() {
      this.$refs.image.click();
    },
    //ファイルの選択変えた時に動きそう
     onFilePicked(e) {
      const files = e.target.files;
      if (files[0] !== undefined) {
        this.imageName.push(files[0].name);
        const fr = new FileReader();
        fr.onload = e => {
          this.flag = "tinpo";
          this.uploadedImage.push(e.target.result);
          //console.log("配列の中身を見てみようのコーナー（いらない）"+this.uploadedImage[1]);
        };
        fr.readAsDataURL(files[0]);
        fr.addEventListener("load", () => {
          this.imageUrl = fr.result;
          this.imageFile.push(files[0]); // this is an image file that can be sent to server...
          console.log("配列の中身を見てみようのコーナー（いらない）:"+this.uploadedImage[1]);
          console.log("name:"+this.imageName);
          console.log("file:"+this.imageFile);
        });
      } 
    },

      logout() {
        const self = this
        firebase.auth().signOut()
        .then(_ => {
          console.log("ログアウト成功")
          this.$store.dispatch('user/fecthUser',)
          //self.$router.push("/login")
        }).catch((error) => {
          alert(error)
        })
      }
      
    },
  
};


</script>