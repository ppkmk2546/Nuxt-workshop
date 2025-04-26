<script setup>

    import Swal from 'sweetalert2';
    import axios from 'axios';
    import config from '@/config';
    import dayjs from 'dayjs';

    definePageMeta({
        layout: 'admin',
    });
    
    // 
    const showModal = ref(false);
    const materials = ref([]);
    const id = ref('');
    const name = ref('');
    const unit = ref('');
    const price = ref(0); // ? ค่าเริ่มต้นเป็น 0 ไม่ต้องการให้ มีติด - 
    const remark = ref('');

    // modal stock material
    const showModalStockMaterial = ref(false);
    const stockMaterialMaterialId = ref('');
    const listMaterials = ref([]);
    const stockMaterialQuantity = ref(0);
    const stockMaterialPrice = ref(0);
    const stockMaterialRemark = ref('');
    const stockMaterialId = ref('');

    // modal stock material history 
    const showModalStockMaterialHistory = ref(false);
    const listStockMaterials = ref ([]);

    onMounted(async () => {
        await fecthData();
    });

    const openModalStockMaterial = async () =>  {
        showModalStockMaterial.value = true;

        try {
            const res = await axios.get(`${config.apiServer}/api/material/list`);
            listMaterials.value = res.data.results;
            stockMaterialMaterialId.value = materials.value[0].id;
        } catch (error) {
            Swal.fire({
                icon: 'error',
                title: 'Error',
                text: error.message
            });
        }
    }

    const closeModalStockMaterial = () => {
        // console.log('closeModalStockMaterial ถูกเรียกใช้แล้ว'); !! ไว้ Debug การทำงานของ ฟังก์ชั่น
        showModalStockMaterial.value = false;
        // stockMaterialMaterialId.value = '';
        stockMaterialQuantity.value = 0;
        stockMaterialPrice.value = 0;
        stockMaterialRemark.value = '';
        stockMaterialId.value = '';
    };
    
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
            // * วนหาผลรวมว่าเรารับเข้า stock เท่าไหร่
            for(const material of res.data.results) {
                material.balance = 0;

                for(const stockMaterial of material.StockMaterial) {
                    material.balance += stockMaterial.quantity;
                }

            }

            materials.value = res.data.results;
        } catch (error) {
            Swal.fire({
                icon: 'error',
                title: 'Error',
                text: error.message,
            });
        }
    };

    const save = async () => {
        if (price.value < 0) {
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
            });
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
            if (button.isConfirmed){
                await axios.delete(`${config.apiServer}/api/material/remove/${id}`);
                await fecthData();
            }
        } catch (error) {
            Swal.fire({
                icon: 'error',
                title: 'Error',
                text: error.message
            });
        }
    }

    const saveStockMaterial = async () => {
        // console.log('saveStockMaterial ถูกเรียกใช้แล้ว'); !! ไว้ Debug การทำงานของ ฟังก์ชั่น
        try {
            const payload = {
                material_id: stockMaterialMaterialId.value,
                quantity: stockMaterialQuantity.value,
                price: stockMaterialPrice.value,
                remark: stockMaterialRemark.value
            };
            await axios.post(`${config.apiServer}/api/stockMaterial/create`, payload);
            closeModalStockMaterial();
            fecthData();

        } catch (error) {
            Swal.fire({
                icon: 'error',
                title: 'Error',
                text: error.message
            });
        }
    }

    // Default ราคา 
    const selectedStockMaterial = () => {
        const material = listMaterials.value.find(m => m.id === stockMaterialMaterialId.value);
        stockMaterialPrice.value = material.price;
        stockMaterialId.value = material.id;
    }

    // HistoryStock
    const fetchDataStockMaterialHistory = async () => {
        try {
            const res = await axios.get(`${config.apiServer}/api/stockMaterial/list`);
            listStockMaterials.value = res.data.results;
        } catch (error) {
            Swal.fire ({
                icon: 'error',
                title: 'Error',
                text: error.message
            });
        }
    }

    const openModalStockMaterialHistory = async () => {
        showModalStockMaterialHistory.value = true;
        await fetchDataStockMaterialHistory();
    }

    const closeModalStockMaterialHistory = () => {
        showModalStockMaterialHistory.value = false;
    }

    const removeStockMaterial = async (id) => {
        try {
            const button = await Swal.fire({
                icon: 'warning',
                title: 'Are you sure ?',
                text: 'Do you want to delete this Receive into stock?',
                showCancelButton: true,
                showConfirmButton: true
            });
            if (button.isConfirmed) {
                await axios.delete(`${config.apiServer}/api/stockMaterial/remove/${id}`);
                fetchDataStockMaterialHistory();
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
        <button class="btn me-2" @click="openModalStockMaterial">
            <i class="fa fa-arrow-circle-down mr-1"></i>
            Receive into stock
        </button>
        <button class="btn" @click="openModalStockMaterialHistory">
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

        <h1 class="mt-3 mb-1">Unit</h1>
        <input type="text" v-model="unit" class="form-control" />

        <h1 class="mt-3 mb-1">Price</h1>
        <input type="number" v-model="price" class="form-control" min="0" />

        <h1 class="mt-3 mb-1">Remark</h1>
        <input type="text" v-model="remark" class="form-control" />

        <button class="btn btn-primary mt-4 w-full" @click="save">
            <i class="fa fa-save mr-1"></i>
            save
        </button>
    </Modal>

    <Modal v-if="showModalStockMaterial" title="Receive into stock" @close="closeModalStockMaterial">
        <h1 class="mb-1">Ingredient</h1>
        <select v-model="stockMaterialMaterialId" class="form-control" @change="selectedStockMaterial"> <!-- ?  คือการ Default ราคา-->
            <option v-for="material in materials" :key="material.id" :value="material.id">
                {{ material.name }}
            </option>
        </select>

        <h1 class="mt-4 mb-1">Quantity</h1>
        <input type="number" v-model="stockMaterialQuantity" class="form-control">

        <h1 class="mt-4 mb-1">Price</h1>
        <input type="nubmer" v-model="stockMaterialPrice" class="form-control">

        <h1 class="mt-4 mb-1">Remark</h1>
        <input type="text" v-model="stockMaterialRemark" class="form-control">
        
        <button class="btn btn-primary mt-4 w-full" @click="saveStockMaterial">
            <i class="fa fa-save mr-1"></i>
            save
        </button>
    </Modal>

    <Modal v-if="showModalStockMaterialHistory" title="Stock history" @close="closeModalStockMaterialHistory" size="xl"> <!-- ? เอามาใช้ที่เราทำการ defineprops เป็น size = 'lg'-->
        <table class="table mt-3">
            <thead>
                <tr>
                    <th width="200px" class="text-left">Ingredient</th>
                    <th width="100px" class="text-right">Quantity</th>
                    <th width="100px" class="text-right">Price</th>
                    <th width="120px" class="text-left">Date</th>
                    <th width="55px"></th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="stockMaterial in listStockMaterials" :key="stockMaterial.id">
                    <td>
                        <div>{{ stockMaterial.Material.name }}</div>
                        <div v-if="stockMaterial.remark" class="text-red-500">
                            {{ stockMaterial.remark }}
                        </div>
                    </td>
                    <td class="text-right">{{ stockMaterial.quantity }}</td>
                    <td class="text-right">{{ stockMaterial.price.toLocaleString('th-TH') }}</td>
                    <td>{{ dayjs(stockMaterial.created_at).format('DD/MM/YYYY HH:MM') }}</td>
                    <td class="text-center">
                        <button class="btn btn-sm btn-danger" @click="removeStockMaterial(stockMaterial.id)">
                            <i class="fa fa-times"></i>
                        </button>
                    </td>
                </tr>
            </tbody>
        </table>


    </Modal>
</template>