<template>
  <div class="panel panel-default">
    <div class="panel-heading">一键领取饿了么手气最佳红包</div>
    <div class="panel-body">
      <div class="text-danger tip">美团辅助已失效，我们正在尝试修复，饿了么或成最大赢家</div>
      <div class="form-group">
        <label>要领取手气最佳红包的手机号码</label>
        <input type="mobile" class="form-control" v-model="mobile" placeholder="11位手机号码" maxlength="11">
      </div>
      <div class="form-group">
        <label>饿了么分享出来的红包链接</label>
        <input type="text" class="form-control" v-model="url" placeholder="红包完整 URL 链接">
        <p class="url-demo">
          <br>1. 拼手气红包：https://h5.ele.me/hongbao/开头的链接
          <br><span>链接不带 lucky_number 的不是拼手气，不能用</span>
          <br>2. 年终奖红包：https://h5.ele.me/yearawards/开头的链接
          <br><span>年终奖红包 手机号随便填 或 留空即可</span>
          <br>3. 短链接：http://url.cn/开头的链接
        </p>
      </div>
      <div class="form-group">
        <label>选择服务器线路</label>
        <select class="form-control" v-model="domain">
          <option v-for="(url, index) in domains" :key="index" :value="index">
            {{index + 1}}号服务器{{index === 0 ? '（官服）' : ''}}
          </option>
        </select>
      </div>
      <div class="form-group">
        <button type="button" class="btn btn-danger submit" :disabled="submit" @click="getHongbao">
          {{submit ? '正在领取...' : '领取手气最佳红包'}}
        </button>
        <button type="button" class="btn btn-default ali-hongbao-m">支付宝天天领红包</button>
      </div>
      <ul class="breadcrumb">
        <li>
          <a href="https://github.com/game-helper/hongbao" target="_blank">本站开源</a>
        </li>
        <li>
          <a href="https://github.com/game-helper/hongbao/issues/new" target="_blank">反馈问题</a>
        </li>
        <li>
          <a href="https://github.com/game-helper/donate" target="_blank">捐赠支持我们</a>
        </li>
      </ul>
      <div>
        <pre><b>如何获取拼手气红包？</b><br>1. 好友下单后，分享到群里的红包<br>2. 饿了么 APP 买过的订单点进去，分享红包</pre>
        <pre><b>如何复制红包链接？</b><br>1. 分享到 QQ，选择 “我的电脑”，PC 版 QQ 复制链接<br>2. 分享到微信，PC 版微信右键用浏览器打开，复制链接<br>3. 长按微信分享的卡片，点击更多，发送邮件，复制链接</pre>
        <pre class="mutiline"><b>手气最佳红包被小号领掉了？</b><br>因为我们内置的账号有限，而访问网站的人太多了。在更换您的手机号领取最佳红包时，被其它人用小号换绑了。遇到这种情况请换一个红包链接再试，或者避开上午的高峰期使用。</pre>
        <pre class="mutiline"><b>服务器很慢或者一直繁忙？</b><br>您可以尝试更换其它服务器线路，或者换一个红包链接再试。问题仍然存在，可以点击上面的反馈问题按钮，向我们反馈。</pre>
      </div>
      <div>
        <img class="qrcode" src="./static/qrcode.jpg">
        <p class="text-center"><b>红包分享交流微信群</b><br>请加上面的微信号邀请你入群<br>加群的朋友非常多，请耐心等待通过</p>
      </div>
    </div>
    <div class="ali-hongbao-pc">
      <img src="./static/alihongbao.jpg">
      <p>支付宝天天领红包</p>
    </div>
  </div>
</template>

<script>
  import 'bootstrap/dist/css/bootstrap.css'
  import axios from 'axios'
  import ClipboardJS from 'clipboard'
  import domains from './service/domains'

  export default {
    data () {
      return {
        url: '',
        mobile: localStorage.getItem('mobile') || '',
        submit: false,
        domains,
        domain: 0
      }
    },
    mounted () {
      this.aliHongbao()
    },
    methods: {
      async getHongbao () {
        if (this.submit) {
          return
        }
        const {url, mobile, domain} = this
        this.submit = true
        try {
          const {data: {message}} = await axios.post(`${domains[domain]}/hongbao`, {url, mobile})
          alert(message)
        } catch (e) {
          console.error(e)
          alert('服务器繁忙，请稍后重试')
        }
        this.submit = false
        localStorage.setItem('mobile', mobile)
      },
      aliHongbao () {
        const clipboard = new ClipboardJS('.ali-hongbao-m', {
          text: () => 'RY2WVP09sm'
        })
        clipboard.on('success', e => {
          alert('打开支付宝即可领取红包（每天仅一次）')
          e.clearSelection()
        })
        clipboard.on('error', e => {
          if (confirm('您的设备不支持复制红包码，是否跳转到支付宝领取？')) {
            window.location.href = 'https://render.alipay.com/p/f/fd-j6lzqrgm/guiderofmklvtvw.html?shareId=2088012515595090&campStr=p1j%2BdzkZl018zOczaHT4Z5CLdPVCgrEXq89JsWOx1gdt05SIDMPg3PTxZbdPw9dL&sign=wFumSPvgk8JDVbYta9%2FILuhrUmaW%2FpXfsnKt%2FUzDj8o%3D&scene=offlinePaymentNewSns'
          }
        })
      }
    }
  }
</script>

<style lang="less">
  .panel {
    width: 410px;
    margin: 15px auto;
  }

  .breadcrumb {
    margin-bottom: 10px;
    white-space: nowrap;
  }

  .submit,
  img {
    width: 100%;
  }

  .tip {
    padding-bottom: 10px;
  }

  .qrcode {
    border-radius: 4px;
    margin-bottom: 15px;
    border: 1px solid #ccc;
  }

  .mutiline {
    white-space: normal;
  }

  .url-demo {
    white-space: nowrap;
    text-overflow: ellipsis;
    overflow: hidden;
    line-height: 1.5;

    span {
      color: #666;
    }
  }

  .ali-hongbao-pc {
    position: fixed;
    top: 50%;
    transform: translateY(-50%);
    left: 50%;
    margin-left: -500px;
    width: 250px;
    z-index: 10;
    text-align: center;
    border: 1px solid #ccc;
    padding: 10px;
    border-radius: 4px;

    p {
      padding-top: 20px;
    }
  }

  .ali-hongbao-m {
    display: none;
  }
  
  @media screen and (max-width: 480px) {
    .panel {
      width: 100%;
      margin: 0;
      border: 0;
      box-shadow: none;
    }

    .ali-hongbao-pc {
      display: none;
    }

    .ali-hongbao-m {
      display: block;
      width: 100%;
      margin-top: 15px;
    }
  }
</style>
