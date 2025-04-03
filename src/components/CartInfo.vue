<template>
    <div v-if="cartId != null && qty > 0">
        <button class="btn btn-success text-white" @click="$router.push(`/cartshow/${cartId}`)">
            {{ cartId }} [{{ qty }}] - {{ money }}฿
        </button>
    </div>
</template>

<script>
import axios from 'axios';
axios.defaults.withCredentials = true;
import { EventBus } from "../event-bus";

export default {
    name: "CartInfo",
    data() {
        return {
            cartId: null,
            qty: 0,
            money: 0
        };
    },
    async mounted() {
        // รับ Event เมื่อมีการอัปเดตตะกร้า
        EventBus.on('cartdtlOK', (data) => {
            if (data.id) {
                this.sumCart(data.id);
            } else {
                console.warn("cartdtlOK event received but data.id is undefined!");
            }
        });

        // รับ Event เมื่อ Logout
        EventBus.on('memlogout', () => {
            this.clearCart();
        });
    },
    methods: {
        async sumCart(cid) {
            console.log(`sumCart received cid: ${cid}`);
            if (!cid) return; // ป้องกันกรณี cid เป็น undefined
            
            try {
                const res = await axios.get(`http://localhost:3000/carts/sumcart/${cid}`);
                console.log("SumCart Data:", res.data);
                this.cartId = res.data.id;
                this.qty = res.data.qty;
                this.money = res.data.money;
            } catch (err) {
                console.error("Error in sumCart:", err);
            }
        },
        clearCart() {
            console.log('Clear Cart');
            this.cartId = null;
            this.qty = 0;
            this.money = 0;
        }
    }
};
</script>

<style></style>
