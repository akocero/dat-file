<style>
h1 {
	font-size: clamp(1.25rem, 1.25rem + 0.625vw, 2rem);
}

.dat-file__container {
	text-align: left;
}

.dat-file__container pre {
	border-radius: 12px;
	background-color: #333;
	padding: clamp(0.75rem, 0.75rem + 1.875vw, 3rem);
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
import Tutorial from './components/Tutorial.vue';

const datFileContent = ref('datFileContent');
const xmlPath = ref('src/assets/');
const xmlFile = 'Book.xml';
const MAPHeader = 'Mustard Seed System Corp. MAP Report';
const MAPFooter = 'End of report - created by Valeed';
const MAPDetails = ref('');

const onSelectedFile = (e) => {
	var file = e.target.files[0],
		reader = new FileReader();

	waitForTextReadComplete(reader);
	reader.readAsText(file);
};

onMounted(async () => {
	// const data = await getXMLData();
	// const arrObjects = convertXMLtoJSON(data);
	// createMAPDetails(arrObjects);
});

// async function getXMLData() {
// 	const xml = await fetch(xmlPath.value + xmlFile);
// 	const data = await xml.text();

// 	let parser = new DOMParser(),
// 		xmlDoc = parser.parseFromString(data, 'text/xml');

// 	return xmlDoc;
// }

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

function convertMAPXMLtoJSON(xml) {
	let books = [];

	console.log(xml.getElementsByTagName('Section'));
	let counter = 0;
	const _arryOfHtml = xml.getElementsByTagName('Section');
	for (const item of _arryOfHtml) {
		console.log(_arryOfHtml.length);
		if (counter != 0 && counter != _arryOfHtml.length - 1) {
			const _item = {
				author: item.children[0].children[1].innerHTML, // 3
				title: item.children[1].children[1].innerHTML, // 2
				genre: item.children[2].children[1].innerHTML, // 4
				price: item.children[3].children[1].innerHTML, // 5
				publish_date: item.children[4].children[1].innerHTML, // 1
				description: item.children[5].children[1].innerHTML // 6 (optional)
			};

			books.push(_item);
		}

		counter++;
	}

	return books;
}

function convertCurrencyToInteger(currency) {
	return parseFloat(currency.replace('$', '').replace(',', '')).toFixed(2);
}

function convertToProperFullName(fullName) {
	const arr = fullName.split(', ');
	return arr[1] + ' ' + arr[0];
}

const mapContent = computed(() => {
	return `${MAPHeader}\n${MAPDetails.value}${MAPFooter}`;
});

function createMAPDetails(arrObjects) {
	const seperator = ','; // not use this if the separator is dynamic
	let htmlDetails = '';
	arrObjects.forEach((item) => {
		htmlDetails += `${item.publish_date},${
			item.title
		},${convertToProperFullName(item.author)},${
			item.genre
		},${convertCurrencyToInteger(item.price)}\n`;
	});

	// console.log(htmlDetails);

	MAPDetails.value = htmlDetails;
}

function parseTextAsXml(text) {
	var parser = new DOMParser(),
		xmlDom = parser.parseFromString(text, 'text/xml');
	// console.log(xmlDom);
	// console.log(
	// 	xmlDom.getElementsByTagName('Section')[1].children[0].children[1]
	// 		.innerHTML
	// );

	console.log(convertMAPXMLtoJSON(xmlDom));

	// const arrObjects = convertXMLtoJSON(xmlDom);
	// createMAPDetails(arrObjects);

	//now, extract items from xmlDom and assign to appropriate text input fields
}

function waitForTextReadComplete(reader) {
	reader.onloadend = function (event) {
		var text = event.target.result;

		parseTextAsXml(text);
	};
}
</script>
