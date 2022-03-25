<template>
  <div class="country-rates__wrapper">
    <div :class="countryList?'country-rates__input-container country-rates_open':'country-rates__input-container'">
      <input class="country-rates__input" readonly="readonly" placeholder="Select country" @click = "showCountryList">
      <div class="country-rates__country-list" ref="countryRates" v-show="countryList">
        <div class="country-rates__item" :countryId="arg['id']"
               v-for="(arg,index) in args"
               :key="args[index]">
                {{arg['name']}}
        </div>
      </div>
    </div>
    <div class="country-rates__table-container">
      <table class="country-rates__table">
        <thead>
        <tr class="country-rates__table-row">
          <th class="country-rates__table-column">Country</th>
          <th class="country-rates__table-column">Type</th>
          <th class="country-rates__table-column">Code</th>
          <th class="country-rates__table-column">Rate per minute</th>
        </tr>
        </thead>
        <tbody class="country-rates__table-body"></tbody>
      </table>

    </div>
  </div>
</template>

<script>
  import fetchJsonp from 'fetch-jsonp';
  export default {
    name: 'App',
    data(){
      return{
        args:[],
        rateData:[],
        countryList:false
      }
    },
    mounted(){
       this.$refs.countryRates.addEventListener('click',(e)=>{
          let id=e.target.getAttribute("countryId");
          this.httpGetData('http://www.ringcentral.com/api/index.php?cmd=getInternationalRates&param[internationalRatesRequest][brandId]=1210&param[internationalRatesRequest][countryId]='+id+'&param[internationalRatesRequest][tierId]=3311&typeResponse=json');
       });
      this.httpGetData('http://www.ringcentral.com/api/index.php?cmd=getCountries&typeResponse=json&luid=22');
    },
    methods:{
      httpGetData(api) {
        fetchJsonp(api, {
          method: 'GET',
        }).then(response=> {
              return response.json()
          }).then(json=> {
              if(json.result){
                  this.args=json.result;
              }
              else if(json.rates){
                    this.sortRateData(json.rates)
              }
        }).catch(ex=>{
              console.log('parsing failed', ex)
        })
       },
       sortRateData(data){
           let valueArr='';
           let countryName=data[0]['key']['name'];
           let sortValueArr=[];

           (data[0]['value'][0].length===undefined) ? valueArr=data[0]['value']:valueArr=data[0]['value'][0];



           for(var key in valueArr){
               console.log(valueArr[key]['type']);
           }
       },
      showCountryList(){
        this.countryList=!this.countryList;
      },
    }

  }
</script>

<style>
  .country-rates__input-container{
    width:100%;
    position: relative;
  }

  .country-rates__input{
    background-color: #fff;
    height: 50px;
    font-size: 18px;
    box-sizing: border-box;
    border: 1px solid #b7ada5;
    display:block;
    width:100%;
    padding:0 20px;
    cursor: pointer;
  }

  .country-rates__input:focus {
    outline: none;
  }

  .country-rates__input-container:after{
    content: '';
    display: block;
    position: absolute;
    right: 15px;
    top: 35%;
    width: 10px;
    height: 10px;
    border-width: 2px 2px 0 0;
    border-style: solid;
    border-color: #b9b9b9;
    transform: rotate(135deg);
    transition: transform .3s ease;
    pointer-events: none;
  }

  .country-rates_open:after{
    content: '';
    display: block;
    position: absolute;
    right: 15px;
    top: 35%;
    width: 10px;
    height: 10px;
    border-width: 2px 2px 0 0;
    border-style: solid;
    border-color: #b9b9b9;
    transform: rotate(315deg);
    transition: transform .3s ease;
    pointer-events: none;
  }

  .country-rates__country-list{
    position: absolute;
    left: 0;
    right: 0;
    padding: 10px 0;
    background-color: #fff;
    max-height: 300px;
    overflow-y: scroll;
    box-shadow: -1px 1px 3px 0px #a7a7a7;
    font-size: 1em;
    z-index: 1;
  }

  .country-rates_open > .country-rates__country-list{
    display: block;
  }

  .country-rates__item{
    position: relative;
    padding: 5px 0 5px 30px;
    font-size: .9em;
    user-select: none;
    -moz-user-select: none;
    -o-user-select:none;
    -khtml-user-select: none;
    -webkit-user-select: none;
    -ms-user-select: none;
    user-select: none;
  }

  .country-rates__item:hover{
    cursor: pointer;
    background: #a7a7a7;
  }

  .country-rates_selected{
    font-weight: bold;
  }

  .country-rates_selected:before {
    content: "";
    position: absolute;
    width: 9px;
    height: 2px;
    transform: rotate(45deg);
    left: 8px;
    top: 15px;
    background: black;
  }

  .country-rates_selected:after {
    content: "";
    position: absolute;
    width: 15px;
    height: 2px;
    transform: rotate(-45deg);
    left: 13px;
    top: 14px;
    background: black;
  }


  /*////////////////////////////////////////TABLE///////////////////////////*/

  .country-rates__table-column{
    border-bottom: 1px solid #d6d6d5;
    padding: 5px;
    text-align: left;
  }

  .country-rates__column-code{
    width: 40%;
    overflow-wrap: break-word;
  }

  .country-rates__table-container{
    padding: 30px 0;
    width: 100%;
  }

  .country-rates__table{
    width:100%;
    position: relative;
  }

  .country-rates__table-body{
    text-align: center;
  }

  .country-rates__table-row{
    height: 50px;
    position: relative;
  }

  /*//////////////////LOADER*/

  .country-rates__preloader_text{
    position: absolute;
    font-size:20px;
    background: deepskyblue;
    color:white;
    left:0;
    right:0;
  }

  .country-rates__preloader{
    position: absolute;
    top: 80px;
    left: 40%;
    transform: translate(-40%, -50%);
    animation: effect 1s linear infinite;
    width: 32px;
    height: 32px;
    border-radius: 50%;
    border: 6px solid rgba(255, 255, 255, 0.3);
    border-top-color: #49cdff;
  }

  @-webkit-keyframes effect {
    0% {
      transform: rotate(0deg);
    }

    100% {
      transform: rotate(360deg);
    }
  }

  @keyframes effect {
    0% {
      transform: rotate(0deg);
    }

    100% {
      transform: rotate(360deg);
    }
  }


  .country-list-preloader:after {
    overflow: hidden;
    display: inline-block;
    vertical-align: bottom;
    -webkit-animation: ellipsis steps(4,end) 900ms infinite;
    animation: ellipsis steps(4,end) 900ms infinite;
    content: "\2026"; /* ascii code for the ellipsis character */
    width: 0px;
  }

  @keyframes ellipsis {
    to {
      width: 20px;
    }
  }

  @-webkit-keyframes ellipsis {
    to {
      width: 20px;
    }
  }
</style>
