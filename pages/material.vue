<script setup>

    import Swal from 'sweetalert2';
    import axios from 'axios';
    import config from '@/config';

    definePageMeta({
        layout: 'admin',
    });
    
    // ? อาจจะลืม balance 
    const showModal = ref(false);
    const materials = ref([]);
    const id = ref('');
    const name = ref('');
    const unit = ref('');
    const price = ref(0); // ? ค่าเริ่มต้นเป็น 0 ไม่ต้องการให้ มีติด - 
    const remark = ref('');

    onMounted(async () => {
        await fecthData();
    });
    
    const closeModal = () => {
        showModal.value = false;
        name.value = '';
        unit.value = '';
        price.value = 0;
        remark.value = '';
    };

    const fecthData = async () => {
        try {
            const res = await axios.get(`${config.apiServer}/api/material/list`);
            materials.value = res.data.results;
        } catch (error) {
            Swal.fire({
                icon: 'error',
                title: 'Error',
                text: error.message,
            })
        }
    };

    const save = async () => {
        if(price.value < 0) {
            Swal.fire({
                icon: 'warning',
                title: 'Warning',
                text: 'ราคาห้ามติดลบ!'
            });
            return;
        }

        try {
            const payload = {
                name: name.value,
                unit: unit.value,
                price: price.value,
                remark: remark.value,
            };
            
            if (id.value === ''){
                await axios.post(`${config.apiServer}/api/material/create`, payload);
            } else {
                await axios.put(`${config.apiServer}/api/material/update/${id.value}`, payload);
                id.value = ''; // ! เคลียร์ค่า id
            }

            await fecthData();
            closeModal();
        } catch (error) {
            Swal.fire({
                icon: 'error',
                title: 'Error',
                text: error.message,
            })
        }
    }

    const edit = (material) => {
        id.value = material.id;
        name.value = material.name;
        unit.value = material.unit;
        price.value = material.price;
        remark.value = material.remark;

        showModal.value = true;
    }

    const remove = async (id) => {
        try {
            const button = await Swal.fire({
                icon: 'warning',
                title: 'Are you sure ?',
                text: 'Do you want to delete this ingredient?',
                showCancelButton: true,
                showConfirmButton: true
            });
            if(button.isConfirmed){
                await axios.delete(`${config.apiServer}/api/material/remove/${id}`);
                await fecthData();
            }
        } catch (error) {
            Swal.fire({
                icon: 'error',
                title: 'Error',
                text: error.message,
            })
        }
    }


    // ? ใช้ทำ mockup จำลองก่อน
    // const materials = ref([
    //     {
    //         id: 1,
    //         name: 'แป้งสาลี',
    //         remark: '',
    //         balance: 20,
    //         unit: 'kg',
    //         pirce: 100,

    //     },
    //     {
    //         id: 2,
    //         name: 'น้ำตาล',
    //         remark: '',
    //         balance: 20,
    //         unit: 'kg',
    //         pirce: 100,

    //     },
    //     {
    //         id: 3,
    //         name: 'นมผง',
    //         remark: '',
    //         balance: 20,
    //         unit: 'kg',
    //         pirce: 100,

    //     },
    // ])
</script>

<template>
    <div class="title">Ingredient</div>
    <div class="p-4">
        <button class="btn me-2" @click="showModal = true">
            <i class="fa fa-plus mr-1"></i>
            Add Ingredient
        </button>
        <button class="btn me-2">
            <i class="fa fa-arrow-circle-down mr-1"></i>
            Receive into stock
        </button>
        <button class="btn">
            <i class="fa fa-history mr-1"></i>
            Stock history
        </button>

        <table class="table mt-4">
            <thead>
                <tr>
                    <th class="text-left">Name</th>
                    <th class="text-left">Remark</th>
                    <th class="text-right">balance</th>
                    <th class="text-right">Unit</th>
                    <th class="text-right">price</th>
                    <th class="text-right">Latest date</th>
                    <th width="110px"></th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="material in materials" :key="material.id">
                    <td>{{ material.name }}</td>
                    <td>{{ material.remark }}</td>
                    <td class="text-right">{{ material.balance }}</td>
                    <td class="text-right">{{ material.unit }}</td>
                    <td class="text-right">{{ material.price.toLocaleString('th-TH')}}</td> <!-- ? การใส่ ลูกน้ำ โดยใช้ LocalString-->
                    <td class="text-right">15/04/2025 3:00</td>
                    <td class="text-center">
                        <button class="btn btn-praimary me-1" @click="edit(material)"> <!-- ? โยน material เข้าไป -->
                            <i class="fa fa-pencil"></i>
                        </button>
                        <button class="btn btn-danger me-1" @click="remove(material.id)"> <!-- ? โยน id เข้าไป-->
                            <i class="fa fa-times"></i>
                        </button>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>

    <Modal v-if="showModal" title="Ingredient" @close="closeModal">
        <h1>Name</h1>
        <input type="text" v-model="name" class="form-control" />

        <h1 class="mt-3">Unit</h1>
        <input type="text" v-model="unit" class="form-control" />

        <h1 class="mt-3">Price</h1>
        <input type="number" v-model="price" class="form-control" min="0" />

        <h1 class="mt-3">Remark</h1>
        <input type="text" v-model="remark" class="form-control" />

        <button class="btn btn-primary mt-3" @click="save">
            <i class="fa fa-save mr-1"></i>
            save
        </button>
    </Modal>
</template>