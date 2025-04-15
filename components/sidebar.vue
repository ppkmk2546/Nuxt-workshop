<script setup>
    import { ref } from 'vue';
    import config from '@/config';
    import Swal from 'sweetalert2';

    const activeMenu = ref('');

    const toggleMenu = (menu) => {
        activeMenu.value = menu;
    };

    const signOut = async () => {
        const button = await Swal.fire({
            icon: 'warning',
            title: 'Do you want to log out?',
            text: 'You will be logged out of the system.',
            showCancelButton: true,
            confirmButtonText: 'Yes',
            cancelButtonText: 'Cancel',
        });

        if (button.isConfirmed) {
            localStorage.removeItem(config.token);
            localStorage.removeItem('nuxt_workshop_user_id');

            navigateTo('/');

        }
    }

</script>

<template>
    <div>
        <div class="sidebar-title">ERP Whey Protein</div>
        <div class="sidebar-avatar">
            <img src="https://avatar.iran.liara.run/public/48" alt="" class="w-16 h-16 rounded-full mx-auto" />
            <div class="text-center text-white text-sm mt-3">Admin system</div>
            <div class="text-center flex justify-center items-center gap-2 mt-3">
                <button class="btn btn-primary text-xs" @click="navigateTo('/userProfile')">
                    <i class="fa-solid fa-user mr-1"></i>
                    Profile
                </button>
                <button class="btn btn-danger text-xs" @click="signOut">
                    <i class="fa-solid fa-right-from-bracket mr-1"></i>
                    Sign Out
                </button>
            </div>
        </div>
        <div class="sidebar-menu">
            <!--  ? ใส่คาสที่เป็น dynamic จะเป็น :class จะแปรผันได้ จะใส่ class ที่ชื่อ active ก็ต่อเมื่อ activeMenu === ตามที่เรากำหนด -->
            <NuxtLink class="nav-link" :class="{'active': activeMenu === 'home'}" to="/home" 
                @click="toggleMenu('home')">
                <i class="fa fa-house"></i>
                <span class="ml-1">Dashboard</span>
            </NuxtLink>
            <NuxtLink class="nav-link" :class="{'active': activeMenu === 'production'}" to="production"
                @click="toggleMenu('production')" >
                <i class="fa fa-copy"></i>
                <span class="ml-1">Record</span>
            </NuxtLink>
            <NuxtLink class="nav-link" :class="{'active': activeMenu === 'productType'}" to="/productType" 
                @click="toggleMenu('productType')">
                <i class="fa fa-file-alt"></i>
                <span class="ml-1">Product Type</span>
            </NuxtLink>
            <NuxtLink class="nav-link" :class="{'active': activeMenu === 'product'}" to="/product" 
                @click="toggleMenu('product')">
                <i class="fa fa-box"></i>
                <span class="ml-1">Product</span>
            </NuxtLink>
            <NuxtLink class="nav-link" :class="{'active': activeMenu === 'ingredients'}" to="/material" 
                @click="toggleMenu('ingredients')">
                <i class="fa fa-cogs"></i>
                <span class="ml-1">Ingredients</span>
            </NuxtLink>
            <NuxtLink class="nav-link" :class="{'active': activeMenu === 'packaging'}" to="/packaging" 
                @click="toggleMenu('packaging')">
                <i class="fa fa-box"></i>
                <span class="ml-1">Packaging</span>
            </NuxtLink>
            <NuxtLink class ="nav-link" :class="{'active': activeMenu === 'formula'}" to="/formula"
                @click="toggleMenu('product formula')">
                <i class="fa fa-receipt"></i>
                <span class="ml-1">Formula</span>
            </NuxtLink>
            <NuxtLink class="nav-link" :class="{'active': activeMenu === 'planing'}" to="/planing"
                @click="toggleMenu('planing')">
                <i class="fa fa-calendar"></i>
                <span class="ml-1">Planing</span>
            </NuxtLink>
            <NuxtLink class="nav-link" :class="{'active': activeMenu === 'report'}" to="/report"
                @click="toggleMenu('report')">
                <i class="fa fa-chart-bar"></i>
                <span class="ml-1">Report</span>
            </NuxtLink>
            <NuxtLink class="nav-link" :class="{'active': activeMenu === 'costAccounting'}" to="/costAccounting"
                @click="toggleMenu('costAccounting')">
                <i class="fa fa-dollar-sign"></i>
                <span class="ml-1">Cost Accounting</span>
            </NuxtLink>
            <NuxtLink class="nav-link" :class="{'active': activeMenu === 'users'}" to="/users"
                @click="toggleMenu('users')">
                <i class="fa fa-users"></i>
                <span class="ml-1">Users</span>
            </NuxtLink>
        </div>
    </div>
</template>