<template>
  <div class="weatherContainer">
    <div class="weatherDropdownContainer">
      <select class="weatherDropdown" v-model="citySelected">                                        
        <option class="weatherOption" :value="city.id" v-for="city in cityPool" >{{city.name}}</option>                                    
      </select>
    </div>
    <div class="weatherBtnContainer">
      <input id="selecBtn" type="button" value="查询" v-on:click="selectFn($event)"/>
    </div>
    <div  class="weatherInfoContainer" v-if="shouldShowWeatherInfo">
      <div><span class="weatherInfoLeft">城市:</span><span class="weatherInfoRight">{{weatherData.city}}</span></div>
      <div><span class="weatherInfoLeft">天气:</span><span class="weatherInfoRight">{{weatherData.weather}}</span></div>
      <div><span class="weatherInfoLeft">温度:</span><span class="weatherInfoRight">{{weatherData.temperature}}</span></div>
      <div><span class="weatherInfoLeft">风向:</span><span class="weatherInfoRight">{{weatherData.wind}}</span></div>
      <div><span class="weatherInfoLeft">更新时间:</span><span class="weatherInfoRight">{{weatherData.updateTime}}</span></div>
    </div>
    <div class="weatherErrorContainer" v-if="shouldShowErrorMsg">{{errorMsg}}</div>
  </div>
 
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      cityPool: [{'id': 0, 'name' : '请选择'}],
      citySelected: '',
      weatherData: '',
      errorMsg: '',
      shouldShowWeatherInfo: false,
      shouldShowErrorMsg: false
    }
  },
  created(){
      this.citySelected = this.cityPool[0].id;
      this.resetPageVisibility();

      this.$axios({
          method:'get',
          url:'/api/cities'
      }).then((response) =>{
          if(response.data.code === '888')
          {
            this.cityPool = this.cityPool.concat(response.data.data.cityModel);
            this.updatePageVisibility(false,'');
          }else{
            this.updatePageVisibility(true,'系统错误，请稍后再试');
          }
      }).catch((error) =>{
        this.updatePageVisibility(true,'获取城市列表失败，请尝试刷新页面');
      })
  },
  methods: {
      selectFn(e) {
        if(this.citySelected != 0)
        {
           this.resetPageVisibility();
          this.$axios({
            method:'get',
            url:'/api/weather/'+this.citySelected
          }).then((response) =>{
            if(response.data.code === '888')
            {
              this.weatherData = response.data.data;
              this.updatePageVisibility(false,'');
            }else{
              this.updatePageVisibility(true,'系统错误，请稍后再试');
            }
            this.shouldShowWeatherInfo = ! this.shouldShowErrorMsg;
          }).catch((error) =>{
              if(error.toString().indexOf("timeout") != -1)
              {
                this.updatePageVisibility(true,'获取天气超时，请稍后再试');
              }else{
                this.updatePageVisibility(true,'系统错误，请稍后再试');
              }
              this.shouldShowWeatherInfo = ! this.shouldShowErrorMsg;
          })   
        }
      },

      resetPageVisibility(){ 
        this.shouldShowWeatherInfo = false;
        this.shouldShowErrorMsg = false;
      },

      updatePageVisibility(shouldShowError, errorMsg)
      {
        if(shouldShowError)
        {
          this.errorMsg = errorMsg;
        }
        this.shouldShowErrorMsg = shouldShowError;
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
.weatherContainer{
  font-family: Helvetica, Arial;
  font-size: 18px !important;
}
.weatherDropdown{
  font-family: Helvetica, Arial;
  font-size: 18px !important;
  height: 30px;
}
.weatherOption{
  font-family: Helvetica, Arial;
  font-size: 18px !important;
}
#selecBtn{
  font-family: Helvetica, Arial;
  font-size: 18px !important;
}
.weatherDropdownContainer{
  display: inline-block;
}
.weatherBtnContainer{
  display: inline-block;
}
.weatherInfoContainer{
  padding-top: 8px;
}
.weatherErrorContainer{
  padding-top: 8px;
}
.weatherInfoLeft{
  display: inline-block;
  width: 50%;
  text-align: right;
}
.weatherInfoRight{
  display: inline-block;
  width: 50%;
  text-align: left;
}
</style>
