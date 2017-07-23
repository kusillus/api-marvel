<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <div class="">
      <input type="text" name="" value="" v-model="characterId">
      <button type="button" name="button" v-on:click="consult">Consultar</button>
    </div>
    {{marNum}}
    ts: {{ts}}<br>
    hash: {{hash}} <br>
    lengthData: {{sources.length}}
    <pre>
      <!-- sources: {{sources}} -->
    </pre>
    <div class="">
      {{attributionText}}
    </div>
    <div class="" v-if="arrayData">
      <div class="" v-for="item in sources">
        <p>{{item.title}}</p>
        count images: {{item.images.length}}
        <div class="" v-if="item.images.length>0">
          imagen cero: {{item.images[0].path}}
          <img :src="item.images[0].path+'.'+item.images[0].extension" alt="">
        </div>
        <div class="" v-else>
          Imagen no disponible
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import cryptoJs from 'crypto-js'
import axios from 'axios'
import _ from 'lodash'
export default {
  name: 'hello',
  data () {
    return {
      marNum: '', // pendiente de usar
      pubKey: '697e24be1be660e207c3d70eef8faa98',
      priKey: '39061bce3a674db7cc87ae3954a44634cecb5171',
      ts: new Date().getTime(),
      characterId: '',
      lengthData: '',
      hash: '',
      sources: [],
      attributionText: '',
      arrayData: false,
      // testHash: cryptoJs.MD5(String(this.ts)).toString(),
      // hash: cryptoJs.MD5(this.ts + this.pubKey + this.priKey).toString(),
      msg: 'Welcome to Your Vue.js PWA'
    }
  },
  methods: {
    consult: _.debounce(function () {
      const vm = this
      vm.ts = new Date().getTime()
      vm.hash = cryptoJs.MD5(String(vm.ts) + vm.priKey + vm.pubKey).toString()
      axios.get('https://gateway.marvel.com:443/v1/public/comics?format=comic&hasDigitalIssue=true&apikey=' + vm.pubKey + '&hash=' + vm.hash)
      .then(function (response) {
        let res = response.data
        vm.attributionText = res.attributionText
        vm.sources = res.data.results
        if (vm.sources.length > 0) {
          vm.arrayData = true
        }
        console.log('hola')
      })
    }, 500)
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #35495E;
}
</style>
