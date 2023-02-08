<template>
	<div class="test">{{ test }}</div>

	<button @click="changeTest"></button>
</template>

<script setup>
import { ref } from 'vue';
const testVar = ref(true);
let test = 'ssss';

const changeTest = () => {
	testVar.value = !testVar.value;
	test = 'ddd';

	console.log(test);

	document.querySelector('.test').innerHTML(test);
};
</script>
