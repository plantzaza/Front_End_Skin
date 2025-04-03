<template>
    <div class="d-flex justify-content-center mt-5">
        <div class="col-lg-6 col-md-8 col-sm-12">
            <form @submit.prevent="handleSubmit">
                <div class="card shadow-sm p-4" style="background-color: #f7fafc; border-radius: 12px;">
                    <div class="card-body">
                        <h2 class="card-title text-center text-black">ลงทะเบียนสมาชิกใหม่</h2>
                        
                        <div class="mb-3">
                            <div class="form-floating">
                                <input type="email" class="form-control" id="memEmail" v-model.trim="memEmail" required placeholder="Email">
                                <label for="memEmail">Email</label>
                            </div>
                        </div>
                        
                        <div class="mb-3">
                            <div class="form-floating">
                                <input type="text" class="form-control" id="memName" v-model.trim="memName" required placeholder="ชื่อ">
                                <label for="memName">ชื่อ</label>
                            </div>
                        </div>
                        
                        <div class="mb-3">
                            <div class="form-floating">
                                <input type="password" class="form-control" id="password" v-model.trim="password" required placeholder="Password">
                                <label for="password">Password</label>
                            </div>
                        </div>
                        
                        <div class="text-end">
                            <button class="btn btn-success" type="submit">
                                <i class="bi bi-check-lg"></i> ตกลง
                            </button>
                        </div>
                    </div>
                </div>
            </form>
            
            <!-- ส่วนแสดงสถานะการลงทะเบียน -->
            <p v-if="regist" class="alert alert-success mt-3 text-center">ลงทะเบียนสำเร็จ</p>
            <p v-else-if="regist === false" class="alert alert-danger mt-3 text-center">ลงทะเบียนผิดพลาด: {{ backendmessage }}</p>
        </div>
    </div>
</template>

<script>
import axios from 'axios';

export default {
    name: 'TheRegister',
    data() {
        return {
            memEmail: '',
            memName: '',
            password: '',
            regist: null,
            backendmessage: ''
        };
    },
    methods: {
        async handleSubmit() {
            const member = {
                memEmail: this.memEmail,
                memName: this.memName,
                password: this.password
            };
            try {
                const response = await axios.post('http://localhost:3000/member', member);
                this.regist = response.data.regist;
                this.backendmessage = response.data.message;
            } catch (err) {
                console.error('Error:', err);
                this.regist = false;
                this.backendmessage = 'ไม่สามารถเชื่อมต่อกับเซิร์ฟเวอร์';
            }
        }
    }
};
</script>

<style>
.card {
    border: none;
}
.btn-success {
    background-color: #4CAF50;
    border: none;
    padding: 10px 20px;
    border-radius: 8px;
    font-size: 16px;
}
</style>