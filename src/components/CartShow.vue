<template>
    <!-- Master --><!-- ถ้าค่าที่อยู่ใน Cookie กับ CusId ไม่ตรงกันแสดงว่าไม่ใช้ผู้ซื้อไม่มีสิทธิอ่าน -->
    <div v-if="memEmail==cusId">
        <div v-for="(ct, cartId) in cart" :key="cartId" class="mt-5">
            <div class="card bg-light">
                <div class="card-body">
                    <h4 class="card-title text-primary opacity-75">คำสั่งซื้อเลขที่ {{ ct.cartId }}</h4>
                    <h5 class="card-subtitle mt-2 text-muted">
                        สั่งซื้อวันที่ {{ formattedDate(ct.cartDate) }}
                    </h5>
                    <div class="text-danger text-end">
                        จำนวนสินค้า {{ ct.sqty }} ชิ้น, ยอดเงิน {{ (Number(ct.sprice) || 0).toLocaleString() }} บาท
                    </div>
                    <hr />
                    <a class="btn btn-danger" @click="clearCart">
                        <i class="bi-cart-x-fill"></i> ลบตะกร้าสินค้า
                    </a>
                    <a class="btn btn-primary float-end" onclick="return confirm('ยืนยันสั่งสินค้า')">
                        <i class="bi-currency-dollar"></i> ยืนยันสั่งสินค้า
                    </a>
                </div>
            </div> <!--card-->
        </div> <!---v for  -->
   
        <!-- Detail -->
        <table class="table  mt-3 ">
            <thead>
            <tr class="bg-secondary bg-opacity-10">
                <td></td>
                <td>สินค้า</td>
                <td></td>
                <td class="text-end">ราคาต่อหน่วย</td>
                <td class="text-center">จำนวน</td>
                <td class="text-end">เป็นเงิน</td>
                <td></td>
            </tr>
            </thead>
            <tbody>
            <tr v-for="(ctd, pdId) in cartDtl" :key="pdId">
                <td>{{ ctd.row_number }}</td>
                <td>{{ ctd.pdId }}</td>
                <td>{{ ctd.pdName }}</td>
                <td class="text-end">{{ ctd.price }}</td>
                <td class="text-center">{{ ctd.qty }}</td>
                <td class="text-end">{{ (Number(ctd.price) * Number(ctd.qty) || 0).toLocaleString() }}</td>
                <td class="text-center">
                    <i class="bi-x-lg text-danger"></i>
                </td>
            </tr>
            </tbody>
        </table>
    </div><!--v-if-->
    <div v-else>
        <div class="alert alert-danger mt-5">คุณไม่มีสิทธิ์ดูรายการนี้</div>
    </div>
</template>
<script>
import axios from 'axios';
axios.defaults.withCredentials = true
import Cookies from 'js-cookie' //imporo js-cookie มาใช้งาน
import {jwtDecode} from 'jwt-decode'; //ใช้แทน jsonwebtoken เพราะ jsonwebtoken ไม่เข้ากับ Vue.js


export default {
    name: "CartShow",
    data() {
        return {
            cart: [],//รับข้อมูล Master
            cartDtl: [], //รับข้อมูล Detail
            cartId: null,
            memEmail: null,
            decodedToken:null,
            cusId:null
        }
    },
    async mounted() { // ตรวจสอบว่าเป็นเจ้าของตะกร้าหรือไม่
        this.getCookie()
        this.cartId = this.$route.params.cartId
        await this.getCart()
        await this.getCartDtl()
    },
    methods: { // แปลงรูปแบบวันที่
    async clearCart() {
    if (!confirm("คุณต้องการลบตะกร้าสินค้าทั้งหมดใช่หรือไม่")) return;

    try {
        const response = await axios.delete(`http://localhost:3000/deletecart/${this.cartId}`);

        if (response.status === 200) {
            alert("ล้างตะกร้าสินค้าเรียบร้อยแล้ว");
            this.cart = [];
            this.cartDtl = [];
        } else {
            throw new Error(response.data.error || "ไม่สามารถลบตะกร้าได้");
        }
    } catch (error) {
        console.error("Error deleting cart:", error);
        alert("เกิดข้อผิดพลาด: " + error.message);
    }
},

        formattedDate(dateStr) {
            const date = new Date(dateStr);
            const year = date.getFullYear();
            const month = String(date.getMonth()+ 1).padStart(2, '0'); // เดือนเริ่มต้นที่ 0, จึงต้อง +1
            const day = String(date.getDate()).padStart(2, '0');
            return `${year}-${month}-${day}`;
        },
    async getCart() {
    console.log('Get Cart')
    await axios.get(`http://localhost:3000/carts/getcart/${this.cartId}`)
        .then(res => {
            console.log("Cart Data:", res.data)  // ตรวจสอบค่าที่ API ส่งมา
            this.cart = res.data
            this.cusId = res.data[0]?.cusId || null
        })
        .catch(err => {  
            console.error(err); 
        });
},
        async getCartDtl() {
            console.log('Get CartCartDtl')
            await axios.get(`http://localhost:3000/carts/getcartdtl/${this.cartId}`)
                .then(res => {
                    console.log("CartDtl \n" + res.data)
                    this.cartDtl = res.data
                })
                .catch(err => { console.error(err); });
        },
        getCookie() {
    try {
        this.token = Cookies.get('token');

        // ตรวจสอบว่าค่า token เป็น string และไม่เป็น undefined/null
        if (!this.token || typeof this.token !== "string") {
            throw new Error("Token is missing or invalid");
        }

        this.decodedToken = jwtDecode(this.token);
        this.memEmail = this.decodedToken.memEmail;
    } catch (err) {
        console.error(`fail decode token: ${err.message}`);
        this.decodedToken = null;
    }
}

    }
}
</script>
<style></style>