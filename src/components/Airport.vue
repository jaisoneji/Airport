<template>
  <div class="container">
    <div class="header">
      <h1 class="filter__header">Filter<span class="airport__header">airports</span></h1>
       <i class="fas fa-th-large"></i> 
    </div>
    <!-- The filters__search div contains search and Types component -->
    <div class="filters__search">
      <div class="type">
        <h3 class="type__label">Type</h3>
        <div class="filter__checkboxes">
          <label >
            <input   class="type__checkbox" type="checkbox" name="Small" value="small" v-model="checkedTypes">
            Small
          </label>
          <label >
            <input   class="type__checkbox" type="checkbox" value="medium" name="Medium" v-model="checkedTypes">
            Medium
          </label>
          <label >
            <input  class="type__checkbox" type="checkbox" value="large"  name="Large" v-model="checkedTypes">
            Large
          </label>
          <label >
            <input  class="type__checkbox" type="checkbox" value="heliport" name="Heliport" v-model="checkedTypes">
            Heliport
          </label>
          <label >
            <input  class="type__checkbox" type="checkbox" value="closed" name="Closed" v-model="checkedTypes">
            Closed
          </label>
          <label >
            <input  class="type__checkbox" type="checkbox" value="In your favourites" name="In your favourites" v-model="checkedTypes">
            In your favourites
          </label>
        </div>
      </div>
      <div class="search">
        <h3 class="search__label">Search</h3>
        <input v-model="searchQuery" class="search__input" type="text" name="search" id="search">
      </div>
    </div>
    <!-- ---Airport Data Tables---- -->
    <div class="airport__data__table">
      <table>
        <th class="col1">Name</th>
        <th>ICAO</th>
        <th>IATA</th>
        <th>Elev.</th>
        <th>Lat.</th>
        <th>Long.</th>
        <th>Type</th>
        <tr class="rows" v-for="(data) in filterArray" :key="data.id">
          <td class="col1">{{data.name}}</td>
          <td>{{data.icao}}</td>
          <td>{{data.iata}}</td>
          <td>{{data.elevation}}</td>
          <td>{{data.latitude}}</td>
          <td>{{data.longitude}}</td>
          <td>{{data.type}}</td>
        </tr>
      </table>
    </div>
    <!-- --------Paggination Footer--- -->
    <div class="pagination__footer">
      <button @click.prevent="movePrev"><i class="fas fa-arrow-left"></i></button>
      <h6>Showing<span style="padding:0 8px;font-weight:bold">{{parseInt(this.paggination_start_counter)+1}} - {{this.paggination_end_counter}}</span >of<span style="padding:0 8px;font-weight:bold">{{this.filteredDataLength}}</span></h6>
      <button @click.prevent="moveNext"><i class="fas fa-arrow-right"></i></button>
    </div>
  </div>
</template>

<script>
import AirportJson from '../data/airports.json';
export default {
  name: 'Airport',
  computed:{
    // The Below computed is used for reactivity purpose
    filterArray(){
      return this.filterArrayOnTypeAndSearch()
    },
    // The below returns filtered data array length
    filteredDataLength(){
      return this.filteredDataArrayLength
    },
    
  },
  mounted(){
    this.AirportData.push(...AirportJson)
    this.filteredDataArrayLength = this.AirportData.length
    console.log(this.AirportData)
    this.filteredData.splice(0,this.filteredData.length)
    for(let i=this.paggination_start_counter;i<this.paggination_end_counter;i++){
      this.filteredData.push(this.AirportData[i])
    }
    console.log(this.filteredData)
    if((localStorage.getItem('start') + 1) > 1){
      this.paggination_start_counter = localStorage.getItem('start')
      this.paggination_end_counter = localStorage.getItem('end')
    }
    else{
      localStorage.setItem('start',0)
      localStorage.setItem('end',5)
    }
    
  },
  data(){
    return{
      paggination_start_counter: 0 ,
      paggination_end_counter: 5,
      checkedTypes:[],
      AirportData : [],
      filteredData:[],
      filteredDataArrayLength:0,
      searchQuery:''
    }
  },
  methods:{
    filterArrayOnTypeAndSearch(){
      
      // The below code will check for any filters selected
      // If none than it returns original Airports Data containing every records with pagination
      if (!this.checkedTypes.length && !this.searchQuery.length){
        this.filteredData.splice(0,this.filteredData.length)
        this.updateFilteredArray(this.paggination_start_counter,this.paggination_end_counter)
        this.filteredDataArrayLength = this.AirportData.length
        return this.filteredData
      }
      // The below code is for search query
      if(this.searchQuery.length){
        return this.searchData()
      }
      // -----The below code Filters Main Data array with respect to filters selected----
      // this.checkedTypes stores filters checked
      var tempArray = []
      // var q = this.searchQuery
      var len = this.AirportData.filter(element =>this.checkedTypes.includes(element.type)).length
      this.filteredDataArrayLength = len
      for(let i=this.paggination_start_counter;i<this.paggination_end_counter;i++){
        console.log(this.searchQuery)
        tempArray.push(this.AirportData.filter(element =>this.checkedTypes.includes(element.type))[i])                                     
      }
                                              
      this.filteredData.splice(0,this.filteredData.length)
      this.filteredData.push(...tempArray)
      return tempArray
    },
    searchData(){
        var tempSearchArray = []
        var leng = this.AirportData.filter(element =>element.name.toLowerCase().includes(this.searchQuery.toLowerCase(),0)).length
        this.filteredDataArrayLength = leng
        for(let i=this.paggination_start_counter;i<this.paggination_end_counter;i++){
          console.log(this.searchQuery)
          tempSearchArray.push(this.AirportData.filter(element =>element.name.toLowerCase().includes(this.searchQuery.toLowerCase(),0))[i])                                     
        }
        this.filteredData.splice(0,this.filteredData.length)
        this.filteredData.push(...tempSearchArray)
        this.filteredDataArrayLength = leng
        return tempSearchArray
    },
    moveNext(){
      this.paggination_start_counter = this.paggination_end_counter
      this.paggination_end_counter += 5
      localStorage.setItem('start',this.paggination_start_counter)
      localStorage.setItem('end',this.paggination_end_counter)
      this.filteredData.splice(0,this.filteredData.length)
      this.updateFilteredArray(this.paggination_start_counter,this.paggination_end_counter)
    },
    movePrev(){
      if(this.paggination_start_counter == 0){
        return
      }
      this.paggination_start_counter = this.paggination_start_counter - 5
      this.paggination_end_counter -= 5
      localStorage.setItem('start',this.paggination_start_counter)
      localStorage.setItem('end',this.paggination_end_counter)
      this.filteredData.splice(0,this.filteredData.length)
      this.updateFilteredArray(this.paggination_start_counter,this.paggination_end_counter)
    },
    updateFilteredArray(start,end){
      for(let i=start;i<end;i++){
        this.filteredData.push(this.AirportData[i])
        console.log(this.filteredData)
      }
    }
    
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.header{
  display: flex;
  width:100%;
  align-items: center;
  justify-content: space-between;
}
button{
  border:none;
}
.fas{
  font-size:xx-large!important;
  background-color: none!important;
}
.container{
  width:90%;
  margin:2rem 0;
  background-color: white;
  padding:2rem;
}
.filters__search{
  display: flex;
  
}
.type{
  width:60%
}
.search{
  width:40%
}
.search__input{
  width:100%
}
.filter__header{
  font-size: 4rem;
}
.airport__header{
  margin-left: 1rem;
  opacity: 0.2;
}
.type__label,.search__label{
  font-size: 2rem;
  margin:1rem 0;
  padding:0;
}
.filter__checkboxes label{
  font-size: larger;
}
INPUT[type=checkbox]
{
    border-radius: 2px;
    border:3px solid black;
    appearance: none;
    -webkit-appearance: none;
    -moz-appearance: none;
    width: 17px;
    height: 17px;
    cursor: pointer;
    position: relative;
    top: 5px;
}

INPUT[type=checkbox]:checked
{
    background-color: black;
    background: black url("data:image/gif;base64,R0lGODlhCwAKAIABAP////3cnSH5BAEKAAEALAAAAAALAAoAAAIUjH+AC73WHIsw0UCjglraO20PNhYAOw==") 0.9px 0.9px no-repeat;
}
.search__input{
  height:1.8rem;
  font-size: 1.5rem;
  padding-bottom: 2px;
  border:none;
  border-bottom: 4px solid black;
}
INPUT[type=text]:focus{
  outline:none;
}
.airport__data__table table{
  width:100%;
  margin-top:2rem;
  background-color: white;
  
}
.col1{
  /* word-break: break-all; */
  width:100%;
}
th{
 background-color: #e7e7e7; 
 margin:0;
}
td, th {
  margin:0;
  padding: 1rem 2rem;
  margin:0;
  text-align: left;
  color:black;
  
}
.col1{
  width:30%;
}
tr:nth-child(even){
  background-color: #f4f4f4;
}
.pagination__footer{
  margin-top:1rem;
  display:flex;
  align-items: center;
  justify-content: space-between;
}
.pagination__footer h6{
  font-size: 20px;
  font-weight: normal;
}
.airport__data__table{
  overflow-x:scroll
}
@media only screen and (max-width: 600px) {
  .header i{
    margin:4px 0 0 4px;
    font-size: medium!important;
  }
  .filters__search{
    display: flex;
    flex-direction: column-reverse;
    width:100%;
  }
  .type{
    width:100%
  }
  .search{
    width:100%
  }
  .filter__header{
    font-size: 2.5rem;
  }
  .airport__header{
    margin-left: 4px;
    opacity: 0.2;
  }
}
</style>
