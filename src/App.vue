<style>
.dat-file__container {
	text-align: left;
}

.dat-file__container pre {
}
</style>

<template>
	<div>
		<h1>Dat File Converter</h1>
		<input type="file" @change="onSelectedFile" />
		<div class="dat-file__container">
			<pre>{{ mapContent }}</pre>
		</div>
		<button @click="downloadFile" v-if="MAPDetails">
			Download DAT File
		</button>
	</div>
</template>

<script setup>
// 1. dat file name = maprpt07022023.dat - Done
// 2. Header  = Mustard Seed System Corp. MAP Report- Done
// 3. Details =
// 4. Footer = End of report - created by Valeed- Done
// 5. Preview Dat File- Done
// 6. Create Download Link - Done
// 7. Add Simple Validation- Done

import { onMounted, ref, computed } from 'vue';
import moment from 'moment';

const datFileContent = ref('datFileContent');
const xmlPath = ref('src/assets/');
const MAPHeader = 'Mustard Seed System Corp. MAP Report';
const MAPFooter = 'End of report - created by Valeed';
const MAPDetails = ref('');

const onSelectedFile = (e) => {
	console.log(e.target.files[0]);
};

onMounted(async () => {
	const data = await getXMLData();
	const arrObjects = convertXMLtoJSON(data);

	createMAPDetails(arrObjects);
});

async function getXMLData() {
	const xml = await fetch(xmlPath.value + 'Book.xml');
	const data = await xml.text();

	console.log(data);

	let parser = new DOMParser(),
		xmlDoc = parser.parseFromString(data, 'text/xml');

	return xmlDoc;
}

const downloadFile = () => {
	const link = document.createElement('a');

	// const content = document.querySelector('textarea').value;

	const file = new Blob([mapContent.value], { type: 'text/plain' });

	link.href = URL.createObjectURL(file);

	const dateNow = convertToReadableDateFormat(new Date());

	link.download = 'maprpt' + dateNow + '.dat';
	link.click();
	URL.revokeObjectURL(link.href);
};

function convertToReadableDateFormat(date) {
	return moment(date).format('DDMMYYYY');
}

function convertXMLtoJSON(xml) {
	let books = [];
	for (let item of xml.getElementsByTagName('book')) {
		const _item = {
			author: item.children[0].innerHTML, // 3
			title: item.children[1].innerHTML, // 2
			genre: item.children[2].innerHTML, // 4
			price: item.children[3].innerHTML, // 5
			publish_date: item.children[4].innerHTML, // 1
			description: item.children[5].innerHTML // 6 (optional)
		};

		books.push(_item);
	}

	return books;
}

const mapContent = computed(() => {
	return `${MAPHeader}
${MAPDetails.value}
${MAPFooter}`;
});

function createMAPDetails(arrObjects) {
	const seperator = ','; // not use this if the separator is dynamic
	let htmlDetails = '';
	arrObjects.forEach((item) => {
		htmlDetails += `
    ${item.publish_date},${item.title},${item.author},${item.genre},${item.price}
    `;
	});

	console.log(htmlDetails);

	MAPDetails.value = htmlDetails;
}
</script>
