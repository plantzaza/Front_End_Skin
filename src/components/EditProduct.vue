<template>
  <div class="container mt-4">
    <div class="card shadow-sm p-4">
      <h2 class="text-center mb-4">แก้ไขสินค้า</h2>

      <div class="mb-3">
        <label class="form-label">ชื่อสินค้า:</label>
        <input v-model="product.pdName" type="text" class="form-control" />
      </div>

      <div class="mb-3">
        <label class="form-label">ราคา:</label>
        <input v-model="product.pdPrice" type="number" class="form-control" />
      </div>

      <div class="mb-3">
        <label class="form-label">ยี่ห้อ:</label>
        <input v-model="product.brand.brandName" type="text" class="form-control" />
      </div>

      <div class="mb-3">
        <label class="form-label">รายละเอียด:</label>
        <textarea v-model="product.pdRemark" class="form-control"></textarea>
      </div>

      <div class="d-flex justify-content-between">
        <button @click="saveProduct" class="btn btn-success">
          <i class="bi bi-check-circle"></i> บันทึก
        </button>
        <button @click="$router.push('/')" class="btn btn-danger">
          <i class="bi bi-x-circle"></i> ยกเลิก
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      product: {
        pdName: "",
        pdPrice: "",
        brand: { brandName: "" },
        pdRemark: "",
      },
    };
  },
  methods: {
    async fetchProduct() {
      try {
        const productId = this.$route.params.id;
        const res = await axios.get(`http://localhost:3000/products/${productId}`);
        this.product = res.data;
      } catch (error) {
        console.error("เกิดข้อผิดพลาดในการดึงข้อมูลสินค้า:", error);
      }
    },
    async saveProduct() {
      try {
        const productId = this.$route.params.id;
        await axios.put(`http://localhost:3000/products/${productId}`, this.product);
        alert("บันทึกข้อมูลสำเร็จ");
        this.$router.push("/");
      } catch (error) {
        console.error("เกิดข้อผิดพลาดในการบันทึกข้อมูล:", error);
        alert("ไม่สามารถบันทึกข้อมูลได้");
      }
    },
  },
  mounted() {
    this.fetchProduct();
  },
};
</script>

<style scoped>
.card {
  max-width: 500px;
  margin: auto;
  border-radius: 10px;
}
</style>
