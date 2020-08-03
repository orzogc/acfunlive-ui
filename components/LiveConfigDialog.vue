<template>
  <el-dialog title="订阅主播" :visible.sync="show" width="35%">
    <div>
      <el-input v-model="inputUID" placeholder="请输入主播uid" style="width: 50%" />
      <span v-if="warn" style="color: red">请输入一个大于0的整数</span>
    </div>
    <div style="margin-top: 20px">
      <el-checkbox v-model="notify" label="开播提醒" />
      <el-checkbox v-model="record" label="自动录播" />
      <el-checkbox v-model="danmu" label="自动下载弹幕" />
    </div>
    <span slot="footer">
      <el-button type="primary" @click="configLive">设置</el-button>
    </span>
  </el-dialog>
</template>

<script>
export default {
  props: {
    showDialog: {
      type: Boolean,
      default: false
    }
  },
  data () {
    return {
      inputUID: '',
      notify: true,
      record: false,
      danmu: false,
      warn: false
    }
  },
  computed: {
    show: {
      get () {
        return this.showDialog
      },
      set (v) {
        if (v === false) {
          this.$emit('hideDialog')
        }
      }
    }
  },
  methods: {
    async configLive () {
      if (this.inputUID === '') {
        this.show = false
        return
      }
      const uid = parseInt(this.inputUID, 10)
      if (isNaN(uid) || uid <= 0) {
        this.warn = true
        return
      }
      this.warn = false
      this.show = false
      let result = true
      if (this.notify) {
        result = result && await fetch('http://127.0.0.1:51880/addnotify/' + uid)
          .then(resp => resp.json())
          .catch(e => console.error(e))
      }
      if (this.record) {
        result = result && await fetch('http://127.0.0.1:51880/addrecord/' + uid)
          .then(resp => resp.json())
          .catch(e => console.error(e))
      }
      if (this.danmu) {
        result = result && await fetch('http://127.0.0.1:51880/adddanmu/' + uid)
          .then(resp => resp.json())
          .catch(e => console.error(e))
      }
      if (result !== true) {
        console.error(`configLive返回错误：${result}`)
      }
    }
  }
}
</script>
