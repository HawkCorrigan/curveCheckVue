<template>
  <div id="app">
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

    Got Curve: {{this.hasCurve}}
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'App',
  data () {
    return {
      achievs: [],
      hasCurve: false,
      errors: [],
      char: {
        name: '',
        realm: ''
      }
    }
  },
  methods: {
    handleSubmit () {
      axios.get(`https://eu.api.battle.net/wow/character/${this.char.realm}/${this.char.name}?fields=achievements&locale=en_GB&apikey=4bw55e6va4j3scwbfapf9d2x8v3awumk`)
        .then(response => {
          this.achievs = response.data.achievements.achievementsCompleted
          this.hasCurve = this.achievs.includes(12110)
        })
        .catch(e => {
          this.errors.push(e)
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
</style>
