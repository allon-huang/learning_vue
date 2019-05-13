<template>
  <div>
    <h3>欢迎 {{name}}</h3>
    <a href="#" @click="logout">退出登录</a>

    <div class="tab-tit">
      <a href="#" @click="curId=0" :class="{'cur':curId===0}">表格增删改查</a>
      <a href="#" @click="curId=1" :class="{'cur':curId===1}">带分页的表格</a>
      <a href="#" @click="curId=2" :class="{'cur':curId===2}">购物清单</a>
      <a href="#" @click="curId=3" :class="{'cur':curId===3}">小目标</a>
    </div>
    <div class="tab-con">
      <div v-show="curId===0">
        <!--表格增删改查-->
        <div style="padding:20px;" id="appone">
          <div class="panel panel-primary">
            <div class="panel-heading">用户管理</div>
            <table class="table table-bordered table-striped text-center">
              <thead>
              <tr>
                <th>用户名</th>
                <th>年龄</th>
                <th>毕业学校</th>
                <th>备注</th>
                <th>操作</th>
              </tr>
              </thead>
              <tbody>
              <template v-for="row in rows ">
                <tr>
                  <td>{{row.Name}}</td>
                  <td>{{row.Age}}</td>
                  <td>{{row.School}}</td>
                  <td>{{row.Remark}}</td>
                  <td><a href="#" @click="Edit(row)">编辑</a>&nbsp;&nbsp;<a href="#" @click="Delete(row.Id)">删除</a></td>
                </tr>
              </template>
              <tr>
                <td><input type="text" class="form-control" v-model="rowtemplate.Name"/></td>
                <td><input type="text" class="form-control" v-model="rowtemplate.Age"/></td>
                <td><select class="form-control" v-model="rowtemplate.School">
                  　　　　　　　　　　　　　　　　
                  <option>中山小学</option>
                  　　　　　　　　　　　　　　　　
                  <option>复兴中学</option>
                  　　　　　　　　　　　　　　　　
                  <option>光明小学</option>
                  　　　　　　　　　　　　　　　　</select></td>
                <td><input type="text" class="form-control" v-model="rowtemplate.Remark"/></td>
                <td>
                  <button type="button" class="btn btn-primary" v-on:click="Save">保存</button>
                </td>
              </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
      <div v-show="curId===1">
        <!--带分页的表格-->
        <div style="padding:20px;" id="apptwo">
          <div class="panel panel-primary">
            <div class="panel-heading">用户管理</div>
            <table class="table table-bordered table-striped text-center">
              <thead>
              <tr>
                <th>用户名</th>
                <th>年龄</th>
                <th>毕业学校</th>
                <th>备注</th>
              </tr>
              </thead>
              <tbody>
              <template v-for="(row, index) in rows ">
                <tr v-if="index>=(curpage-1)*pagesize&&index<curpage*pagesize">
                  <td>{{row.Name}}</td>
                  <td>{{row.Age}}</td>
                  <td>{{row.School}}</td>
                  <td>{{row.Remark}}</td>
                </tr>
              </template>
              </tbody>
            </table>
          </div>
          <nav style="float:right;">
            <ul class="pagination pagination-lg">
              <template v-for="page in Math.ceil(rows.length/pagesize)">
                <li v-on:click="PrePage()" id="prepage" v-if="page==1" class="disabled"><a href="#">上一页</a></li>
                <li v-if="page==1" class="active" v-on:click="NumPage(page, $event)"><a href="#">{{page}}</a></li>
                <li v-else v-on:click="NumPage(page, $event)"><a href="#">{{page}}</a></li>
                <li id="nextpage" v-on:click="NextPage()" v-if="page==Math.ceil(rows.length/pagesize)"><a
                  href="#">下一页</a></li>
              </template>
            </ul>
          </nav>
        </div>
      </div>
      <div v-show="curId===2">
        <div class="page-shopping-cart" id="shopping-cart">
          <h4 class="cart-title">购物清单</h4>
          <div class="cart-product-title clearfix">
            <div class="td-check fl"><span class="check-span fl check-all" :class="{'check-true':isSelectAll}" @click="selectProduct(isSelectAll)"></span>全选</div>            <div class="td-product fl">商品</div>
            <div class="td-num fl">数量</div>
            <div class="td-price fl">单价(元)</div>
            <div class="td-total fl">金额(元)</div>
            <div class="td-do fl">操作</div>
          </div>
          <div class="cart-product clearfix">
            <table>
              <tbody>
              <!--遍历的时候带上索引-->
              <tr v-for="(item,index) in productList">
                <td class="td-check"><span class="check-span" @click="item.select=!item.select" :class="{'check-true':item.select}"></span></td>
                <td class="td-product"><img :src="item.pro_img" width="98" height="98">
                  <div class="product-info">
                    <h6>{{item.pro_name}}</h6>
                    <p>品牌：{{item.pro_brand}}&nbsp;&nbsp;产地：{{item.pro_place}}</p>
                    <p>规格/纯度:{{item.pro_purity}}&nbsp;&nbsp;起定量：{{item.pro_min}}</p>
                    <p>配送仓储：{{item.pro_depot}}</p>
                  </div>
                  <div class="clearfix"></div>
                </td>
                <td class="td-num">
                  <div class="product-num">
                    <a href="javascript:;" class="num-reduce num-do fl" @click="item.pro_num--"><span></span></a>
                    <input type="text" class="num-input" v-model="item.pro_num">
                    <a href="javascript:;" class="num-add num-do fr" @click="item.pro_num++"><span></span></a>
                  </div>
                </td>
                <td class="td-price">
                  <p class="red-text">￥<span class="price-text">{{item.pro_price.toFixed(2)}}</span></p>
                </td>
                <td class="td-total">
                  <p class="red-text">￥<span class="total-text">{{item.pro_price*item.pro_num}}</span>.00</p>
                </td>
                <td class="td-do"><a href="javascript:;" class="product-delect" @click="deleteOneProduct(index)">删除</a></td>
              </tr>
              </tbody>
            </table>
          </div>
          <div class="cart-product-info">
            <a class="delect-product" href="javascript:;" @click="deleteProduct"><span></span>删除所选商品</a>
            <a class="keep-shopping" href="#"><span></span>继续购物</a>
            <a class="btn-buy fr" href="javascript:;">去结算</a>
            <p class="fr product-total">￥<span>{{getTotal.totalPrice}}</span></p>
            <p class="fr check-num"><span>{{getTotal.totalNum}}</span>件商品总计（不含运费）：</p>
          </div>
          <div class="cart-worder clearfix">
            <a href="javascript:;" class="choose-worder fl"><span></span>绑定跟单员</a>
            <div class="worker-info fl">
            </div>
          </div>
        </div>
      </div>
      <div v-show="curId===3">
        <div id="appthree" class="main">
          <div class="list">
            <h3>添加小目标</h3>
            <input type="text" class="text-keyword" placeholder="输入小目标后，按回车确认" @keyup.13='addList' v-model="addText"/>
            <!--如果noend等于0，就是全部完成了就显示‘全部完成了’，如果没有就是显示已完成多少条（prolist.length-noend）和未完成多少条（noend）-->
            <p>共有{{prolist.length}}个目标，{{noend==0?"全部完成了":'已完成'+(prolist.length-noend)+'，还有'+noend+'条未完成'}}</p>
            <p>
              <input type="radio" name="chooseType" checked="true" @click='chooseList(1)'/><label>所有目标</label>
              <input type="radio" name="chooseType" @click='chooseList(2)'/><label>已完成目标</label>
              <input type="radio" name="chooseType" @click='chooseList(3)'/><label>未完成目标</label>
            </p>
          </div>
          <ul>
            <li class="li1" v-for="(list,index) in newList" :class="{'eidting':curIndex===index}">
              <div>
                <span class="status-span" @click="changeType(index)" :class="{'status-end':list.status}"></span>
                <span @dblclick="curIndex=index">{{list.name}}</span>
                <span class="close" @click='delectList(list)'>X</span>
              </div>
              <input type="text" class="text2" v-model='list.name' @keyup.esc='cancelEdit(list)' @blur='edited' @focus='editBefore(list.name)' @keyup.enter='edited' v-focus/>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>
<!--表格增删改查、带分页的表格-->
<style type="text/css">
  table thead tr th {
    text-align: center;
  }

  .tab-tit {
    font-size: 0;
    width: 600px;
    margin: 0 auto;
  }

  .tab-tit a {
    display: inline-block;
    height: 40px;
    line-height: 40px;
    font-size: 16px;
    width: 25%;
    text-align: center;
    background: #ccc;
    color: #333;
    text-decoration: none;
  }

  .tab-tit .cur {
    background: #09f;
    color: #fff;
  }
</style>
<!--购物清单-->
<style>
  .fl {
    float: left;
  }

  .fr {
    float: right;
  }

  blockquote, body, dd, div, dl, dt, fieldset, form, h1, h2, h3, h4, h5, h6, img, input, li, ol, p, table, td, textarea, th, ul {
    margin: 0;
    padding: 0;
  }

  .clearfix {
    zoom: 1;
  }

  .clearfix:after {
    clear: both;
  }

  .clearfix:after {
    content: '.';
    display: block;
    overflow: hidden;
    visibility: hidden;
    font-size: 0;
    line-height: 0;
    width: 0;
    height: 0;
  }

  a {
    text-decoration: none;
    color: #333;
  }

  img {
    vertical-align: middle;
  }

  .page-shopping-cart {
    margin: 50px auto;
    font-size: 14px;
    border: 1px solid #e3e3e3;
    border-top: 2px solid #317ee7;
  }

  .page-shopping-cart .cart-title {
    color: #317ee7;
    font-size: 16px;
    text-align: left;
    padding-left: 20px;
    line-height: 68px;
  }

  .page-shopping-cart .red-text {
    color: #e94826;
  }

  .page-shopping-cart .check-span {
    display: block;
    width: 24px;
    height: 20px;
    background: url("shopping_cart.png") no-repeat 0 0;
  }

  .page-shopping-cart .check-span.check-true {
    background: url("shopping_cart.png") no-repeat 0 -22px;
  }

  .page-shopping-cart .td-check {
    width: 70px;
  }

  .page-shopping-cart .td-product {
    width: 460px;
  }

  .page-shopping-cart .td-num, .page-shopping-cart .td-price, .page-shopping-cart .td-total {
    width: 270px;
  }

  .page-shopping-cart .td-do {
    width: 150px;
  }

  .page-shopping-cart .cart-product-title {
    text-align: center;
    height: 38px;
    line-height: 38px;
    padding: 0 20px;
    background: #f7f7f7;
    border-top: 1px solid #e3e3e3;
    border-bottom: 1px solid #e3e3e3;
  }

  .page-shopping-cart .cart-product-title .td-product {
    text-align: center;
    font-size: 14px;
  }

  .page-shopping-cart .cart-product-title .td-check {
    text-align: left;
  }

  .page-shopping-cart .cart-product-title .td-check .check-span {
    margin: 9px 6px 0 0;
  }

  .page-shopping-cart .cart-product {
    padding: 0 20px;
    text-align: center;
  }

  .page-shopping-cart .cart-product table {
    text-align: center;
    font-size: 14px;
    clear: both;
  }

  .page-shopping-cart .cart-product table td {
    padding: 20px 0;
  }

  .page-shopping-cart .cart-product table tr {
    border-bottom: 1px dashed #e3e3e3;
  }

  .page-shopping-cart .cart-product table tr:last-child {
    border-bottom: none;
  }

  .page-shopping-cart .cart-product table .product-num {
    border: 1px solid #e3e3e3;
    display: inline-block;
    text-align: center;
  }

  .page-shopping-cart .cart-product table .product-num .num-do {
    width: 24px;
    height: 28px;
    display: block;
    background: #f7f7f7;
  }

  .page-shopping-cart .cart-product table .product-num .num-reduce span {
    background: url("shopping_cart.png") no-repeat -40px -22px;
    display: block;
    width: 6px;
    height: 2px;
    margin: 13px auto 0 auto;
  }

  .page-shopping-cart .cart-product table .product-num .num-add span {
    background: url("shopping_cart.png") no-repeat -60px -22px;
    display: block;
    width: 8px;
    height: 8px;
    margin: 10px auto 0 auto;
  }

  .page-shopping-cart .cart-product table .product-num .num-input {
    width: 42px;
    height: 28px;
    line-height: 28px;
    border: none;
    text-align: center;
  }

  .page-shopping-cart .cart-product table .td-product {
    text-align: left;
    font-size: 12px;
    line-height: 20px;
  }

  .page-shopping-cart .cart-product table .td-product img {
    border: 1px solid #e3e3e3;
    margin-right: 10px;
  }

  .page-shopping-cart .cart-product table .td-product .product-info {
    display: inline-block;
    vertical-align: middle;
  }

  .page-shopping-cart .cart-product table .td-do {
    font-size: 12px;
  }

  .page-shopping-cart .cart-product-info {
    height: 50px;
    line-height: 50px;
    background: #f7f7f7;
    padding-left: 20px;
  }

  .page-shopping-cart .cart-product-info .delect-product {
    color: #666;
  }

  .page-shopping-cart .cart-product-info .delect-product span {
    display: inline-block;
    vertical-align: top;
    margin: 18px 8px 0 0;
    width: 13px;
    height: 15px;
    background: url("shopping_cart.png") no-repeat -60px 0;
  }

  .page-shopping-cart .cart-product-info .product-total {
    font-size: 14px;
    color: #e94826;
  }

  .page-shopping-cart .cart-product-info .product-total span {
    font-size: 20px;
  }

  .page-shopping-cart .cart-product-info .check-num {
    color: #333;
  }

  .page-shopping-cart .cart-product-info .check-num span {
    color: #e94826;
  }

  .page-shopping-cart .cart-product-info .keep-shopping {
    color: #666;
    margin-left: 40px;
  }

  .page-shopping-cart .cart-product-info .keep-shopping span {
    display: inline-block;
    vertical-align: top;
    margin: 18px 8px 0 0;
    width: 15px;
    height: 15px;
    background: url("shopping_cart.png") no-repeat -40px 0;
  }

  .page-shopping-cart .cart-product-info .btn-buy {
    height: 50px;
    color: #fff;
    font-size: 20px;
    display: block;
    width: 110px;
    background: #ff7700;
    text-align: center;
    margin-left: 30px;
  }

  .page-shopping-cart .cart-worder {
    padding: 20px;
  }

  .page-shopping-cart .cart-worder .choose-worder {
    color: #fff;
    display: block;
    background: #39e;
    width: 140px;
    height: 40px;
    line-height: 40px;
    border-radius: 4px;
    text-align: center;
    margin-right: 20px;
  }

  .page-shopping-cart .cart-worder .choose-worder span {
    display: inline-block;
    vertical-align: top;
    margin: 9px 10px 0 0;
    width: 22px;
    height: 22px;
    background: url("shopping_cart.png") no-repeat -92px 0;
  }

  .page-shopping-cart .cart-worder .worker-info {
    color: #666;
  }

  .page-shopping-cart .cart-worder .worker-info img {
    border-radius: 100%;
    margin-right: 10px;
  }

  .page-shopping-cart .cart-worder .worker-info span {
    color: #000;
  }

  .choose-worker-box {
    width: 620px;
    background: #fff;
  }

  .choose-worker-box .box-title {
    height: 40px;
    line-height: 40px;
    background: #F7F7F7;
    text-align: center;
    position: relative;
    font-size: 14px;
  }

  .choose-worker-box .box-title a {
    display: block;
    position: absolute;
    top: 15px;
    right: 16px;
    width: 10px;
    height: 10px;
    background: url("shopping_cart.png") no-repeat -80px 0;
  }

  .choose-worker-box .box-title a:hover {
    background: url("shopping_cart.png") no-repeat -80px -22px;
  }

  .choose-worker-box .worker-list {
    padding-top: 30px;
    height: 134px;
    overflow-y: auto;
  }

  .choose-worker-box .worker-list li {
    float: left;
    width: 25%;
    text-align: center;
    margin-bottom: 30px;
  }

  .choose-worker-box .worker-list li p {
    margin-top: 8px;
  }

  .choose-worker-box .worker-list li.cur a {
    color: #f70;
  }

  .choose-worker-box .worker-list li.cur a img {
    border: 1px solid #f70;
  }

  .choose-worker-box .worker-list li a:hover {
    color: #f70;
  }

  .choose-worker-box .worker-list li a:hover img {
    border: 1px solid #f70;
  }

  .choose-worker-box .worker-list li img {
    border: 1px solid #fff;
    border-radius: 100%;
  }
</style>
<!--小目标-->
<style>
  .hidden{display: none;}
.main{width: 800px;margin: 0 auto;}
li{list-style-type: none;line-height: 40px;position: relative;border: 1px solid transparent;padding: 0 20px;}
li .status-span{display: block;width: 10px;height: 10px;background: #ccc;margin: 14px 10px 0 0 ;float: left;}
li .status-span.status-end{
  background: #09f;
}
li .close{position: absolute;color: #f00;font-size: 20px;line-height: 40px;height: 40px;right: 20px;cursor: pointer;display: none;top: 0;}
li:hover{border: 1px solid #09f;}
li:hover .close{display: block;}
li div{display: block;}
li.eidting div{display: none;}
li .text2{height: 40px;padding-left: 10px;box-sizing: border-box;margin-left: 10px;width: 80%;display: none;}
li.eidting .text2{display: block;}
li .text-keyword{height: 40px;padding-left: 10px;box-sizing: border-box;margin-left: 10px;width: 80%;display: none;}
.text-keyword{box-sizing: border-box;width: 100%;height: 40px;padding-left: 10px;outline: none;}
</style>
<script>
  /*引入公共方法*/
  import { setCookie,getCookie,delCookie } from '../../assets/js/cookie.js'
  export default{
    data(){
      return {
//        表格增删改查、带分页的表格
        name: '',
        curId: 0,
        rows: [
          {Id: 1, Name: '小明', Age: 18, School: '光明小学', Remark: '三好学生'},
          {Id: 2, Name: '小刚', Age: 20, School: '复兴中学', Remark: '优秀班干部'},
          {Id: 3, Name: '吉姆格林', Age: 19, School: '光明小学', Remark: '吉姆做了汽车公司经理'},
          {Id: 4, Name: '李雷', Age: 25, School: '复兴中学', Remark: '不老实的家伙'},
          {Id: 5, Name: '韩梅梅', Age: 22, School: '光明小学', Remark: '在一起'},
          {Id: 1, Name: '小明', Age: 18, School: '光明小学', Remark: '三好学生'},
          {Id: 2, Name: '小刚', Age: 20, School: '复兴中学', Remark: '优秀班干部'},
          {Id: 3, Name: '吉姆格林', Age: 19, School: '光明小学', Remark: '吉姆做了汽车公司经理'},
          {Id: 4, Name: '李雷', Age: 25, School: '复兴中学', Remark: '不老实的家伙'},
          {Id: 5, Name: '韩梅梅', Age: 22, School: '光明小学', Remark: '在一起'},
          {Id: 1, Name: '小明', Age: 18, School: '光明小学', Remark: '三好学生'},
          {Id: 2, Name: '小刚', Age: 20, School: '复兴中学', Remark: '优秀班干部'},
          {Id: 3, Name: '吉姆格林', Age: 19, School: '光明小学', Remark: '吉姆做了汽车公司经理'},
          {Id: 4, Name: '李雷', Age: 25, School: '复兴中学', Remark: '不老实的家伙'},
          {Id: 5, Name: '韩梅梅', Age: 22, School: '光明小学', Remark: '在一起'},
          {Id: 1, Name: '小明', Age: 18, School: '光明小学', Remark: '三好学生'},
          {Id: 2, Name: '小刚', Age: 20, School: '复兴中学', Remark: '优秀班干部'},
          {Id: 3, Name: '吉姆格林', Age: 19, School: '光明小学', Remark: '吉姆做了汽车公司经理'},
          {Id: 4, Name: '李雷', Age: 25, School: '复兴中学', Remark: '不老实的家伙'},
          {Id: 5, Name: '韩梅梅', Age: 22, School: '光明小学', Remark: '在一起'},
        ],
        pagesize: 6,
        curpage: 1,//当前页的页码
        rowtemplate: {Id: 0, Name: '', Age: '', School: '', Remark: ''},
//        购物清单
        productList: [
          {
            'pro_name': '【斯文】甘油 | 丙三醇',//产品名称
            'pro_brand': 'skc',//品牌名称
            'pro_place': '韩国',//产地
            'pro_purity': '99.7%',//规格
            'pro_min': "215千克",//最小起订量
            'pro_depot': '上海仓海仓储',//所在仓库
            'pro_num': 3,//数量
            'pro_img': '../../assets/logo.png',//图片链接
            'pro_price': 800//单价
          },
          {
            'pro_name': '【HS】韩束 | 高锰酸钾',//产品名称
            'pro_brand': 'hansu',//品牌名称
            'pro_place': '韩国',//产地
            'pro_purity': '99.99%',//规格
            'pro_min': "312千克",//最小起订量
            'pro_depot': '上海仓海仓储',//所在仓库
            'pro_num': 1,//数量
            'pro_img': '../../assets/logo.png',//图片链接
            'pro_price': 1200//单价
          }
        ],
//        小目标
        addText:'',
        //name-名称,status-完成状态
        prolist:[
          {name:"HTML5",status:false},
          {name:"CSS3",status:false},
          {name:"vue",status:false},
          {name:"react",status:false}
        ],
        newList:[],
        curIndex:'',
        beforeEditText:"",
        curType:0
      }
    },
    mounted(){
//      退出登录
      /*页面挂载获取保存的cookie值，渲染到页面上*/
      let uname = getCookie('username')
      this.name = uname
      /*如果cookie不存在，则跳转到登录页*/
      if (uname == "") {
        this.$router.push('/')
      }
//      购物清单
      //为productList添加select（是否选中）字段，初始值为true
      var _this = this;
      //为productList添加select（是否选中）字段，初始值为true
      this.productList.map(function (item) {
        _this.$set(item, 'select', true);
      })
      //要像上面这样写双向绑定才能起效，下面的写法是有问题的，双向绑定不起效的！
      //this.productList.map(function (item) {item.select=true})
//      小目标
      //初始化，把prolist赋值给newList。默认显示所有目标
      this.newList=this.prolist;
    },
    computed: {
//      购物清单
      //检测是否全选
      isSelectAll(){
        //如果productList中每一条数据的select都为true，返回true，否则返回false;
        return this.productList.every(function (val) { return val.select});
      },
      //获取总价和产品总件数
      getTotal(){
        //获取productList中select为true的数据。
        var _proList=this.productList.filter(function (val) { return val.select}),totalPrice=0;
        for(var i=0,len=_proList.length;i<len;i++){
          //总价累加
          totalPrice+=_proList[i].pro_num*_proList[i].pro_price;
        }
        //选择产品的件数就是_proList.length，总价就是totalPrice
        return {totalNum:_proList.length,totalPrice:totalPrice}
      },
//      小目标
      //计算属性，返回未完成目标的条数，就是数组里面status=false的条数
      noend(){
        return this.prolist.filter(function(item){
          return !item.status
        }).length;
      }
    },
    methods: {
//      退出登录
      logout(){
        this.$http.post('/asyysy/asyysy_core/user/logout').then((res) => {
          console.log(res)
        delCookie('username')
        if (res.bodyText == "用户未登录") {
          setTimeout(function () {
            this.$router.push('/')
          }.bind(this), 1000)
        }
      })
      },
//      表格增删改查、带分页的表格
      Save(event) {
        if (this.rowtemplate.Id == 0) {
          //设置当前新增行的Id
          this.rowtemplate.Id = this.rows.length + 1;
          this.rows.push(this.rowtemplate);
        }
        //还原模板
        this.rowtemplate = {Id: 0, Name: '', Age: '', School: '', Remark: ''}
      },
      Delete (id) {
        //实际项目中参数操作肯定会涉及到id去后台删除，这里只是展示，先这么处理。
        for (var i = 0; i < this.rows.length; i++) {
          if (this.rows[i].Id == id) {
            this.rows.splice(i, 1);
            break;
          }
        }
      },
      Edit (row) {
        this.rowtemplate = row;
      },
      //上一页方法
      PrePage (event) {
        $(".pagination .active").prev().trigger("click");
      },
      //下一页方法
      NextPage (event) {
        $(".pagination .active").next().trigger("click");
      },
      //点击页码的方法
      NumPage (num, event) {
        if (this.curpage == num) {
          return;
        }
        this.curpage = num;
        $(".pagination li").removeClass("active");
        if (event.target.tagName.toUpperCase() == "LI") {
          $(event.target).addClass("active");
        }
        else {
          $(event.target).parent().addClass("active");
        }
        if (this.curpage == 1) {
          $("#prepage").addClass("disabled");
        }
        else {
          $("#prepage").removeClass("disabled");
        }
        if (this.curpage == Math.ceil(this.rows.length / this.pagesize)) {
          $("#nextpage").addClass("disabled");
        }
        else {
          $("#nextpage").removeClass("disabled");
        }
      },
//      购物清单
      //全选与取消全选
      selectProduct(_isSelect){
        //遍历productList，全部取反
        for (var i = 0, len = this.productList.length; i < len; i++) {
          this.productList[i].select = !_isSelect;
        }
      },
      //删除已经选中(select=true)的产品
      deleteProduct () {
        this.productList=this.productList.filter(function (item) {return !item.select})
      },
      //删除单条产品
      deleteOneProduct (index) {
        //根据索引删除productList的记录
        this.productList.splice(index,1);
      },
//      小目标
      addList(){
        //添加进来默认status=false,就是未完成状态
        this.prolist.push({
          name:this.addText,
          status:false
        });
        //添加后，清空addText
        this.addText="";
      },
      chooseList(type){
        //type=1时，选择所有目标
        //type=2时，选择所有已完成目标
        //type=3时，选择所有未完成目标
        this.curType=type;
        switch(type){
          case 1:this.newList=this.prolist;break;
          case 2:this.newList=this.prolist.filter(function(item){return item.status});break;
          case 3:this.newList=this.prolist.filter(function(item){return !item.status});break;
        }
      },
      /*改变单条数据的完成状态*/
      changeType(index){
        this.newList[index].status=!this.newList[index].status;
        //更新数据
        this.chooseList(this.curType);
      },
      delectList(list){
        var index=this.prolist.indexOf(list);
        //根据索引，删除数组某一项
        this.prolist.splice(index,1);
        //更新newList  newList可能经过this.prolist.filter()赋值，这样的话，删除了prolist不会影响到newList  那么就要手动更新newList
        //this.newList=this.prolist;
        this.chooseList(this.curType);
      },
      //修改前
      editBefore(name){
        //先记录当前项（比如这一项，{name:"HTML5",status:false}）
        //beforeEditText="HTML5"
        this.beforeEditText=name;
      },
      //修改完成后
      edited(){
        //修改完了，设置curIndex=""，这样输入框就隐藏，其它元素就会显示。因为在li元素 写了：:class="{'eidting':curIndex===index}"  当curIndex不等于index时，eidting类名就清除了！
        //输入框利用v-model绑定了当前项（比如这一项，{name:"HTML5",status:false}）的name,当在输入框编辑的时候，比如改成‘HTML’,实际上当前项的name已经变成了‘HTML’，所以，这一步只是清除eidting类名，隐藏输入框而已
        //还有一个要注意的就是虽然li遍历的是newList，比如改了newList的这一项（{name:"HTML5",status:false}），比如改成这样（{name:"HTML",status:true}）。实际上prolist的这一项（{name:"HTML5",status:false}），也会被改成（{name:"HTML",status:true}）。因为这里是一个对象，而且公用一个堆栈！修改其中一个，另一个会被影响到
        this.curIndex="";
      },
      //取消修改
      cancelEdit(val){
        //上面说了输入框利用v-model绑定了当前项（比如这一项，{name:"HTML5",status:false}）的name,当在输入框编辑的时候，比如改成‘HTML’,实际上当前项的name已经变成了‘HTML’，所以，这一步就是把之前保存的beforeEditText赋值给当前项的name属性，起到一个恢复原来值得作用！
        val.name=this.beforeEditText;
        this.curIndex="";
      }
    },
    directives:{
      "focus":{
        update(el){
          el.focus();
        }
      }
    }
  }
</script>
