<template>
  <div>
    <slot></slot>
  </div>
</template>

<script>
export default {
  name: "myFrom",
  provide() {
    return {
      myFrom: this
    }
  },
  props: {
    model: {
      type: Object,
      require: true
    },
    rules: {
      type: Object,
    },
  },
  methods: {
    //全局校验
    validate(cb) {
      const task = this.$children.filter(item=>item.prop).map(item=>item.validate());
      Promise.all(task).then(()=> cb(true)).then(()=>cb(false));
    }
  },
}
</script>

<style scoped>

</style>