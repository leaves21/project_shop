<template>
  <div>
    <el-button type="primary" size="default" :icon="Share">Click!</el-button>
    <el-button type="success" size="medium" :icon="Edit"></el-button>
    <el-pagination layout="total, sizes, prev, pager, next, jumper" :total="400" />
  </div>
</template>

<script setup lang="ts">
import { Share, Edit } from '@element-plus/icons-vue'
</script>

<style scoped></style>
