# 实现自定义组件 from:

**分析实现 from 表单要实现三个组件：**

-   myInput:
-   myInputItem
-   myFrom

## 1.myInput 要实现的功能：

-   ①.双向绑定：即：@input 事件，:value
-   ②.要向父组件配发校验消息的事件

## 2.myInputItem 要实现的功能：

-   ①.给 myInput 预留插槽：-slot
-   ②.能够展示 label 和校验信息
-   ③.能够进行校验

## 3.myFrom 要实现的功能：

-   ①.给 myInputItem 预留插槽
-   ②.设置数据和校验规则
-   ③.全局校验

## 4.代码分析：

```html
<MyFrom :model="model" :rules="rules" ref="myFrom">
	<MyInputItem label="用户名" prop="username">
		<MyInput v-model="model.user"></MyInput>
	</MyInputItem>
	<MyInputItem label="用户密码" prop="password">
		<MyInput v-model="model.password"></MyInput>
	</MyInputItem>
</MyFrom>
```

### 4.1 里层 MyInput 组建的实现：

**组件：**

```html
<MyInput v-model="model.password"></MyInput>
```

**实现：**
主要功能：

-   ①.双向绑定：即：实现@input 事件，绑定 value 属性[父组件 v-model 与@input 事件中配发 input 并发送绑定的 value 的值，与其绑定]
-   ②.要向父组件配发校验消息的事件

```html
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
```

### 4.2 里层 MyInputItem 组建的实现：

主要功能：

-   ①.给 myInput 预留插槽：-slot
-   ②.能够展示 label 和校验信息
    通过传值 label,prop 属性值，在子组件中进行操作[作为元素渲染，校验时字段的获取等]
-   ③.能够进行校验
    利用 async-validator 插件进行校验
    **组件：**

```html
<MyInputItem label="用户密码" prop="password">
	<MyInput v-model="model.password"></MyInput>
</MyInputItem>
```

**实现：**

```html
<template>
	<div>
		<label v-if="label">{{label}}</label>
		<slot></slot>
		<p v-if="errMsg">{{errMsg}}</p>
	</div>
</template>

<script>
	import Schema from 'async-validator';

	export default {
		name: 'myInputItem',
		inject: ['myFrom'],
		data() {
			return {
				errMsg: '',
			};
		},
		props: {
			label: {
				type: String,
				default: '',
			},
			prop: {
				type: String,
				default: '',
			},
		},
		mounted() {
			// 监听子组件配发的校验事件去执行：
			this.$on('validate', () => {
				this.validate();
			});
		},
		methods: {
			validate() {
				// 执行组件校验：
				// 1.获取校验规则
				const rules = this.myFrom.rules[this.prop];
				// 2.获取校验信息
				const value = this.myFrom.model[this.prop];
				// 3.执行校验
				const schema = new Schema({ [this.prop]: rules });

				return schema.validate({ [this.prop]: value }, (errors) => {
					if (errors) {
						//有错：
						this.errMsg = errors[0].message;
					} else {
						this.errMsg = '';
					}
				});
				// console.log(schema);
			},
		},
	};
</script>

<style></style>
```

### 4.3 里层 MyFrom 组建的实现：

-   ①.给 myInputItem 预留插槽[显示子组件]
-   ②.设置数据和校验规则[绑定属性，model,rules,传输到子组件中用于校验]
-   ③.全局校验

**组件：**

```html
<MyFrom :model="model" :rules="rules" ref="myFrom">
	<MyInputItem label="用户名" prop="username">
		<MyInput v-model="model.user"></MyInput>
	</MyInputItem>
	<MyInputItem label="用户密码" prop="password">
		<MyInput v-model="model.password"></MyInput>
	</MyInputItem>
	<MyInputItem>
		<button @click="handleLogin">登录</button>
	</MyInputItem>
</MyFrom>
```

**实现：**

```html
<template>
	<div>
		<slot></slot>
	</div>
</template>

<script>
	export default {
		name: 'myFrom',
		provide() {
			return {
				myFrom: this,
			};
		},
		props: {
			model: {
				type: Object,
				require: true,
			},
			rules: {
				type: Object,
			},
		},
		methods: {
			//全局校验
			validate(cb) {
				const task = this.$children
					.filter((item) => item.prop)
					.map((item) => item.validate());
				Promise.all(task)
					.then(() => cb(true))
					.then(() => cb(false));
			},
		},
	};
</script>

<style scoped></style>
```
