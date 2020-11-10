<template>
  <div style="position: relative; top: 8px;">
    <el-checkbox v-model="notify" label="开播提醒" @change="changeNotify" />
    <el-checkbox v-model="record" label="自动录播" @change="changeRecord" />
    <el-checkbox v-model="danmu" label="自动下载弹幕" @change="changeDanmu" />
    <el-popconfirm
      icon="el-icon-info"
      icon-color="red"
      :title="'确定删除 ' + config.name + '（' + config.uid + '） 的设置？'"
      @onConfirm="deleteLive"
    >
      <el-button slot="reference" size="small" style="position: absolute; right: 30px;">
        删除主播
      </el-button>
    </el-popconfirm>
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
      notify: this.config.notify.notifyOn,
      record: this.config.record,
      danmu: this.config.danmu
    }
  },
  watch: {
    config (val, oldVal) {
      this.notify = this.config.notify.notifyOn
      this.record = this.config.record
      this.danmu = this.config.danmu
    }
  },
  methods: {
    async changeNotify (checked) {
      let result = ''
      if (checked) {
        result = await fetch('http://localhost:51880/addnotifyon/' + this.config.uid)
          .then(resp => resp.json())
          .catch(e => console.error(e))
      } else {
        result = await fetch('http://localhost:51880/delnotifyon/' + this.config.uid)
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
        result = await fetch('http://localhost:51880/addrecord/' + this.config.uid)
          .then(resp => resp.json())
          .catch(e => console.error(e))
      } else {
        result = await fetch('http://localhost:51880/delrecord/' + this.config.uid)
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
        result = await fetch('http://localhost:51880/adddanmu/' + this.config.uid)
          .then(resp => resp.json())
          .catch(e => console.error(e))
      } else {
        result = await fetch('http://localhost:51880/deldanmu/' + this.config.uid)
          .then(resp => resp.json())
          .catch(e => console.error(e))
      }
      if (result !== true) {
        console.error(`changeDanmu返回错误：${result}`)
      }
    },
    async deleteLive () {
      if (this.config.isRecord) {
        this.stopRec(this.config.uid)
      }
      const result = await fetch('http://localhost:51880/delconfig/' + this.config.uid)
        .then(resp => resp.json())
        .catch(e => console.error(e))
      if (result !== true) {
        console.error(`deleteLive返回错误：${result}`)
      }
    }
  }
}
</script>
