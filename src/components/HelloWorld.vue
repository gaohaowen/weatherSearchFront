<template>
  <div class="">
    <div>
    <select v-model="citySelected">                                        
      <option :value="city" v-for="city in cityPool" >{{city}}</option>                                    
    </select>
    </div>
    <div>
      <input type="button" value="查询" v-on:click="selectFn($event)"/>
    </div>
    <div v-if="!shouldShowError">
      <div><span>城市</span><span>{{weatherData.city}}</span></div>
      <div><span>天气</span><span>{{weatherData.weather}}</span></div>
      <div><span>温度</span><span>{{weatherData.temperature}}</span></div>
      <div><span>风向</span><span>{{weatherData.wind}}</span></div>
      <div><span>更新时间</span>{{weatherData.updateTime}}<span></span></div>
    </div>
    <div v-if="shouldShowError">{{showError}}</div>
    <div v-if="shouldShowCityPoolTimeoutError">{{showCityPoolTimeoutError}}</div>
    <div v-if="shouldShowWeatherTimeError">{{showWeatherTimeoutError}}</div>
  </div>
 
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      cityPool: '',
      citySelected: '',
      weatherData: '',
      showError: '系统错误，请稍后再试',
      showCityPoolTimeoutError: '获取城市列表失败，请刷新页面',
      showWeatherTimeoutError: '获取天气超时，请稍后再试',
      shouldShowError: false,
      shouldShowCityPoolTimeoutError: false,
      shouldShowWeatherTimeError: false
    }
  },
  created(){
      this.shouldShowError = false;
      this.$axios({
          method:'get',
          url:'http://localhost:8080/api/cities'
      }).then((response) =>{
          if(response.data.code === '888')
          {
            this.cityPool = response.data.data.cities;
            this.citySelected = this.cityPool[0];
          }else{
            this.shouldShowError = true;
          }
      }).catch((error) =>{
        this.shouldShowCityPoolTimeoutError = true;
      })
  },
  methods: {
      selectFn(e) {
        this.shouldShowError = false;
        this.$axios({
          method:'get',
          url:'http://localhost:8080/api/weather/'+this.citySelected
        }).then((response) =>{
          if(response.data.code === '888')
          {
            this.weatherData = response.data.data;
          }else{
            this.shouldShowError = true;
          }
        }).catch((error) =>{
            if(error.toString().indexOf("timeout") != -1)
            {
              console.log(error)
              this.shouldShowWeatherTimeError = true;
            }else{
              console.log(error)
              this.shouldShowError = true;
            }
           
        })   
      }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
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
  color: #42b983;
}
</style>
