<template>
  <div id="app">
    <input type="text">
    <button @click="send">提交</button>
  </div>
</template>

<script>
  import axios from 'axios'  //引入axios
  export default {
    name: 'App',
    methods: {
      send() {
        console.log()
        this.$ajax({
          method: 'post',
          url: '/asyysy/asyysy_core/indexJson',
          data:{
            url:"http://www.baidu.com"
          },
          http: {
            root: '/',
            headers: {
              "Content-Type": "application/json",
              "Token": "651a05e2ca3408d7985a5d46cb4ffe73"
            }
          }
        });

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

        // /admin/v2/create
      //   var ajax = new XMLHttpRequest();
      //   var token = '651a05e2ca3408d7985a5d46cb4ffe73';
      //   var longUrl = 'http://www.baidu.com';
      //
      //   ajax.open('post','https://dwz.cn/admin/v2/create', 'true');
      //
      //   ajax.setRequestHeader("Content-Type", "application/json");
      //   ajax.setRequestHeader("Token", token);
      //
      //   // 发送请求
      //   ajax.send(JSON.stringify({
      //     url: longUrl
      //   }));
      //
      //   ajax.onreadystatechange = function () {
      //     if (ajax.readyState === 4 && ajax.status === 200) {
      //       // 获取缩短后的网址
      //       console.log(ajax.responseText);
      //     }
      //   }
      }
    }
  }
</script>


