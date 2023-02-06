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
const rawXML = ref('rawXML');

const xmlPath = ref('src/assets/');

const onSelectedFile = (e) => {
	console.log(e.target.files[0]);
};

onMounted(async () => {
	const data = await getXMLData();
	convertXMLtoJSON(data);
});

async function getXMLData() {
	const xml = await fetch(xmlPath.value + 'Book.xml');
	const data = await xml.text();

	let parser = new DOMParser(),
		xmlDoc = parser.parseFromString(data, 'text/xml');

	return xmlDoc;
}

function convertXMLtoJSON(xml) {
	let books = [];
	for (let item of xml.getElementsByTagName('book')) {
		const _item = {
			author: item.children[0].innerHTML,
			title: item.children[2].innerHTML
		};

		books.push(_item);
	}

	console.log(books);
}
</script>
