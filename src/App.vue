<template>
	<div>
		<h1>Dat File Converter</h1>
		<input type="file" @change="onSelectedFile" />
		<pre>
			{{ rawXML }}
		</pre
		>
	</div>
</template>

<script setup>
// 1. get the file from input
// 2. console log filename
import { onMounted, ref } from 'vue';
const rawXML = ref('');
const onSelectedFile = (e) => {
	console.log(e.target.files[0]);
};

onMounted(() => {
	fetchXML();
});

const fetchXML = async () => {
	const xml = await fetch('src/assets/Book.xml');
	const data = await xml.text();
	console.log({ data });

	let _rawXML = xmlValue(data);
	console.log(_rawXML);
	// console.log();

	const _books = _rawXML.getElementsByTagName('book');

	console.log(typeof _books);

	let books = [];
	for (let item of _books) {
		const _item = {
			author: item.children[0].innerHTML,
			title: item.children[2].innerHTML
		};

		books.push(_item);
	}

	console.log(books);
};

const xmlValue = (data) => {
	let parser = new DOMParser(),
		xmlDoc = parser.parseFromString(data, 'text/xml');

	return xmlDoc;
};
</script>
