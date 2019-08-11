<template>
  <el-select clearable :value="value" @change="fn" placeholder="请选择">
    <el-option v-for="item in channelOptions" :key="item.id" :label="item.name" :value="item.id"></el-option>
  </el-select>
</template>
<script>
export default {
  name: 'my-channel',
  props: ['value'],
  data() {
    return {
    //   myValue: null,
      channelOptions: []
    };
  },
  created() {
    //获取频道下拉选项数据
    this.getChannelOptions();
  },
  methods: {
    async getChannelOptions() {
      const {
        data: { data }
      } = await this.$http.get("channels");
      this.channelOptions = data.channels;
    },
    fn (val) {
        //数据提交给父组件
        if (val === '')  val = null
        this.$emit('input', val)
    }
  }
};
</script>
<style scoped lang="less"></style>