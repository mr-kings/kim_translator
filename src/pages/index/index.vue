<template>
  <div class="user">
    <div class="avatar">
      <img v-if="isAuthed" class="user_avatar animated bounce" :src="userInfo.avatarUrl" alt="">
      <button v-else class="user_avatar animated bounce" open-type="getUserInfo" @getuserinfo="getUserInfo">点击授权</button>
    </div>

    <div class="nickName">
      <p class="hover-underline-animation animated swing" v-if="userInfo.nickName">欢迎 <span>{{userInfo.nickName}}</span> 来到Kim翻译助手</p>
    </div>

    <div class="navButton">
      <div class="btn draw" @tap="transition('text')">文本翻译</div>
      <div class="btn center" @tap="transition('voice')">语音翻译</div>
    </div>
  </div>
</template>

<script>
import {mapState, mapGetters, mapMutations, mapActions} from 'vuex'
export default {
  data () {
    return {
      isAuthed: false,
      userInfo: {}
    }
  },
  beforeMount(){
    // 获取用户信息
    this.handleGetUserInfo()
  },
  computed: {
    //...mapState(['userInfo']),
    //...mapGetters(['userInfo'])
  },
  methods: {
    getUserInfo (data) {
      // console.log(data)
      // 允许授权
      if(data.mp.detail.rawData){
        // 获取用户信息
        // console.log(data.mp)
        this.userInfo = JSON.parse(data.mp.detail.rawData)
        this.$store.dispatch('setUserInfo',this.userInfo)
        this.isAuthed = true
      }
    },
    handleGetUserInfo () {
      // 调用获取用户信息接口
      wx.getUserInfo({
        success: (res) => {
          this.userInfo = res.userInfo
          this.$store.dispatch('setUserInfo',this.userInfo)
          this.isAuthed = true
        },
        fail: (err) => {
          //console.log('用户获取失败')
        }
      })
    },
    transition(mark){
      //console.log(mark)
      if(this.isAuthed){
        wx.navigateTo({
          url: '/pages/'+mark+'/main'
        })
      }else{
        wx.showModal({
          title: '温馨提示',
          content: '您还没有授权将无法使用该功能，请点击授权',
          success (res) {
            if (res.confirm) {
              //console.log('用户点击确定')
            } else if (res.cancel) {
              //console.log('用户点击取消')
            }
          }
        })
      }
    }
  }
}
</script>

<style scoped>
.user {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.avatar {
  height: 400rpx;
  line-height: 400rpx;
  align-self: center;
}
.nickName{
  margin: 30rpx 0 100rpx 0;
  color: #666;
}
.user_avatar {
  display: block;
  overflow: hidden;
  width: 200rpx;
  height: 200rpx;
  line-height: 200rpx;
  margin-top: 100rpx;
  border-radius: 50%;
  font-size: 22rpx;
  box-shadow:0px 0px 0px 3px #0087ca;
}
.user_avatar img:hover {
  transform: rotate(7deg);
  /* transform: scale(1.2,1.2); */
}
.hover-underline-animation {
  display: inline-block;
  position: relative;
  color: #0087ca;
}
.hover-underline-animation::after {
  content: '';
  position: absolute;
  width: 100%;
  transform: scaleX(0);
  height: 2px;
  bottom: 0;
  left: 0;
  background-color: #0087ca;
  transform-origin: bottom right;
  transition: transform 0.25s ease-out;
}
.hover-underline-animation:hover::after {
  transform: scaleX(1);
  transform-origin: bottom left;
}
.btn {
  background: none;
  border: 0;
  border-radius: none;
  box-sizing: border-box;
  box-shadow: inset 0 0 0 2px #f45e61;
  color: #f45e61;
  font-size: inherit;
  font-weight: 700;
  margin: 1em;
  padding: 30rpx 60rpx;
  text-align: center;
  text-transform: capitalize;
  position: relative;
  vertical-align: middle;
}
.btn::before, .btn::after {
  box-sizing: border-box;
  content: '';
  position: absolute;
  width: 100%;
  height: 100%;
}
.draw {
  -webkit-transition: color 0.25s;
          transition: color 0.25s;
}
.draw::before, .draw::after {
  border: 2px solid transparent;
  width: 0;
  height: 0;
}
.draw::before {
  top: 0;
  left: 0;
}
.draw::after {
  bottom: 0;
  right: 0;
}
.draw:hover {
  color: #60daaa;
}
.draw:hover::before, .draw:hover::after {
  width: 100%;
  height: 100%;
}
.draw:hover::before {
  border-top-color: #60daaa;
  border-right-color: #60daaa;
  -webkit-transition: width 0.25s ease-out, height 0.25s ease-out 0.25s;
          transition: width 0.25s ease-out, height 0.25s ease-out 0.25s;
}
.draw:hover::after {
  border-bottom-color: #60daaa;
  border-left-color: #60daaa;
  -webkit-transition: border-color 0s ease-out 0.5s, width 0.25s ease-out 0.5s, height 0.25s ease-out 0.75s;
          transition: border-color 0s ease-out 0.5s, width 0.25s ease-out 0.5s, height 0.25s ease-out 0.75s;
}
.center:hover {
  color: #6477b9;
}
.center::before, .center::after {
  top: 0;
  left: 0;
  height: 100%;
  width: 100%;
  -webkit-transform-origin: center;
      -ms-transform-origin: center;
          transform-origin: center;
}
.center::before {
  border-top: 2px solid #6477b9;
  border-bottom: 2px solid #6477b9;
  -webkit-transform: scale3d(0, 1, 1);
          transform: scale3d(0, 1, 1);
}
.center::after {
  border-left: 2px solid #6477b9;
  border-right: 2px solid #6477b9;
  -webkit-transform: scale3d(1, 0, 1);
          transform: scale3d(1, 0, 1);
}
.center:hover::before, .center:hover::after {
  -webkit-transform: scale3d(1, 1, 1);
          transform: scale3d(1, 1, 1);
  -webkit-transition: -webkit-transform 0.8s;
          transition: transform 0.8s;
}

</style>
