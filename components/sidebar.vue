<script setup>
    const activeMenu = ref('');

    const toggleMenu = (menu) => {
        activeMenu.value = menu;
    };

</script>

<template>
    <div>
        <div class="sidebar-title">ERP Whey Protein</div>
        <div class="sidebar-avatar">
            <img src="https://avatar.iran.liara.run/public/48" alt="" class="w-16 h-16 rounded-full mx-auto" />
            <div class="text-center text-white text-sm mt-3">Admin system</div>
            <div class="text-center flex justify-center items-center gap-2 mt-3">
                <button class="btn btn-primary text-xs">
                    <i class="fa-solid fa-user mr-1"></i>
                    Profile
                </button>
                <button class="btn btn-danger text-xs">
                    <i class="fa-solid fa-right-from-bracket mr-1"></i>
                    Sign Out
                </button>
            </div>
        </div>
        <div class="sidebar-menu">
            <!--  ? ใส่คาสที่เป็น dynamic จะเป็น :class จะแปรผันได้ จะใส่ class ที่ชื่อ active ก็ต่อเมื่อ activeMenu === ตามที่เรากำหนด -->
            <NuxtLink class="nav-link" :class="{'active': activeMenu === 'home'}" to="/home" 
                @click="toggleMenu('home')">
                <i class="fa-solid fa-house"></i>
                <span class="nav-text">Dashboard</span>
            </NuxtLink>
            <NuxtLink class="nav-link" :class="{'active': activeMenu === 'productType'}" to="/productType" 
                @click="toggleMenu('productType')">
                <i class="fa-solid fa-box"></i>
                <span class="nav-text">Product Type</span>
            </NuxtLink>
        </div>
    </div>
</template>