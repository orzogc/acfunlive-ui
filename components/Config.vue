<template>
  <div>
    <el-button style="margin-left: 30px; margin-top: 30px;" @click="liveDialog = true">
      订阅主播
    </el-button>
    <LiveConfigDialog :show-dialog="liveDialog" @hideDialog="liveDialog = false" />
    <el-divider />
  </div>
</template>

<script>
export default {
  data () {
    return {
      liveDialog: false
    }
  }
}
</script>
