<template>
    <Modal :show="show" @close="closeModal">
        <div class="p-6">
            <h2 class="text-lg font-medium text-gray-900">Edit Product</h2>

            <div class="mt-6">
                <InputLabel for="code" value="Code" />
                <TextInput
                    id="code"
                    v-model="form.code"
                    type="text"
                    class="mt-1 block w-full"
                    required
                    autofocus
                />
                <InputError :message="form.errors.code" class="mt-2" />
            </div>

            <div class="mt-6">
                <InputLabel for="name" value="Name" />
                <TextInput
                    id="name"
                    v-model="form.name"
                    type="text"
                    class="mt-1 block w-full"
                    required
                />
                <InputError :message="form.errors.name" class="mt-2" />
            </div>

            <div class="mt-6 flex justify-end">
                <SecondaryButton @click="closeModal"> Batal </SecondaryButton>

                <PrimaryButton
                    class="ms-3"
                    :class="{ 'opacity-25': false }"
                    :disabled="false"
                    @click="updateProduct"
                >
                    Update
                </PrimaryButton>
            </div>
        </div>
    </Modal>
</template>

<script setup>
import { ref, watch } from "vue";
import Modal from "@/Components/Modal.vue";
import InputLabel from "@/Components/InputLabel.vue";
import TextInput from "@/Components/TextInput.vue";
import InputError from "@/Components/InputError.vue";
import SecondaryButton from "@/Components/SecondaryButton.vue";
import PrimaryButton from "@/Components/PrimaryButton.vue";
import { router } from "@inertiajs/vue3";
import Swal from "sweetalert2";

const props = defineProps({
    product: Object,
    showModal: Boolean,
    form: Object, // Terima form state dari parent
});

const emit = defineEmits(["close", "updated"]);

const show = ref(props.showModal);

watch(
    () => props.showModal,
    (newValue) => {
        show.value = newValue;
        if (props.product) {
            props.form.code = props.product.code;
            props.form.name = props.product.name;
        } else {
            props.form.code = "";
            props.form.name = "";
        }
    }
);

const closeModal = () => {
    show.value = false;
    emit("close");
};

const updateProduct = () => {
    if (props.product?.id) {
        router.put(`/products/${props.product.id}`, props.form, {
            // Sesuaikan rute produk
            onSuccess: () => {
                Swal.fire(
                    "Berhasil!",
                    "Data produk berhasil diperbarui.",
                    "success"
                );
                emit("updated");
                closeModal();
            },
            onError: (errors) => {
                console.log(errors);
            },
        });
    }
};
</script>
