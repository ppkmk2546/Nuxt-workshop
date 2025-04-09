<script setup>

</script>

<template>
    <div>
        <div class="sidebar-title">ERP Whey Protein</div>
        <div class="sidebar-avatar">
            <img src="https://avatar.iran.liara.run/public/48" alt="" class="w-16 h-16 rounded-full mx-auto" />
            <div class="text-center text-white text-sm mt-3">Admin system</div>
        </div>
        <div class="sidebar-menu">
            <NuxtLink class="nav-link" to="/home">
                <i class="fa-solid fa-house"></i>
                <span class="nav-text">Dashboard</span>
            </NuxtLink>
            <NuxtLink class="nav-link" to="/productType">
                <i class="fa-solid fa-box"></i>
                <span class="nav-text">Product Type</span>
            </NuxtLink>

        </div>
    </div>
</template>