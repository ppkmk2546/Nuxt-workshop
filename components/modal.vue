<script setup>
    const emit = defineEmits(['close']);

    defineProps({
        title: String
    });

    //  ? close modal
    const closeModal = () => {
        emit('close');
    }

    //  ? ese close modal
    const escCloseModal = (event) => {
        if(event.key === 'Escape') {
            closeModal();
        }
    }

    // ? check keyboard esc
    onMounted(() => {
        window.addEventListener('keydown', escCloseModal);
    });
</script>

<template>
    <!--  ? optional เพิ่มตรงกล่อง div นอกสุด z-50 -->
    <div class="fixed inset-0 flex items-center justify-center bg-gray-300/30 shadow-lg">
        <div class="w-full max-w-lg">
            <div class="modal-title p-4 rounded-tl-xl rounded-tr-lx shadow-lg flex justify-between items-center bg-gray-700 text-gray-100">
                <h2 class="text-lg font-semibold">{{ title }}</h2>
                <button class="btn bg-white text-gray-900 border-2 border-gray-300 px-2 py-1" @click="closeModal">
                    <i class="fa fa-times"></i>
                </button>
            </div>
            <div class="modal-body bg-white p-4 rounded-bl-xl rounded-br-xl shadow-lg">
                <slot />
            </div>
        </div>
    </div>
</template>