<template>
	<div>
		<h1>Dat File Converter</h1>
		<input type="file" @change="onSelectedFile" />
	</div>
</template>

<script setup>
// 1. get the file from input
// 2. console log filename

const onSelectedFile = (e) => {
	console.log(e.target.files[0]);
};
</script>
