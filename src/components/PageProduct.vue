<template>
    <!-- vue.js form ถ้าไม่กำหนด @submit.prevent จะทำการเปลี่ยนหน้าเมือ submit -->
    <form @submit.prevent="searchProduct()">
        <div class="row">
            <div class="h1 col-md-6 col-sm-12text-danger">สกินที่วางขาย!!!</div>
            <div class="col-md-4 col-sm-6">
                <input type="text" class="form-control" v-model="stext">
            </div>
            <div class="col">
                <button class="btn btn-primary" type="submit" @click="searchProduct()">ค้นหา</button>
            </div>
        </div>
    </form>

    <div class="row">
        <div v-for="(pd,pdId) in products" :key="pdId" class="col-lg-4 col-md-6 col-sm-12">
            <div class="card" style="width: 18rem;">
                <img :src="`http://localhost:3000/img_pd/${pd.pdId}.jpg`" class="card-img-top" alt="">
                <div class="card-body">
                    <h5 class="card-title">{{ pd.pdName }}</h5>
                    <p class="card-text">{{ pd.brand.brandName }} - {{ pd.pdPrice }}</p>
                    <!-- <a href="#" class="btn btn-primary">Go somewhere</a> -->
                    <!-- กำหนด router-link แสดง Component ProductShow -->
                    <!-- จะมีการส่งParameter ไปกับ router-link ด้วยจะต้องใช้ :to และกำหนด params -->
                    <!-- และส่งเป็นลักษณะชุดข้อมูล key:value -->
                    <!-- สามารถส่ง parameter ได้หลายตัวโดยระบุเป็นชุดข้อมูลซ้่อนเข้าไปได้ -->
                    <router-link :to="{name:'ProductShow',params:{pdId:pd.pdId} }"
                        style="text-decoration: none;">
                        <div  class="btn btn-primary">ดูรายละเอียด</div>
                    </router-link>                    
                </div>
            </div>
        </div>
    </div>

</template>

<script>
import axios from 'axios'
export default {
  name: "PageProduct",
  data() {
    return {
      products: [],
      stext:'',
    };
  },
    mounted(){
        // fetch (`http://localhost:3000/products`) //ดึงข้อมูลจาก Backend
        // .then(res=>res.json()) //ได้ค่า res ใช้ res เป็นParameter เรียก fn. แปลงเป็น JSON format
        // .then(data=>this.products=data) //ได้ data ใช้ data เป็นParameter กำหนดค่าให้ products
        // .catch(err=>console.log(err.message)) //ถ้าผิดพลากแสดง err
        axios.get(`http://localhost:3000/products/ten`)
        .then(res=>{
          this.products = res.data
        })
        .catch(err=>console.log(err.mesage))
    },
    methods : {
      searchProduct(){
        axios.get(`http://localhost:3000/products/search/${this.stext}`)
        .then(res=>{
          this.products = res.data
        })
        .catch(err=>console.log(err.mesage))
      }
    }

};
</script>

<style>
</style>
