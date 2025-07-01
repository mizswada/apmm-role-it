<template>
    <div class="p-6 space-y-6">
        <div class="flex justify-between items-center">
            <h1 class="text-2xl font-semibold mb-6">Permohonan Latihan</h1>
            <rs-button variant="primary" @click="openAddModal">
                <Icon name="material-symbols:add" class="mr-1" />
                Tambah Latihan
            </rs-button>
        </div>
        <rs-card>
            <div class="p-4">           
                <rs-table
                    :data="tableData"
                    :field="['bil', 'namaPemohon', 'noTentera', 'jawatan', 'namaLatihan', 'status', 'tindakan']"
                    :options="{
                        variant: 'default',
                        striped: true,
                        borderless: true,
                        hover: true
                    }"
                    :options-advanced="{
                        sortable: true,
                        responsive: true,
                        filterable: true
                    }"
                    advanced
                >
                    <template v-slot:bil="{ text }">
                        {{ text }}
                    </template>
                    <template v-slot:namaPemohon="{ text }">
                        {{ text }}
                    </template>
                    <template v-slot:noTentera="{ text }">
                        {{ text }}
                    </template>
                    <template v-slot:jawatan="{ text }">
                        <rs-badge variant="dark">{{ text }}</rs-badge>
                    </template>
                    <template v-slot:namaLatihan="{ text }">
                        {{ text }}
                    </template>
                    <template v-slot:status="{ text }">
                        <rs-badge :variant="getBadgeVariant(text)">
                            {{ text }}
                        </rs-badge>
                    </template>
                    <template v-slot:tindakan="{ value }">
                        <rs-button
                            variant="primary"
                            size="sm"
                            @click="openViewModal(value)"
                        >
                            Lihat
                        </rs-button>
                    </template>
                </rs-table>
            </div>
        </rs-card>

        <!-- View Modal -->
        <rs-modal v-model="isViewModalOpen" size="lg">
            <template #header>
                <h3 class="text-lg font-semibold">Maklumat Latihan</h3>
            </template>
            <template #body>
                <div class="space-y-4">
                    <div class="grid grid-cols-1 gap-4">
                        <div>
                            <label class="font-medium">Nama Pemohon:</label>
                            <p>{{ selectedItem?.namaPemohon }}</p>
                        </div>
                        <div>
                            <label class="font-medium">Nama Latihan:</label>
                            <p>{{ selectedItem?.namaLatihan }}</p>
                        </div>
                        <div>
                            <label class="font-medium">Deskripsi Latihan:</label>
                            <p>{{ selectedItem?.deskripsiLatihan }}</p>
                        </div>
                        <div>
                            <label class="font-medium">Lampiran:</label>
                            <div v-if="selectedItem?.attachment" class="mt-2">
                                <rs-button
                                    variant="primary"
                                    size="sm"
                                    @click="downloadAttachment(selectedItem.attachment)"
                                >
                                    Muat Turun
                                </rs-button>
                            </div>
                            <p v-else>Tiada lampiran</p>
                        </div>
                    </div>
                </div>
            </template>
            <template #footer>
                <div class="flex justify-end gap-2">
                    <rs-button
                        variant="secondary"
                        @click="isViewModalOpen = false"
                    >
                        Tutup
                    </rs-button>
                </div>
            </template>
        </rs-modal>

        <!-- Add Modal -->
        <rs-modal v-model="isAddModalOpen" size="lg">
            <template #header>
                <h3 class="text-lg font-semibold">Tambah Latihan Baharu</h3>
            </template>
            <template #body>
                <form @submit.prevent="handleSubmit" class="space-y-4">
                    <div class="space-y-4">
                        <div>
                            <label class="block font-medium mb-1">Nama Pemohon</label>
                            <FormKit 
                                type="select"
                                v-model="formData.namaPemohon"
                                :options="userList"
                                placeholder="Pilih pemohon"
                                class="w-full"
                            />
                        </div>
                        <div>
                            <label class="block font-medium mb-1">Nama Latihan</label>
                            <FormKit 
                                v-model="formData.namaLatihan"
                                placeholder="Masukkan nama latihan"
                                class="w-full"
                            />
                        </div>
                        <div>
                            <label class="block font-medium mb-1">Penerangan Latihan</label>
                            <FormKit  type="textarea" 
                                v-model="formData.deskripsiLatihan"
                                placeholder="Masukkan penerangan latihan"
                                rows="4"
                                class="w-full"
                            />
                        </div>
                        <div>
                            <label class="block font-medium mb-1">Status</label>
                            <FormKit
                                type="select"
                                v-model="formData.status"
                                :options="statusOptions"
                                placeholder="Pilih status"
                                class="w-full"
                            />
                        </div>
                        <div>
                            <label class="block font-medium mb-1">Lampiran</label>
                            <FormKit
                                type="file"
                                @change="handleFileUpload"
                                accept=".pdf,.doc,.docx"
                                class="w-full"
                            />
                        </div>
                    </div>
                </form>
            </template>
            <template #footer>
                <div class="flex justify-end gap-2">
                    <rs-button
                        variant="secondary"
                        @click="isAddModalOpen = false"
                    >
                        Batal
                    </rs-button>
                    <rs-button
                        variant="primary"
                        @click="handleSubmit"
                    >
                        Simpan
                    </rs-button>
                </div>
            </template>
        </rs-modal>
    </div>    
    <div> 
    </div>
</template>

<script setup>
const tableData = ref([
    {
        bil: 1,
        namaPemohon: 'Lt. Kdr. Sarah binti Omar',
        noTentera: 'NT002',
        jawatan: 'Pegawai Operasi',
        namaLatihan: 'Kursus Pengurusan Projek',
        status: 'Dalam Proses',
        deskripsiLatihan: 'Kursus ini memberi pendedahan kepada peserta tentang asas pengurusan projek.',
        attachment: 'dokumen-latihan.pdf'
    }
]);

// Updated user list data
const userList = ref([
    { 
        label: 'NT001 | Kapt. Ahmad bin Ismail | Ketua Bahagian',
        value: {
            noTentera: 'NT001',
            nama: 'Kapt. Ahmad bin Ismail',
            jawatan: 'Ketua Bahagian'
        }
    },
    { 
        label: 'NT002 | Lt. Kdr. Sarah binti Omar | Pegawai Operasi',
        value: {
            noTentera: 'NT002',
            nama: 'Lt. Kdr. Sarah binti Omar',
            jawatan: 'Pegawai Operasi'
        }
    },
    { 
        label: 'NT003 | Lt. Mohd Rizal bin Hassan | Pegawai Teknikal',
        value: {
            noTentera: 'NT003',
            nama: 'Lt. Mohd Rizal bin Hassan',
            jawatan: 'Pegawai Teknikal'
        }
    }
]);

const isViewModalOpen = ref(false);
const isAddModalOpen = ref(false);
const selectedItem = ref(null);

const statusOptions = ref([
    { 
        label: 'Dalam Proses',
        value: 'Dalam Proses'
    },
    { 
        label: 'Lulus',
        value: 'Lulus'
    },
    { 
        label: 'Ditolak',
        value: 'Ditolak'
    }
]);

const formData = ref({
    namaPemohon: '',
    namaLatihan: '',
    deskripsiLatihan: '',
    status: '',
    attachment: null
});

const getBadgeVariant = (status) => {
    switch (status.toLowerCase()) {
        case 'dalam proses':
            return 'warning';
        case 'lulus':
            return 'success';
        case 'ditolak':
            return 'danger';
        default:
            return 'primary';
    }
};

const openViewModal = (item) => {
    selectedItem.value = item;
    isViewModalOpen.value = true;
};

const openAddModal = () => {
    formData.value = {
        namaPemohon: '',
        namaLatihan: '',
        deskripsiLatihan: '',
        status: '',
        attachment: null
    };
    isAddModalOpen.value = true;
};

const handleFileUpload = (event) => {
    formData.value.attachment = event.target.files[0];
};

const handleSubmit = () => {
    // Add new entry to tableData
    const newEntry = {
        bil: tableData.value.length + 1,
        namaPemohon: formData.value.namaPemohon.nama,
        noTentera: formData.value.namaPemohon.noTentera,
        jawatan: formData.value.namaPemohon.jawatan,
        namaLatihan: formData.value.namaLatihan,
        status: formData.value.status || 'Dalam Proses',
        deskripsiLatihan: formData.value.deskripsiLatihan,
        attachment: formData.value.attachment?.name || null
    };
    
    tableData.value.push(newEntry);
    isAddModalOpen.value = false;
    
    // Reset form
    formData.value = {
        namaPemohon: '',
        namaLatihan: '',
        deskripsiLatihan: '',
        status: '',
        attachment: null
    };
};

const downloadAttachment = (filename) => {
    // Implement download logic here
    console.log('Downloading:', filename);
};
</script>

<style lang="scss" scoped>
.rs-card {
    @apply bg-white rounded-lg shadow;
}
</style>