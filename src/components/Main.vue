<template>
  <b-row>
      <b-col md="12" lg="6">
        <b-form class="margin-top-30">
          <b-form-input type="text" placeholder="example: 1500001" maxlength="7" v-model="zip"/>
          <div class="margin-top-10">
            <b-button type="button" @click="search()" variant="info">検索</b-button>
            <b-button type="button" @click="reset()" variant="outline-info">リセット</b-button>
          </div>
        </b-form>
        <b-list-group id="search-result" class="margin-top-30">
          <b-list-group-item>Prefecture: {{prefecture}}</b-list-group-item>
          <b-list-group-item>City: {{city}}</b-list-group-item>
          <b-list-group-item>Address: {{address}}</b-list-group-item>
        </b-list-group>
      </b-col>
    </b-row>
</template>

<script>
import axios from 'axios-jsonp-pro'

export default {
  data () {
    return {
      prefecture: '',
      city: '',
      address: '',
      zip: ''
    }
  },
  methods: {
    search () {
      var url = 'http://zipcloud.ibsnet.co.jp/api/search?zipcode=' + this.zip
      var data = this
      axios
        .jsonp(url)
        .then(json => {
          console.log(json)
          if (json.status === 200 && json.results !== null) {
            var result = json.results[0]
            data.prefecture = result.address1 + ' (' + result.kana1 + ')'
            data.city = result.address2 + ' (' + result.kana2 + ')'
            data.address = result.address3 + ' (' + result.kana3 + ')'
          } else {
            if (json.message === null) {
              alert('存在しない郵便番号のようです。')
            } else {
              alert(json.message)
            }
          }
        })
        .catch(xhr => {
          console.log(xhr)
        })
    },
    reset () {
      this.zip = ''
      this.prefecture = ''
      this.city = ''
      this.address = ''
    }
  }
}
</script>

<style>
.margin-top-10 {
  margin-top: 10px;
}
.margin-top-30 {
  margin-top: 30px;
}
</style>
