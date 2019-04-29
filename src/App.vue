<template>
  <div id="app">
    <input type="text">
    <button @click="send">提交</button>
    <ul>
      <li v-for="item in asyysy_data" v-bind:onclick="clickItem('{{item.pkid}}')" v-if="item.pkid != 3">{{item.keyword}}</li>
    </ul>
  </div>
</template>

<script>
  import axios from 'axios'  //引入axios
  export default {
    name: 'App',
    data(){
      return{
        asyysy_data:[]
      }
    },
    mounted:function(){
      // 初始化执行方法
      this.getAsyysyData();
    },
    methods: {
      clickItem(pkid){
        console.log(pkid);
      },
      getAsyysyData(){
        let that = this;
        this.$ajax({
          method: 'post',
          url: '/asyysy/asyysy_core/indexJson',
          data:{
            url:"http://www.baidu.com"
          }
        }).then(function (res) {
          that.asyysy_data = res.data;
        })
      },
      send() {
        let that = this;
        console.log()

        axios.post("/baidu_dwz/admin/v2/create",{
          url:'https://www.baidu.com'
        },{
          headers: {
            'content-type': 'application/json',
            "Token":'651a05e2ca3408d7985a5d46cb4ffe73'  //token换成从缓存获取
          }
        }).then(function(res){
          console.log('post请求...');
          console.log(res.data);
        });
      }
    }
  }
</script>


