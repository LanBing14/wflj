<template>
    <div id="balance">
      <mt-header fixed title="余额" style="font-size:1.2rem;height: 3rem;">
        <mt-button slot="right" @click="changeDetails">明细</mt-button>
      </mt-header>
      <div class="balanceInfo">
        <p class="freezeMoney">提现中：￥{{freezeMoney}}元</p>
        <p class="withdraw">￥{{balance}}</p>
        <p class="withdrawTitle">可提现金额</p>


      </div>
      <div class="apply_for" @click="goApply">申请提现</div>

      <!--蒙版-->
      <div class="box" id="box" v-show="isShow" @click="hideToggle"></div>
      <div class="hintBox" v-show="hintShow">
        <div class="attention">
          <p class="title"><img src="../../img/jingao.png" alt="">提现提醒</p>
          <p class="light">您需下单购买任一套餐，才可提现</p>
        </div>
        <div class="btns">
          <div class="cancel" @click="goNext">下次提现</div>
          <div class="true" @click="goPlaces">立即下单</div>
        </div>
      </div>
    </div>
</template>

<script>

  import axios from "axios";
  import qs from "qs";
	import utils from "../common/common.js";
  
    export default {
        name:'balance',
        data(){
          return{
            isShow:false,
            hintShow:false,
            balance:"",
          freezeMoney:"",
           checkOrder:0,


          }
        },
        methods:{
        	//首页相关信息+openid存
			login: function() {
				var $this = this;
				var data = qs.stringify({
					paramUrl: window.location.href
				});
				var baseUrl = BaseUrl + "index/getCode";
				axios({
						method: "POST",
						url: baseUrl,
						type: "json",
						data: data
					})
					.then(function(data) {
						let datas = data.data.data;
						//						   location.href = datas.url;
						if((data.data.status = 1)) {
							//							 window.location.href = datas.url;
							this.targetUrl = datas.url;
							var code = utils.getQueryString(
								"http://egg.wufuapp.com/?code=061VzK2U1j3xy61xoe3U16cI2U1VzK21&state=1#/",
								"code"
							);
						} else {

						}
					})
					.catch(function() {
						//alert("程序异常，联系技术人员")
					});
			},
			//获取openid
			logins: function() {
				var $this = this;
				var baseUrl = BaseUrl + "index/getUserOpenid";
				var data = qs.stringify({
					code: utils.getQueryString("code")
				});
				axios({
					method: "post",
					url: baseUrl,
					type: "json",
					data: data
				}).then(function(data) {
					if(data.data.status == 1) {
						$this.openid = data.data.data.openid;
						localStorage.setItem('openid', $this.openid);
					}
				});
			},

          //返回到明细
          changeDetails(){
            this.$router.push({path:'/balanceDetails'});
          },
          goNext(){
            this.isShow = !this.isShow;
            this.hintShow = !this.hintShow;
          },
          hideToggle(){
            this.isShow = !this.isShow;
            this.hintShow = !this.hintShow;
          },
          //点击申请提现的判断条件
          goApply(){
//        	this.goPlace();
			  var $this = this;
            var baseUrl = BaseUrl+"index/getUserBalance";
            var datas = qs.stringify({
              openid: localStorage.getItem('openid'),
            });

            axios({
              method: "post",
              url: baseUrl,
              type: "json",
              data: datas
            }).then(function(data) {
                let datas = data.data.data;
                if ((data.data.status = 1)) {
                  $this.checkOrder=datas.checkOrder;
                  if($this.checkOrder == 1){
                  	$this.$router.push({name:'placeNow',query:{balances:$this.balance}});
                  }else{
                  	 $this.isShow = true;
              		$this.hintShow = true;
                  }
                } else {

                }
              })
              .catch(function() {
                //alert("程序异常，联系技术人员")
              });

            //如果没有购买过则弹出’提醒去下单‘的框
//          var $this = this;
//          if($this.checkOrder==0){
//            this.isShow = !this.isShow;
//            this.hintShow = !this.hintShow;
//          }else {
//            $this.$router.push({name:'placeNow',params:{balances:$this.balance}});
//
//          }

            //如果满足这两个条件Toast‘申请成功’

          },
          //点击'立即下单'跳转到下单页面
          goPlace(){
            var $this = this;
            var baseUrl = BaseUrl+"index/getUserBalance";
            var datas = qs.stringify({
              openid: localStorage.getItem('openid'),
            });

            axios({
              method: "post",
              url: baseUrl,
              type: "json",
              data: datas
            }).then(function(data) {
                let datas = data.data.data;
                if ((data.data.status = 1)) {
                	console.log(data)
                  $this.balance=datas.balance == null ?0:datas.balance;
                  $this.freezeMoney=datas.freezeMoney == null ? 0:datas.freezeMoney;
                  $this.checkOrder=datas.checkOrder;
                } else {

                }
              })
              .catch(function() {
                //alert("程序异常，联系技术人员")
              });



           //
          },
          goPlaces(){
            this.$router.push(
            	{path:'/product_details',
            	query:{
            		showSize:1
            	}
            	});
          },
          //首页相关信息+openid存
			login: function() {
				var $this = this;
				var data = qs.stringify({
					paramUrl: window.location.href
				});
				var baseUrl = BaseUrl + "index/getCode";
				axios({
						method: "POST",
						url: baseUrl,
						type: "json",
						data: data
					})
					.then(function(data) {
						let datas = data.data.data;
						//						   location.href = datas.url;
						if((data.data.status = 1)) {
							//							 window.location.href = datas.url;
							this.targetUrl = datas.url;
							var code = utils.getQueryString(
								"http://egg.wufuapp.com/?code=061VzK2U1j3xy61xoe3U16cI2U1VzK21&state=1#/",
								"code"
							);
						} else {

						}
					})
					.catch(function() {
						//alert("程序异常，联系技术人员")
					});
			},
			//获取openid
			logins: function() {
				var $this = this;
				var baseUrl = BaseUrl + "index/getUserOpenid";
				var data = qs.stringify({
					code: utils.getQueryString("code")
				});
				axios({
					method: "post",
					url: baseUrl,
					type: "json",
					data: data
				}).then(function(data) {
					if(data.data.status == 1) {
						$this.openid = data.data.data.openid;
						localStorage.setItem('openid', $this.openid);
					}
				});
			},

        },
        mounted(){
        	if(!localStorage.getItem("openid")) {
				if(utils.getQueryString("code")) {
					this.logins();
				} else {
					this.login();

				}
			}
          this.goPlace();

        },
        components:{}
    }

</script>

<style lang="scss" scoped>
#balance{
  min-height: 41.6rem;
  .mint-header {
    background: #cc3e36;
  }
  .balanceInfo{
    padding-top: 3rem;
    width: 100%;
    text-align: center;
    .freezeMoney{
      width: 85%;
      text-align: right;
      line-height: 5rem;
    }
    .withdraw{
      font-size: 1.3rem;
      color: #cc3e36;
      margin: 2rem 0 .5rem 0;
    }
    .withdrawTitle{
      font-size: 1.1rem;
    }
  }
  .apply_for{
    width: 40%;
    line-height: 2.5rem;
    font-size: 1.2rem;
    text-align: center;
    margin: 2rem auto;
    border-radius: .3rem;
    border: 1px solid #0b0b0b;
  }

  /*蒙版*/
  .box{
    opacity:.2;
    background:#040000;
    z-index:9;
    width:100%;
    height:100%;
    position:fixed;
    top:0;
    /*display:none*/
  }
  .hintBox{
    position:fixed;
    top:13rem;
    z-index:999;
    overflow-y:auto;
    overflow-x:hidden;
    text-align: center;
    background: #ffffff;
    width: 80%;
    margin: 0 10%;
    padding-bottom: 1rem;
    color:#0B0B0B;
    font-size: .8rem;
    border-radius: .4rem;
    .attention{
      padding-top: 1rem;
      padding-bottom: 1.5rem;
      width: 85%;
      margin: 0 auto;
      .title{
        line-height: 1.1rem;
        display: flex;
        align-items: center;
        color: #000000;
        text-align: left;
        padding-left: 2.8rem;
        img{
          width: .9rem;
          height: .9rem;
          margin: .5rem;
        }
      }
      .light{
        color: #666666;
        line-height: 1.3rem;
        text-align: left;
        padding-left: 3.5rem;
      }
    }
    .btns {
      width: 100%;
      font-size: .9rem;
      margin-top: 2rem;
      display: flex;
      align-items: center;
      justify-content: center;
      .cancel {
        width: 40%;
        line-height: 2.5rem;
        border: 1px solid #030000;
        border-radius: 2rem;
        color: #050505;
        margin-right: 2%;
      }
      .true {
        width: 40%;
        line-height: 2.5rem;
        background: #C9161D;
        border-radius: 2rem;
        color: #ffffff;
        margin-left: 2%;
      }
    }
  }
}
</style>

