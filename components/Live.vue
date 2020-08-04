<template>
  <el-collapse accordion>
    <el-collapse-item
      v-for="live in lives"
      :key="live.UID"
    >
      <template slot="title">
        <span :style="live.isLive ? 'color: black' : 'color:grey'">
          {{ live.Name }}（{{ live.UID }}）
        </span>
        <span v-if="live.isLive">：</span>
        <el-link v-if="live.isLive" type="primary" :href="live.URL" target="_blank">
          {{ live.Title }}
        </el-link>
        <span v-if="live.isRecord" style="color: red">（正在录播）</span>
        <span v-if="live.isRecord" style="position: absolute; right: 30px;">
          <el-popconfirm
            icon="el-icon-info"
            icon-color="red"
            :title="'确定停止 ' + live.Name + '（' + live.UID + '） 的录播？'"
            @onConfirm="stopRecord(live.UID)"
          >
            <el-button slot="reference" size="small">
              停止录播
            </el-button>
          </el-popconfirm>
        </span>
        <span v-else-if="live.isLive" style="position: absolute; right: 30px;">
          <el-button size="small" @click="startRecord(live.UID)">
            启动录播
          </el-button>
        </span>
      </template>
      <LiveConfig :config="live" :stop-rec="stopRecord" />
    </el-collapse-item>
  </el-collapse>
</template>

<script>
export default {
  data () {
    return {
      lives: [],
      timer: ''
    }
  },
  created () {
    this.getLive()
    this.timer = setInterval(() => {
      this.getLive()
    }, 10000)
  },
  beforeDestroy () {
    this.cancelTimer()
  },
  methods: {
    async getLive () {
      let l = await fetch('http://127.0.0.1:51880/liststreamer')
        .then(resp => resp.json())
        .catch(e => console.error(e)) || []
      const living = await fetch('http://127.0.0.1:51880/listlive')
        .then(resp => resp.json())
        .catch(e => console.error(e)) || []
      const recording = await fetch('http://127.0.0.1:51880/listrecord')
        .then(resp => resp.json())
        .catch(e => console.error(e)) || []
      l = l.map(function (live) {
        living.map(function (v) {
          if (v.UID === live.UID) {
            live.isLive = true
            live.Title = v.Title
            live.URL = v.URL
          }
        })
        live.isRecord = recording.filter(v => v.UID === live.UID).length > 0
        return live
      })
      // 根据是否在直播和uid排序
      l.sort(function (a, b) {
        if (a.isLive && b.isLive) {
          return a.UID < b.UID ? -1 : 1
        }
        if (!a.isLive && !b.isLive) {
          return a.UID < b.UID ? -1 : 1
        }
        return a.isLive && !b.isLive ? -1 : 1
      })
      this.lives = l
    },
    cancelTimer () {
      clearInterval(this.timer)
    },
    async startRecord (uid) {
      const result = await fetch('http://127.0.0.1:51880/startrecdan/' + uid)
        .then(resp => resp.json())
        .catch(e => console.error(e))
      if (result !== true) {
        console.error(`startRecord返回错误：${result}`)
      }
    },
    async stopRecord (uid) {
      this.stopDialog = false
      const result = await fetch('http://127.0.0.1:51880/stoprecord/' + uid)
        .then(resp => resp.json())
        .catch(e => console.error(e))
      if (result !== true) {
        console.error(`stopRecord返回错误：${result}`)
      }
    }
  }
}
</script>
