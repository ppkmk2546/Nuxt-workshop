<script setup>
    import config from '@/config';
    import axios from 'axios'; // ยิง request 
    import Swal from 'sweetalert2';

    const showModal = ref(false);

    definePageMeta({
        layout: 'admin'
    })

    const closeModal = () => {
        showModal.value = false;
    }

    const name = ref('');
    const remark = ref('');

    // save
    const save = async () => {
        try {
            const payload = {
                name: name.value,
                remark: remark.value,
            }
            const res = await axios.post(config.apiServer + '/api/productType/create', payload);
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
    <div>  
        <h1 class="text-lg">Product Type</h1>
    </div>
    <div class="mt-3">
        <button class="btn" @click="showModal = true">
            <i class="fa fa-plus mr-1"></i>
            Add Product Type
        </button>
    </div>

    <!-- @close คือชื่อ ของ emit ที่เราสร้างไว้ใน modal.vue -->
    <Modal v-if="showModal" title="Add Product Type" @close="closeModal">
        <h1>Name</h1>
        <input v-model="name" type="text" id="name" class="form-control" />

        <h1 class="mt-3">Remark</h1>
        <input v-model="remark" type="text" class="form-control" id="remark" />

        <button class="btn mt-3" @click="save">
            <i class="fa fa-check mr-1"></i>
            Save
        </button>
    </Modal>
</template>