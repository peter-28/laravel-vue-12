<script setup>
import AuthenticatedLayout from "@/Layouts/AuthenticatedLayout.vue";
import { Head, Link, router, usePage } from "@inertiajs/vue3";
import { ref } from "vue";
import { Button } from "@/components/ui/button";
import EditProductModal from "@/Components/ModalEditProduct.vue";
import { ChevronRight } from "lucide-vue-next";
import {
    Table,
    TableBody,
    TableCaption,
    TableCell,
    TableHead,
    TableHeader,
    TableRow,
} from "@/Components/ui/table";
import Swal from "sweetalert2";
import { data } from "autoprefixer";

const props = defineProps({
    data: Array,
});

const showEditModal = ref(false);
const editingProduct = ref(null);
const form = ref({
    code: "",
    name: "",
    qty: "",
    price: "",
    description: "",
});

const openEditProductModal = (product) => {
    editingProduct.value = product;
    form.value.code = product.code;
    form.value.name = product.name;
    showEditModal.value = true;
};

const closeEditModal = () => {
    showEditModal.value = false;
    editingProduct.value = null;
    form.value.code = "";
    form.value.name = "";
};

const handleProductUpdated = () => {
    usePage().reload(); // Atau cara lain untuk memperbarui data
};

const handleDelete = (productId) => {
    Swal.fire({
        title: "Apakah Anda yakin?",
        text: "Anda tidak akan dapat mengembalikan ini!",
        icon: "warning",
        showCancelButton: true,
        confirmButtonColor: "#3085d6",
        cancelButtonColor: "#d33",
        confirmButtonText: "Ya, hapus!",
        cancelButtonText: "Batal",
    }).then((result) => {
        if (result.isConfirmed) {
            router.delete(`/products/${productId}`); // Sesuaikan rute produk
            Swal.fire("Terhapus!", "Produk berhasil dihapus.", "success");
        }
    });
};
</script>

<template>
    <Head title="Product" />
    <AuthenticatedLayout>
        <template #header>
            <h2 class="text-xl font-semibold leading-tight text-gray-800">
                Master Product
            </h2>
        </template>

        <div class="py-12">
            <div class="mx-auto max-w-7xl sm:px-6 lg:px-8">
                <div class="overflow-hidden bg-white shadow-sm sm:rounded-lg">
                    <div class="p-6 text-gray-900">
                        <div class="justify-between">
                            <h5>Data Product</h5>
                            <Button variant="outline" size="icon">
                                <ChevronRight class="w-4 h-4" />Create
                            </Button>
                        </div>
                        <Table>
                            <TableHeader>
                                <TableRow>
                                    <TableHead class="w-[80px]">ID</TableHead>
                                    <TableHead>Code</TableHead>
                                    <TableHead>Name</TableHead>
                                    <TableHead>Qty</TableHead>
                                    <TableHead>Price</TableHead>
                                    <TableHead>Description</TableHead>
                                    <TableHead class="text-center"
                                        >Aksi</TableHead
                                    >
                                </TableRow>
                            </TableHeader>
                            <TableBody>
                                <TableRow
                                    v-for="product in props.data"
                                    :key="product.id"
                                >
                                    <TableCell class="font-medium">{{
                                        product.id
                                    }}</TableCell>
                                    <TableCell>{{ product.code }}</TableCell>
                                    <TableCell>{{ product.name }}</TableCell>
                                    <TableCell>{{ product.qty }}</TableCell>
                                    <TableCell>{{ product.price }}</TableCell>
                                    <TableCell>{{
                                        product.description
                                    }}</TableCell>
                                    <TableCell class="text-center">
                                        <button
                                            @click="
                                                openEditProductModal(product)
                                            "
                                            class="px-2 py-1 text-sm font-semibold text-blue-500 hover:text-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-500 rounded-md"
                                        >
                                            Edit
                                        </button>
                                        <button
                                            @click="handleDelete(product.id)"
                                            class="ml-2 px-2 py-1 text-sm font-semibold text-red-500 hover:text-red-700 focus:outline-none focus:ring-2 focus:ring-red-500 rounded-md"
                                        >
                                            Delete
                                        </button>
                                    </TableCell>
                                </TableRow>
                            </TableBody>
                        </Table>
                    </div>
                </div>
            </div>
        </div>

        <EditProductModal
            :product="editingProduct"
            :showModal="showEditModal"
            @close="closeEditModal"
            @updated="handleProductUpdated"
            :form="form"
        />
    </AuthenticatedLayout>
</template>
