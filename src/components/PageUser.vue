<template>
	<section>
		<div>
			<div >
				<input type="text"  placeholder="Строка для поиска" v-model="inputSearchValue">
				<input type="button" value="Найти" @click="searchValue=inputSearchValue">
				<input type="button"  value="Очистить" @click="searchValue=null">
			</div>
			<table >
				<tr>
					<th  v-for="(value,name, index) in getArray[0]" :key="index">
						<div @click="setSort(name)">
							<span class="header-table"><p>{{name}}</p>
								<i class="icons-display material-icons">swap_vert</i>
								<span v-if="elSort.key==name">
									<i v-if="elSort.value" class="material-icons"> arrow_circle_down</i>
									<i v-else class="material-icons"> arrow_circle_up </i>
								</span> 
							</span>
							
						</div>
						
					</th>
				</tr>
				<tr v-for="(item, index) in getArray" :key="index">
					<td v-for="(cols, index) in item" :key="index" @click="$emit('emit-table',item) ">
						<span>{{cols}}</span>
					</td>
				</tr>
			</table>
			<div v-if="totalPagePagin!=1">
				<div v-if="totalPagePagin<7">
					<input v-for="(value,index) in totalPagePagin" :key="index" type="button" :name="value" :value="value" @click="pagePagin=value" :class="[value==pagePagin? 'activeButton': 'Button']"> 			
				</div>
				<div v-else>
					<div v-if="totalPagePagin-pagePagin>totalPagePagin-6">
						<input v-for="(value,index) in 6" :key="index" type="button" :name="value" :value="value" @click="pagePagin=value"  :class="[value==pagePagin? 'activeButton': 'Button']"> 
						<span>...</span>
						<input type="button" :name="totalPagePagin" :value="totalPagePagin" @click="pagePagin=totalPagePagin"  class="Button"> 
					</div>
					
					<div v-else-if="totalPagePagin-pagePagin<totalPagePagin-10">
						<input type="button" :name="1" :value="1" @click="pagePagin=1"  class="Button"> 
						<span>...</span>
						<input v-for="(value,index) in 6" :key="index" type="button" :name="totalPagePagin-6+value" :value="totalPagePagin-6+value"	
						@click="pagePagin=totalPagePagin-6+value" :class="[totalPagePagin-6+value==pagePagin? 'activeButton': 'Button']"> 	
					</div>
					<div v-else>
						<input  type="button" :name="1" :value="1" @click="pagePagin=1" class="Button"> 
						<span>...</span>
						<input v-for="(value,index) in 3" :key="index" type="button" :name="pagePagin-2+value" :value="pagePagin-2+value" @click="pagePagin=pagePagin-2+value" :class="[pagePagin-2+value==pagePagin? 'activeButton': 'Button']"> 	
						<span>...</span>
						<input  type="button" :name="totalPagePagin" :value="totalPagePagin" @click="pagePagin=totalPagePagin" class="Button"> 

					</div>
				</div>
			</div>
		</div>
	</section>
</template>

<script>


export default {
	name: 'PageUser',
	props:['arrayData'],
	data(){
		return {

			elSort:{
				key:null,
				value:null
			},
			searchValue:null,
			inputSearchValue: null,
			selectElement:null,

			countCoolsForPage: 50,
			pagePagin: 1,
			totalPagePagin:1,
		}
	},
	computed :{
		getArray: function () {
	
			let array = this.arrayData;
			if(this.searchValue){
				array = array.filter(item => Object.values(item).find(item => item==this.searchValue));				
			}

			this.setPagination(array.length);

			if(this.elSort.key){
				array.sort((a,b) => a[this.elSort.key]>b[this.elSort.key]?this.elSort.value?1:-1:this.elSort.value?-1:1)
			}

			const endElement = this.pagePagin*this.countCoolsForPage;
			const startElement = endElement-this.countCoolsForPage;

			return array.slice(startElement, endElement)
		},


	},
	methods :{		
		
		setSort: function(el){
			if (this.elSort.key == el){
				this.elSort.value =! this.elSort.value;
			}
			else{
				this.elSort.key = el;
				this.elSort.value = true;
			}		
		},
		setPagination: function(arrayLength){
			this.totalPagePagin = 1
			if (arrayLength-this.countCoolsForPage>0){
				const mod = arrayLength%this.countCoolsForPage
				this.totalPagePagin = mod != 0? ((arrayLength-mod)/this.countCoolsForPage)+1:arrayLength/this.countCoolsForPage;
			}
		},
	
	}
	
}


</script>

<style scoped>
.activeButton {
	background-color: coral;
}
.header-table .icons-display{ 
	display: none;
}
.header-table:hover .icons-display{
	display: block;
}
</style>
