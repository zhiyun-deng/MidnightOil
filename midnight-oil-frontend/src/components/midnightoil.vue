<template>
  <div id="midnightoil" class="midnightoil">
    <h2 style="text-align:center">Co-work McGill Random Pairing</h2>
    <p>
      <span style="color:red">Error: Message text comes here</span>
      <span>{{msg}}</span>
    </p>
    <img src='https://placekitten.com/200/300'/>
    <a href="https://zoom.us/oauth/authorize?response_type=code&client_id=CpVB04MsSzyrqxe6kYPzNw&redirect_uri=https%3A%2F%2Fmidnight-oil.herokuapp.com%2F">Install</a>
    <div class="container" style="margin: auto; width: 50%; height: 100%; display: flex; flex-direction: row; justify-content: center;">
     <table>
      <tr v-for="timeslot in times">
        <td>{{timeslot.startTime}}</td>
        <td>{{timeslot.startDate}}</td>
        <td>
          <button v-on:click = "createRequest(timeslot)" type="button" class="btn btn-primary">Book</button>
        </td>
      </tr>
     </table>
    </div>
  </div>
  
</template>

<script>
import axios from 'axios'

var config = require('../../config')

var frontendUrl = 'http://' + config.dev.host + ':' + config.dev.port
var backendUrl = 'http://' + config.dev.backendHost + ':' + config.dev.backendPort

var AXIOS = axios.create({
  baseURL: backendUrl,
  headers: { 'Access-Control-Allow-Origin': frontendUrl }
})
export default {
  name: 'midnightoil',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
      code: "",
      accessToken: "",
      error: false,
      times: []
    };
  },
  created(){
    
    
    this.code = this.$route.query.code;
    this.msg = this.$route.query.code;
    AXIOS.get('/times')
    .then(response=>{
      this.times=response.data
    })
    .catch(e=>{
      this.error=true
      console.log(e)
    })
      
    if(this.code){
      AXIOS.get('/token?code=' + this.code)
      .then((response) => {
        console.log("successful login")
        this.error = false
        this.accessToken = response.data
        if(this.accessToken){
          document.cookie = 'Token=' + response.data
        }
        console.log(this.accessToken)

      })
      .catch(error => {
        this.error = true
        console.log(error)
      })
    }

  },
  mounted: function() {
        console.log(this.code);
        $('#target').weekly_schedule(settings);
    },
  methods:{
    createRequest: function(timeslot){
      if (this.accessToken === '') {
        if(document.cookie.length>6){
          this.accessToken = document.cookie.substr(6)
        }
        else{
        this.error = true
        this.msg = 'You must login to Zoom first first'
        return
        }

      }
      console.log(timeslot.startTime)
     AXIOS.post('/request?date='+timeslot.startDate+'&startTime='+timeslot.startTime+'&token='+this.accessToken)
    .then(response=>{
      this.msg = response.data
      console.log(response.data)
    })
    .catch(e=>{
      this.error=true
      console.log(e)
    })

    }
  } 
}






</script>
<style>

</style>