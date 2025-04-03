<template>
  <div v-if="products">
    <div class="row">
      <div class="col-md-6 col-sm-12">
        <div class="card mx-auto mb-3" style="width: 18rem">
          <img
            :src="`http://localhost:3000/img_pd/${products.pdId}.jpg`"
            alt=""
            class="rounded shadow-sm"
          />
        </div>
      </div>
      <div class="col-md-6 col-sm-12">
        <table class="table">
          <tbody>
            <tr class="table-secondary">
              <td><h4>{{ products.pdId }}</h4></td>
              <td><h4>{{ products.pdName }}</h4></td>
            </tr>
            <tr>
              <td><h5>ราคา</h5></td>
              <td><h5>{{ products.pdPrice }}</h5></td>
            </tr>
            <tr>
              <td><h5>ระดับของสกิน</h5></td>
              <td><h5>{{ products.brand.brandName }}</h5></td>
            </tr>
            <tr>
              <td><h5>รายละเอียด</h5></td>
              <td><h5>{{ products.pdRemark }}</h5></td>
            </tr>
          </tbody>
        </table>
        <button class="btn btn-primary" @click="chkLogin()">
          <i class="bi bi-cart lead"></i> ใส่ตะกร้า&nbsp;
        </button>
        <button class="btn btn-warning float-end" @click="goToEditProduct()">
          <i class="bi bi-pencil-fill lead"></i> แก้ไข&nbsp;&nbsp;
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import Cookies from "js-cookie";
import { jwtDecode } from "jwt-decode";
import axios from "axios";
import { EventBus } from "../event-bus";

axios.defaults.withCredentials = true;

export default {
  name: "ProductShow",
  data() {
    return {
      token: "",
      decodedToken: null,
      memEmail: null,
      cartId: null,
      products: null,
      id: null,
    };
  },
  mounted() {
    this.id = this.$route.params.pdId;
    axios
      .get(`http://localhost:3000/products/${this.id}`)
      .then((res) => {
        this.products = res.data;
      })
      .catch((err) => {
        console.error(err);
      });
  },
  methods: {
    goToEditProduct() {
      if (this.products && this.products.pdId) {
        this.$router.push(`/editproduct/${this.products.pdId}`);
      } else {
        console.error("Product ID is missing");
      }
    },
    async getCookie() {
      try {
        this.token = await Cookies.get("token");
        this.decodedToken = jwtDecode(this.token);
        this.memEmail = this.decodedToken.memEmail;
      } catch (err) {
        console.error(`Fail to decode token: ${err}`);
      }
    },
    async chkLogin() {
      console.log("chkLogin");
      await this.getCookie();
      if (!this.memEmail) {
        alert("ต้อง Login เข้าระบบก่อน");
        console.warn("ยังไม่ได้ login");
        return false;
      }
      console.log("login แล้ว");
      await this.chkCart();
      if (!this.cartId) {
        await this.addCart();
      }
      this.addCartDtl();
    },
    async chkCart() {
      console.log("chkCart");
      try {
        const response = await axios.post(`http://localhost:3000/carts/chkcart`, { memEmail: this.memEmail });
        this.cartId = response.data.cartId;
        console.log(this.cartId);
      } catch (err) {
        console.log(err);
      }
    },
    async addCart() {
      console.log("addCart");
      try {
        const response = await axios.post(`http://localhost:3000/carts/addcart`, { cusId: this.memEmail });
        this.backendMessage = response.data.messageAddCart;
        this.cartId = response.data.cartId;
        console.log(response.data);
      } catch (err) {
        console.log(err);
      }
    },
    async addCartDtl() {
      console.log("addCartDtl");
      try {
        const cartdtl = {
          cartId: this.cartId,
          pdId: this.id,
          pdPrice: this.products?.pdPrice || 0,
        };
        const response = await axios.post(`http://localhost:3000/carts/addcartdtl`, cartdtl);
        EventBus.emit("cartdtlOK", { id: this.cartId });
        this.backendMessage = response.data.messageAddCartDtl;
        console.log(response.data);
      } catch (err) {
        console.log(err);
      }
    },
  },
};
</script>

<style></style>