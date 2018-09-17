<template>
  <div id="Help_to_rob1">
    <!--商品图片-->
    <div class="goodBox">
      <img src="../../img/swiper.png" alt="">
      <div class="mine">
        <img src="../../img/touxiang.png" alt="">
        <div class="brove">太棒了吧~成功领到30枚鸡蛋</div>
      </div>
    </div>
    <!--标题+去购买-->
    <div class="link fx">
      <p class="fx-f1" style="padding-right:1.5rem;width: 65%;">新鲜鸡蛋 无抗生素鸡蛋无抗生素供港澳鸡蛋</p>
      <p class="fx fx-jf-c fx-ai-c" @click="goBuy">去购买</p>
    </div>
    <!--帮抢按钮-->
    <div class="help" v-if="isMyself" @click="helpRobMy">喊好友帮我抢</div>
    <div class="help" v-if="!isMyself" @click="helpRobTa">帮TA抢</div>
    <!--蒙版-->
    <div class="box" id="box" v-show="isShow" @click="hideToggle"></div>
    <img src="../../img/shareHint.png" alt="" class="shareHint" v-show="isShow" @click="hideToggle">
  </div>
</template>

<script>
import wx from "weixin-js-sdk";
import utils from "../common/common.js";
import axios from "axios";
import qs from "qs";
export default {
  name: "Help_to_rob1",
  data() {
    return {
      isShow: false,
      isMyself: false,
      isClick: 1
    };
  },
  methods: {
    //点击分享图标显示
    showShare() {
      this.isShow = !this.isShow;
    },
    //点击蒙版隐藏
    hideToggle() {
      this.isShow = !this.isShow;
    },
    //帮我抢
    helpRobMy() {
      if (this.isClick == 1) {
      } else if (this.isClick == 0) {
      }
    },
    //帮ta抢
    helpRobTa() {
      if (this.isClick == 0) {
        this.isShow = true;
      } else if (this.isClick == 1) {
      }
    },
    //去购买
    goBuy() {
      this.$router.push({ path: "/" });
    },
    isMyselfClick() {
      var that = this;
      var baseUrl = BaseUrl + "index/joinActivity";
      var data = qs.stringify({
        activityId: 12,
        openid: "oX6js0WrM2E-Pbz0tg3ltsDdPByY"
      });
      axios({
        method: "post",
        url: baseUrl,
        type: "json",
        data: data
      }).then(info => {
        var data = info.data.data;
        this.isClick = data.isClick;
        // 帮我抢
        if (data.isMyself == 1) {
          that.isMyself = true;
        } else if (data.isMyself == 0) {
          // 帮他抢
          that.isMyself = false;
        }
      });
    }
    //微信自定义分享
    // login: function() {
    //   var $this = this;
    //   var data = qs.stringify({
    //     paramUrl: window.location.href
    //   });
    //   var baseUrl = BaseUrl + "index/getCode";
    //   axios({
    //     method: "POST",
    //     url: baseUrl,
    //     type: "json",
    //     data: data
    //   })
    //     .then(function(data) {
    //       let datas = data.data.data;
    //       console.log(datas.url);
    //       location.href = datas.url;
    //       if ((data.data.status = 1)) {
    //         // window.location.href = datas.url;
    //         this.targetUrl = datas.url;
    //         var code = utils.getQueryString(
    //           "http://egg.wufuapp.com/?code=061VzK2U1j3xy61xoe3U16cI2U1VzK21&state=1#/",
    //           "code"
    //         );
    //         console.log(code);
    //         // localStorage.setItem("openid", datas.openid);
    //         // $this.content = datas.content;
    //       } else {
    //         Toast({
    //           message: datas.msg,
    //           duration: 1500
    //         });
    //       }
    //     })
    //     .catch(function() {
    //       //alert("程序异常，联系技术人员")
    //     });
    // },

    // logins: function() {
    //   var $this = this;
    //   var baseUrl = BaseUrl + "index/getUserOpenid";
    //   var data = qs.stringify({
    //     code: utils.getQueryString("code")
    //   });
    //   axios({
    //     method: "post",
    //     url: baseUrl,
    //     type: "json",
    //     data: data
    //   }).then(function(data) {
    //     if (data.status == 1) {
    //       console.log(data.data.openid);
    //       this.openid = data.data.openid;
    //     }
    //   });
    // },
    // wxShare(desc) {
    //   var $this = this;
    //   var hrefUrl = window.location.href;
    //   var baseUrl = BaseUrl + "index/getSignPackage?=" + hrefUrl;

    //   axios({
    //     method: "post",
    //     url: baseUrl,
    //     type: "json"
    //   })
    //     .then(function(data) {
    //       let datas = data.data.data.signpackage;
    //       console.log(datas);

    //       if (data.data.status == 1) {
    //         wx.config({
    //           debug: true,
    //           appId: datas.aapId,
    //           timestamp: datas.aapId.toString(),
    //           nonceStr: datas.nonceStr,
    //           signature: datas.signature, // res.data是从后台接口返回的xinxi
    //           jsApiList: [
    //             "onMenuShareTimeline",
    //             "onMenuShareAppMessage",
    //             "checkJsApi",
    //             "startRecord",
    //             "stopRecord",
    //             "translateVoice",
    //             "scanQRCode",
    //             "openCard"
    //           ]
    //         });
    //       } else {
    //       }
    //     })
    //     .catch(function() {
    //       //alert("程序异常，联系技术人员")
    //     });

    //   wx.ready(function() {
    //     // 分享给朋友
    //     wx.onMenuShareAppMessage({
    //       title: "仅30枚！鸡蛋免费抢啦~我只能帮你到这了", // 分享标题
    //       desc:
    //         "绿色无抗乌鸡蛋，30枚免费抢，数量有限，再不抢就没有啦好吃好玩好赚钱", // 分享描述
    //       link: "http://egg.wufuapp.com/#/Free_egg_robbing", // 分享链接http://wufuapp.com/index.php/api_egg/index/getSignPackage
    //       //link: 'http://wufuapp.com/index.php/api_egg/index/getSignPackage',
    //       imgUrl:
    //         "http://biuimage.oss-cn-qingdao.aliyuncs.com/uploads/ueditor/image/20180907/1536308595897761.png", // 分享图标
    //       type: "", // 分享类型,music、video或link，不填默认为link
    //       dataUrl: "", // 如果type是music或video，则要提供数据链接，默认为空
    //       success: function() {
    //         console.log("已分享给朋友");
    //       },
    //       cancel: function() {
    //         console.log("取消分享朋友");
    //         // 用户取消分享后执行的回调函数
    //       },
    //       fail: function(res) {
    //         window.alert(JSON.stringify(res));
    //       }
    //     });
    //     // 分享到朋友圈
    //     wx.onMenuShareTimeline({
    //       title:
    //         "仅30枚！绿皮无抗乌鸡蛋免费抢啦，我还差一些，就抢满了，大家帮帮我", // 分享标题
    //       // link: 'http://egg.wufuapp.com/#/Free_egg_robbing',
    //       link: "http://wufuapp.com/index.php/api_egg/index/getSignPackage",
    //       imgUrl: "http://wufuapp.com/index.php/api_egg/index/getSignPackage", // 分享图标
    //       success: function() {
    //         console.log("已分享到朋友圈");
    //       },
    //       cancel: function() {
    //         console.log("取消分享朋友圈");
    //       },
    //       fail: function(res) {
    //         window.alert(JSON.stringify(res));
    //       }
    //     });
    //   });
    // }
  },
  created() {
    document.title = "抢蛋免费领";
    this.isMyselfClick();
  },
  mounted() {
    // this.wxShare("仅30枚！鸡蛋免费抢啦~我只能帮你到这了");
    // this.wxShare(
    //   "仅30枚！绿皮无抗乌鸡蛋免费抢啦，我还差一些，就抢满了，大家帮帮我"
    // );
  },
  components: {}
};
</script>

<style lang="scss" scoped>
#Help_to_rob1 {
  background: #30bbac;
  min-height: 41.6rem;
  /*商品图片*/
  .goodBox {
    width: 100%;
    height: 16rem;
    background: #fff;
    img {
      width: 100%;
      height: 100%;
    }
    .mine {
      display: flex;
      align-items: center;
      padding: 1rem;
      position: relative;
      top: -16.5rem;
      z-index: 9;
      img {
        width: 10%;
        height: 2rem;
      }
      .brove {
        background: rgba(238, 238, 238, 1);
        opacity: 0.51;
        border-radius: 1rem;
        margin-left: 0.5rem;
      }
    }
  }
  /*标题+去购买*/
  .link {
    display: flex;
    align-items: center;
    font-size: 0.95rem;
    color: #2e0406;
    box-sizing: border-box;
    height: 5rem;
    width: 100%;
    background-color: rgba(0, 0, 0, 0.1);
    position: relative;
    z-index: 22;
    padding: 1rem;
  }
  .link > p:last-child {
    font-size: 1.1rem;
    padding: 0 1rem;
    width: 5rem;
    background-color: #cc3e36;
    color: white;
    border-radius: 2rem;
    letter-spacing: 0.05rem;
    line-height: 2.5rem;
    text-align: center;
  }
  /*帮抢按钮*/
  .help {
    width: 50%;
    line-height: 3.56rem;
    background: rgba(255, 176, 17, 1);
    border-radius: 3rem;
    color: #2e0406;
    text-align: center;
    margin: 0 auto;
    margin-top: 5rem;
  }
  /*蒙版*/
  .box {
    opacity: 0.56;
    background: #000;
    z-index: 9;
    width: 100%;
    height: 100%;
    position: fixed;
    top: 0;
    /*display:none*/
  }
  .shareHint {
    width: 80%;
    margin: 0 auto;
    display: block;
    z-index: 999;
    position: absolute;
    top: 3rem;
    left: 3.3rem;
  }
}
</style>
