<template>
  <el-tabs value="liveStatus" tab-position="left">
    <el-tab-pane name="liveStatus">
      <template slot="label">
        <span style="font-size: 24px">直播状态</span>
      </template>
      <Live />
    </el-tab-pane>
    <el-tab-pane name="config">
      <template slot="label">
        <span style="font-size: 24px">设置</span>
      </template>
      <Config />
    </el-tab-pane>
  </el-tabs>
</template>
