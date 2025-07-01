<template>
    <div class="p-6 space-y-6 bg-white rounded shadow p-4">
        <rs-card>
            <template #header>
                <div class="flex justify-between items-center">
                <h2 class="text-lg font-semibold">Cannibilize Asset</h2>
                <rs-button variant="primary" @click="openAddCannaidizeAssetModal">Tambah Asset</rs-button>
                </div>
            </template>
            
            <rs-table
                :data="cannaidizeAssetData"
                :columns="[
                { key: 'id', label: 'Id' },
                { key: 'pemilikAsal', label: 'Pemilik Asal' },
                { key: 'penerima', label: 'Penerima' },
                { key: 'nama', label: 'Nama Asset' },
                { key: 'serialNo', label: 'No. Siri' },
                { key: 'lokasi', label: 'Lokasi' },
                { key: 'status', label: 'Status' },
                { key: 'kaadaan', label: 'Keadaan' },
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
                defaultSort: { column: 'nama', direction: 'asc' }
                }"
                advanced
            >
                <template v-slot:status="row">
                <rs-badge
                    :variant="row.value.status === 'Tersedia' ? 'success' : 'warning'"
                >
                    {{ row.value.status }}
                </rs-badge>
                </template>
                
                <template v-slot:kaadaan="row">
                <rs-badge
                    :variant="row.value.kaadaan === 'Baik' ? 'success' : row.value.kaadaan === 'Sederhana' ? 'warning' : 'danger'"
                >
                    {{ row.value.kaadaan }}
                </rs-badge>
                </template>
                
                <template v-slot:tindakan="row">
                <div class="flex gap-2">
                    <rs-button variant="primary" size="sm" @click="viewCannaidizeAssetItem(row.value)">Lihat</rs-button>
                    <rs-button variant="warning" size="sm" @click="editCannaidizeAssetItem(row.value)">Kemaskini</rs-button>
                    <rs-button variant="danger" size="sm" @click="deleteCannaidizeAssetItem(row.value.id)">Padam</rs-button>
                </div>
                </template>
            </rs-table>
        </rs-card>
    </div>
    <!-- Cannaidize Asset Modal -->
    <rs-modal v-model="isCannaidizeAssetModalOpen" size="lg">
    <template #header>
        <h3 class="text-lg font-semibold">
        {{ isViewingCannaidizeAsset ? 'Maklumat Asset' : isEditingCannaidizeAsset ? 'Kemaskini Asset' : 'Tambah Asset' }}
        </h3>
    </template>

    <template #body>
        <!-- View Mode -->
        <div v-if="isViewingCannaidizeAsset" class="space-y-6">
        <!-- Asset Details -->
        <div class="bg-gray-50 p-4 rounded-lg">
            <div class="grid grid-cols-2 gap-4">
            <div>
                <h4 class="font-medium text-gray-700">nama Asset:</h4>
                <p class="text-lg font-semibold">{{ cannaidizeAssetForm.nama }}</p>
            </div>
            <div>
                <h4 class="font-medium text-gray-700">No. Siri:</h4>
                <p class="font-mono">{{ cannaidizeAssetForm.serialNo }}</p>
            </div>
            <div>
                <h4 class="font-medium text-gray-700">Lokasi:</h4>
                <p>{{ cannaidizeAssetForm.lokasi }}</p>
            </div>
            <div>
                <h4 class="font-medium text-gray-700">Status:</h4>
                <rs-badge
                :variant="cannaidizeAssetForm.status === 'Tersedia' ? 'success' : 'warning'"
                >
                {{ cannaidizeAssetForm.status }}
                </rs-badge>
            </div>
            <div>
                <h4 class="font-medium text-gray-700">Keadaan:</h4>
                <rs-badge
                :variant="cannaidizeAssetForm.kaadaan === 'Baik' ? 'success' : cannaidizeAssetForm.kaadaan === 'Sederhana' ? 'warning' : 'danger'"
                >
                {{ cannaidizeAssetForm.kaadaan }}
                </rs-badge>
            </div>
            <div>
                <h4 class="font-medium text-gray-700">Kapal Sumber:</h4>
                <p>{{ cannaidizeAssetForm.pemilikAsal }}</p>
            </div>
            <div>
                <h4 class="font-medium text-gray-700">Penerima:</h4>
                <p>{{ cannaidizeAssetForm.penerima }}</p>
            </div>
            <div>
                <h4 class="font-medium text-gray-700">Tarikh Pemeriksaan Terakhir:</h4>
                <p>{{ cannaidizeAssetForm.tarikhTerakhirPemeriksaan }}</p>
            </div>
            <div>
                <h4 class="font-medium text-gray-700">Tarikh Cannaidize:</h4>
                <p>{{ cannaidizeAssetForm.cannaidizeDate }}</p>
            </div>
            </div>
        </div>

        <!-- Remarks -->
        <div class="bg-white p-4 rounded-lg border">
            <h4 class="font-medium text-gray-700 mb-2">Catatan:</h4>
            <p>{{ cannaidizeAssetForm.remarks }}</p>
        </div>
        </div>
        
        <!-- Edit/Add Mode -->
        <FormKit v-else type="form" :value="cannaidizeAssetForm" @submit="handleCannaidizeAssetSubmit">
        <div class="grid grid-cols-2 gap-4">
            <FormKit
            type="text"
            nama="nama"
            label="nama Asset"
            placeholder="Contoh: Enjin Utama MTU 12V 2000 M90"
            validation="required"
            />
            <FormKit
            type="text"
            nama="serialNo"
            label="No. Siri"
            validation="required"
            />
        </div>

        <div class="grid grid-cols-2 gap-4">
            <FormKit
            type="text"
            nama="lokasi"
            label="Lokasi"
            placeholder="Contoh: Engine Room"
            validation="required"
            />
            <FormKit
            type="select"
            nama="status"
            label="Status"
            :options="['Tersedia', 'Dalam Penggunaan']"
            validation="required"
            />
        </div>

        <div class="grid grid-cols-2 gap-4">
            <FormKit
            type="select"
            nama="kaadaan"
            label="Keadaan"
            :options="['Baik', 'Sederhana', 'Rosak']"
            validation="required"
            />
            <FormKit
            type="text"
            nama="pemilikAsal"
            label="Kapal Sumber"
            placeholder="Contoh: KM SATRIA"
            validation="required"
            />
        </div>

        <div class="grid grid-cols-2 gap-4">
            <FormKit
            type="text"
            nama="penerima"
            label="Penerima"
            placeholder="Contoh: KM NAGA"
            validation="required"
            />
            <FormKit
            type="date"
            nama="tarikhTerakhirPemeriksaan"
            label="Tarikh Pemeriksaan Terakhir"
            validation="required"
            />
        </div>

        <div class="grid grid-cols-2 gap-4">
            <FormKit
            type="date"
            nama="cannaidizeDate"
            label="Tarikh Cannaidize"
            validation="required"
            />
            <FormKit
            type="textarea"
            nama="remarks"
            label="Catatan"
            placeholder="Catatan mengenai asset ini..."
            :rows="3"
            />
        </div>
        </FormKit>
    </template>

    <template #footer>
        <div class="flex justify-end space-x-2">
        <rs-button variant="secondary" @click="closeCannaidizeAssetModal">Batal</rs-button>
        <rs-button v-if="!isViewingCannaidizeAsset" variant="primary" @click="submitCannaidizeAssetForm">
            {{ isEditingCannaidizeAsset ? 'Kemaskini' : 'Tambah' }}
        </rs-button>
        </div>
    </template>
    </rs-modal>
</template>

<script setup>
    // Cannaidize Asset data
  const cannaidizeAssetData = ref([
    {
      id: 1,
      pemilikAsal: 'KM SATRIA',
      penerima: 'KM MARLIN',
      nama: 'Enjin Utama MTU 12V 2000 M90',
      serialNo: 'CA-2024-001',
      lokasi: 'Engine Room',
      status: 'Tersedia',
      tarikhTerakhirPemeriksaan: '2024-01-15',
      kaadaan: 'Baik',      
      tarikhPindahMilik: '2024-01-10',
      catatan: 'Enjin utama yang telah dibaiki dan sedia untuk digunakan'
    },
    {
      id: 2,
      pemilikAsal: 'KM MARLIN',
      penerima: 'KM NAGA',
      nama: 'Sistem Radar Furuno FAR-2117',
      serialNo: 'CA-2024-002',
      lokasi: 'Bridge',
      status: 'Dalam Penggunaan',
      tarikhTerakhirPemeriksaan: '2024-02-20',
      kaadaan: 'Baik',
      tarikhPindahMilik: '2024-02-15',
      catatan: 'Sistem radar yang telah diperbaiki dan sedang digunakan'
    },
    {
      id: 3,
      pemilikAsal: 'KM NAGA',
      penerima: 'KM SATRIA',
      nama: 'Generator Set Caterpillar 3406',
      serialNo: 'CA-2024-003',
      lokasi: 'Generator Room',
      status: 'Tersedia',
      tarikhTerakhirPemeriksaan: '2024-03-10',
      kaadaan: 'Sederhana',
      tarikhPindahMilik: '2024-03-05',
      catatan: 'Generator set yang memerlukan penyelenggaraan ringan'
    },
    {
      id: 4,
      pemilikAsal: 'KM SATRIA',
      penerima: 'KM MARLIN',
      nama: 'Sistem Komunikasi VHF Sailor 6222',
      serialNo: 'CA-2024-004',
      lokasi: 'Radio Room',
      status: 'Tersedia',
      tarikhTerakhirPemeriksaan: '2024-02-28',
      kaadaan: 'Baik',
      tarikhPindahMilik: '2024-02-25',
      catatan: 'Sistem komunikasi yang berfungsi dengan baik'
    },
    {
      id: 5,
      pemilikAsal: 'KM MARLIN',
      penerima: 'KM NAGA',
      nama: 'Panel Elektrik Utama',
      serialNo: 'CA-2024-005',
      lokasi: 'Electrical Room',
      status: 'Dalam Penggunaan',
      tarikhTerakhirPemeriksaan: '2024-03-15',
      kaadaan: 'Baik',   
      tarikhPindahMilik: '2024-03-12',
      catatan: 'Panel elektrik yang telah diperbaiki dan sedang digunakan'
    }
  ]);

  // Cannaidize Asset Modal state
  const isCannaidizeAssetModalOpen = ref(false);
  const isViewingCannaidizeAsset = ref(false);
  const isEditingCannaidizeAsset = ref(false);
  const cannaidizeAssetForm = ref({
    id: null,
    nama: '',
    serialNo: '',
    lokasi: '',
    status: 'Tersedia',
    tarikhTerakhirPemeriksaan: '',
   kaadaan: 'Baik',
    pemilikAsal: '',
    penerima: '',
    tarikhPindahMilik: '',
    catatan: ''
  });

  // Open modal for adding cannaidize asset
  const openAddCannaidizeAssetModal = () => {
    isViewingCannaidizeAsset.value = false;
    isEditingCannaidizeAsset.value = false;
    const today = new Date().toISOString().split('T')[0];
    
    cannaidizeAssetForm.value = {
      id: null,
      nama: '',
      serialNo: `CA-${new Date().getFullYear()}-${String(cannaidizeAssetData.value.length + 1).padStart(3, '0')}`,
      lokasi: '',
      status: 'Tersedia',
      tarikhTerakhirPemeriksaan: today,
     kaadaan: 'Baik',
      pemilikAsal: '',
      penerima: '',
      tarikhPindahMilik: today,
      catatan: ''
    };
    isCannaidizeAssetModalOpen.value = true;
  };

  // Open modal for viewing cannaidize asset
  const viewCannaidizeAssetItem = (item) => {
    isViewingCannaidizeAsset.value = true;
    isEditingCannaidizeAsset.value = false;
    cannaidizeAssetForm.value = { ...item };
    isCannaidizeAssetModalOpen.value = true;
  };

  // Open modal for editing cannaidize asset
  const editCannaidizeAssetItem = (item) => {
    isViewingCannaidizeAsset.value = false;
    isEditingCannaidizeAsset.value = true;
    cannaidizeAssetForm.value = { ...item };
    isCannaidizeAssetModalOpen.value = true;
  };

  // Close cannaidize asset modal
  const closeCannaidizeAssetModal = () => {
    isCannaidizeAssetModalOpen.value = false;
  };

  // Submit cannaidize asset form
  const submitCannaidizeAssetForm = () => {
    if (isEditingCannaidizeAsset.value) {
      // Update existing item
      const index = cannaidizeAssetData.value.findIndex((i) => i.id === cannaidizeAssetForm.value.id);
      if (index !== -1) {
        cannaidizeAssetData.value[index] = { ...cannaidizeAssetForm.value };
      }
    } else {
      // Add new item
      cannaidizeAssetData.value.push({
        ...cannaidizeAssetForm.value,
        id: Date.now() // Temporary id
      });
    }
    closeCannaidizeAssetModal();
  };

  // Delete cannaidize asset item
  const deleteCannaidizeAssetItem = (id) => {
    if (confirm('Adakah anda pasti untuk memadamkan rekod ini?')) {
      const index = cannaidizeAssetData.value.findIndex((i) => i.id === id);
      if (index !== -1) {
        cannaidizeAssetData.value.splice(index, 1);
      }
    }
  };

  // Handle cannaidize asset FormKit form submission
  const handleCannaidizeAssetSubmit = (values) => {
    cannaidizeAssetForm.value = {
      ...cannaidizeAssetForm.value,
      ...values
    };
    submitCannaidizeAssetForm();
  };
</script>

<style lang="scss" scoped>

</style>    