<template>
    <div class="p-6 space-y-6 bg-white rounded shadow p-4">
        <rs-card>
            <template #header>
                <div class="flex justify-between items-center">
                <h2 class="text-lg font-semibold">Planning Maintenance System (PMS)</h2>
                <rs-button variant="primary" @click="openAddPMSModal">Tambah Baru</rs-button>
                </div>
            </template>
            
            <rs-table
                :data="pmsData"
                :columns="[
                { key: 'bil', label: 'Bil' },
                { key: 'namaAset', label: 'Nama Aset' },
                { key: 'jenisPembaikan', label: 'Jenis Pembaikan' },
                { key: 'tahunPembaikan', label: 'Tahun Pembaikan' },
                { key: 'tindakan', label: 'Tindakan' }
                ]"
                :options="{
                variant: 'default',
                striped: true,
                borderless: true,
                }"
                :options-advanced="{
                sortable: true,
                responsive: true,
                filterable: true,
                defaultSort: { column: 'bil', direction: 'asc' }
                }"
                advanced
            >
                <template v-slot:jenisPembaikan="row">
                <rs-badge
                    :variant="row.value.jenisPembaikan === 'AD' ? 'info' : 'success'"
                >
                    {{ row.value.jenisPembaikan }}
                </rs-badge>
                </template>
                
                <template v-slot:tindakan="row">
                <div class="flex gap-2">
                    <rs-button variant="warning" size="sm" @click="editPMSItem(row.value)">Edit</rs-button>
                    <rs-button variant="danger" size="sm" @click="deletePMSItem(row.value.bil)">Delete</rs-button>
                </div>
                </template>
            </rs-table>
        </rs-card>
    </div>

    <!-- PMS Modal -->
    <rs-modal v-model="isPMSModalOpen" size="lg">
    <template #header>
        <h3 class="text-lg font-semibold">
        {{ isEditingPMS ? 'Kemaskini PMS' : 'Tambah PMS Baru' }}
        </h3>
    </template>

    <template #body>
        <FormKit type="form" :value="pmsForm" @submit="handlePMSSubmit">
        <div class="grid grid-cols-2 gap-4">
            <FormKit
            type="text"
            name="namaAset"
            label="Nama Aset"
            placeholder="Contoh: KM SATRIA"
            validation="required"
            />
            <FormKit
            type="select"
            name="jenisPembaikan"
            label="Jenis Pembaikan"
            :options="['AD', 'Refit']"
            validation="required"
            />
        </div>

        <div class="grid grid-cols-2 gap-4">
            <FormKit
            type="number"
            name="tahunPembaikan"
            label="Tahun Pembaikan"
            validation="required|number|min:2020|max:2050"
            min="2020"
            max="2050"
            step="1"
            />
        </div>
        </FormKit>
    </template>

    <template #footer>
        <div class="flex justify-end space-x-2">
        <rs-button variant="secondary" @click="closePMSModal">Batal</rs-button>
        <rs-button variant="primary" @click="submitPMSForm">
            {{ isEditingPMS ? 'Kemaskini' : 'Tambah' }}
        </rs-button>
        </div>
    </template>
    </rs-modal>
</template>

<script setup>
    // PMS data
    const pmsData = ref([
        {
            bil: 1,
            namaAset: 'KM SATRIA',
            jenisPembaikan: 'AD',
            tahunPembaikan: 2024,
            tindakan: 'Edit'
        },
        {
            bil: 2,
            namaAset: 'KM MARLIN',
            jenisPembaikan: 'Refit',
            tahunPembaikan: 2025,
            tindakan: 'Edit'
        },
        {
            bil: 3,
            namaAset: 'KM NAGA',
            jenisPembaikan: 'AD',
            tahunPembaikan: 2024,
            tindakan: 'Edit'
        },
        {
            bil: 4,
            namaAset: 'KM LEKIU',
            jenisPembaikan: 'AD',
            tahunPembaikan: 2026,
            tindakan: 'Edit'
        },
        {
            bil: 5,
            namaAset: 'KM JEBAT',
            jenisPembaikan: 'Refit',
            tahunPembaikan: 2027,
            tindakan: 'Edit'
        },
        {
            bil: 6,
            namaAset: 'KM MAHARAJALELA',
            jenisPembaikan: 'AD',
            tahunPembaikan: 2024,
            tindakan: 'Edit'
        },
        {
            bil: 7,
            namaAset: 'KM HANG TUAH',
            jenisPembaikan: 'Refit',
            tahunPembaikan: 2025,
            tindakan: 'Edit'
        },
        {
            bil: 8,
            namaAset: 'KM SIANGIN',
            jenisPembaikan: 'AD',
            tahunPembaikan: 2026,
            tindakan: 'Edit'
        }
    ]);

    // PMS Modal state
    const isPMSModalOpen = ref(false);
    const isEditingPMS = ref(false);
    const pmsForm = ref({
        bil: null,
        namaAset: '',
        jenisPembaikan: 'AD',
        tahunPembaikan: new Date().getFullYear()
    });

    // Open modal for adding PMS
    const openAddPMSModal = () => {
        isEditingPMS.value = false;
        pmsForm.value = {
            bil: pmsData.value.length + 1,
            namaAset: '',
            jenisPembaikan: 'AD',
            tahunPembaikan: new Date().getFullYear()
        };
        isPMSModalOpen.value = true;
    };

    // Open modal for editing PMS
    const editPMSItem = (item) => {
        isEditingPMS.value = true;
        pmsForm.value = { ...item };
        isPMSModalOpen.value = true;
    };

    // Close PMS modal
    const closePMSModal = () => {
        isPMSModalOpen.value = false;
    };

    // Submit PMS form
    const submitPMSForm = () => {
        if (isEditingPMS.value) {
            // Update existing item
            const index = pmsData.value.findIndex((i) => i.bil === pmsForm.value.bil);
            if (index !== -1) {
                pmsData.value[index] = { ...pmsForm.value };
            }
        } else {
            // Add new item
            pmsData.value.push({
                ...pmsForm.value,
                bil: pmsData.value.length + 1
            });
        }
        closePMSModal();
    };

    // Delete PMS item
    const deletePMSItem = (bil) => {
        if (confirm('Adakah anda pasti untuk memadamkan rekod ini?')) {
            const index = pmsData.value.findIndex((i) => i.bil === bil);
            if (index !== -1) {
                pmsData.value.splice(index, 1);
                // Reorder bil numbers
                pmsData.value.forEach((item, idx) => {
                    item.bil = idx + 1;
                });
            }
        }
    };

    // Handle PMS FormKit form submission
    const handlePMSSubmit = (values) => {
        pmsForm.value = {
            ...pmsForm.value,
            ...values
        };
        submitPMSForm();
    };
</script>

<style lang="scss" scoped>

</style>    