<template>
	<section>
		<div class="container">
			<div class="search">
				<input 
					type="text"  
					class="search-input"
					placeholder="Строка для поиска" 
					v-model="inputSearchValue"
				>
				<button type="button" @click="searchValue=inputSearchValue" class="button">Найти</button>
				<button	type="button" @click="searchValue=null" class="button">Очистить</button>
			</div>

			<div v-if="getArray.length!=0" class="table">
				<table class="table">
					<tr class="header-table">
						<th v-for="(value,name, index) in getArray[0]" :key="index">
							<div class="header" @click="setSort(name)">
								<span class="header-name"><p>{{String(name).toUpperCase()}}</p></span>
								<span class="header-icons">
									<i class="icons-swap material-icons">swap_vert</i>
									<template v-if="elSort.key==name">
										<i v-if="elSort.value" class="icons-arrow material-icons"> arrow_circle_down</i>
										<i v-else class="icons-arrow material-icons"> arrow_circle_up </i>
									</template> 
								</span>								
							</div>							
						</th>
					</tr>

					<tr class="item" v-for="(item, index) in getArray" :key="index">
						<td 
							v-for="(cols, index) in item" 
							:key="index" 
							@click="$emit('emit-table',item)"
						>
							<span>{{cols}}</span>
						</td>
					</tr>
				</table>

				<div v-if="totalPagePagin!=1" class="pagination">
					<div v-if="totalPagePagin<7">
						<button
							type="button" 
							v-for="(value,index) in totalPagePagin" 
							:key="index" 
							:name="value"  
							@click="pagePagin=value" 
							class="button"
							:class="[value==pagePagin? 'active': '']"
						>{{value}}
						</button>
					</div>

					<div v-else>
						<div v-if="totalPagePagin-pagePagin>totalPagePagin-6">
							<button 
								type="button" 
								v-for="(value,index) in 6" 
								:key="index" 
								:name="value" 
								class="button"
								@click="pagePagin=value"  
								:class="[value==pagePagin? 'active': '']"
							> {{value}} 
							</button>
							<span>...</span>
							<button 
								type="button" 
								:name="totalPagePagin" 
								
								@click="pagePagin=totalPagePagin"  
								class="button"
							>{{totalPagePagin}}
							</button>
						</div>						

						<div v-else-if="totalPagePagin-pagePagin<totalPagePagin-10">
							<button 
								type="button" 
								:name="1" 
								@click="pagePagin=1"  
								class="button"
							> 1 </button>
							<span>...</span>
							<button 
								type="button" 
								v-for="(value,index) in 6" 
								:key="index" 
								:name="totalPagePagin-6+value" 
								@click="pagePagin=totalPagePagin-6+value" 								
								class="button"
								:class="[totalPagePagin-6+value==pagePagin? 'active': '']"
							> {{totalPagePagin-6+value}}
							</button>
						</div>

						<div v-else>
							<input  
								type="button" 
								:name="1" 
								:value="1" 
								@click="pagePagin=1" 
								class="button"
							> 
							<span>...</span>
							<input 
								type="button" 
								v-for="(value,index) in 3" 
								:key="index" 
								:name="pagePagin-2+value" 
								:value="pagePagin-2+value" 
								@click="pagePagin=pagePagin-2+value" 
								class="button"
								:class="[pagePagin-2+value==pagePagin? 'active': '']"
							> 	
							<span>...</span>
							<input  
								type="button" 
								:name="totalPagePagin" 
								:value="totalPagePagin" 
								@click="pagePagin=totalPagePagin" 
								class="button"
							> 
						</div>
					</div>
				</div>
			</div>

			<div v-else>
				<span>По запросу "{{searchValue}}" нет данных.</span>
			</div>
		</div>
	</section>
</template>

<script>


export default {
	name: 'PageUser',
	props:{
		arrayData:{
			type: Array,
			required: true,
			default:()=>[],
		}
	},
	data(){
		return {
			elSort:{
				key:String(),
				value:Boolean(),
			},
			searchValue:String(),
			inputSearchValue: String(),

			countCoolsForPage: 50,
			pagePagin: 1,
			totalPagePagin:1,
		}
	},
	computed :{
		getArray: function () {
	
			let array = [...this.arrayData];
			if(this.searchValue){
				array = array.filter(item => Object.values(item).find(item => item==this.searchValue));			
				this.defaultSettings();
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
		defaultSettings: function(){
			this.pagePagin=1;
			this.elSort.key=null;
			this.elSort.value=false;

		}
	
	}
	
}


</script>
<style lang="scss" scoped>
.search {
	margin: 0.5em;
	padding: 0.5em;
	.search-input {
		font-size: 1em;
		padding: 10px 20px 9px 40px;
		border: 1px solid #ddd;
	}
}


.table, td, th {  
	border: 1px solid #ddd;
	text-align: left;
}

.table {
	border-collapse: collapse;
	width: 100%;

	th {
		min-width: 5em;
	}

  	th, td {
		padding: 0.5em;
	}
	tr.header-table, tr.item:hover{
		background-color: #f1f1f1;
	}

	.header-table{
		.header{
			display: flex;
			text-align: center;
			margin: 0 auto;

			.header-name{
				min-width: 70%;
			}

			.header-icons{
				display: flex;

				.icons-swap{
					margin: auto;
				}
				.icons-arrow{
					margin: auto;
				}
			}

			.icons-swap{
				display: none;
			}

			&:hover{
				.icons-swap{
					display: block;
				}
			}

		}
	}
}


.pagination{
	width: 100%;
	text-align: center;
}

</style>
