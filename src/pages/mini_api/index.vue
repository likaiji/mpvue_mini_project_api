<template>
    <div class="page">
        <button type="primary" @click="getLocation()">获取经纬度</button>

        <button type="primary" @click="scan_code()">扫码(相机+图片)</button>

        <button type="primary" @click="get_phone_message()">获取手机客户端信息</button>
        <p v-if="phone_message.batteryLevel != null">手机品牌：{{phone_message.brand}}</p>
        <p v-if="phone_message.model != null">手机型号：{{phone_message.model}}</p>
        <p v-if="phone_message.model != null">设备像素比：{{phone_message.pixelRatio}}</p>
        <p v-if="phone_message.model != null">屏幕宽度：{{phone_message.screenWidth}}</p>
        <p v-if="phone_message.model != null">屏幕高度：{{phone_message.screenHeight}}</p>
        <p v-if="phone_message.model != null">可使用窗口宽度：{{phone_message.windowWidth}}</p>
        <p v-if="phone_message.model != null">可使用窗口高度：{{phone_message.windowHeight}}</p>
        <p v-if="phone_message.model != null">状态栏的高度：{{phone_message.statusBarHeight}}</p>
        <p v-if="phone_message.model != null">微信设置的语言：{{phone_message.language}}</p>
        <p v-if="phone_message.model != null">微信版本号：{{phone_message.version}}</p>
        <p v-if="phone_message.model != null">操作系统版本：{{phone_message.system}}</p>
        <p v-if="phone_message.model != null">客户端平台：{{phone_message.platform}}</p>
        <p v-if="phone_message.model != null">用户字体大小设置。以“我-设置-通用-字体大小”中的设置为准，单位：px：{{phone_message.fontSizeSetting}}</p>
        <p v-if="phone_message.model != null">客户端基础库版本：{{phone_message.SDKVersion}}</p>

        <button type="primary" @click="get_login_message()">获取用户信息</button>
        <img v-if="userInfo.avatarUrl != null" :src="userInfo.avatarUrl" alt="">
        <p v-if="userInfo.nickName  != null">登录用户：{{userInfo.nickName}}</p>


        <button @click="show_modal()" type='primary'>点我展示modal</button>
        <button @click="show_alert()" type='primary'>点我展示弹窗</button>


        <button @click="recording()" type='primary'>录音开始</button>
        <button @click="end_recording()" type='primary'>录音结束</button>
        <button @click="play_radio()" type='primary'>播放录音</button>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                userInfo:{}, // 用户信息
                phone_message: {}, // 手机客户端信息
                radioUrl:'' // 录音文件地址
            }
        },
        computed: {

        },
        methods: {
           // 获取经纬度
           getLocation: function(){
              wx.getLocation({
                  type: 'gcj02', //返回可以用于wx.openLocation的经纬度
                  success: function(res) {
                      console.log(res)
                      var latitude = res.latitude
                      var longitude = res.longitude
                      wx.openLocation({
                          latitude: latitude,
                          longitude: longitude,
                          scale: 28
                      })
                  }
              })
           },
            // 扫码(相机+图片)
            scan_code: function(){
                wx.scanCode({
                    success: (res) => {
                        console.log(res)
                    }
                })
            },
            // 获取手机客户端信息
            get_phone_message: function(){
               let that = this;
                wx.getSystemInfo({
                    success: function(res) {
                        console.log(res)
                        that.phone_message = res;
                        // console.log(res.model)
                        // console.log(res.pixelRatio)
                        // console.log(res.windowWidth)
                        // console.log(res.windowHeight)
                        // console.log(res.language)
                        // console.log(res.version)
                        // console.log(res.platform)
                    }
                })
            },
            // 获取用户信息
            get_login_message:function(){
                wx.getUserInfo({
                    success: res => {
                        console.log(res)
                        this.userInfo = res.userInfo;
                        console.log(this.userInfo)
                        // app.globalData.userInfo = res.userInfo
                        // that.setData({
                        //     userInfo: res.userInfo,
                        //     hasUserInfo: true
                        // })
                    }
                })
                // wx.login({
                //     success: function (res) {
                //         console.log(res)
                //         if (res.code) {
                //             wx.getUserInfo({
                //                 success: res => {
                //                     console.log(res.userInfo)
                //                     app.globalData.userInfo = res.userInfo
                //                     that.setData({
                //                         userInfo: res.userInfo,
                //                         hasUserInfo: true
                //                     })
                //                 }
                //             })
                //         } else {
                //             console.log('登录失败！' + res.errMsg)
                //         }
                //     }
                // });
            },
            //点我展示modal
            show_modal:function(){
                wx.showModal({
                    title: '提示',
                    content: '这是一个模态弹窗',
                    success: function(res) {
                        if (res.confirm) {
                            console.log('用户点击确定')
                        } else if (res.cancel) {
                            console.log('用户点击取消')
                        }
                    }
                })
            },
            //点我展示弹窗
            show_alert:function(){
                // wx.showToast({
                //     title: '成功',
                //     icon: 'success',
                //     duration: 2000
                // })
                wx.showToast({
                    title: '加载中...',
                    icon: 'loading',
                    duration: 2000
                })
            },

            // 开始录音
            recording:function(){
                var that = this;
                wx.startRecord({
                    success: function (res) {
                        var tempFilePath = res.tempFilePath;
                        that.radioUrl = tempFilePath;
                        wx.showToast({
                            title: '录音完成',
                            icon: 'success',
                            duration: 2000
                        })
                    },
                    fail: function (res) {
                        wx.showModal({
                            title: '提示',
                            content: '录音失败',
                            showCancel: false
                        })
                    }
                })
            },
            // 结束录音
            end_recording:function(){
                wx.stopRecord()
            },
            // 播放录音
            play_radio:function(){
                console.log(this.radioUrl)
                let that = this;
                let src = this.radioUrl;
                if(src == ''){
                    wx.showModal({
                        title: '提示',
                        content: '请先录音',
                        showCancel: false
                    })
                    return;
                }
                wx.playVoice({
                    filePath: that.radioUrl,
                    fail: function(res){
                        wx.showModal({
                            title: '提示',
                            content: '录音播放失败',
                            showCancel: false
                        })
                    }
                })

            }
        }
    }
</script>

<style scoped>

</style>
