<script setup>
import AuthenticatedLayout from "@/Layouts/AuthenticatedLayout.vue";
import { Head, Link, router } from "@inertiajs/vue3";
import { ref } from "vue"; // Import ref untuk membuat state

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
import Modal from "@/Components/Modal.vue"; // Asumsikan Anda memiliki komponen Modal
import InputLabel from "@/Components/InputLabel.vue";
import TextInput from "@/Components/TextInput.vue";
import InputError from "@/Components/InputError.vue";
import SecondaryButton from "@/Components/SecondaryButton.vue";
import PrimaryButton from "@/Components/PrimaryButton.vue";
import DangerButton from "@/Components/DangerButton.vue"; // Anda mungkin ingin PrimaryButton atau Button

const props = defineProps({
    data: Array,
});

const editUser = ref(false); // State untuk menampilkan/menyembunyikan modal edit
const editingUser = ref(null); // State untuk menyimpan data user yang sedang diedit
const form = ref({
    name: "",
    email: "",
});

const openEditModal = (user) => {
    editingUser.value = user;
    form.value.name = user.name;
    form.value.email = user.email;
    editUser.value = true;
};

const closeEditModal = () => {
    editUser.value = false;
    editingUser.value = null;
    form.value.name = "";
    form.value.email = "";
};

const updateUser = () => {
    if (editingUser.value) {
        router.put(``, form.value, {
            onSuccess: () => {
                closeEditModal();
                Swal.fire(
                    "Berhasil!",
                    "Data user berhasil diperbarui.",
                    "success"
                );
            },
            onError: (errors) => {
                // Tangani error di sini
            },
        });
    }
};

const handleDelete = (userId) => {
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
            router.delete(`/users/${userId}`);
            Swal.fire("Terhapus!", "User berhasil dihapus.", "success");
        }
    });
};
</script>

<template>
    <Head title="Dashboard" />

    <AuthenticatedLayout>
        <template #header>
            <h2 class="text-xl font-semibold leading-tight text-gray-800">
                Dashboard
            </h2>
        </template>

        <div class="py-12">
            <div class="mx-auto max-w-7xl sm:px-6 lg:px-8">
                <div class="overflow-hidden bg-white shadow-sm sm:rounded-lg">
                    <div class="p-6 text-gray-900">
                        Data Pengguna
                        <Table>
                            <TableHeader>
                                <TableRow>
                                    <TableHead class="w-[80px]">ID</TableHead>
                                    <TableHead>Nama</TableHead>
                                    <TableHead>Email</TableHead>
                                    <TableHead>Dibuat Pada</TableHead>
                                    <TableHead class="text-center"
                                        >Aksi</TableHead
                                    >
                                </TableRow>
                            </TableHeader>
                            <TableBody>
                                <TableRow
                                    v-for="user in props.data"
                                    :key="user.id"
                                >
                                    <TableCell class="font-medium">{{
                                        user.id
                                    }}</TableCell>
                                    <TableCell>{{ user.name }}</TableCell>
                                    <TableCell>{{ user.email }}</TableCell>
                                    <TableCell>{{
                                        new Date(
                                            user.created_at
                                        ).toLocaleDateString()
                                    }}</TableCell>
                                    <TableCell class="text-center">
                                        <button
                                            @click="openEditModal(user)"
                                            class="px-2 py-1 text-sm font-semibold text-blue-500 hover:text-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-500 rounded-md"
                                        >
                                            Edit
                                        </button>
                                        <button
                                            @click="handleDelete(user.id)"
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

        <Modal :show="editUser" @close="closeEditModal">
            <div class="p-6">
                <h2 class="text-lg font-medium text-gray-900">Edit User</h2>

                <div class="mt-6">
                    <InputLabel for="name" value="Nama" />
                    <TextInput
                        id="name"
                        v-model="form.name"
                        type="text"
                        class="mt-1 block w-full"
                        required
                        autofocus
                    />
                </div>

                <div class="mt-6">
                    <InputLabel for="email" value="Email" />
                    <TextInput
                        id="email"
                        v-model="form.email"
                        type="email"
                        class="mt-1 block w-full"
                        required
                    />
                </div>

                <div class="mt-6 flex justify-end">
                    <SecondaryButton @click="closeEditModal">
                        Batal
                    </SecondaryButton>

                    <PrimaryButton
                        class="ms-3"
                        :class="{ 'opacity-25': false }"
                        :disabled="false"
                        @click="updateUser"
                    >
                        Update
                    </PrimaryButton>
                </div>
            </div>
        </Modal>
    </AuthenticatedLayout>
</template>
