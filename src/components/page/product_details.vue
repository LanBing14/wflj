<template>
  <div id="product_details">
    <!-- <mt-header fixed title="商品详情" style="font-size:1.2rem;height: 3rem;"></mt-header> -->
    <div class="swipeImg">
      <mt-swipe :auto="3000">
        <mt-swipe-item v-for="(img,index) in imgs" :key="index"> <img :src=img alt=""></mt-swipe-item>
      </mt-swipe>
    </div>
    <!-- 	<div style="width:100px;height:100px;background-color: #aaa;color:#fff" @click="clearStor">
			清缓存
		</div> -->
    <!--商品名称-->
    <div class="productInfo">
      <div class="infoOne">
        <p class="eggName">{{title}}</p>
        <div class="moneyReward" @click="goMakeMoney">
          <img class="iconMoney" src="../../img/shangjin.png" />
          <p class="moneyTitle">赚赏金</p>
        </div>
      </div>
      <div class="infoTwo">
        <p class="active1">包月发送</p>
        <p class="active2">快递 {{freight}}元</p>
        <p class="active3">月销 {{sale}}</p>
      </div>
    </div>
    <!--选择套餐-->
    <div class="chooseSpecial" @click="showToggle">
      <p class="choose">选择套餐</p>
      <img src="../../img/right.png" alt="">
    </div>
    <!--全部评价-->
    <div class="all_evaluate">
      <div class="titleBox">全部评价({{comments}})</div>
      <div class="userEvaluate" v-for="(item,index) in clist" :key="index">
        <div class="evaluateBox">
          <img :src=item.img class="touxiang">
          <div>{{item.userName}}</div>
          <div class="userPhone">{{item.phone}}</div>
          <div class="zan">
            <img src="../../img/zan-weixuanzhong.png" alt="" class="xin" @click="goZan" v-show="isZan">
            <img src="../../img/zan-xuanzhong.png" alt="" class="xin" @click="goCancelZan" v-show="isCancel">
            <p class="praise">好评</p>
          </div>
        </div>
        <div class="content">{{item.content}}</div>
      </div>
      <div class="evaluateBtn" @click="goEvaluateAll">全部评价</div>
    </div>
    <!--商品详情-->
    <div class="productDetails">
      <p v-html="content"></p>
    </div>
    <!--立即购买-->
    <div class="buyAndRob">
      <div class="rob" @click="goRobEgg">
        <p class="free">发起抢蛋</p>
        <p class="waitRob">您还有{{eggNum}}枚鸡蛋待抢</p>
      </div>
      <div class="buy" @click="showToggle">
        <p>立即购买</p>
      </div>
    </div>
    <!--蒙版-->
    <div class="box" @touchmove.prevent id="box" v-show="isShow " @click="hideMind"></div>
    <img src="../../img/shareHint.png" alt="" @click="hideMind" class="shareHint" v-if="isShow && showShare">
    <!--领取提醒-->
    <div class="getRemind" v-show="isGetEgg">
      <p class="remindTop">领取提醒</p>
      <p class="remindCenter">您需下单购买任一套餐，才可提现</p>
      <div class="btns">
        <div class="cancel" @click="hideToggle">下次领取</div>
        <div class="true" @click="goGetNow">立即下单</div>
      </div>
    </div>
    <!--领蛋提醒-->
    <div class="remindOut" v-show="isFalse">
      <p class="remindTop">领蛋提醒</p>
      <p class="remindCenter">您已抢满30枚绿壳无抗乌鸡蛋,可立即领取</p>
      <div class="remindBot">
        <div class="btn btn1" @click="hideToggle">下次获取</div>
        <div class="btn btn2" @click="getNow">立即领取</div>
      </div>
    </div>
    <!--领取提醒-下单-->
    <div class="getRemind_xiadan" v-show="isXiadan">
      <p class="remindTop">领取提醒</p>
      <p class="remindCenter">您需下单购买任一套餐，免费领取30枚绿壳无抗翡翠蛋</p>
      <div class="color-choice">
        <div v-for="(list,key) in packageList" :key="key" class="color" :class="{active:iskey==key}" @click="choice(key)">{{list.number}}枚/{{list.dateTime}}个月</div>
      </div>
      <p class="infuse" v-for="(item,index) in packageList">每月配送30枚，共{{item.dateTime}}个</p>
      <div class="btns">
        <div class="cancel" @click="hideToggle">下次领取</div>
        <div class="true" @click="goPlace">立即下单</div>
      </div>
    </div>
    <!--选择规格-->
    <div class="choice-style" v-show="isShowAt">
      <div class="choice-box">
        <div class="goods-details">
          <img src="../../img/swiper.png" alt="#" class="good-picture">
          <div class="money">
            <p class="price">￥{{sellPrice}}</p>
            <p class="style">库存{{stock}}件</p>
          </div>
          <!--<img src="../../img/close@2x.png" alt="#" class="close"  @click="hideToggle">-->
        </div>
        <div class="choice">
          <p class="colors">套餐规格</p>
          <div class="color-choice">
            <div v-for="(list,key) in packageList" :key="key" class="color" :class="{active:iskey==key}" @click="choice(key)">{{list.number}}枚/{{list.dateTime}}个月</div>
          </div>
        </div>
        <p class="infuse">每月配送30枚，共{{dateTime}}个月</p>
        <div class="numbers">
          <p class="number">购买数量</p>
          <div class="modified">
            <div class="low" @click="sum"></div>
            <input type="number" v-model="amount" class="count" readonly="readonly">
            <div class="plus" @click="sums">+</div>
          </div>
        </div>
        <div class="yes" @click="goConfirmation">确认</div>
      </div>
    </div>
  </div>
</template>

<script>
import { Header } from "mint-ui";
import axios from "axios";
import qs from "qs";
import { Toast } from "mint-ui";
import { Indicator } from "mint-ui";

import utils from "../common/common.js";
export default {
  name: "product_details",
  data() {
    return {
      //      slideshow:[],//轮播
      iskey: -1, //价格选择 颜色判断
      isShow: false, //蒙版
      isShowAt: false, //规格
      clist: [],
      isFalse: false, //领蛋提醒
      isGetEgg: false, //领取提醒
      isXiadan: false, //领取提醒-下单
      showShare: false,
      img: "",
      phone: "",
      comments: 0,
      content: "",
      contents: "",
      eggNum: "",
      freight: "",
      id: 16,
      imgs: [],
      isFreight: "",
      isHot: 0,
      packageList: [],
      title: "",
      isZan: true, //未点击的“赞”
      isCancel: false, //点击过的“赞”
      sellPrice: "",
      stock: "",
      number: 0,
      dateTime: "",
      price: 0,
      sale: "0",
      avgPrice: "",
      amount: 1, //input数量
      openid: "",
      targetUrl: "",
      appId: "",
      nonceStr: "",
      timestamp: "",
      signature: "",
      activityId: 12,
      userName: "",
      packageId: "", //套餐id
      isPayOrder: 0,
      isApply: 1
    };
  },
  methods: {
    clearStor() {
      localStorage.setItem("openid", "");
      alert("已清除openid" + localStorage.getItem("openid"));
    },
    //获取微信字段
    WxZd() {
      //获取微信请求的字段
      const openid = localStorage.getItem("openid");

      var data = qs.stringify({
        originUrl: window.location.href
      });
      console.log(data);
      var baseUrl = BaseUrl + "index/getSignPackage";
      axios({
        method: "POST",
        url: baseUrl,
        type: "json",
        data: data
      }).then(
        function(res) {
          var $this = this;
          var info = res.data.data.signPackage;
          this.appId = info.appId;
          this.nonceStr = info.nonceStr;
          this.timestamp = info.timestamp;
          this.signature = info.signature;
          wx.config({
            debug: false,
            appId: this.appId, // 和获取Ticke的必须一样------必填，公众号的唯一标识
            timestamp: this.timestamp, // 必填，生成签名的时间戳
            nonceStr: this.nonceStr, // 必填，生成签名的随机串
            signature: this.signature, // 必填，签名，见附录1
            //需要分享的列表项:发送给朋友，分享到朋友圈，
            jsApiList: ["onMenuShareAppMessage", "onMenuShareTimeline"]
          });

          //处理验证失败的信息
          wx.error(function(res) {
            // logUtil.printLog("验证失败返回的信息:", res);
            console.log(res);
          });
          //处理验证成功的信息
          wx.ready(function() {
            //              alert(window.location.href.split('#')[0]);
            //分享到朋友圈
            wx.onMenuShareTimeline({
              title:
                "仅30枚！绿壳无抗乌鸡蛋免费抢啦，数量有限，先抢先得、好吃好玩好赚钱", // 分享标题
              //            link: "http://egg.wufuapp.com/#/Free_egg_robbing", // 分享链接，该链接域名或路径必须与当前页面对应的公众号JS安全域名一致
              link:
                "http://egg.wufuapp.com?openid=" +
                openid +
                "&activityId=" +
                $this.activityId,
              imgUrl:
                "http://biuimage.oss-cn-qingdao.aliyuncs.com/uploads/ueditor/image/20180910/1536581455920471.png", // 分享图标
              success: function(res) {
                // 用户确认分享后执行的回调函数
                // logUtil.printLog("分享到朋友圈成功返回的信息为:", res);
                // _this.showMsg("分享成功!");
              },
              cancel: function(res) {
                // 用户取消分享后执行的回调函数
                // logUtil.printLog("取消分享到朋友圈返回的信息为:", res);
              }
            });
            //分享给朋友
            wx.onMenuShareAppMessage({
              title: "仅仅30枚! 鸡蛋免费抢啦~我只能帮你到这了", // 分享标题
              desc:
                "绿壳无抗乌鸡蛋，30枚免费抢，数量有限，先抢先得、好吃好玩好赚钱", // 分享描述
              link:
                "http://egg.wufuapp.com?openid=" +
                openid +
                "&activityId=" +
                $this.activityId,
              imgUrl:
                "http://biuimage.oss-cn-qingdao.aliyuncs.com/uploads/ueditor/image/20180910/1536581455920471.png", // 分享图标
              type: "", // 分享类型,music、video或link，不填默认为link
              dataUrl: "", // 如果type是music或video，则要提供数据链接，默认为空
              success: function(res) {
                // 用户确认分享后执行的回调函数
                // logUtil.printLog("分享给朋友成功返回的信息为:", res);
                // console.log(res);
              },
              cancel: function(res) {
                // 用户取消分享后执行的回调函数
                console.log(res);
                // logUtil.printLog("取消分享给朋友返回的信息为:", res);
              }
            });
          });
        }.bind(this)
      );
    },
    // 返回上一页
    goBack() {
      this.$router.push({
        path: "/"
      });
    },
    // 跳转到分享
    goShare() {},
    // 点击‘全部评价’
    goEvaluateAll() {
      this.$router.push({
        path: "/evaluateAll"
      });
    },
    // 点击显示选择套餐
    showToggle() {
      this.isShowAt = !this.isShowAt;
      this.isShow = !this.isShow;
    },
    // 点击蒙版，套餐选择宽隐藏
    hideToggle() {
      this.isShow = !this.isShow;
      this.isFalse = !this.isFalse;
    },
    hideMind() {
      this.isShowAt = false;
      this.isShow = false;
      this.showShare = false;
      this.isFalse = false;
    },
    //点击 领取提醒 -‘立即下单’
    goGetNow() {
      this.isGetEgg = !this.isGetEgg;
      this.isFalse = true;
    },
    //点击 领蛋提醒 -‘立即领取’
    getNow() {
      //if已下单，跳转到领取成功页面
      //      this.$router.push({path: "/getEgg_success"});
      //if未下单，弹出‘领取提醒’-下单的弹框进行选择套餐，
      if (this.isPayOrder == 1) {
        this.$router.push({ path: "/Brought_egg_success" });
      } else {
        this.isGetEgg = false;
        this.isXiadan = true;
      }

      // 其中套餐选中后点击‘立即下单’，弹出‘规格’框
    },
    //getNow();套餐选中后点击‘立即下单’，弹出‘规格’框
    goPlace() {
      this.isXiadan = !this.isXiadan;
      this.isShowAt = true;
    },

    // 点击确认跳转到“order_confirmation”页面，确认订单
    goConfirmation() {
      console.log(this.number);
      this.$router.push({
        path: "/order_confirmation",
        query: {
          //        id: 17,
          packageId: this.packageId,
          number: this.amount,
          openid: this.openid,
          sellPrice: this.sellPrice
        }
      });
    },
    // 点击赚赏金跳到 make_money 页面
    goMakeMoney() {
      this.$router.push({
        path: "/make_money"
      });
    },
    // 点击“免费抢蛋”跳到“”抢蛋进度的活动页面
    goRobEgg() {
      this.isShow = !this.isShow;
      this.showShare = !this.showShare;
      // this.$router.push({ path: "/Free_egg_robbing" });
    },
    // '点'赞 + '取消' 赞
    goZan() {
      this.isZan = !this.isZan;
      this.isCancel = !this.isCancel;
    },
    goCancelZan() {
      this.isZan = !this.isZan;
      this.isCancel = !this.isCancel;
    },
    //规格选中后的样式
    choice: function(i) {
      console.log(this.packageList[i]);
      this.iskey = i;
      this.sellPrice = this.packageList[i].sellPrice;
      this.stock = this.packageList[i].stock;
      this.number = this.packageList[i].number;
      this.dateTime = this.packageList[i].dateTime;
      this.avgPrice = this.packageList[i].avgPrice;
      this.price = this.sellPrice / this.stock;
      this.packageId = this.packageList[i].id;
    },
    //input数量
    sums: function() {
      this.amount++;
    },
    sum: function() {
      if (this.amount >= 2) {
        this.amount--;
      } else {
        Toast({
          message: "数量不能小于1",
          duration: 1500
        });
      }
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
          location.href = datas.url;
          if ((data.data.status = 1)) {
            window.location.href = datas.url;
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
        if (data.data.status == 1) {
          $this.openid = data.data.data.openid;
          localStorage.setItem("openid", $this.openid);
          $this.moun();
        }
      });
    },
    moun() {
      var $this = this;
      var baseUrl = BaseUrl + "api/productDetail";
      var data = qs.stringify({
        id: 17,
        openid: localStorage.getItem("openid")
      });
      axios({
        method: "post",
        url: baseUrl,
        type: "json",
        data: data
      }).then(info => {
        console.log(info);
        $this.title = info.data.data.title;
        $this.freight = info.data.data.freight;
        $this.activityId = info.data.data.activityId;
        $this.imgs = info.data.data.imgs;
        $this.packageList = info.data.data.packageList;
        $this.sale = info.data.data.sale;
        $this.comments = info.data.data.comments;
        $this.dateTime = info.data.data.packageList.dateTime;
        $this.avgPrice = info.data.data.packageList[0].avgPrice;
        $this.sellPrice = info.data.data.packageList[0].sellPrice;
        $this.stock = info.data.data.packageList[0].stock;
        //    this.amount = info.data.data.packageList.number;
        $this.eggNum = info.data.data.eggNum;
        $this.isApply = info.data.data.isApply;
        $this.clist = info.data.data.clist;
        $this.isPayOrder = info.data.data.isPayOrder;
        $this.phone = info.data.data.clist[0].phone;
        $this.userName = info.data.data.clist[0].username;
        $this.content = info.data.data.content;
        $this.img = info.data.data.clist[0].img;
        $this.contents = info.data.data.clist[0].content;

        // Indicator.close();
        this.WxZd();
        this.choice(0);
        if ($this.eggNum == 0 && $this.isApply == 0) {
          $this.isFalse = true;
          $this.isShow = true;
        }
      });
    },
    //支付
    PAY: function() {
      var $this = this;
      var baseUrl = BaseUrl + "api/payOrder?+openid=" + openId;
      var data = qs.stringify({
        id: 7,
        payType: 1
      });
      axios({
        method: "post",
        url: baseUrl,
        type: "json",
        data: data,
        headers: {
          "Content-Type": "Access-Control-Allow-Origin"
        }
      })
        .then(function(data) {
          let datas = data.data.data;
          if ((data.isType = 0)) {
            $this.params = datas.params;
          } else {
            // Toast({
            //   message: datas.msg,
            //   duration: 1500
            // });
          }
        })
        .catch(function() {});
    }
  },
  created() {
    var showSize = this.$route.query.showSize;
    if (showSize == 1) {
      this.isShowAt = true;
      this.isShow = true;
    }
  },
  mounted() {
    // Indicator.open('Loading...');

    if (!localStorage.getItem("openid")) {
      if (utils.getQueryString("code")) {
        this.logins();
      } else {
        this.login();
      }
    } else {
      this.moun();
    }
    // this.WxZd();

    //			if(!localStorage.getItem("openid")) {
    //				this.logins();
    //			}

    // console.log(this.activityId)
  },
  components: {}
};
</script>

<style lang="scss" scoped>
#product_details {
  .mint-header {
    background: #cc3e36;
  }
  .swipeImg {
    width: 100%;
    height: 23.875rem;
    // margin-top: 3rem;
    img {
      width: 100%;
      height: 100%;
    }
  }
  /*商品名称*/
  .productInfo {
    .infoOne {
      display: flex;
      align-items: center;
      .eggName {
        width: 65%;
        height: 3rem;
        font-size: 1.2rem;
        color: #2a1011;
        margin: 0.5rem 1rem;
        overflow: hidden;
        white-space: normal;
        text-overflow: ellipsis;
      }
      .moneyReward {
        width: 30%;
        height: 2.5rem;
        background: #e5e5e4;
        border-radius: 2rem 0 0 2rem;
        display: flex;
        .iconMoney {
          width: 1.5rem;
          height: 1.5rem;
          padding-left: 0.9rem;
          padding-top: 0.4rem;
        }
        .moneyTitle {
          margin-left: 0.5rem;
          color: #c9161d;
          line-height: 2.5rem;
          display: inline-block;
        }
      }
    }
    .infoTwo {
      width: 90%;
      display: flex;
      align-items: center;
      margin: 0rem 1rem;
      color: #8b8989;
      line-height: 2.5rem;
      font-size: 0.9rem;
      .active1 {
        width: 35%;
        text-align: left;
      }
      .active2 {
        width: 30%;
        text-align: center;
      }
      .active3 {
        width: 40%;
        text-align: right;
      }
    }
  }
  /*选择套餐*/
  .chooseSpecial {
    width: 95%;
    margin: 0 auto;
    font-size: 1rem;
    line-height: 3rem;
    display: flex;
    align-items: center;
    .choose {
      width: 92%;
      padding-left: 0.4rem;
    }
    img {
      width: 1.5rem;
      height: 1.5rem;
    }
  }
  /*全部评价*/
  .all_evaluate {
    .titleBox {
      background-color: #f0f0f2;
      font-size: 1rem;
      color: #000;
      padding-left: 1rem;
      line-height: 3rem;
      margin-top: 1rem;
    }
    .userEvaluate {
      .evaluateBox {
        display: flex;
        align-items: center;
        padding: 0.6rem 1rem;
        .touxiang {
          width: 2.5rem;
          height: 2.5rem;
          //   border: 1px solid #ccc;
          border-radius: 50%;
        }
        .userPhone {
          color: #c1c5c8;
          font-size: 1rem;
          margin-left: 1rem;
        }
        .zan {
          display: flex;
          align-items: center;
          position: relative;
          right: -8rem;
          float: right;
          .xin {
            width: 1.8em;
            height: 2rem;
          }
          .praise {
            color: #c1c5c8;
          }
        }
      }
      .content {
        width: 90%;
        white-space: initial;
        overflow: hidden;
        text-overflow: ellipsis;
        display: -webkit-box;
        -webkit-line-clamp: 2;
        -webkit-box-orient: vertical;
        margin: 0.5rem auto;
      }
    }
    .evaluateBtn {
      color: #000;
      font-size: 1.2rem;
      width: 11rem;
      line-height: 2.5rem;
      background-color: #fff;
      margin: 0 auto;
      margin-top: 2rem;
      margin-bottom: 1rem;
      text-align: center;
      border: 1px solid #1c1c1c;
      border-radius: 5rem;
    }
  }
  /*商品详情*/
  .productDetails {
    margin-bottom: 3.5rem;
    img {
      width: 100%;
    }
  }
  /*立即购买*/
  .buyAndRob {
    width: 95%;
    padding: 0 0.7rem;
    display: flex;
    align-items: center;
    bottom: 0;
    position: fixed;
    height: 3.5rem;
    background: #ffffff;
    .rob {
      width: 50%;
      height: 2.5rem;
      text-align: center;
      border: 1px solid #cc3e36;
      color: #cc3e36;
      border-radius: 5rem;
      .free {
        font-size: 1rem;
        line-height: 1.4rem;
      }
      .waitRob {
        font-size: 0.8rem;
        line-height: 1rem;
        color: rgb(223, 45, 21);
      }
    }
    .buy {
      width: 50%;
      height: 3rem;
      font-size: 1.2rem;
      line-height: 3rem;
      text-align: center;
      background: #cc3e36;
      color: #ffffff;
      border-radius: 5rem;
      margin-left: 1rem;
    }
  }
  /*蒙版*/
  .box {
    opacity: 0.9;
    background: #000;
    z-index: 9;
    width: 100%;
    height: 100%;
    position: fixed;
    top: 0;
  }
  .shareHint {
    width: 80%;
    margin: 0 auto;
    display: block;
    z-index: 999;
    position: fixed;
    top: 3rem;
    left: 3.3rem;
  }
  /*领取提醒*/
  .getRemind {
    background: #fff;
    width: 90%;
    color: #000;
    text-align: center;
    position: fixed;
    top: 30%;
    left: 1rem;
    z-index: 11;
    border-radius: 0.3rem;
    .remindTop {
      font-size: 20px;
      line-height: 3rem;
    }
    .remindCenter {
      height: 3rem;
      line-height: 2rem;
      font-size: 16px;
    }
    .color-choice {
      display: flex;
      align-items: center;
      flex-wrap: wrap;
      margin-top: 1rem;
      .color {
        border: 1px solid #79797b;
        color: #000;
        border-radius: 5rem;
        width: 30%;
        margin: 0.5rem 0.3rem;
        line-height: 2.3rem;
        text-align: center;
      }
      .active {
        border: 1px solid #c01a20;
        color: #c01a20;
      }
    }
    .btns {
      width: 100%;
      font-size: 0.9rem;
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
        background: #c9161d;
        border-radius: 2rem;
        color: #ffffff;
        margin-left: 2%;
      }
    }
  }
  /*领蛋提醒*/
  .remindOut {
    background: #fff;
    width: 90%;
    color: #000;
    text-align: center;
    position: fixed;
    top: 30%;
    left: 1rem;
    z-index: 11;
    border-radius: 0.3rem;
    .remindTop {
      font-size: 20px;
      line-height: 3rem;
    }
    .remindCenter {
      height: 3rem;
      line-height: 2rem;
      font-size: 16px;
    }
    .remindBot {
      .btn {
        display: inline-block;
        width: 30%;
        margin: 1rem;
        line-height: 2.5rem;
        border-radius: 2rem;
      }
      .btn1 {
        border: 1px solid #ccc;
      }
      .btn2 {
        background: #c9161d;
        color: #ffffff;
      }
    }
  }
  /*领取提醒-下单*/
  .getRemind_xiadan {
    background: #fff;
    width: 90%;
    color: #000;
    text-align: center;
    position: fixed;
    top: 30%;
    left: 1rem;
    z-index: 11;
    border-radius: 0.3rem;
    .remindTop {
      font-size: 20px;
      line-height: 3rem;
    }
    .remindCenter {
      height: 3rem;
      line-height: 2rem;
      font-size: 16px;
    }
    .color-choice {
      display: flex;
      align-items: center;
      flex-wrap: wrap;
      margin-top: 1rem;
      width: 100%;
      .color {
        border: 1px solid #79797b;
        color: #000;
        border-radius: 5rem;
        width: 35%;
        margin: 0.5rem 0.3rem;
        line-height: 2.3rem;
        text-align: center;
      }
      .active {
        border: 1px solid #c01a20;
        color: #c01a20;
      }
    }
    .infuse {
      color: #eb2f36;
      font-size: 1rem;
      padding-left: 1rem;
      line-height: 3rem;
    }
    .btns {
      width: 100%;
      font-size: 0.9rem;
      margin: 2rem auto;
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
        background: #c9161d;
        border-radius: 2rem;
        color: #ffffff;
        margin-left: 2%;
      }
    }
  }
  /*选择规格*/
  .choice-style {
    .choice-box {
      position: fixed;
      background: #fff;
      width: 100%;
      height: 31rem;
      bottom: 0;
      z-index: 999;
      overflow-y: auto;
      overflow-x: hidden;
      .goods-details {
        display: flex;
        padding: 1rem 0 0 1rem;
        .good-picture {
          width: 6rem;
          height: 6rem;
        }
        .money {
          padding-left: 1rem;
          .price {
            font-size: 1.2rem;
            line-height: 1.0683760684rem;
            text-align: left;
            color: #c01a20;
            padding-top: 0.5555555556rem;
            margin-bottom: 0.5555555556rem;
          }
          .style {
            font-family: PingFangSC-Regular;
            font-size: 1rem;
            color: #333;
            text-align: left;
            margin: 0.2136752137rem 0 0 0.5128205128rem;
          }
        }
        .close {
          width: 1rem;
          height: 1rem;
          position: relative;
          float: right;
          right: -7.8rem;
        }
      }
      .choice {
        .colors {
          margin: 2rem 0 0 0;
          padding-left: 1rem;
          font-size: 1.2rem;
        }
        .color-choice {
          display: flex;
          align-items: center;
          flex-wrap: wrap;
          margin-top: 1rem;
          .color {
            border: 1px solid #79797b;
            color: #000;
            border-radius: 5rem;
            width: 30%;
            margin: 0.5rem 0.3rem;
            line-height: 2.3rem;
            text-align: center;
          }
          .active {
            border: 1px solid #c01a20;
            color: #c01a20;
          }
        }
      }
      .infuse {
        color: #eb2f36;
        font-size: 1rem;
        padding-left: 1rem;
        line-height: 3rem;
      }
      .numbers {
        display: flex;
        align-items: center;
        .number {
          padding-left: 1rem;
          font-size: 1.2rem;
          line-height: 3rem;
        }
        .modified {
          display: flex;
          align-items: center;
          justify-content: center;
          border-radius: 5rem;
          border: 1px solid #79797b;
          width: 9rem;
          height: 2.3rem;
          position: relative;
          right: -7rem;
          .low {
            background: #000;
            width: 1rem;
            height: 0.1rem;
            margin-right: 0.3rem;
          }
          input {
            border: none;
            width: 4.6rem;
            text-align: center;
            font-size: 1.4rem;
          }
          .plus {
            font-size: 2rem;
            margin-left: 0.3rem;
            margin-top: -0.2rem;
          }
        }
      }
      .yes {
        font-size: 1.3rem;
        color: #c01a20;
        border-top: 1px solid #79797b;
        position: fixed;
        bottom: 0;
        width: 100%;
        text-align: center;
        line-height: 3.5rem;
      }
    }
  }
}
</style>