<template>
	<div>
		<input @input="onInput" :value="value" v-bind="$attrs" />
	</div>
</template>

<script>
export default {
	name: 'myInput',
	props: {
		value: {
			type: String,
			default: '',
		},
	},
	methods: {
		onInput(e) {
			// 向父组件配发消息
			this.$emit('input', e.target.value);
			//父组件只需要绑定v-model即可

			// 向父组件配发校验信息：
			this.$parent.$emit('validate');
		},
	},
};
</script>

<style lang="less" scoped></style>
