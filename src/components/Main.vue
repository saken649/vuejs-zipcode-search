<template>
  <b-row>
    <b-col md="12" lg="6">
      <b-form class="margin-top-30">
        <b-form-input type="text" placeholder="example: 1500001" maxlength="7" v-model="zip"/>
        <div class="margin-top-10">
          <b-button type="button" @click="search()" variant="info">Search</b-button>
          <b-button type="button" @click="reset()" variant="outline-info">Reset</b-button>
        </div>
      </b-form>
      <b-list-group id="search-result" class="margin-top-30">
        <b-list-group-item>Prefecture: {{prefecture}}{{prefecture_en}}</b-list-group-item>
        <b-list-group-item>City: {{address1}}{{address1_en}}</b-list-group-item>
        <b-list-group-item>Address: {{address2}}{{address2_en}}</b-list-group-item>
        <b-list-group-item>: {{address3}}{{address3_en}}</b-list-group-item>
        <b-list-group-item>: {{address4}}{{address4_en}}</b-list-group-item>
      </b-list-group>
    </b-col>
  </b-row>
</template>

<script>
import axios from 'axios-jsonp-pro'

// APIから返ってくる項目
var apiReturnList = [
  'prefecture',
  'address1',
  'address2',
  'address3',
  'address4'
]

export default {
  data () {
    var ret = {}
    apiReturnList.forEach(function (val) {
      ret[val] = ''
      ret[val + '_en'] = ''
    })
    ret['zip'] = ''
    return ret
  },
  methods: {
    search () {
      // zipcloudではGitHubPagesでMixedContent問題が発生するためこちらに変更
      var url = 'https://madefor.github.io/postal-code-api/api/v1/' + this.zip.substr(0, 3) + '/' + this.zip.substr(-4) + '.json'
      var data = this
      axios
        .get(url)
        .then(response => {
          console.log(response)
          var result = response.data.data[0]
          apiReturnList.forEach(function (val) {
            data[val] = (result.ja[val] !== '') ? result.ja[val] : ''
            data[val + '_en'] = (result.en[val] !== '') ? ' (' + result.en[val] + ')' : ''
          })
        })
        .catch(xhr => {
          console.log(xhr)
          alert('住所が存在しません。')
        })
    },
    reset () {
      var data = this
      apiReturnList.forEach(function (val) {
        data.$set(data, val, '')
        data.$set(data, val + '_en', '')
      })
      data.$set(data, 'zip', '')
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
