<script setup>
    import axios from 'axios';
    import Swal from 'sweetalert2';
    import config from '~/config';

    definePageMeta({
        layout: 'admin'
    });

    const showModalAddPackaging = ref(false);
    const id = ref('');
    const name = ref('');
    const remark = ref('');
    const packagings = ref([]);

    onMounted(async () => {
        await fecthData();
    });

    const closeModalAddPackaging = () => {
        showModalAddPackaging.value = false;
    }

    const save = async () => {
        try {
            // เตรียมข้อมูลส่งไปหลังบ้าน
            const payload = {
                name: name.value,
                remark: remark.value
            }

            if (id.value === '') {
                await axios.post(`${config.apiServer}/api/packaging/create`, payload);
            } else {
                await axios.put(`${config.apiServer}/api/packaging/update/${id.value}`, payload);
                id.value = '';
            }
            closeModalAddPackaging();
            fecthData();

        } catch (error) {
            Swal.fire({
                icon: 'error',
                title: 'Error',
                text: error.message
            });
        }
    }

    const fecthData = async () => {
        try {
            const response = await axios.get(`${config.apiServer}/api/packaging/list`);
            packagings.value = response.data.results;
        } catch (error) {
            Swal.fire({
                icon: 'error',
                title: 'Error',
                text: error.message
            });
        }
    }

    const remove = async (id) => {
        try {
            const button = await Swal.fire({
                icon: 'warning',
                title: 'Are you sure?',
                text: 'Do you want to delete this packaging?',
                showCancelButton: true,
                showConfirmButton: true
            });

            if (button.isConfirmed) {
                await axios.delete(`${config.apiServer}/api/packaging/remove/${id}`);

                Swal.fire({
                    icon: 'success',
                    title: 'Success',
                    text: 'Packaging deleted successfully!',
                    timer: 1500
                });
                
                fecthData();
            }

        } catch (error) {
            Swal.fire({
                icon: 'error',
                title: 'Error',
                text: error.message
            });
        }
    }

    const edit = (packaging) => {
        id.value = packaging.id;
        name.value = packaging.name;
        remark.value = packaging.remark;

        showModalAddPackaging.value = true;
    }
</script>

<template>
    <div class="title">Packaging</div>
    <div class="p-4">
        <button class="btn" @click="showModalAddPackaging = true">
            <i class="fa fa-plus mr-1"></i>
            Add Packaging
        </button>

        <table class="table mt-3">
            <thead>
                <tr>
                    <th width="200px" class="text-left">Name</th>
                    <th class="text-left">Remark</th>
                    <th width="110px" class=""></th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="packaging in packagings" :key="packaging.id">
                    <td>{{ packaging.name }}</td>
                    <td>{{ packaging.remark }}</td>
                    <td>
                        <button class="btn btn-primary me-1" @click="edit(packaging)">
                            <i class="fa fa-pencil"></i>
                        </button>
                        <button class="btn btn-danger" @click="remove(packaging.id)">
                            <i class="fa fa-times"></i>
                        </button>
                    </td>

                </tr>
            </tbody>
        </table>
    </div>

    <Modal v-if="showModalAddPackaging" @close="closeModalAddPackaging" title="Add Packaging">
        <h1 class="mt-4 mb-1">Name Packaging</h1>
        <input type="text" v-model="name" class="form-control">

        <h1 class="mt-4 mb-1">Remark</h1>
        <input type="text" v-model="remark" class="form-control">

        <button class="btn btn-primary mt-4 w-full" @click="save"> 
            <i class="fa fa-save mr-1"></i>
            save
        </button>
    </Modal>
</template>