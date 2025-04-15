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
    const id = ref('');
    const productTypes = ref([]);

    onMounted(async () => {
        await fetchData();
    });

    const fetchData = async () => {
        try {
            const res = await axios.get(config.apiServer + '/api/productType/list'); // ยิง request ไปที่ api
            productTypes.value = res.data.results;
        } catch (error) {
            Swal.fire({
                icon: 'error',
                title: 'Error',
                text: error.message,
            })
        }
    }

    // ? save
    const save = async () => {
        try {
            const payload = {
                name: name.value,
                remark: remark.value,
            }

            if (id.value === '') {
                await axios.post(config.apiServer + '/api/productType/create', payload);
            } else {
                await axios.put(config.apiServer + '/api/productType/update/' + id.value, payload);
                id.value = '';
            }

        //     if (res.data.message === 'success'){ 
        //     Swal.fire({
        //         icon: 'success',
        //         title: 'Success',
        //         text: 'Product Type created successfully.',
        //     })
        // }
            await fetchData();
            closeModal();
            Swal.fire({
                icon: 'success',
                title: 'Success',
                text: 'successfully.',
            })

    } catch (error) {
            Swal.fire({
                icon: 'error',
                title: 'Error',
                text: error.message,
            })
        }
    }

    // ? update
    const update = (productTypes) => {
        id.value = productTypes.id;
        name.value = productTypes.name;
        remark.value = productTypes.remark;
        showModal.value = true;

    }

    // ? romove 
    const remove = async (id) => {
        try{
            const button  = await Swal.fire({
                icon: 'warning',
                title: 'Are you sure?',
                text: 'You won\'t be able to revert this!',
                showCancelButton: true,
                showConfirmButton: true,
            })
            if (button.isConfirmed) {
                await axios.delete(config.apiServer + '/api/productType/remove/' + id);
                await fetchData();
            }
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
    <h1 class="title">Product Type</h1>
    <div class="p-4 ">
        <button class="btn" @click="showModal = true">
            <i class="fa fa-plus mr-1"></i>
            Add Product Type
        </button>
        <table class="table mt-3">
            <thead>
                <tr>
                    <th class="text-left">Name</th>
                    <th class="text-left">Remark</th>
                    <th class="" width="110px"></th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="productType in productTypes" :key="productType.id"> <!-- ! ยังไม่เข้าใจในส่วน :key="productType.id"--> 
                    <td>{{ productType.name }}</td>
                    <td>{{ productType.remark }}</td>
                    <td class="text-center">
                        <button class="btn btn-primary me-1" @click="update(productType)">
                            <i class="fa fa-pencil"></i>
                            <!-- Edit -->
                        </button>
                        <button class="btn btn-danger" @click="remove(productType.id)">
                            <i class="fa fa-trash"></i>
                            <!-- Delete -->
                        </button>
                    </td>
                </tr>
            </tbody>
        </table>
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