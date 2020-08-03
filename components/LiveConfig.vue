<template>
  <div>
    <el-checkbox v-model="notify" label="开播提醒" @change="changeNotify" />
    <el-checkbox v-model="record" label="自动录播" @change="changeRecord" />
    <el-checkbox v-model="danmu" label="自动下载弹幕" @change="changeDanmu" />
    <el-button size="small" style="position: absolute; right: 30px;" @click="deleteLive">
      删除主播
    </el-button>
  </div>
</template>

<script>
export default {
  props: {
    config: {
      type: Object,
      default: null
    },
    stopRec: {
      type: Function,
      default: null
    }
  },
  data () {
    return {
      notify: this.config.Notify.NotifyOn,
      record: this.config.Record,
      danmu: this.config.Danmu
    }
  },
  watch: {
    config (val, oldVal) {
      this.notify = this.config.Notify.NotifyOn
      this.record = this.config.Record
      this.danmu = this.config.Danmu
    }
  },
  methods: {
    async changeNotify (checked) {
      let result = ''
      if (checked) {
        result = await fetch('http://127.0.0.1:51880/addnotify/' + this.config.UID)
          .then(resp => resp.json())
          .catch(e => console.error(e))
      } else {
        result = await fetch('http://127.0.0.1:51880/delnotify/' + this.config.UID)
          .then(resp => resp.json())
          .catch(e => console.error(e))
      }
      if (result !== true) {
        console.error(`changeNotify返回错误：${result}`)
      }
    },
    async changeRecord (checked) {
      let result = ''
      if (checked) {
        result = await fetch('http://127.0.0.1:51880/addrecord/' + this.config.UID)
          .then(resp => resp.json())
          .catch(e => console.error(e))
      } else {
        result = await fetch('http://127.0.0.1:51880/delrecord/' + this.config.UID)
          .then(resp => resp.json())
          .catch(e => console.error(e))
      }
      if (result !== true) {
        console.error(`changeRecord返回错误：${result}`)
      }
    },
    async changeDanmu (checked) {
      let result = ''
      if (checked) {
        result = await fetch('http://127.0.0.1:51880/adddanmu/' + this.config.UID)
          .then(resp => resp.json())
          .catch(e => console.error(e))
      } else {
        result = await fetch('http://127.0.0.1:51880/deldanmu/' + this.config.UID)
          .then(resp => resp.json())
          .catch(e => console.error(e))
      }
      if (result !== true) {
        console.error(`changeDanmu返回错误：${result}`)
      }
    },
    async deleteLive () {
      if (this.config.isRecord) {
        this.stopRec(this.config.UID)
      }
      const result = await fetch('http://127.0.0.1:51880/delconfig/' + this.config.UID)
        .then(resp => resp.json())
        .catch(e => console.error(e))
      if (result !== true) {
        console.error(`deleteLive返回错误：${result}`)
      }
    }
  }
}
</script>
