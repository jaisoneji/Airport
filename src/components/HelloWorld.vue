<template>
  <div class="container">
    <h1 class="filter__header">Filter<span class="airport__header">airports</span></h1>
    <div class="filters__search">
      <div class="type">
        <h3 class="type__label">Type</h3>
        <div class="filter__checkboxes">
          <label >
            <input   class="type__checkbox" type="checkbox" name="Small" value="small" v-model="checkedTypes" id="">
            Small
          </label>
          <label >
            <input   class="type__checkbox" type="checkbox" value="medium" name="Medium" v-model="checkedTypes" id="">
            Medium
          </label>
          <label >
            <input  class="type__checkbox" type="checkbox" value="large"  name="Large" v-model="checkedTypes" id="">
            Large
          </label>
          <label >
            <input  class="type__checkbox" type="checkbox" value="heliport" name="Heliport" v-model="checkedTypes" id="">
            Heliport
          </label>
          <label >
            <input  class="type__checkbox" type="checkbox" value="closed" name="Closed" v-model="checkedTypes" id="">
            Closed
          </label>
          <label >
            <input  class="type__checkbox" type="checkbox" value="In your favourites" name="In your favourites" v-model="checkedTypes" id="">
            In your favourites
          </label>
        </div>
      </div>
      <div class="search">
        <h3 class="search__label">Search</h3>
        <input class="search__input" type="text" name="search" id="search">
      </div>
    </div>
    <!-- ---Tables---- -->
    <div class="airport__data__table">
      <table>
        <th class="col1">Name</th>
        <th>ICAO</th>
        <th>IATA</th>
        <th>Elev.</th>
        <th>Lat.</th>
        <th>Long.</th>
        <th>Type</th>
        <tr class="rows" v-for="data in filterArray" :key="data.id">
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
    <!-- --------Paggination--- -->
    <div class="pagination__footer">
      <button @click.prevent="movePrev"><i class="fas fa-arrow-left"></i></button>
      <h6>Showing<span style="padding:0 8px;font-weight:bold">{{this.paggination_start_counter}}-{{this.paggination_end_counter}}</span >of<span style="padding:0 8px;font-weight:bold">{{this.filteredDataLength}}</span></h6>
      <button @click.prevent="moveNext"><i class="fas fa-arrow-right"></i></button>
    </div>

  </div>
</template>

<script>
import AirportJson from '../data/airports.json';
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  computed:{
    filterArray(){
      // var tempArray = []
      // console.log(this.checkedTypes)
      // if (!this.checkedTypes.length){
      //   for(let i=this.paggination_start_counter;i<this.paggination_end_counter;i++){
      //     this.filteredData.push(this.AirportData[i])
      //   }
      //   return this.filteredData

      // }
     
      // this.AirportData.filter(element =>this.checkedTypes.includes(element.type))
      // for(let i=this.paggination_start_counter;i<this.paggination_end_counter;i++){
      //   tempArray.push(this.AirportData.filter(element =>this.checkedTypes.includes(element.type))[i])
      // }
      return this.paginate()
    },
    filteredDataLength(){
      return this.filteredDataArrayLength
    }
  },
  mounted(){
    this.AirportData.push(...AirportJson)
    this.filteredDataArrayLength = this.AirportData.length
    console.log(this.AirportData)
    for(let i=this.paggination_start_counter;i<this.paggination_end_counter;i++){
      this.filteredData.push(this.AirportData[i])
    }

  },
  data(){
    return{
      paggination_start_counter:0,
      paggination_end_counter:5,
      checkedTypes:[],
      AirportData : [],
      filteredData:[],
      filteredDataArrayLength:0
    }
  },
  methods:{
    paginate(){
      if (!this.checkedTypes.length){
        // this.filteredDataArrayLength = this.AirportData.length
        this.filteredData.splice(0,this.filteredData.length)
        // console.log(this.AirportData)
        this.paggination_start_counter = 0;
        this.paggination_end_counter = 5;
        for(let i=this.paggination_start_counter;i<this.paggination_end_counter;i++){
          this.filteredData.push(this.AirportData[i])
        }
        this.filteredDataArrayLength = this.AirportData.length
        return this.filteredData
      }
      var tempArray = []
      var len = this.AirportData.filter(element =>this.checkedTypes.includes(element.type)).length
      this.filteredDataArrayLength = len
      for(let i=this.paggination_start_counter;i<this.paggination_end_counter;i++){
        tempArray.push(this.AirportData.filter(element =>this.checkedTypes.includes(element.type))[i])
      }
      this.filteredData.splice(0,this.filteredData.length)
      this.filteredData.push(...tempArray)
      return tempArray
    },
    moveNext(){
      this.paggination_start_counter = this.paggination_end_counter
      this.paggination_end_counter += 5
      this.filteredData.splice(0,this.filteredData.length)
      for(let i=this.paggination_start_counter;i<this.paggination_end_counter;i++){
        this.filteredData.push(this.AirportData[i])
      }
    },
    movePrev(){
      if(this.paggination_start_counter == 0){
        return
      }
      this.paggination_start_counter = this.paggination_start_counter - 5
      this.paggination_end_counter -= 5
      this.filteredData.splice(0,this.filteredData.length)
      for(let i=this.paggination_start_counter;i<this.paggination_end_counter;i++){
        this.filteredData.push(this.AirportData[i])
      }
    },
    // updateAirportType(){
    //   this.filterArray
    // }
    
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
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
  
  width:100%;
}
th{
 background-color: #e7e7e7; 
 margin:0;
}
td, th {
  word-wrap:break-word;
  line-break: strict;
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
  margin-top:2rem;
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
  .filters__search{
    display: flex;
    flex-direction: column;
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
