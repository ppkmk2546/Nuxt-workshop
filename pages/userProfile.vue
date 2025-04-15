<script setup>
    import config from '@/config';
    import axios from 'axios';
    import Swal from 'sweetalert2';

    definePageMeta({
        layout: 'admin'
    });

    const name = ref('');
    const username = ref('');
    const password = ref('');
    const confirmPassword = ref('');
    const level = ref('admin');

    onMounted(() => {
        fetchData();
    });

    const fetchData = async () => {
        try {
            const token = localStorage.getItem(config.token);
            const headers = {
                'Authorization': `Bearer ${token}`,
            };

            const response = await axios.get(config.apiServer + '/api/user/info', { headers });

            name.value = response.data.result.name;
            username.value = response.data.result.username;
            level.value = response.data.result.level;
        } catch (error) {
            Swal.fire({
                icon: 'error',
                title: 'Error',
                text: error.message,
            })
        }
    }

    const save = async () => {
        try {
            const token = localStorage.getItem(config.token);
            const headers = {
                'Authorization': `Bearer ${token}`,
            };

            if (password.value !== confirmPassword.value){
                Swal.fire({
                    icon: 'warning',
                    title: 'Password Mismatch',
                    text: 'Password and Confirm Password do not match.',
                    showConfirmButton: true,

                });

                return;
            }

            const payload = {
                name: name.value,
                username: username.value,
                password: password.value,
                level: level.value,
            };

            await axios.put(config.apiServer + '/api/user/update', payload, { headers });

            Swal.fire({
                icon: 'success',
                title: 'update',
                text: 'update success',
                timer: 1000,

            })
        } catch (error) {
            Swal.fire({
                icon: 'error',
                title: 'Error',
                text: error.message,
            })
        }
    }
</script>

<template>
    <div class="title">User Profile</div>
    <div class="p-4">
        <div class="form-group">
            <div>
                <label for="name">Name</label>
                <input type="text" id="name" v-model="name" class="form-control" />
            </div>

            <div class="mt-4">
                <label for="username">Username</label>
                <input type="text" id="username" v-model="username" class="form-control" />
            </div>

            <div class="mt-4">
                <label for="password">Password <span class="text-red-500">(Enter if changing)</span></label>
                <input type="password" id="password" v-model="password" class="form-control" />
            </div>

            <div class="mt-4">
                <label for="confirmPasword">ConfirmPassword <span class="text-red-500">(Enter if changing)</span></label>
                <input type="password" id="confirmPassword" v-model="confirmPassword" class="form-control" />
            </div>

            <div class="mt-4">
                <input type="radio" id="admin" v-model="level" value="admin" class="me-1" />
                <label for="admin">Admin</label>
                <input type="radio" id="user" v-model="level" value="user" class="ms-3 me-1" />
                <label for="user">User</label>
            </div>

            <div class="mt-4">
                <button class="btn btn-primary" @click="save">
                    <i class="fa fa-check mr-1"></i>
                    Save
                </button>
            </div>
        </div>
    </div>
</template>