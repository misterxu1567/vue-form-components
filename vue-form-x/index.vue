<template>
    <div class="formComponentX">
        <div class="formBox">
            <input type="text" v-model="subFormData.trueName" :style="{'color': mainColor}" :placeholder="formConfig.namePlaceholder" @focus="addStatusFn" @blur="clearStatusFn" class="inputBox">
            <div class="selBox" v-if="formConfig.gradeStatus">
                <select class="selectGrade" v-model="gradeIndex" :style="gradeInputStyle" :class="{'placeholder': gradeIndex == 0}" @focus="addStatusFn" @blur="clearStatusFn">
                    <option v-for="(option, index) in gradeSubjectData" :disabled="option.val == 0" :key="index" :value="index">{{option.prop}}</option>
                </select>
                <i class="iconfont icon-xiajiantou" :style="{color: mainColor}"></i>
            </div>
            <div class="selBox" v-if="formConfig.gradeStatus || formConfig.subjectStatus">
                <select class="selectGrade" v-model="subFormData.subject" :style="subInputStyle" :class="{'disabled': gradeIndex == 0, 'placeholder': subFormData.subject === '选择科目'}" :disabled="gradeIndex == 0"  @focus="addStatusFn" @blur="clearStatusFn">
                    <option value="-1" disabled>选择科目</option>
                    <option v-for="(item ,index) in  gradeSubjectData[gradeIndex]['subject']" :disabled="item === '选择科目'" :key="index" :value="item.val">{{item.prop}}</option>
                </select>
                <i class="iconfont icon-xiajiantou" :style="{color: mainColor}"></i>
            </div>
            <input type="tel" maxlength="11" :style="{'color': mainColor}" v-model="subFormData.telephone" :placeholder="formConfig.phonePlaceholder" @focus="addStatusFn" @blur="clearStatusFn" class="inputBox">
            <!--发送验证码-->
            <div class="yzmBox" v-if="formConfig.yzmStatus">
                <input type="tel" :style="{'color': mainColor}" v-model="subFormData.yzm" :placeholder="formConfig.yzmPlaceholder" @focus="addStatusFn" @blur="clearStatusFn" class="inputBox">
                <button class="sendYzmBtn" :disabled="yzmStatus" :class="{'sendYzmDisabled': yzmStatus}" @click="remainingTimeFn" >{{yzmTxt}}</button>
            </div>
            <!--城市选择-->
            <ul class="cityBox" v-if="formConfig.cityStatus">
                <li>
                    <select class="" :style="provinceSelStyle" v-model="province">
                        <option value="-1" disabled>省</option>
                        <option v-for="(val, key) in cityData['100000']" :key="val" :value="key">{{val}}</option>
                    </select>
                    <i class="iconfont icon-xiajiantou" :style="{color: mainColor}"></i>
                </li>
                <li>
                    <select class="" :style="citySelStyle" v-model="city">
                        <option value="-1" disabled>市</option>
                        <option v-for="(val, key) in cityData[province]" :key="val" :value="key">{{val}}</option>
                    </select>
                    <i class="iconfont icon-xiajiantou" :style="{color: mainColor}"></i>
                </li>
                <li>
                    <select class="" :style="countySelStyle" v-model="county">
                        <option value="-1" disabled>县／区</option>
                        <option v-for="(val, key) in cityData[city]" :key="val" :value="key">{{val}}</option>
                    </select>
                    <i class="iconfont icon-xiajiantou" :style="{color: mainColor}"></i>
                </li>
            </ul>
            <!--详细地址-->
            <input type="text" v-if="formConfig.cityStatus" v-model="subFormData.detail" :style="{'color': mainColor}" placeholder="请输入详细地址" @focus="addStatusFn" @blur="clearStatusFn" class="inputBox">
            <button class="btn" @click="submitFormFn" :style="{'backgroundColor': formConfig.submitBtnImg ? '' : mainColor, 'height': formConfig.submitBtnImg ? 'auto' : '44px' }">
                {{formConfig.submitBtnImg ? '' : '提交'}}
                <img v-if="formConfig.submitBtnImg" :src="formConfig.submitBtnImg">
            </button>
        </div>
    </div>
</template>

<script>
    import gradeSubjectData from './const/gradeSub';
    import cityData from './const/city';

    export default {
        name: '',
        props: {
            formConfig: {
                type: Object,
                required: false,
                default: {
                    namePlaceholder: '姓名',
                    phonePlaceholder: '手机',
                    yzmPlaceholder: '验证码',
                    yzmStatus: false,
                    gradeStatus: false,
                    subjectStatus: false,
                    cityStatus: true,
                    submitBtnImg: ''
                }
            },
            mainColor: {
                type: String,
                required: false,
                default: '#666'
            }
        },
        data () {
            return {
                gradeIndex: 0,
                gradeSubjectData: gradeSubjectData,
                subFormData: {
                    trueName: '',
                    telephone: '',
                    grade: 0,
                    subject: '-1',
                    yzm: '',
                    detail: ''
                },
                yzmTxt: '发送验证码',
                yzmStatus: false,
                cityData: cityData,
                province: '-1',
                city: '-1',
                county: '-1'
            }
        },
        computed: {
            gradeInputStyle () {
                var s = {};
                if (this.gradeIndex != 0) {
                    s['color'] = this.mainColor;
                }
                return s;
            },
            subInputStyle () {
                var s = {};
                if (this.subFormData.subject != -1) {
                    s['color'] = this.mainColor;
                }
                return s;
            },
            provinceSelStyle () {
                var s = {};
                if (this.province != -1) {
                    s['color'] = this.mainColor;
                }
                return s;
            },
            citySelStyle () {
                var s = {};
                if (this.city != -1) {
                    s['color'] = this.mainColor;
                }
                return s;
            },
            countySelStyle () {
                var s = {};
                if (this.county != -1) {
                    s['color'] = this.mainColor;
                }
                return s;
            }
        },
        mounted () {},
        methods: {
            // 动态修改输入框状态
            addStatusFn (e) {
                e.target.style.borderColor = this.mainColor;
            },
            clearStatusFn (e) {
                e.target.style.borderColor = '#DFDFDF';
            },
            typeValidateFn (str, type){
                let email = /^\w+[@]\w+[.][\w.]+$/;
                let idCard = /(^\d{15}$)|(^\d{17}([0-9]|X|x)$)/;
                let phone = /^1[3|4|5|6|7|8|9]\d{9}$/;
                let postalcode = /^\d{6}$/;
                let chinese = /[^\a-zA-Z\u4E00-\u9FA5]/g; // 汉字
                let number = /\D/g;
                let a_num = /\W/g; // 仅限数字、字母、下划线 等价于[^A-Za-z0-9_]
                let bool = false;
                switch (type) {
                    case 'email':
                        bool = email.test(str);
                        break;
                    case 'idCard':
                        bool = idCard.test(str);
                        break;
                    case 'phone':
                        bool = phone.test(str);
                        break;
                    case 'postalcode':
                        bool = postalcode.test(str);
                        break;
                    case 'number':
                        bool = number.test(str);
                        return bool;
                        break;
                    case 'chinese':
                        bool = chinese.test(str);
                        return bool;
                        break;
                    case 'a_num':
                        bool = a_num.test(str);
                        return bool;
                        break;
                    default:
                        alert('未知类型');
                }
                return !bool
            },
            // 验证码倒计时
            remainingTimeFn () {
                var reqData = {};
                reqData['telephone'] = this.subFormData.telephone;
                if (this.typeValidateFn(this.subFormData.telephone, 'phone')) {
                    this.$toast('手机号码格式错误');
                    return false;
                };
                this.$emit('sendYzmFn');
                var time = 60;
                this.yzmStatus = true;
                var timer = setInterval(() => {
                    this.yzmTxt = --time + ' s';
                    if (time === -1) {
                        this.yzmTxt = '发送验证码';
                        this.yzmStatus = false;
                        clearInterval(timer);
                    }
                }, 1000);
            },
            // 提交
            submitFormFn () {
                if ((this.typeValidateFn(this.subFormData.trueName, 'chinese')) || this.subFormData.trueName == '') {
                    this.$toast('名字格式错误');
                    return false;
                };
                if (this.formConfig.gradeStatus && (this.gradeIndex == '' || this.gradeIndex == 0)) {
                    this.$toast('年级不能为空');
                    return false;
                };
                if ((this.formConfig.gradeStatus || this.formConfig.subjectStatus) && this.subFormData.subject == -1 ) {
                    this.$toast('科目不能为空');
                    return false;
                };
                if (this.typeValidateFn(this.subFormData.telephone, 'phone')) {
                    this.$toast('手机号码格式错误');
                    return false;
                };
                if (this.formConfig.yzmStatus && this.subFormData.yzm == '') {
                    this.$toast('验证码不能为空');
                    return false;
                };
                if (this.formConfig.cityStatus && (this.province == -1 || this.city == -1 || this.county == -1) ) {
                    this.$toast('城市不能为空');
                    return false;
                };
                if (this.formConfig.cityStatus && this.subFormData.detail == '') {
                    this.$toast('地址详情不能为空');
                    return false;
                };
                let dataInfo = {};
                dataInfo['name'] = this.subFormData.trueName;
                dataInfo['telephone'] = this.subFormData.telephone;
                if (this.formConfig.gradeStatus) {
                    dataInfo['grade'] = this.subFormData.grade;
                }
                if (this.formConfig.subjectStatus) {
                    dataInfo['subject'] = this.subFormData.subject;
                }
                if (this.formConfig.yzmStatus) {
                    dataInfo['yzm'] = this.subFormData.yzm;
                }
                if (this.formConfig.cityStatus) {
                    dataInfo['address'] = `${this.cityData['100000'][this.province]} ${this.cityData[this.province][this.city]} ${this.cityData[this.city][this.county]} ${this.subFormData.detail}`;
                }
                this.$emit('submitFn', dataInfo);
            }
        },
        watch: {
            province (newVal) {
                if (this.cityData[newVal]) {
                    this.city = this.county = -1;
                }
            }
        }
    }
</script>
<style>
    @font-face {font-family: "iconfont";
        src: url('//at.alicdn.com/t/font_869949_fpzm7n2wfho.eot?t=1553756151496'); /* IE9 */
        src: url('//at.alicdn.com/t/font_869949_fpzm7n2wfho.eot?t=1553756151496#iefix') format('embedded-opentype'), /* IE6-IE8 */
        url('data:application/x-font-woff2;charset=utf-8;base64,d09GMgABAAAAAAKgAAsAAAAABkAAAAJTAAEAAAAAAAAAAAAAAAAAAAAAAAAAAAAAHEIGVgCCcApIYwE2AiQDCAsGAAQgBYRtBzQbjQXILrBt2NMWqQlbS9Gh2uyT4ASAeHh+P9y577+ZSNuiaBZNa5aWRYEoFhulkqgqSYeddky/SImfZxc040gKsIFtgFubWtdGLTdAXbBoTbTxKfFdtivXiVS2Hv3TBQDrJ8XXfcYIm/vEvdM/gT5I6wHlugeNNWlSF1AXB1KgYw2KrIQybhi74AUeJ1CpFwHX5ttDqiSzxwXi1DUqVSr4ZJkly4XShqVZvFCiPD1MW8rz6PvxQT9KJCUFu2zzZGanhm84acX9/30HAcFPQKtLUGAakIndxti6rGAcVhnLGQSOVRy88f9/YBVHNQn219mF1aAbxoM3POmjJhM8Vk/NgM5J04nmw8/R0++Dv6/W8aftHF7zc28evn9/bW2835z7m8n7mab/N2e+lnPgeG2IflY0phaI4Ppp+0d1c79KVRm86sWRMRvmAMr3Yg/+hmRgUzZktiVlTdLYnjSfpaJSJf7DrkbPVH3LqVOhXMsER5meDIVyw2Rmp1GiyixKlVtEpSl6dZUmTVbkCkyaAKHeQlLrJQr1DpnZixKtXpSqjywq7UfTllVGw62sMipBG8wNskWRdm1SiYt3aOJAcV4W8U7IVzcIst0tJxcYIS8xoyemP0RDYApBgdMwCAgKk4eWtJPIZcA7uulNbUthqmIVQ4pANmDcgMxCIjroLCqVz+8gIxZQuKOhLn6C2JXbP5AYMYBcqKNBDY/ySpcw+gahgYCREFBgEAoECCjNgzzIItrShMjFAHdahaHq9vba8O92QSXbkMJJL1ReuMpzsyKhOJUCAAAA') format('woff2'),
        url('//at.alicdn.com/t/font_869949_fpzm7n2wfho.woff?t=1553756151496') format('woff'),
        url('//at.alicdn.com/t/font_869949_fpzm7n2wfho.ttf?t=1553756151496') format('truetype'), /* chrome, firefox, opera, Safari, Android, iOS 4.2+ */
        url('//at.alicdn.com/t/font_869949_fpzm7n2wfho.svg?t=1553756151496#iconfont') format('svg'); /* iOS 4.1- */
    }
    .iconfont {
        font-family: "iconfont" !important;
        font-size: 16px;
        font-style: normal;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
    }
    .icon-xiajiantou:before {
        content: "\e731";
    }
    /**/
    .formComponentX .formBox{
        width: 90%;
        margin:0 auto;
    }
    .formComponentX .inputBox{
        display: block;
        width: 100%;
        height: 44px;
        border-radius: 4px;
        background: #fff;
        border:1px solid #DFDFDF;
        padding: 0 10px;
        box-sizing: border-box;
        font-size: 12px;
        margin-bottom: 10px;
        color:#333;
    }
    .formComponentX .selBox{
        position: relative;
        width: 100%;
        height: 44px;
        margin-bottom: 10px;
    }
    .formComponentX .selectGrade{
        display: block;
        width: 100%;
        height: 100%;
        border:1px solid #DFDFDF;
        box-sizing: border-box;
        border-radius: 4px;
        background: #fff;
        color:#333;
        font-size: 12px;
        text-indent: 10px;
    }
    .formComponentX .selBox .iconfont{
        position: absolute;
        right:4px;
        top:14px;
    }
    .formComponentX .disabled{
        color:#838383;
        background-color: #f1f1f1;
    }
    .formComponentX .placeholder{
        color:#838383;
        font-size: 12px;
    }
    .formComponentX .inputBox::-webkit-input-placeholder {
        color: #828282;
        font-size: 12px;
    }
    .formComponentX .placeholder{
        color:#828282;
    }

    .formComponentX  .formBox .btn{
        display: block;
        width: 100%;
        margin:20px auto 0;
        border-radius: 22px;
        font-size: 16px;
        font-weight: bold;
        color:#fff;
        letter-spacing: 4px;
    }
    .formComponentX  .formBox .btn>img{
        display: block;
        width: 100%;
        height: auto;
    }
    /**/
    .formComponentX .yzmBox{
        overflow: hidden;
    }
    .formComponentX .yzmBox .inputBox{
        float: left;
        width: 66%;
    }
    .formComponentX .yzmBox .sendYzmBtn{
        float: right;
        width: 32.3%;
        height: 44px;
        border-radius: 4px;
        background: #fff;
        border:1px solid #DFDFDF;
        box-sizing: border-box;
        font-size: 12px;
        color:#666;
    }
    .formComponentX .yzmBox .sendYzmDisabled{
        color: #bdbdbd;
        background: #f8f8f8;
    }
    /**/
    .formComponentX .cityBox{
        display: block;
        overflow: hidden;
        height: 46px;
        margin-bottom: 10px;
    }
    .formComponentX .cityBox>li{
        float: left;
        width: 32.3%;
        height: 44px;
        margin-right: 1.5%;
        background-color: #fff;
        border:1px solid #DFDFDF;
        border-radius: 4px;
        position: relative;
        box-sizing: border-box;
        padding-right: 20px;
    }
    .formComponentX .cityBox select{
        display: block;
        background: none;
        width: 100%;
        height: 100%;
        font-size: 12px;
        text-indent: 10px;
        color: #828282;
        position: relative;
        z-index: 2;
    }
    .formComponentX .cityBox>li .iconfont{
        position: absolute;
        right:4px;
        top:14px;
        z-index: 1;
    }
    .formComponentX .cityBox>li:last-child{
        margin-right: 0;
    }
</style>