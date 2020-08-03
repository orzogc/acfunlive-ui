<template>
  <div>
    <el-checkbox v-model="notify" label="开播提醒" @change="changeNotify" />
    <el-checkbox v-model="record" label="自动录播" @change="changeRecord" />
    <el-checkbox v-model="danmu" label="自动下载弹幕" @change="changeDanmu" />
    <el-button
      size="small"
      style="position: absolute; right: 30px;"
      @click="deleteDialog = true"
    >
      删除主播
    </el-button>
    <el-dialog :visible.sync="deleteDialog" width="20%">
      确定删除 {{ config.Name }}（{{ config.UID }}） 的设置？
      <span slot="footer">
        <el-button type="primary" @click="deleteLive">确定</el-button>
      </span>
    </el-dialog>
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
      danmu: this.config.Danmu,
      deleteDialog: false
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
      this.deleteDialog = false
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
