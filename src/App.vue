<template>
  <div class="container mx-auto pt-16" role="main">
    <div class="grid md:grid-cols-10 gap-10">
      <div class="col-span-6 xl:col-span-7 py-5">
        <h1 class="text-4xl font-extrabold text-custom-rose-900 mb-16">Desserts</h1>
        <div class="grid grid-cols-1 md:grid-cols-2 xl:grid-cols-3 gap-8 gap-y-14 px-4 md:px-0">
          <div v-for="(product, index) in products" :key="index">
            <div class="relative">
              <img :src="product.image.thumbnail" :srcset="`
                  ${product.image.mobile} 480w,
                  ${product.image.tablet} 768w,
                  ${product.image.desktop} 1280w
                  
      `" sizes="(max-width: 480px) 100vw,
           (max-width: 768px) 100vw,
           1280px" :alt="product.name" class="w-full rounded-2xl" />

              <button type="button" class="add-to-cart-button" v-if="!cartItems.some(item => item.id === product.id)"
                @click="addToCart(product)">
                <AddToCartIcon />
                <span>Add
                  to Cart</span>
              </button>
              <button type="button" class="quantity-button" v-if="cartItems.some(item => item.id === product.id)">
                <div @click="decrementQuantity(product)"
                  class="w-6 h-6 rounded-full flex items-center justify-center border-1 border-white cursor-pointer  hover:bg-white transition-all duration-300 hover:text-red-700">
                  <DecrementIcon />
                </div>
                <span>{{cartItems.find(item => item.id === product.id)?.quantity}}</span>
                <div @click="incrementQuantity(product)"
                  class="w-6 h-6 rounded-full flex items-center justify-center border-1 border-white cursor-pointer  hover:bg-white transition-all duration-300 hover:text-red-700">
                  <IncrementIcon />
                </div>
                <span class="sr-only">Increment</span>
                <span class="sr-only">Decrement</span>
              </button>
            </div>
            <div class=" mt-14">
              <span class="text-custom-rose-300">{{ product.category }}</span>
              <h2 class="text-xl font-semibold">{{ product.name }}</h2>
              <span class="text-custom-red font-semibold text-xl ">${{ product.price.toFixed(2) }}</span>
            </div>
          </div>
        </div>
      </div>
      <!-- Cart Section -->
      <div class="col-span-6 xl:col-span-3 p-6 rounded-lg">
        <div class="bg-white rounded-xl px-6 py-4 ">
          <h1 class="pl-6 py-5 font-bold text-2xl text-custom-red">Your Cart ({{cartItems.reduce((total, item) => total
            + item.quantity, 0)}})</h1>
          <div class="flex flex-col gap-y-4">
            <div class="border-b border-b-custom-rose-100 pb-3.5" v-for="cartItem in cartItems" :key="cartItem.id">
              <h3 class="text-md font-medium mb-1">{{ cartItem.name }}</h3>
              <div class="flex items-center gap-4">
                <span class="text-custom-red font-medium">{{ cartItem.quantity }}x</span>
                <div class="flex items-center gap-2 text-sm flex-1">
                  <span class="text-custom-rose-300 ">@${{ cartItem.price.toFixed(2) }}</span>
                  <span class="text-custom-rose-500 font-semibold">${{ (cartItem.price * cartItem.quantity).toFixed(2)
                  }}</span>
                </div>
                <div>
                  <button type="button" @click="removeFromCart(cartItem)"
                    class="cursor-pointer text-custom-rose-500 border p-1 rounded-full hover:text-custom-rose-900 hover:border-custom-rose-900 transition-all duration-300">
                    <RemoveIcon />
                    <span class="sr-only">Remove</span>
                  </button>
                </div>
              </div>
            </div>

            <!-- Cart is empty -->
            <div class="flex-col flex items-center gap-y-6" v-if="cartItems.length === 0">
              <EmptyCartIcon />
              <span class="pb-10 text-sm text-custom-rose-500 font-medium">Your added items will appear here</span>
            </div>
            <!-- Cart is empty -->

            <div class="mt-3 flex justify-between items-center" v-if="cartItems.length > 0">
              <span class="text-sm text-custom-rose-500 font-medium">Order Total:</span>
              <span class="text-2xl text-custom-rose-900 font-bold">${{cartItems.reduce((total, item) => total +
                item.price * item.quantity, 0).toFixed(2)}}</span>
            </div>
            <p v-if="cartItems.length > 0"
              class="py-3 flex items-center justify-center gap-1 text-center text-custom-rose-900 bg-custom-rose-50 text-sm">
              <CarbonIcon /> This is a <span class="font-semibold">carbon-neutral</span> delivery
            </p>
            <button type="button" @click="isConfirmedModalOpen = true" v-if="cartItems.length > 0"
              class="py-3 bg-custom-red rounded-4xl text-white mt-2 cursor-pointer">Confirm Order </button>
            <!-- Confirm Order -->
            <div v-if="isConfirmedModalOpen"
              class="fixed bottom-0  inset-0 bg-black/45  flex items-end sm:items-center justify-center z-50">
              <div class="bg-white  sm:min-w-6/12 xl:min-w-4/12 rounded-xl px-8 py-8 flex flex-col gap-y-2.5">
                <OrderConfirmedIcon class="mb-2" />
                <h1 class="text-5xl  font-bold w-1/2 md:w-full ">Order Confirmed</h1>
                <span class="text-sm text-custom-rose-300 ">We hope you enjoy your food!</span>
                <div class="bg-custom-rose-100 rounded-md flex flex-col gap-y-6 p-8 mt-4 max-h-80 overflow-y-auto">
                  <div v-for="cart in cartItems" :key="cart.id" class="flex justify-between">
                    <div class="flex items-center">
                      <img :src="cart.image.thumbnail" :alt="cart.name" class="w-10 h-10 rounded-md mr-2">
                      <div class="flex flex-col justify-center gap-x-7">
                        <span class="text-sm font-semibold text-custom-rose-900">{{ cart.name }}</span>
                        <span class="text-xs text-custom-red font-bold">{{ cart.quantity }}x<span
                            class="text-custom-rose-500 pl-3  font-medium">@${{ cart.price.toFixed(2) }}</span></span>
                      </div>
                    </div>
                    <span class="font-semibold text-md">${{ (cart.price * cart.quantity).toFixed(2) }}</span>
                  </div>
                  <div class="flex justify-between items-center mt-5">
                    <span class="text-sm text-custom-rose-500 font-medium">Order Total:</span>
                    <span class="text-2xl text-custom-rose-900 font-bold">${{cartItems.reduce((total, item) => total +
                      item.price * item.quantity, 0).toFixed(2)}}</span>
                  </div>
                </div>
                <button type="button" @click="handleStartNewOrder"
                  class="py-3 bg-custom-red rounded-3xl text-white mt-2 cursor-pointer w-full flex items-center justify-center gap-x-2">
                  <span>Start new Order</span>
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue';
import AddToCartIcon from './components/Icons/AddToCartIcon.vue';
import EmptyCartIcon from './components/Icons/EmptyCartIcon.vue';
import DecrementIcon from './components/Icons/DecrementIcon.vue';
import IncrementIcon from './components/Icons/IncrementIcon.vue';
import RemoveIcon from './components/Icons/RemoveIcon.vue';
import CarbonIcon from './components/Icons/CarbonIcon.vue';
import OrderConfirmedIcon from './components/Icons/OrderConfirmedIcon.vue';
const products = ref([]);
const cartItems = ref([]);
const isConfirmedModalOpen = ref(false);
const addToCart = (product) => {
  if (!cartItems.value.includes(product.id)) {
    cartItems.value.push({ ...product, quantity: 1 });
  }
};
const fetchProducts = async () => {
  try {
    const response = await fetch('/data.json');
    if (response.ok) {
      products.value = await response.json();
    } else {
      console.error('Failed to fetch products:', response.status);
    }
  } catch (error) {
    console.error('Error fetching products:', error);
  }
};
const incrementQuantity = (product) => {
  const item = cartItems.value.find(item => item.id === product.id);
  if (item) {
    item.quantity++;
  }
};
const decrementQuantity = (product) => {
  const item = cartItems.value.find(item => item.id === product.id);
  if (item && item.quantity > 1) {
    item.quantity--;
  }
};
const removeFromCart = (cartItem) => {
  cartItems.value = cartItems.value.filter(item => item.id !== cartItem.id);
};
const handleStartNewOrder = () => {
  isConfirmedModalOpen.value = false;
  cartItems.value = [];
};
const isOpen = ref(false);
onMounted(() => {
  fetchProducts();
});
</script>