<template>
  <div>
    <label v-if="label">{{label}}</label>
    <slot></slot>
    <p v-if="errMsg">{{errMsg}}</p>
  </div>
</template>

<script>
import Schema from "async-validator";

export default {
  name:'myInputItem',
  inject:['myFrom'],
  data() {
    return {
      errMsg: ''
    }
  },
  props: {
    label: {
      type: String,
      default: ''
    },
    prop: {
      type: String,
      default: ''
    }
  },
  mounted() {
    // 监听校验事件：
    this.$on('validate',() => {
      this.validate();
    })
  },
  methods: {
    validate () {
      // 执行组件校验：
      // 1.获取校验规则
      const rules = this.myFrom.rules[this.prop];
      // 2.获取校验信息
      const value = this.myFrom.model[this.prop];
      // 3.执行校验
      const schema = new Schema({[this.prop]:rules});

      return schema.validate({[this.prop]:value},errors=>{
        if(errors) {
          //有错：
          this.errMsg = errors[0].message;
        }else{
          this.errMsg = '';
        }
      })
      // console.log(schema);
    }
  },
};
</script>

<style>
</style>