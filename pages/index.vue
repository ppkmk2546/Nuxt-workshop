<template>
    <div class="min-h-screen flex items-center justify-center bg-gradient-to-t from-[#0f172a] to-[#1e293b]">
      <div class="w-full max-w-md p-8 space-y-6 bg-white shadow-lg rounded-lg">
        <h1 class="text-3xl font-bold text-gray-900 text-center">Sign In</h1>
        <p class="text-sm text-gray-500 text-center">Welcome to Whey Protein Management</p>
        <form action="" class="mt-8 space-y-6" @submit.prevent="handleSubmit">
          <div class="rounder-md">
            <div class="username">
              <label for="">Username</label>
              <input v-model="username" required class="form-control" placeholder="Username" />
            </div>
            <div class="password mt-5">
              <label for="">Password</label>
              <input v-model="password"  type="password" required class="form-control" placeholder="Password" />
            </div>
          </div>
          <div class="flex justify-between items-center">
            <div class="flex">
              <input type="checkbox" class="h-4 w-4 accent-blue-600 focus:ring-blue-500 border-gray-300 rounded-sm" />
              <label for="remember-me" class="ml-2 block text-sm text-gray-900">Remember me</label>
            </div>
            <div class="text-sm">
              <a href="#" class="font-medium text-blue-600 hover:text-blue-500 transition duration-150 ease-in-out">Forgot Your password?</a>
            </div>
          </div>
          <div>
              <button type="submit" class="btn-full">Sign In</button>
          </div>
        </form>
      </div>
    </div>
</template>
  
  <script setup>
    import axios from 'axios';
    import Swal from 'sweetalert2';
    import config from '@/config';
  
    const username = ref('')
    const password = ref('')
  
    const handleSubmit = async () => {
      try {
        if (!username.value || !password.value) {
          Swal.fire({
            icon: 'warning',
            title: 'Warning',
            text: '⚠️ Please fill in all fields.',
          })
        } else {
          const response = await axios.post(`${config.apiServer}/api/user/signIn`,{
            username: username.value,
            password: password.value
          })
          
          if (response.status === 200) {
            localStorage.setItem(config.token, response.data.token); // !
            localStorage.setItem('nuxt_workshop_user_id', response.data.id); // !

            navigateTo('/home');
          } else { // ! ต้องดัก else เรื้่องรหัสผิด มันไป catch ล่างเลย 
            Swal.fire({
              icon: 'error',
              title: 'Error',
              text: 'Invalid username or password.',
            })
          }
        }
      } catch (error) {
        Swal.fire({
          icon: 'error', // ! อาจจะต้องปรับการการแสดงผลเมื่อ user กรอก รหัสผิด
          title: 'Error',
          text: error.message,
        })
      }
    }
  </script>
  