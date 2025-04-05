<template>
  <div class="min-h-screen flex items-center justify-center bg-gradient-to-t from-[#0f172a] to-[#1e293b]">
    <div class="w-full max-w-md p-8 space-y-6 bg-white shadow-lg rounded-lg">
      <h1 class="text-3xl font-bold text-gray-900 text-center">Sign In</h1>
      <h2 class="text-gray-500">Sign In to continue</h2>

      <form action="" class="mt-6 space-y-6" @submit.prevent="">
        <div class="rounder-md shadow-sm -space-y-px">
          <div class="username">
            <label for="">Username</label>
            <input required class="form-control" placeholder="Username" />
          </div>
          <div class="password mt-4">
            <label for="">Password</label>
            <input type="password" required class="form-control" placeholder="Password" />
          </div>
        </div>
        <div class="flex justify-between items-center">
          <div class="flex">
            <input type="checkbox" id="remember-me" name="remember-me"
              class="h-4 w-4 text-blue-600 focus:ring-blue-500 border-gray-300 rounded" />
            <label for="remember-me" class="ml-2 block text-sm text-gray-900">Remember me</label>
          </div>
        </div>
      </form>
    </div>
  </div>
</template>
