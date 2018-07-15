<template>
  <div id="app">
    <div id="header">
    Super fancy ultra mega curve lookup page
    </div>
    <form @submit.prevent="handleSubmit">
      <label>
        Name:
        <input type="text" v-model="char.name"/>
      </label>
      <label>
        Realm:
        <input type="text" v-model="char.realm"/>
      </label>
      <button type="submit">Check Curve</button>
    </form>

    <div v-if="show">
      Got Curve: {{this.hasCurve}} <br/>
      <button type="button"
        v-clipboard:copy="inviteString"
        v-clipboard:error="onError"
        v-clipboard:success="onCopy">COPY ME
      </button>
    </div>
    <div v-if="copyStatus===null">Copy not done yet</div>
    <div v-if="copyStatus===true">Copy success</div>
    <div v-if="copyStatus===false">Copy not successful. Yell at Smaac</div>
    <div v-if="wrongName">
      Couldn't find player
    </div>
  </div>
</template>

<script>
import Vue from 'vue'
import axios from 'axios'
import Autocomplete from './components/Autocomplete'
import VueClipboard from 'vue-clipboard2'
import VueFuse from 'vue-fuse'
import jsonData from './assets/EU.json'

Vue.use(VueClipboard)
Vue.use(VueFuse)

export default {
/* eslint-disable */
  name: 'App',
  components: {Autocomplete},
  data () {
    return {
      achievements: [],
      hasCurve: false,
      errors: [],
      options: {
        keys: ["name"]
      },
      json_data: jsonData.realms,
      inviteString: '',
      copyStatus:null,
      char: {
        name: '',
        realm: ''
      },
      show: false,
      wrongName: false
    }
  },
  methods: {
    onError(){
      this.copyStatus=false

    },
    onCopy(){
      this.copyStatus=true
    },
    handleSubmit () {
      this.copyStatus=null;
      if(this.char.name.includes(" ") || this.char.name.includes("-")){
        let arr=this.char.name.split(/ (.+)|-(.+)/);
        console.log(arr);
        this.char.name=arr[0];
        this.char.realm=arr[1];
      }
      this.char.name.replace(/ /g,'')
      if(this.char.name.includes("-")){
        let arr=this.char.name.split(/-(.+)/);
        this.char.name=arr[0];
        this.char.realm=arr[1];
      }

      this.$search(this.char.realm, this.json_data, this.options).then(results=>{
        this.char.realm=results[0].name

        this.show = false
        this.wrongName = false
        axios.get(`https://eu.api.battle.net/wow/character/${this.char.realm}/${this.char.name}?fields=achievements&locale=en_GB&apikey=${process.env.WOW_API_KEY}`)
          .then(response => {
            this.achievements = response.data.achievements.achievementsCompleted
            this.hasCurve = this.achievements.includes(12110)
            this.show = true
            this.inviteString=`/inv ${this.char.name}-${this.char.realm}`
          })
          .catch(e => {
            if (e.response) {
              if (e.response.status === 404) {
                this.wrongName = true
              }
            }
          })
      })
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

#header {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  font-size: xx-large;
}
</style>
