<template>
  <div id="app">
    <div>
        <p>Тестовое задание</p>
    </div>
    <div>
		<div v-if="isLoadingDate"  >
			<div v-if="isLoadDate">
				<PageUser v-if="arraySortData.length!=0" :arrayData="arraySortData" @emit-table="itemForFiltrModal"/> 
				<span v-else> Данных нет </span>
			</div>
			<div v-else> Данные загружаются </div>

		</div>
      	<div v-else-if="isError" > 
			<span>Error, Перезагрузите страницу</span> 
		</div>
		<div v-else > 
			  <input type="button" value="Покозать пользователей" @click="getDate"> 
		</div>
    </div>
	<modalWindow v-if="isShowModal" :selectElement="selectElement" @emit-modal="isShowModal=$event"/>
  </div>
</template>

<script>
import PageUser from './components/PageUser.vue';
import modalWindow from './components/modalWindow.vue';

export default {
	name: 'App',
	components: {
		PageUser,
		modalWindow
	},
	data(){
		return {
				isLoadDate:false,
				isLoadingDate:false,
				isError:false,
				isShowModal: false,
				selectElement: null,				
				arrayData:[],
		}
	},

	computed:{
		arraySortData: function () {
				let array=[];
				if (this.isLoadDate){
					array = this.arrayData.map(items=>{					
						let { fullname, uname, company, email, address}={...items};
						let state = address.state
						return {fullname,uname,company,email, state}
					});
				}
				return array
			},
	},
	methods:{
		getDate : async function(){
			try{
				this.isLoadingDate = true;
				const response = await fetch('http://www.filltext.com/?rows=1000&id={index}&fullname={firstName}~{lastName}&company={business}&email={email}&uname={username}&address={addressObject}')
				const reader  = response.body.getReader();

				let receivedLength = 0; 
				let chunks = [];
				while(true) {
					const {done, value} = await reader.read();
					if (done) {
						break;
					}
					console.log(`Получено ${value.length} байт`)

					chunks.push(value);
					receivedLength += value.length;
				}

				let chunksAll = new Uint8Array(receivedLength); 
				let position = 0;
				for(let chunk of chunks) {
					chunksAll.set(chunk, position); 
					position += chunk.length;
				}

				let result = new TextDecoder("utf-8").decode(chunksAll)
				let content = JSON.parse(result);
				this.arrayData=content;
				this.isLoadDate = true;
 

			}
			catch (e) {
				this.isError = true;
			}

		},
		itemForFiltrModal: function(el){
			let {fullname,email} = {...el};
		 	this.selectElement = {fullname,email};
		 	this.isShowModal = true;
		}
	}
}
</script>

<style>

</style>
