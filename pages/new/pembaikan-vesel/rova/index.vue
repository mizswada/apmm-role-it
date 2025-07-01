<template>
    <div class="p-6 space-y-6 bg-white rounded shadow p-4">
        <!-- ROVA Overview Cards -->
        <div class="grid grid-cols-1 md:grid-cols-4 gap-4 mb-6">
            <rs-card class="bg-blue-50 border-blue-200">
                <div class="text-center">
                    <div class="text-2xl font-bold text-blue-600">{{ rovaStats.totalVessels }}</div>
                    <div class="text-sm text-gray-600">Jumlah Vesel</div>
                </div>
            </rs-card>
            <rs-card class="bg-green-50 border-green-200">
                <div class="text-center">
                    <div class="text-2xl font-bold text-green-600">{{ rovaStats.operational }}</div>
                    <div class="text-sm text-gray-600">Operasi</div>
                </div>
            </rs-card>
            <rs-card class="bg-yellow-50 border-yellow-200">
                <div class="text-center">
                    <div class="text-2xl font-bold text-yellow-600">{{ rovaStats.maintenance }}</div>
                    <div class="text-sm text-gray-600">Penyelenggaraan</div>
                </div>
            </rs-card>
            <rs-card class="bg-red-50 border-red-200">
                <div class="text-center">
                    <div class="text-2xl font-bold text-red-600">{{ rovaStats.unavailable }}</div>
                    <div class="text-sm text-gray-600">Tidak Tersedia</div>
                </div>
            </rs-card>
        </div>

        <!-- Main ROVA Table -->
        <rs-card>
            <template #header>
                <div class="flex justify-between items-center">
                    <h2 class="text-lg font-semibold">Return of Vessel Availability (ROVA)</h2>
                    <rs-button variant="primary" @click="openAddROVAModal">Tambah Vesel Baru</rs-button>
                </div>
            </template>
            
            <rs-table
                :data="rovaData"
                :columns="[
                    { key: 'bil', label: 'Bil' },
                    { key: 'namaVesel', label: 'Nama Vesel' },
                    { key: 'statusOperasi', label: 'Status Operasi' },
                    { key: 'jenisPenyelenggaraan', label: 'Jenis Penyelenggaraan' },
                    { key: 'tarikhMula', label: 'Tarikh Mula' },
                    { key: 'tarikhSelesai', label: 'Tarikh Selesai' },
                    { key: 'tempohHari', label: 'Tempoh (Hari)' },
                    { key: 'peratusSiap', label: 'Peratus Siap' },
                    { key: 'statusKesediaan', label: 'Status Kesediaan' },
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
                <template v-slot:statusOperasi="row">
                    <rs-badge
                        :variant="getStatusVariant(row.value.statusOperasi)"
                    >
                        {{ row.value.statusOperasi }}
                    </rs-badge>
                </template>

                <template v-slot:jenisPenyelenggaraan="row">
                    <rs-badge
                        :variant="row.value.jenisPenyelenggaraan === 'AD' ? 'info' : 
                                 row.value.jenisPenyelenggaraan === 'Refit' ? 'warning' : 'success'"
                    >
                        {{ row.value.jenisPenyelenggaraan }}
                    </rs-badge>
                </template>

                <template v-slot:peratusSiap="row">
                    <div class="flex items-center gap-2">
                        <rs-progress-bar 
                            :value="row.value.peratusSiap" 
                            :variant="getProgressVariant(row.value.peratusSiap)"
                            class="w-16"
                        />
                        <span class="text-sm">{{ row.value.peratusSiap }}%</span>
                    </div>
                </template>

                <template v-slot:statusKesediaan="row">
                    <rs-badge
                        :variant="getReadinessVariant(row.value.statusKesediaan)"
                    >
                        {{ row.value.statusKesediaan }}
                    </rs-badge>
                </template>
                
                <template v-slot:tindakan="row">
                    <div class="flex gap-2">
                        <rs-button variant="info" size="sm" @click="viewROVADetails(row.value)">Lihat</rs-button>
                        <rs-button variant="warning" size="sm" @click="editROVAItem(row.value)">Edit</rs-button>
                        <rs-button variant="danger" size="sm" @click="deleteROVAItem(row.value.bil)">Delete</rs-button>
                    </div>
                </template>
            </rs-table>
        </rs-card>
    </div>

    <!-- ROVA Modal -->
    <rs-modal v-model="isROVAModalOpen" size="xl">
        <template #header>
            <h3 class="text-lg font-semibold">
                {{ isEditingROVA ? 'Kemaskini ROVA' : 'Tambah ROVA Baru' }}
            </h3>
        </template>

        <template #body>
            <FormKit type="form" :value="rovaForm" @submit="handleROVASubmit">
                <div class="grid grid-cols-2 gap-4">
                    <FormKit
                        type="text"
                        name="namaVesel"
                        label="Nama Vesel"
                        placeholder="Contoh: KM SATRIA"
                        validation="required"
                    />
                    <FormKit
                        type="select"
                        name="statusOperasi"
                        label="Status Operasi"
                        :options="['Operasi', 'Penyelenggaraan', 'Tidak Tersedia', 'Latihan']"
                        validation="required"
                    />
                </div>

                <div class="grid grid-cols-2 gap-4">
                    <FormKit
                        type="select"
                        name="jenisPenyelenggaraan"
                        label="Jenis Penyelenggaraan"
                        :options="['AD', 'Refit', 'Emergency Repair', 'Preventive', 'Tidak Berkaitan']"
                        validation="required"
                    />
                    <FormKit
                        type="select"
                        name="statusKesediaan"
                        label="Status Kesediaan"
                        :options="['Sedia', 'Dalam Proses', 'Tidak Sedia', 'Menunggu Kelulusan']"
                        validation="required"
                    />
                </div>

                <div class="grid grid-cols-2 gap-4">
                    <FormKit
                        type="date"
                        name="tarikhMula"
                        label="Tarikh Mula Penyelenggaraan"
                        validation="required"
                    />
                    <FormKit
                        type="date"
                        name="tarikhSelesai"
                        label="Tarikh Selesai Penyelenggaraan"
                        validation="required"
                    />
                </div>

                <div class="grid grid-cols-2 gap-4">
                    <FormKit
                        type="number"
                        name="peratusSiap"
                        label="Peratus Siap (%)"
                        validation="required|number|min:0|max:100"
                        min="0"
                        max="100"
                        step="5"
                    />
                    <FormKit
                        type="number"
                        name="kosPenyelenggaraan"
                        label="Kos Penyelenggaraan (RM)"
                        validation="required|number|min:0"
                        min="0"
                        step="1000"
                    />
                </div>

                <FormKit
                    type="textarea"
                    name="catatan"
                    label="Catatan"
                    placeholder="Catatan tambahan mengenai status vesel..."
                    rows="3"
                />

                <div class="grid grid-cols-2 gap-4">
                    <FormKit
                        type="text"
                        name="kontraktor"
                        label="Kontraktor"
                        placeholder="Nama kontraktor penyelenggaraan"
                    />
                    <FormKit
                        type="text"
                        name="pegawaiBertanggungjawab"
                        label="Pegawai Bertanggungjawab"
                        placeholder="Nama pegawai yang bertanggungjawab"
                    />
                </div>
            </FormKit>
        </template>

        <template #footer>
            <div class="flex justify-end space-x-2">
                <rs-button variant="secondary" @click="closeROVAModal">Batal</rs-button>
                <rs-button variant="primary" @click="submitROVAForm">
                    {{ isEditingROVA ? 'Kemaskini' : 'Tambah' }}
                </rs-button>
            </div>
        </template>
    </rs-modal>

    <!-- ROVA Details Modal -->
    <rs-modal v-model="isDetailsModalOpen" size="lg">
        <template #header>
            <h3 class="text-lg font-semibold">Butiran ROVA - {{ selectedROVA?.namaVesel }}</h3>
        </template>

        <template #body>
            <div v-if="selectedROVA" class="space-y-4">
                <div class="grid grid-cols-2 gap-4">
                    <div>
                        <label class="block text-sm font-medium text-gray-700">Nama Vesel</label>
                        <p class="mt-1 text-sm text-gray-900">{{ selectedROVA.namaVesel }}</p>
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700">Status Operasi</label>
                        <rs-badge :variant="getStatusVariant(selectedROVA.statusOperasi)" class="mt-1">
                            {{ selectedROVA.statusOperasi }}
                        </rs-badge>
                    </div>
                </div>

                <div class="grid grid-cols-2 gap-4">
                    <div>
                        <label class="block text-sm font-medium text-gray-700">Jenis Penyelenggaraan</label>
                        <rs-badge :variant="selectedROVA.jenisPenyelenggaraan === 'AD' ? 'info' : 'warning'" class="mt-1">
                            {{ selectedROVA.jenisPenyelenggaraan }}
                        </rs-badge>
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700">Status Kesediaan</label>
                        <rs-badge :variant="getReadinessVariant(selectedROVA.statusKesediaan)" class="mt-1">
                            {{ selectedROVA.statusKesediaan }}
                        </rs-badge>
                    </div>
                </div>

                <div class="grid grid-cols-2 gap-4">
                    <div>
                        <label class="block text-sm font-medium text-gray-700">Tarikh Mula</label>
                        <p class="mt-1 text-sm text-gray-900">{{ formatDate(selectedROVA.tarikhMula) }}</p>
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700">Tarikh Selesai</label>
                        <p class="mt-1 text-sm text-gray-900">{{ formatDate(selectedROVA.tarikhSelesai) }}</p>
                    </div>
                </div>

                <div class="grid grid-cols-2 gap-4">
                    <div>
                        <label class="block text-sm font-medium text-gray-700">Tempoh (Hari)</label>
                        <p class="mt-1 text-sm text-gray-900">{{ selectedROVA.tempohHari }} hari</p>
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700">Peratus Siap</label>
                        <div class="flex items-center gap-2 mt-1">
                            <rs-progress-bar 
                                :value="selectedROVA.peratusSiap" 
                                :variant="getProgressVariant(selectedROVA.peratusSiap)"
                                class="w-20"
                            />
                            <span class="text-sm">{{ selectedROVA.peratusSiap }}%</span>
                        </div>
                    </div>
                </div>

                <div class="grid grid-cols-2 gap-4">
                    <div>
                        <label class="block text-sm font-medium text-gray-700">Kos Penyelenggaraan</label>
                        <p class="mt-1 text-sm text-gray-900">RM {{ formatNumber(selectedROVA.kosPenyelenggaraan) }}</p>
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700">Kontraktor</label>
                        <p class="mt-1 text-sm text-gray-900">{{ selectedROVA.kontraktor || 'Tidak dinyatakan' }}</p>
                    </div>
                </div>

                <div>
                    <label class="block text-sm font-medium text-gray-700">Pegawai Bertanggungjawab</label>
                    <p class="mt-1 text-sm text-gray-900">{{ selectedROVA.pegawaiBertanggungjawab || 'Tidak dinyatakan' }}</p>
                </div>

                <div v-if="selectedROVA.catatan">
                    <label class="block text-sm font-medium text-gray-700">Catatan</label>
                    <p class="mt-1 text-sm text-gray-900">{{ selectedROVA.catatan }}</p>
                </div>
            </div>
        </template>

        <template #footer>
            <div class="flex justify-end">
                <rs-button variant="secondary" @click="closeDetailsModal">Tutup</rs-button>
            </div>
        </template>
    </rs-modal>
</template>

<script setup>
    // ROVA data with comprehensive vessel availability information
    const rovaData = ref([
        {
            bil: 1,
            namaVesel: 'KM SATRIA',
            statusOperasi: 'Penyelenggaraan',
            jenisPenyelenggaraan: 'AD',
            tarikhMula: '2024-01-15',
            tarikhSelesai: '2024-03-15',
            tempohHari: 60,
            peratusSiap: 75,
            statusKesediaan: 'Dalam Proses',
            kosPenyelenggaraan: 2500000,
            kontraktor: 'Boustead Naval Shipyard',
            pegawaiBertanggungjawab: 'Lt. Kdr. Ahmad Zulkarnain',
            catatan: 'Penyelenggaraan AD tahunan - sistem navigasi dan komunikasi'
        },
        {
            bil: 2,
            namaVesel: 'KM MARLIN',
            statusOperasi: 'Operasi',
            jenisPenyelenggaraan: 'Tidak Berkaitan',
            tarikhMula: '2024-01-01',
            tarikhSelesai: '2024-01-01',
            tempohHari: 0,
            peratusSiap: 100,
            statusKesediaan: 'Sedia',
            kosPenyelenggaraan: 0,
            kontraktor: '',
            pegawaiBertanggungjawab: 'Lt. Kdr. Mohd Firdaus',
            catatan: 'Vesel dalam keadaan operasi penuh'
        },
        {
            bil: 3,
            namaVesel: 'KM NAGA',
            statusOperasi: 'Penyelenggaraan',
            jenisPenyelenggaraan: 'Refit',
            tarikhMula: '2024-02-01',
            tarikhSelesai: '2024-06-30',
            tempohHari: 150,
            peratusSiap: 45,
            statusKesediaan: 'Dalam Proses',
            kosPenyelenggaraan: 8500000,
            kontraktor: 'Lumut Naval Shipyard',
            pegawaiBertanggungjawab: 'Kdr. Ismail Hassan',
            catatan: 'Penyelenggaraan refit utama - penggantian enjin dan sistem propulsi'
        },
        {
            bil: 4,
            namaVesel: 'KM LEKIU',
            statusOperasi: 'Tidak Tersedia',
            jenisPenyelenggaraan: 'Emergency Repair',
            tarikhMula: '2024-03-10',
            tarikhSelesai: '2024-04-10',
            tempohHari: 30,
            peratusSiap: 20,
            statusKesediaan: 'Tidak Sedia',
            kosPenyelenggaraan: 1200000,
            kontraktor: 'Malaysia Marine and Heavy Engineering',
            pegawaiBertanggungjawab: 'Lt. Kdr. Zulkifli Omar',
            catatan: 'Pembaikan kecemasan sistem pendingin enjin'
        },
        {
            bil: 5,
            namaVesel: 'KM JEBAT',
            statusOperasi: 'Latihan',
            jenisPenyelenggaraan: 'Preventive',
            tarikhMula: '2024-03-01',
            tarikhSelesai: '2024-03-15',
            tempohHari: 15,
            peratusSiap: 90,
            statusKesediaan: 'Menunggu Kelulusan',
            kosPenyelenggaraan: 150000,
            kontraktor: 'Internal Maintenance Team',
            pegawaiBertanggungjawab: 'Lt. Kdr. Nor Azlan',
            catatan: 'Penyelenggaraan pencegahan sebelum latihan operasi'
        },
        {
            bil: 6,
            namaVesel: 'KM MAHARAJALELA',
            statusOperasi: 'Operasi',
            jenisPenyelenggaraan: 'Tidak Berkaitan',
            tarikhMula: '2024-01-01',
            tarikhSelesai: '2024-01-01',
            tempohHari: 0,
            peratusSiap: 100,
            statusKesediaan: 'Sedia',
            kosPenyelenggaraan: 0,
            kontraktor: '',
            pegawaiBertanggungjawab: 'Kdr. Abdul Rahman',
            catatan: 'Vesel dalam misi rondaan maritim'
        },
        {
            bil: 7,
            namaVesel: 'KM HANG TUAH',
            statusOperasi: 'Penyelenggaraan',
            jenisPenyelenggaraan: 'AD',
            tarikhMula: '2024-02-15',
            tarikhSelesai: '2024-04-15',
            tempohHari: 60,
            peratusSiap: 60,
            statusKesediaan: 'Dalam Proses',
            kosPenyelenggaraan: 1800000,
            kontraktor: 'Boustead Naval Shipyard',
            pegawaiBertanggungjawab: 'Lt. Kdr. Khairul Anuar',
            catatan: 'Penyelenggaraan AD - sistem senjata dan radar'
        },
        {
            bil: 8,
            namaVesel: 'KM SIANGIN',
            statusOperasi: 'Operasi',
            jenisPenyelenggaraan: 'Tidak Berkaitan',
            tarikhMula: '2024-01-01',
            tarikhSelesai: '2024-01-01',
            tempohHari: 0,
            peratusSiap: 100,
            statusKesediaan: 'Sedia',
            kosPenyelenggaraan: 0,
            kontraktor: '',
            pegawaiBertanggungjawab: 'Lt. Kdr. Mohd Faizal',
            catatan: 'Vesel dalam operasi keselamatan maritim'
        }
    ]);

    // ROVA Statistics
    const rovaStats = computed(() => {
        const total = rovaData.value.length;
        const operational = rovaData.value.filter(v => v.statusOperasi === 'Operasi').length;
        const maintenance = rovaData.value.filter(v => v.statusOperasi === 'Penyelenggaraan').length;
        const unavailable = rovaData.value.filter(v => v.statusOperasi === 'Tidak Tersedia').length;
        
        return { totalVessels: total, operational, maintenance, unavailable };
    });

    // ROVA Modal state
    const isROVAModalOpen = ref(false);
    const isEditingROVA = ref(false);
    const isDetailsModalOpen = ref(false);
    const selectedROVA = ref(null);
    
    const rovaForm = ref({
        bil: null,
        namaVesel: '',
        statusOperasi: 'Operasi',
        jenisPenyelenggaraan: 'Tidak Berkaitan',
        tarikhMula: '',
        tarikhSelesai: '',
        tempohHari: 0,
        peratusSiap: 100,
        statusKesediaan: 'Sedia',
        kosPenyelenggaraan: 0,
        kontraktor: '',
        pegawaiBertanggungjawab: '',
        catatan: ''
    });

    // Helper functions
    const getStatusVariant = (status) => {
        switch (status) {
            case 'Operasi': return 'success';
            case 'Penyelenggaraan': return 'warning';
            case 'Tidak Tersedia': return 'danger';
            case 'Latihan': return 'info';
            default: return 'secondary';
        }
    };

    const getReadinessVariant = (readiness) => {
        switch (readiness) {
            case 'Sedia': return 'success';
            case 'Dalam Proses': return 'warning';
            case 'Tidak Sedia': return 'danger';
            case 'Menunggu Kelulusan': return 'info';
            default: return 'secondary';
        }
    };

    const getProgressVariant = (percentage) => {
        if (percentage >= 80) return 'success';
        if (percentage >= 50) return 'warning';
        return 'danger';
    };

    const formatDate = (dateString) => {
        if (!dateString) return '-';
        return new Date(dateString).toLocaleDateString('ms-MY');
    };

    const formatNumber = (number) => {
        return new Intl.NumberFormat('ms-MY').format(number);
    };

    const calculateDuration = (startDate, endDate) => {
        if (!startDate || !endDate) return 0;
        const start = new Date(startDate);
        const end = new Date(endDate);
        const diffTime = Math.abs(end - start);
        return Math.ceil(diffTime / (1000 * 60 * 60 * 24));
    };

    // Open modal for adding ROVA
    const openAddROVAModal = () => {
        isEditingROVA.value = false;
        rovaForm.value = {
            bil: rovaData.value.length + 1,
            namaVesel: '',
            statusOperasi: 'Operasi',
            jenisPenyelenggaraan: 'Tidak Berkaitan',
            tarikhMula: new Date().toISOString().split('T')[0],
            tarikhSelesai: new Date().toISOString().split('T')[0],
            tempohHari: 0,
            peratusSiap: 100,
            statusKesediaan: 'Sedia',
            kosPenyelenggaraan: 0,
            kontraktor: '',
            pegawaiBertanggungjawab: '',
            catatan: ''
        };
        isROVAModalOpen.value = true;
    };

    // Open modal for editing ROVA
    const editROVAItem = (item) => {
        isEditingROVA.value = true;
        rovaForm.value = { ...item };
        isROVAModalOpen.value = true;
    };

    // View ROVA details
    const viewROVADetails = (item) => {
        selectedROVA.value = item;
        isDetailsModalOpen.value = true;
    };

    // Close ROVA modal
    const closeROVAModal = () => {
        isROVAModalOpen.value = false;
    };

    // Close details modal
    const closeDetailsModal = () => {
        isDetailsModalOpen.value = false;
        selectedROVA.value = null;
    };

    // Submit ROVA form
    const submitROVAForm = () => {
        // Calculate duration
        rovaForm.value.tempohHari = calculateDuration(rovaForm.value.tarikhMula, rovaForm.value.tarikhSelesai);
        
        if (isEditingROVA.value) {
            // Update existing item
            const index = rovaData.value.findIndex((i) => i.bil === rovaForm.value.bil);
            if (index !== -1) {
                rovaData.value[index] = { ...rovaForm.value };
            }
        } else {
            // Add new item
            rovaData.value.push({
                ...rovaForm.value,
                bil: rovaData.value.length + 1
            });
        }
        closeROVAModal();
    };

    // Delete ROVA item
    const deleteROVAItem = (bil) => {
        if (confirm('Adakah anda pasti untuk memadamkan rekod ROVA ini?')) {
            const index = rovaData.value.findIndex((i) => i.bil === bil);
            if (index !== -1) {
                rovaData.value.splice(index, 1);
                // Reorder bil numbers
                rovaData.value.forEach((item, idx) => {
                    item.bil = idx + 1;
                });
            }
        }
    };

    // Handle ROVA FormKit form submission
    const handleROVASubmit = (values) => {
        rovaForm.value = {
            ...rovaForm.value,
            ...values
        };
        submitROVAForm();
    };
</script>

<style lang="scss" scoped>
.rs-card {
    transition: all 0.3s ease;
    
    &:hover {
        transform: translateY(-2px);
        box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
    }
}

.progress-bar {
    height: 8px;
    border-radius: 4px;
}
</style>    