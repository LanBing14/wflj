<template>
  <div id="Help_to_rob2">
    <div class="bgImg">
      <div class="helpSuccess">恭喜您！
        <br/> 成功帮助好友抢得无抗绿壳乌鸡蛋
        <span>一</span> 枚</div>
      <img src="../../img/bghehu.png" alt="">
    </div>
    <div class="userInfo">
      <img :src="img" alt="" class="touxiang">
      <p class="userName">{{name}}</p>
      <p class="alrealy">已经抢到
        <span>{{residueEggNum}}枚</span>鸡蛋</p>
    </div>
    <div class="addEgg">
      <img src="../../img/addEgg.png" alt="">
    </div>
    <div class="joinMy">
      <p class="alJoin">已有
        <span>{{robNumber}}
        </span>个小伙伴加入抢蛋小分队</p>
      <p class="tit">如此好蛋，不容错过，快召唤你的抢蛋小分队吧，将健康安全，营养美味的翡翠蛋，带回家！</p>
    </div>
    <div class="btns">
      <!-- <div class="help1" @click="goQiang">喊好友帮ta抢</div> -->
      <div class="help2" @click="goFriendQiang">喊好友帮我抢</div>
    </div>
    <!--蒙版关注公众号-->
    <div @click="hideToggle">
      <div class="box" id="box" v-show="isShow"></div>
      <div class="attention" v-show="isShow">
        <img src="../../img/guanzhu.png" alt="">
        <div class="titles">
          <p class="tit1">关注公众号,快去邀请好友抢蛋</p>
          <!-- <p class="tit2">FOCUS US</p> -->
        </div>
        <img src="../../img/code.jpg" alt="" class="Rcode">
      </div>
    </div>
    <div @cick="handleClick">
      <div class="box" id="box" v-show="isFalse"></div>
      <img src="../../img/shareHint.png" alt="" class="shareHint" v-if="isFalse">
    </div>

  </div>
</template>

<script>
import qs from "qs";
import axios from "axios";
import utils from "../common/common.js";
import wx from "weixin-js-sdk";
export default {
  name: "Help_to_rob2",
  data() {
    return {
      isShow: false,
      isFalse: false,
      robNumber: "xx",
      residueEggNum: "xx",
      hasRobEggNum: "xx",
      name: "xx",
      activityId: "",
      openid: localStorage.getItem("openid"),
      img: ""
    };
  },
  methods: {
    //点击蒙版，关注公众号的弹框隐藏
    hideToggle() {
      this.isShow = false;
    },
    handleClick() {
      this.isFalse = false;
    },
    //喊好友帮ta抢
    goQiang(desc) {
      this.isFalse = true;

      /*if 未关注公众号，点击显示‘关注’面板*/
      /*if 已关注公众号，跳转下一页面*/
      // this.wxShare(desc);
      //      this.$router.push({path:'/Free_egg_robbing',query:{}});
    },
    //喊好友帮我抢
    goFriendQiang() {
      // this.wxShare();
      this.isShow = true;
      this.WxZd();
    },
    //首页相关信息+openid存
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
    //         var code = getQueryString(
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
    WxZd() {
      var $this = this;
      //获取微信请求的字段
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
              link:
                "http://egg.wufuapp.com?openid=" +
                $this.openid +
                "&activityId=" +
                $this.activityId,
              imgUrl:
                "http://biuimage.oss-cn-qingdao.aliyuncs.com/uploads/ueditor/image/20180910/1536581455920471.png", // 分享图标
              success: function(res) {
                // 用户确认分享后执行的回调函数
                // logUtil.printLog("分享到朋友圈成功返回的信息为:", res);
                // console.log(res);
                // _this.showMsg("分享成功!");
                this.isPub = true;
              },
              cancel: function(res) {
                // 用户取消分享后执行的回调函数
                // logUtil.printLog("取消分享到朋友圈返回的信息为:", res);
                console.log(res);
              }
            });
            //分享给朋友
            wx.onMenuShareAppMessage({
              title: "仅仅30枚! 鸡蛋免费抢啦~我只能帮你到这了", // 分享标题
              desc:
                "绿壳无抗乌鸡蛋，30枚免费抢，数量有限，先抢先得、好吃好玩好赚钱", // 分享描述
              link:
                "http://egg.wufuapp.com?openid=" +
                $this.openid +
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
                this.isPub = true;
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
    }
  },
  mounted() {
    //this.wxShare('仅30枚！鸡蛋免费抢啦~我只能帮你到这了');
    //this.wxShare('仅30枚！绿皮无抗乌鸡蛋免费抢啦，我还差一些，就抢满了，大家帮帮我');
    let openid = localStorage.getItem("openid");
    var data = qs.stringify({
      activityId: this.$route.query.activityId,
      openid: openid
    });

    var baseUrl = BaseUrl + "index/robEgg";
    axios({
      method: "post",
      url: baseUrl,
      type: "json",
      data: data
    }).then(res => {
      console.log(res);
      this.hasRobEggNum = res.data.data.hasRobEggNum;
      this.residueEggNum = res.data.data.residueEggNum;
      this.robNumber = res.data.data.robNumber;
      this.name = res.data.data.username;
      this.img = res.data.data.img;
      this.activityId = res.data.data.activityId;
    });
  },
  created() {
    document.title = "抢蛋免费领";
    // if (utils.getQueryString("code")) {
    //   console.log(utils.getQueryString("code"));
    //   this.logins();
    //   this.wxShare();
    // } else {
    //   this.login();
    // }
  },
  components: {}
};
</script>

<style lang="scss" scoped>
#Help_to_rob2 {
  background: #77e5d9;
  min-height: 41.6rem;

  .bgImg {
    width: 90%;
    margin: 0 auto;
    .helpSuccess {
      width: 85%;
      height: 5rem;
      line-height: 2rem;
      position: absolute;
      left: 6%;
      background-color: #eee326;
      top: 3%;
      span {
        font-size: 18px;
      }
    }
    img {
      width: 100%;
      margin-top: 1rem;
      margin-bottom: 4rem;
    }
  }
  .userInfo {
    color: #3e3209;
    position: absolute;
    top: 3.5rem;
    margin-bottom: 1rem;
    margin: 0 auto;
    text-align: center;
    margin-top: 2rem;
    width: 100%;
    .touxiang {
      width: 3rem;
      height: 2.5rem;
      border-radius: 50%;
    }
    .userName {
      font-size: 1rem;
      // margin-top: 1rem;
      line-height: 1.2rem;
    }
    .alrealy {
      font-size: 0.9rem;
      span {
        font-size: 1rem;
        color: #820303;
        font-weight: bold;
      }
    }
  }
  .addEgg {
    position: absolute;
    top: 12rem;
    left: 50%;
    transform: translate(-50%);
    width: 8rem;
    margin: 0 auto;
    height: 5rem;
    img {
      width: 100%;
      height: 100%;
    }
  }
  .joinMy {
    position: absolute;
    top: 14rem;
    transform: translate(-50%);
    background-color: #fff;
    width: 89%;
    left: 50%;
    text-align: center;
    // border: 1px solid #ccc;
    .alJoin {
      color: #2e2c09;
      font-size: 0.9rem;
      span {
        font-size: 1.5rem;
        color: #8b0205;
      }
    }
    .tit {
      color: #941e08;
      font-size: 1.1rem;
      line-height: 2rem;
      width: 90%;
      display: inline-block;
    }
  }
  .btns {
    display: flex;
    align-items: center;
    justify-content: center;
    text-align: center;
    .help1 {
      width: 9rem;
      line-height: 3rem;
      color: #2e0406;
      border-radius: 2rem;
      border: 1px solid #2e0406;
      margin-right: 1rem;
    }
    .help2 {
      width: 10rem;
      line-height: 4rem;
      color: #2e0406;
      border-radius: 2rem;
      background: #ffb011;
      margin-left: 1rem;
    }
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
  .attention {
    position: fixed;
    width: 100%;
    top: 12rem;
    z-index: 999;
    overflow-y: auto;
    overflow-x: hidden;
    img {
      width: 80%;
      display: block;
      margin: 0 auto;
      height: 13rem;
    }
    .titles {
      position: absolute;
      top: 0.8rem;
      width: 100%;
      margin: 0 auto;
      text-align: center;
      font-size: 0.9rem;
      .tit1 {
        color: #bd2021;
      }
      .tit2 {
        color: #bc1f21;
      }
    }
    .Rcode {
      display: block;
      width: 35%;
      height: 60%;
      position: absolute;
      top: 2.1rem;
      margin: 0 32.5%;
    }
  }
}
</style>
