<script setup>
import axios from 'axios';
import DrawerHead from './DrawerHead.vue';
import CartItemList from './CartItemList.vue';
import InfoBlock from './InfoBlock.vue';
import { ref, inject,  computed } from 'vue';

const isCreating = ref(false)
const orderId = ref(null)

const props = defineProps ({
    vatPrice: Number,
    totalPrice: Number
})
const {cart,closeDrawer} = inject('cart')

const createOrder = async () => {
  try {
    isCreating.value = true
    const {data} = await axios.post('https://1dba12d59af42e98.mokky.dev/orders', {
      items: cart.value,
      totalPrice: props.totalPrice.value

    })
    cart.value = []
    orderId.value = data.id
    return data;
  } catch (error) {
    console.log(error);
  } finally {
    isCreating.value = false
  }
}
const cartIsEmpty = computed(() => cart.value.length === 0);
const buttonDisabled = computed(() => isCreating.value || cartIsEmpty.value);

</script>
<template>
    <div class="fixed top-0 left-0 h-full w-full bg-black z-10 opacity-70"></div>
    <div class="bg-white  w-96 h-full fixed right-0 top-0 z-20 p-8 ">
        <DrawerHead/>

        
        <div v-if="!totalPrice || orderId"  class="h-full flex items-center">
            <InfoBlock 
                v-if="!totalPrice && !orderId"
                title="Корзина пустая" 
                description="Добавьте хотя бы одну пару кроссовок, чтобы сделать заказ." 
                image-url="/package-icon.png"
            />
            <InfoBlock 
                v-if="orderId"
                title="Заказ оформлен" 
                :description="`Ваш заказ #${orderId} скоро будет передан курьерской службой`" 
                image-url="/order-success-icon.png"
            />
          
        </div>
        <div v-if="!totalPrice"  class="h-full flex items-center">
            <InfoBlock 
                title="Заказ оформлен" 
                description="Ваш заказ скоро будет передан курьерской службой" 
                image-url="/order-success-icon.png"
            />
        </div>
        <div v-else>
            <CartItemList />
            <div  class="flex gap-4 flex-col  mt-7">
                <div>
                <div class="flex gap-2 ">
                    <span>Итого:</span>
                    <div class="flex-1 border-b border-dashed"></div>
                    <b>{{ totalPrice }} ₽</b>
                </div>
            </div>
            <div>
            <div class="flex gap-2 ">
                <span>Налог 5%:</span>
                <div class="flex-1 border-b border-dashed"></div>
                <b>{{ vatPrice }} ₽</b>
            </div>
            </div>
            <button 
                @click="createOrder"
                :disabled="buttonDisabled" 
                class="mt-4 transiton bg-lime-500 w-full rounded-xl py-3 text-white hover:bg-lime-600 active:bg-lime-700 disabled:bg-slate-300 cursor-pointer">
                Оформить заказ
            </button>
            </div>
        </div>
     
    </div>
</template>
