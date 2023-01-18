<template>
  <div>
    <h3>Nova Poshta</h3>
    <p>Введіть більше 4 символів</p>
    <input type="text" @input="displayCities" v-model="city" placeholder="Місто">
    <select v-if="cities.length > 0 && city.length > 4" v-model="selectedCity" @change="selectCity">
      <option v-for="item in cities" :value="item.Ref">{{ item.Present }}</option>
    </select>

    <hr>

    <input type="text" @input="displayWarehouses" v-model="warehouseId" placeholder="№_ відділення">
   <select v-if="warehouses.length > 0 && warehouseId.length > 0" v-model="selectedWarehouse" @change="selectWarehouse">
      <option v-for="item in warehouses" :value="item.SiteKey">{{ item.Description }}</option>
    </select>
    
  </div>
</template>

<script>
import { myApi } from '../api';
export default {
  name:"App",
  data() {
    return {
      currentArea:"",
      cities:[],
      city:'',
      cityRef:'',
      warehouses:[],
      warehouseRef:'',
      warehouseId:'',
    };
  },
  methods:{
    displayCities(){
      if(this.city.length>3){
        myApi.getRequest("Address","searchSettlements",{
          CityName : this.city,
          Limit : "10",
          Page : "1"
        })
        .then((res)=>{
          const dataObj = res.data["data"]
          this.cities = dataObj[0].Addresses
          console.log(dataObj)
        })
      }
    },
    setCityValue(){
      this.city = this.cities.find(city => city.Ref === this.cityRef).Present
    },
    displayWarehouses(){
      myApi.getRequest("Address","getWarehouses",{
      SettlementRef : this.cityRef,
      WarehouseId:this.warehouseId
      })
      .then((res)=>{
        this.warehouses = res.data.data
      })
      .catch(e=>console.error(e));
    },
    setWarehouseValue(){
      this.warehouseId = this.warehouses.find(warehouse => warehouse.SiteKey === this.warehouseRef).Description
    }
  }
}
</script>

