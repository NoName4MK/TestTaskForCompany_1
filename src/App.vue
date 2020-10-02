<template>
  <div id="app" class="aplication">
    <div class="header-aplication">
        <p>Тестовое задание</p>
    </div>
    <div>
		<div v-if="isLoadingDate"  >
			<div v-if="isLoadDate">
				<PageUser v-if="arraySortData.length!=0" :arrayData="arraySortData" @emit-table="itemForFiltrModal"/> 
				<span v-else> Данных нет </span>
			</div>
			<div v-else class="loader"> </div>

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
				isLoadDate:Boolean(),
				isLoadingDate:Boolean(),
				isError:Boolean(),
				isShowModal: Boolean(),
				selectElement: Object(),				
				arrayData:Array,
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

<style lang="scss">

.aplication {
	width: 100%;
    min-width: 600px;
	font-size: 1.1em;

	.button {
		border: none;
		outline: none;
		padding: 10px 16px;
		background-color: #f1f1f1;
		cursor: pointer;
		font-size: 1.1em;

		&:hover {
			background-color: #ddd;
		}

		&.active {
			background-color: #666;
			color: white;
		}
	}
}



.loader {
  border: 16px solid #f3f3f3;
  border-radius: 50%;
  border-top: 16px solid #3498db;
  width: 120px;
  height: 120px;
  animation: spin 2s linear infinite;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

</style>
