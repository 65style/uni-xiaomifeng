<template>
  <view class="baobei">
    <form @submit="formSubmit" @reset="formReset">
      <view class="info">
        <view class="info-title">报备说明：</view>
        <view class="info-desc"
          >1、填写用户的身份证、姓名、基本情况说明 及
          身份证、房产证等图片资料即可对客户进行报备；</view
        >
        <view class="info-desc"
          >2、报备成功后，平台将会为您的客户设置为期15天的客户保护期。</view
        >
      </view>

      <view class="item">
        <view class="item-label">
          客户姓名
        </view>
        <view class="item-input">
          <input
            type="text"
            name="name"
            placeholder="请输入客户姓名"
            maxlength="11"
          />
        </view>
      </view>
      <view class="item">
        <view class="item-label">
          客户手机号
        </view>
        <view class="item-input">
          <input
            type="number"
            name="phoneNum"
            placeholder="请输入客户手机号"
            maxlength="11"
          />
        </view>
      </view>
      <view class="item">
        <view class="item-label">
          身份证号码
        </view>
        <view class="item-input">
          <input
            type="text"
            name="IDCard"
            placeholder="请输入身份证号码"
            maxlength="18"
          />
        </view>
      </view>
      <view class="item-textarea">
        <view class="item-label">
          基本情况说明<text class="item-label-small">(小于200字)</text>
        </view>
        <view class="item-input uni-textarea">
          <textarea name="baseInfo" placeholder="请填写基本情况说明" />
        </view>
      </view>

      <view class="item-upload">
        <view class="item-label">
          图片资料<text class="item-label-small">(1-4张)</text>
        </view>
        <view class="item-image-list">
          <view class="uni-uploader__files">
            <block v-for="(image, index) in imageList" :key="index">
              <view class="uni-uploader__file">
                <image
                  class="uni-uploader__img"
                  :src="image"
                  :data-src="image"
                  @tap="previewImage"
                ></image>
                <view class="image-clear" @click.stop="removeImage(index)">
                  <icon type="clear" size="26" />
                </view>
              </view>
            </block>
            <view class="uni-uploader__input-box" v-if="imageList.length !== 4">
              <view class="uni-uploader__input" @tap="chooseImage">
                <image
                  class="upload-image"
                  src="../../static/upload-btn.png"
                ></image>
                <text class="upload-text">请上传图片</text>
              </view>
            </view>
          </view>
        </view>
      </view>

      <button class="submit" form-type="submit">
        提交报备资料
      </button>
    </form>
    <fabButton></fabButton>
  </view>
</template>

<script>
import fabButton from "../../components/fabButton";
import { mapState } from "vuex";
export default {
  components: {
    fabButton
  },
  computed: mapState(["userInfo"]),
  data() {
    return {
      productId: "",
      imageList: [],
      imgArr: []
    };
  },
  onLoad(opts) {
    this.productId = opts.id || "";
    console.log(opts);
  },
  methods: {
    validAllData(data) {
      // if (!data.name.trim()) {
      //   uni.showToast({
      //     title: "请输入客户姓名！",
      //     icon: "none",
      //     duration: 2000
      //   });
      //   return false;
      // }
      if (!data.phoneNum.trim()) {
        uni.showToast({
          title: "请输入客户手机号",
          icon: "none",
          duration: 2000
        });
        return false;
      }
      if (!/^1[3456789]\d{9}$/.test(data.phoneNum)) {
        uni.showToast({
          title: "您输入的手机号无效，请重新输入",
          icon: "none",
          duration: 2000
        });
        return false;
      }
      if (!data.phoneNum.trim()) {
        uni.showToast({
          title: "请输入身份证号码",
          icon: "none",
          duration: 2000
        });
        return false;
      }
      if (
        !/^\d{6}(18|19|20)?\d{2}(0[1-9]|1[012])(0[1-9]|[12]\d|3[01])\d{3}(\d|X)$/i.test(
          data.IDCard
        )
      ) {
        uni.showToast({
          title: "您输入的身份证号码无效，请重新输入",
          icon: "none",
          duration: 2000
        });
        return false;
      }
      if (!data.baseInfo.trim()) {
        uni.showToast({
          title: "请输入基本情况说明！",
          icon: "none",
          duration: 2000
        });
        return false;
      }
      if (!this.imageList.length) {
        uni.showToast({
          title: "请上传图片资料！",
          icon: "none",
          duration: 2000
        });
        return false;
      }
      return true;
    },
    async formSubmit(e) {
      let formdata = e.detail.value;
      formdata.productId = this.productId;
      console.log(formdata);
      let valid = await this.validAllData(formdata);
      console.log(valid);
      if (valid) {
        formdata.imgArr = this.imgArr.join("|");
        formdata.userPhone = this.userInfo.phoneNum;
        formdata.productId = this.productId;
        // this.$minApi
        //   .submitBaobei(formdata)
        //   .then(res => {
        //     console.log(res);
        //     if (res.code !== 1) {
        //       uni.showToast({ title: res.msg, icon: "none", duration: 2000 });
        //       return;
        //     }
        //     let name = await this.plusXing(this.formdata.name, 1, 0)
        //     let IDCard = await this.plusXing(this.formdata.IDCard, 6, 4)
        //     let params = {
        //       name: name,
        //       IDCard: IDCard,
        //       expirationTime: res.expirationTime
        //     };
        //     uni.redirectTo({
        //       url: "result?params=" + JSON.stringify(params)
        //     });
        //   })
        //   .catch(err => {
        //     console.log(err);
        //   });
        this.$minApi
          .submitBaobei(formdata)
          .then(res => {
            console.log(res);
            if (res.code !== 1) {
              uni.showToast({ title: res.msg, icon: "none", duration: 2000 });
              return;
            }
            var name =
              formdata.name.substr(0, 1) +
              new Array(formdata.name.length).join("*");
            var IDCard =
              formdata.IDCard.substr(0, 6) +
              new Array(formdata.IDCard.length - 10).join("*") +
              formdata.IDCard.substr(-4);
            var expirationTime = res.expirationTime;
            var params = {
              name,
              IDCard,
              expirationTime
            };
            console.log(params);
            uni.redirectTo({
              url: "result?params=" + JSON.stringify(params)
            });
          })
          .catch(err => {
            uni.showModal({ title: "请求失败", content: JSON.stringify(err) });
          });
      }
    },
    plusXing(str, frontLen, endLen) {
      var len = str.length - frontLen - endLen;
      var xing = "";
      for (var i = 0; i < len; i++) {
        xing += "*";
      }
      return (
        str.substring(0, frontLen) + xing + str.substring(str.length - endLen)
      );
    },

    chooseImage: async function() {
      uni.chooseImage({
        count: 1,
        sizeType: ['compressed'], //可以指定是原图还是压缩图，默认二者都有
        success: res => {
          console.log(res);
          if (res.tempFilePaths.length > 4) {
            uni.showToast({
              title: "最多选择4张图片",
              icon: "none",
              duration: 2000
            });
            return;
          }
          
          const tempFilePaths = res.tempFilePaths;
          this.getImageInfo(tempFilePaths[0]).then(res => {
            uni.showLoading({ title: "上传图片中..." });
            uni.uploadFile({
              url: "/erp/Api/uploadHousePic",
              filePath: res,
              name: "file",
              formData: {
                type: "baobei"
              },
              success: uploadFileRes => {
                var result = JSON.parse(uploadFileRes.data);
                console.log(result);
                if (result.code !== "100") {
                  console.log(44444444444444);
                  uni.showToast({
                    title: result.msg,
                    icon: "none",
                    duration: 2000
                  });
                  return;
                }
                this.imageList.push(this.imgBaseUrl + result.url);
                this.imgArr.push(result.url);
                uni.hideLoading();
              }
            });
            // uploadTask.onProgressUpdate(res => {
            //   console.log("上传进度" + res.progress);
            //   console.log("已经上传的数据长度" + res.totalBytesSent);
            //   console.log(
            //     "预期需要上传的数据总长度" + res.totalBytesExpectedToSend
            //   );
  
            //   // // 测试条件，取消上传任务。
            //   if (res.progress === 100) {
            //     uni.hideLoading();
            //   }
            // });

          })
        }
      });
    },
        // 压缩图片
    getImageInfo(src) {
      return new Promise((resolve, reject) => {
        // uni.showLoading({
        //   title: "压缩中...",
        //   icon: "none"
        // });
        uni.getImageInfo({
          src: src,
          success(res) {
            console.log("压缩前", res);
            // 缩放图片需要的canvas
            var canvas = document.createElement("canvas");
            var context = canvas.getContext("2d");
            var img = new Image();
            img.src = src;
            // base64地址图片加载完毕后
            img.onload = function() {
              // 图片原始尺寸
              var originWidth = res.width;
              var originHeight = res.height;
              // 最大尺寸限制
              var maxWidth = 1080,
                maxHeight = 1080;
              // 目标尺寸
              var targetWidth = originWidth,
                targetHeight = originHeight;
              // 图片尺寸超过400x400的限制
              if (originWidth > maxWidth || originHeight > maxHeight) {
                if (originWidth / originHeight > maxWidth / maxHeight) {
                  // 更宽，按照宽度限定尺寸
                  targetWidth = maxWidth;
                  targetHeight = Math.round(
                    maxWidth * (originHeight / originWidth)
                  );
                } else {
                  targetHeight = maxHeight;
                  targetWidth = Math.round(
                    maxHeight * (originWidth / originHeight)
                  );
                }
              }

              // canvas对图片进行缩放
              canvas.width = targetWidth;
              canvas.height = targetHeight;
              // 清除画布
              context.clearRect(0, 0, targetWidth, targetHeight);
              // 图片压缩
              context.drawImage(img, 0, 0, targetWidth, targetHeight);
              // canvas转为blob并上传
              var dataUrl = canvas.toDataURL();
              console.log("压缩后", dataUrl);
              resolve(dataUrl);
              // uni.hideLoading();
            };
          }
        });
      });
    },
    // 删除图片
    removeImage(i) {
      console.log("remove");
      this.imageList.splice(i, 1);
      this.imgArr.splice(i, 1);
    },
    previewImage: function(e) {
      var current = e.target.dataset.src;
      uni.previewImage({
        current: current,
        urls: this.imageList
      });
    }
  }
};
</script>

<style>
.baobei {
  position: relative;
  padding-bottom: 100upx;
  background-color: #ffffff;
}
.info {
  padding: 30upx;
  border-bottom: 1upx solid #dddddd;
  font-size: 28upx;
}
.submit {
  position: fixed;
  left: 0;
  bottom: 0;
  width: 100%;
  height: 100upx;
  line-height: 100upx;
  border: none;
  border-radius: 0;
  background-color: #d99d40;
  text-align: center;
  font-size: 36upx;
  color: #ffffff;
  z-index: 100;
}
.submit::after {
  border: none;
  border-radius: 0;
}

.item {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  padding: 0 30upx;
  height: 100upx;
  border-bottom: 1upx solid #dddddd;
}
.item .item-label {
  flex: 1;
  font-size: 28upx;
}
.item .item-input {
  flex: 2;
  font-size: 28upx;
}
.item .item-input input {
  font-size: 28upx;
}
.item .item-input.captcha,
.item .item-input.input-city {
  display: flex;
  flex-direction: row;
  align-items: center;
  height: 100upx;
}

.item-textarea,
.item-upload {
  padding: 0 30upx;
  border-bottom: 1upx solid #dddddd;
  font-size: 28upx;
}
.item-textarea .item-label,
.item-upload .item-label {
  height: 80upx;
  line-height: 100upx;
}
.item-textarea .item-label .item-label-small,
.item-upload .item-label .item-label-small {
  color: #818181;
  font-size: 24upx;
}
.item-textarea .item-input {
  width: 100%;
  height: 200upx;
}
.item-textarea .item-input textarea {

  font-size: 28upx;
}
.uni-textarea uni-textarea {
  width: 100%;
  /* padding: 0 30upx; */
  line-height: 1.6;
  /* font-size: 28upx; */
  height: 180upx;
}
.item-image-list {
  padding-bottom: 30upx;
}
.uni-uploader__files {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
}
.uni-uploader__input-box {
  position: relative;
  margin: 10upx;
  width: 300upx;
  height: 200upx;
  border: 2upx dashed #d9d9d9;
}
.uni-uploader__input {
  position: absolute;
  z-index: 1;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  /* opacity: 0; */
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  margin: 0 auto;
  text-align: center;
}
.uni-uploader__file {
  position: relative;
  margin: 10upx;
  width: 300upx;
  height: 200upx;
}
.uni-uploader__img {
  display: block;
  width: 300upx;
  height: 200upx;
  border: 2upx dashed #d9d9d9;
}
.upload-image {
  width: 90upx;
  height: 90upx;
}
.upload-text {
  margin-top: 20upx;
  font-size: 26upx;
  color: #949494;
}
.image-clear {
  position: absolute;
  top: 0;
  right: 0;
}
</style>