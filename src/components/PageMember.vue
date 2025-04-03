<template>
  <div class="container mt-5">
    <div class="row">
      <!-- ข้อมูลสมาชิก -->
      <div class="col-md-6 text-end">
        <h3 class="text-black">{{ memEmail }}</h3>
        <h5 class="text-secondary">{{ memName }}</h5>
        <pre class="thank-you-text">
ขอบพระคุณ
ขอบพระใจ
ขอบพระไทยแต้งกิ้ว
ที่เข้ามาใช้บริการเว็บ 
Plantzaza Shop เพื่อซื้อสกิน skin valorant ราคาสุดคุ้มค่า
</pre>
      </div>
        
      <!-- รูปภาพโปรไฟล์ -->
      <div class="col-md-6 d-flex justify-content-center">
        <div class="card shadow-lg p-2 rounded-4" style="width: 18rem">
          <img
            :src="profileImage"
            :alt="memEmail"
            class="card-img-top rounded-3"
            @error="imageExists = false"
          />
        </div>
      </div>
    </div>

    <!-- Form อัปโหลดรูป -->
    <form @submit.prevent="uploadFile" class="mt-4">
      <div class="row justify-content-center">
        <div class="col-auto">
          <input class="form-control" type="file" id="formFile" @change="onFileChange" required />
        </div>
        <div class="col-auto">
          <button class="btn btn-primary" type="submit">
            <i class="bi bi-upload"></i>&nbsp;Upload
          </button>
        </div>
      </div>

      <!-- แสดงข้อความหลังอัปโหลด -->
      <div class="row justify-content-center mt-3" v-if="fileMessage">
        <div class="col-auto">
          <div class="alert" :class="{'alert-success': fileMessage !== 'fail', 'alert-danger': fileMessage === 'fail'}">
            {{ fileMessage }}
          </div>
        </div>
      </div>
    </form>
  </div>
</template>

<script>
import Cookies from "js-cookie";
import { jwtDecode } from "jwt-decode";
import axios from "axios";

axios.defaults.withCredentials = true;

export default {
  name: "PageMember",
  data() {
    return {
      token: "",
      decodedToken: null,
      memEmail: null,
      memName: null,
      dutyId: null,
      imageExists: true,
      fileMessage: null,
      file: null,
      imageTimestamp: Date.now(),
    };
  },
  computed: {
    profileImage() {
      return this.imageExists
        ? `http://localhost:3000/img_mem/${this.memEmail}.jpg?timestamp=${this.imageTimestamp}`
        : `http://localhost:3000/img_mem/default.jpg`;
    },
  },
  mounted() {
    this.getCookie();
  },
  methods: {
    getCookie() {
      try {
        this.token = Cookies.get("token");
        this.decodedToken = jwtDecode(this.token);
        this.memEmail = this.decodedToken.memEmail;
        this.memName = this.decodedToken.memName;
        this.dutyId = this.decodedToken.dutyId;
      } catch (err) {
        console.error(`ไม่สามารถอ่าน Token ได้: ${err}`);
      }
    },
    onFileChange(e) {
      this.file = e.target.files[0];
    },
    async uploadFile() {
      if (!this.file) {
        this.fileMessage = "กรุณาเลือกไฟล์ก่อน";
        return;
      }
      const formData = new FormData();
      formData.append("memEmail", this.memEmail);
      formData.append("file", this.file);
      try {
        const response = await axios.post(
          "http://localhost:3000/members/uploadImg",
          formData,
          { headers: { "Content-Type": "multipart/form-data" } }
        );
        this.fileMessage = response.data.message;
        this.imageTimestamp = Date.now(); // รีโหลดรูปใหม่
        this.imageExists = true;
      } catch (err) {
        this.fileMessage = "fail";
      }
    },
  },
};
</script>

<style>
.card img {
  object-fit: cover;
  height: 250px;
  transition: opacity 0.3s ease-in-out;
}

.btn-primary:hover {
  background-color: #0044cc;
}

.alert {
  font-size: 14px;
  padding: 10px;
}
</style>
