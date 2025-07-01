<script setup>
  definePageMeta({
    title: "Kapal Profile",
    layout: "default",
    middleware: ["auth"],
  });

  // Mock data (example only)
  const kapalImage = "@/assets/image/vessels/kelas_langkawi.png"; // Replace with your real image source
  const statusKapal = "OPS";
  const nextAdDate = "2025-06-01";
  const nextPrefitDate = "2026-07-15";
  const totalCrew = 35;

  const tabs = [
    { name: "Profile", key: "profile" },
    { name: "Rupacara Aset", key: "rupacara" },
    { name: "ROVA", key: "rova" },    
    { name: "Aset", key: "osl" }, 
    { name: "Stock", key: "stock" },  
    { name: "PMS", key: "pms" },
    { name: "Kad Kerja", key: "jobcard" },
    { name: "Krew", key: "krew" },
    
  ];

  const field = ref(['tarikhKemaskini', 'penerangan','tindakan']);
  const fieldKrew = ref(['BIL', 'noTentera', 'nama', 'bahagian','tindakan']);


  const activeTab = ref("profile");

  // Rupacara data
  const rupacaraData = ref([
    {
      id: 1,
      tarikhKemaskini: "2024-03-20",
      penerangan: "Penggantian enjin utama dan sistem kawalan kapal",
      portView: "@/assets/img/vessels/port_view.png",
      starboardView: "@/assets/img/vessels/starboard_view.png",
      forwardView: "@/assets/img/vessels/forward_view.png",
      afterView: "@/assets/img/vessels/after_view.png",
      engineRoom: "@/assets/img/vessels/engine_room_1.jpg",
      closedBridge: "@/assets/img/vessels/closed_bridge_1.jpg"
    },
    {
      id: 2,
      tarikhKemaskini: "2024-03-15",
      penerangan: "Penambahbaikan sistem radar dan komunikasi",
      portView: "@/assets/img/vessels/port_view_2.jpg",
      starboardView: "@/assets/img/vessels/starboard_view_2.jpg",
      forwardView: "@/assets/img/vessels/forward_view_2.jpg",
      afterView: "@/assets/img/vessels/after_view_2.jpg",
      engineRoom: "@/assets/img/vessels/engine_room_2.jpg",
      closedBridge: "@/assets/img/vessels/closed_bridge_2.jpg"
    }
  ]);

  // Sort data in descending order by tarikhKemaskini
  const sortedRupacaraData = computed(() => {
    return [...rupacaraData.value].sort((a, b) => {
      return new Date(b.tarikhKemaskini) - new Date(a.tarikhKemaskini);
    });
  });

  // Modal state
  const isRupacaraModalOpen = ref(false);
  const isViewingRupacara = ref(false);
  const rupacaraForm = ref({
    id: null,
    tarikhKemaskini: new Date().toISOString().split('T')[0],
    penerangan: "",
    portView: null,
    starboardView: null,
    forwardView: null,
    afterView: null,
    engineRoom: null,
    closedBridge: null
  });

  // Open modal for adding rupacara
  const openAddRupacaraModal = () => {
    isViewingRupacara.value = false;
    rupacaraForm.value = {
      id: null,
      tarikhKemaskini: new Date().toISOString().split('T')[0],
      penerangan: "",
      portView: null,
      starboardView: null,
      forwardView: null,
      afterView: null,
      engineRoom: null,
      closedBridge: null
    };
    isRupacaraModalOpen.value = true;
  };

  // Open modal for viewing rupacara
  const openViewRupacaraModal = (item) => {
    isViewingRupacara.value = true;
    rupacaraForm.value = { ...item };
    isRupacaraModalOpen.value = true;
  };

  // Close modal
  const closeRupacaraModal = () => {
    isRupacaraModalOpen.value = false;
  };

  // Submit rupacara form
  const submitRupacaraForm = () => {
    // Add new item
    rupacaraData.value.push({
      ...rupacaraForm.value,
      id: Date.now() // Temporary ID
    });
    closeRupacaraModal();
  };

  // Handle FormKit form submission
  const handleRupacaraSubmit = (values) => {
    rupacaraForm.value = {
      ...rupacaraForm.value,
      ...values
    };
    submitRupacaraForm();
  };

  // ROVA data
  const rovaData = ref([
    {
      id: 1,
      bulan: "Januari 2024",
      statusKapal: "Operational",
      tarikhMula: "2024-01-01",
      tarikhTamat: "2024-01-31",
      peratus: 85,
      catatan: "Kapal beroperasi dengan baik sepanjang bulan"
    },
    {
      id: 2,
      bulan: "Februari 2024",
      statusKapal: "Maintenance",
      tarikhMula: "2024-02-01",
      tarikhTamat: "2024-02-29",
      peratus: 45,
      catatan: "Kapal menjalani penyelenggaraan berjadual"
    },
    {
      id: 3,
      bulan: "Mac 2024",
      statusKapal: "Operational",
      tarikhMula: "2024-03-01",
      tarikhTamat: "2024-03-31",
      peratus: 92,
      catatan: "Kapal beroperasi dengan prestasi tinggi"
    }
  ]);

  // Sort ROVA data in descending order by tarikhMula
  const sortedRovaData = computed(() => {
    return [...rovaData.value].sort((a, b) => {
      return new Date(b.tarikhMula) - new Date(a.tarikhMula);
    });
  });

  // ROVA Modal state
  const isRovaModalOpen = ref(false);
  const isViewingRova = ref(false);
  const isEditingRova = ref(false);
  const rovaForm = ref({
    id: null,
    bulan: "",
    statusKapal: "Operational",
    tarikhMula: "",
    tarikhTamat: "",
    peratus: 0,
    catatan: ""
  });

  // Open modal for adding ROVA
  const openAddRovaModal = () => {
    isViewingRova.value = false;
    isEditingRova.value = false;
    const today = new Date();
    const firstDay = new Date(today.getFullYear(), today.getMonth(), 1);
    const lastDay = new Date(today.getFullYear(), today.getMonth() + 1, 0);
    
    rovaForm.value = {
      id: null,
      bulan: `${getMonthName(today.getMonth())} ${today.getFullYear()}`,
      statusKapal: "Operational",
      tarikhMula: firstDay.toISOString().split('T')[0],
      tarikhTamat: lastDay.toISOString().split('T')[0],
      peratus: 0,
      catatan: ""
    };
    isRovaModalOpen.value = true;
  };

  // Helper function to get month name
  function getMonthName(monthIndex) {
    const months = [
      "Januari", "Februari", "Mac", "April", "Mei", "Jun",
      "Julai", "Ogos", "September", "Oktober", "November", "Disember"
    ];
    return months[monthIndex];
  }

  // Open modal for viewing ROVA
  const openViewRovaModal = (item) => {
    isViewingRova.value = true;
    isEditingRova.value = false;
    rovaForm.value = { ...item };
    isRovaModalOpen.value = true;
  };

  // Open modal for editing ROVA
  const openEditRovaModal = (item) => {
    isViewingRova.value = false;
    isEditingRova.value = true;
    rovaForm.value = { ...item };
    isRovaModalOpen.value = true;
  };

  // Close ROVA modal
  const closeRovaModal = () => {
    isRovaModalOpen.value = false;
  };

  // Submit ROVA form
  const submitRovaForm = () => {
    if (isEditingRova.value) {
      // Update existing item
      const index = rovaData.value.findIndex((i) => i.id === rovaForm.value.id);
      if (index !== -1) {
        rovaData.value[index] = { ...rovaForm.value };
      }
    } else {
      // Add new item
      rovaData.value.push({
        ...rovaForm.value,
        id: Date.now() // Temporary ID
      });
    }
    closeRovaModal();
  };

  // Delete ROVA item
  const deleteRovaItem = (id) => {
    if (confirm('Adakah anda pasti untuk memadamkan rekod ini?')) {
      const index = rovaData.value.findIndex((i) => i.id === id);
      if (index !== -1) {
        rovaData.value.splice(index, 1);
      }
    }
  };

  // Handle ROVA FormKit form submission
  const handleRovaSubmit = (values) => {
    rovaForm.value = {
      ...rovaForm.value,
      ...values
    };
    submitRovaForm();
  };

  // Vessel profile data
  const vesselProfile = ref({
    generalInfo: {
      name: "KD KEDAH",
      pennantNumber: "F171",
      class: "KEDAH Class",
      type: "Patrol Vessel",
      commissioned: "2006-06-12",
      builder: "Boustead Naval Shipyard Sdn. Bhd.",
      homePort: "Lumut, Perak"
    },
    specifications: {
      displacement: "1,850 tons",
      length: "91.1 meters",
      beam: "12.5 meters",
      draft: "3.4 meters",
      propulsion: "2 × MTU 16V 4000 M90 diesel engines",
      speed: "22 knots (maximum)",
      range: "5,000 nautical miles at 12 knots",
      complement: "68 personnel (8 officers, 60 enlisted)"
    },
    armament: {
      mainGun: "1 × Bofors 57mm Mk3 naval gun",
      secondaryGuns: "2 × MSI Defence 30mm cannon",
      missiles: "MBDA Exocet MM40 Block II anti-ship missiles",
      torpedoes: "J+S torpedo launcher system",
      other: "Decoy launchers, Electronic warfare suite"
    },
    electronics: {
      radar: "Thales SMART-S Mk2 3D surveillance radar",
      sonar: "Thales Kingklip hull-mounted sonar",
      combatSystem: "Atlas TACTICOS combat management system",
      communication: "Integrated communications system with SATCOM capability"
    },
    maintenance: {
      lastMaintenance: "2023-09-15",
      nextScheduled: "2024-09-15",
      certifications: "Lloyd's Register Classification, MARPOL compliance"
    }
  });

  // PMS data
  const pmsData = ref([
    {
      id: 1,
      type: "AD",
      year: 2024,
      location: "Lumut, Perak",
      description: "Penyelenggaraan tahunan kapal"
    },
    {
      id: 2,
      type: "AD",
      year: 2025,
      location: "Lumut, Perak",
      description: "Penyelenggaraan tahunan kapal"
    },
    {
      id: 3,
      type: "AD",
      year: 2026,
      location: "Lumut, Perak",
      description: "Penyelenggaraan tahunan kapal"
    },
    {
      id: 4,
      type: "AD",
      year: 2027,
      location: "Lumut, Perak",
      description: "Penyelenggaraan tahunan kapal"
    },
    {
      id: 5,
      type: "Refit",
      year: 2028,
      location: "Lumut, Perak",
      description: "Penyelenggaraan prefit kapal"
    },
    {
      id: 6,
      type: "AMP",
      year: 2029,
      location: "Lumut, Perak",
      description: "Penyelenggaraan sistem elektronik kapal"
    },
    {
      id: 7,
      type: "AD",
      year: 2030,
      location: "Lumut, Perak",
      description: "Penyelenggaraan tahunan kapal"
    },
    {
      id: 8,
      type: "Refit",
      year: 2031,
      location: "Lumut, Perak",
      description: "Penyelenggaraan refit lengkap kapal"
    },
    {
      id: 9,
      type: "AD",
      year: 2032,
      location: "Lumut, Perak",
      description: "Penyelenggaraan tahunan kapal"
    },
    {
      id: 10,
      type: "AMP",
      year: 2033,
      location: "Lumut, Perak",
      description: "Penyelenggaraan sistem pendorongan kapal"
    }
  ]);

  // Ship Equipment data
  const shipEquipmentData = ref([
    {
      id: 1,
      equipmentNo: "EQ-2024-001",
      name: "Sistem Radar",
      category: "Elektronik",
      manufacturer: "Thales",
      model: "SMART-S Mk2",
      serialNumber: "TH-2345-8976",
      installationDate: "2006-06-15",
      status: "Beroperasi"
    },
    {
      id: 2,
      equipmentNo: "EQ-2024-002",
      name: "Sonar Sistem",
      category: "Elektronik",
      manufacturer: "Thales",
      model: "Kingklip",
      serialNumber: "TH-5678-1234",
      installationDate: "2006-06-20",
      status: "Beroperasi"
    },
    {
      id: 3,
      equipmentNo: "EQ-2024-003",
      name: "Enjin Utama 1",
      category: "Pendorongan",
      manufacturer: "MTU",
      model: "16V 4000 M90",
      serialNumber: "MTU-1234-5678",
      installationDate: "2006-05-10",
      status: "Beroperasi"
    },
    {
      id: 4,
      equipmentNo: "EQ-2024-004",
      name: "Enjin Utama 2",
      category: "Pendorongan",
      manufacturer: "MTU",
      model: "16V 4000 M90",
      serialNumber: "MTU-1234-5679",
      installationDate: "2006-05-10",
      status: "Penyelenggaraan"
    },
    {
      id: 5,
      equipmentNo: "EQ-2024-005",
      name: "Senjata Utama",
      category: "Persenjataan",
      manufacturer: "Bofors",
      model: "57mm Mk3",
      serialNumber: "BF-9876-5432",
      installationDate: "2006-07-05",
      status: "Beroperasi"
    },
    {
      id: 6,
      equipmentNo: "EQ-2024-006",
      name: "Sistem Komunikasi",
      category: "Komunikasi",
      manufacturer: "Harris",
      model: "RF-7800",
      serialNumber: "HR-4567-8901",
      installationDate: "2006-06-25",
      status: "Beroperasi"
    }
  ]);

  // Stock/Inventory data
  const stockData = ref([
    {
      id: 1,
      stockNo: "STK-2024-001",
      name: "Minyak Enjin",
      category: "Alat Ganti",
      quantity: 50,
      unit: "Liter",
      location: "Stor A",
      lastUpdated: "2024-03-15",
      minimumStock: 20,
      status: "Mencukupi"
    },
    {
      id: 2,
      stockNo: "STK-2024-002",
      name: "Penapis Minyak",
      category: "Alat Ganti",
      quantity: 15,
      unit: "Unit",
      location: "Stor B",
      lastUpdated: "2024-03-10",
      minimumStock: 5,
      status: "Mencukupi"
    },
    {
      id: 3,
      stockNo: "STK-2024-003",
      name: "Lampu Isyarat",
      category: "Elektrik",
      quantity: 8,
      unit: "Unit",
      location: "Stor C",
      lastUpdated: "2024-02-28",
      minimumStock: 10,
      status: "Kurang"
    },
    {
      id: 4,
      stockNo: "STK-2024-004",
      name: "Tali Tambat",
      category: "Peralatan Deck",
      quantity: 3,
      unit: "Gulung",
      location: "Stor D",
      lastUpdated: "2024-03-05",
      minimumStock: 2,
      status: "Mencukupi"
    },
    {
      id: 5,
      stockNo: "STK-2024-005",
      name: "Alat Ganti Radar",
      category: "Elektronik",
      quantity: 2,
      unit: "Set",
      location: "Stor E",
      lastUpdated: "2024-03-20",
      minimumStock: 1,
      status: "Mencukupi"
    },
    {
      id: 6,
      stockNo: "STK-2024-006",
      name: "Bateri",
      category: "Elektrik",
      quantity: 5,
      unit: "Unit",
      location: "Stor C",
      lastUpdated: "2024-03-18",
      minimumStock: 8,
      status: "Kurang"
    }
  ]);

  // Stock Modal state
  const isStockModalOpen = ref(false);
  const isViewingStock = ref(false);
  const isEditingStock = ref(false);
  const stockForm = ref({
    id: null,
    stockNo: "",
    name: "",
    category: "",
    quantity: 0,
    unit: "",
    location: "",
    lastUpdated: "",
    minimumStock: 0,
    status: "Mencukupi"
  });

  // Open modal for adding stock
  const openAddStockModal = () => {
    isViewingStock.value = false;
    isEditingStock.value = false;
    const today = new Date().toISOString().split('T')[0];
    const newStockNo = `STK-${new Date().getFullYear()}-${String(stockData.value.length + 1).padStart(3, '0')}`;
    
    stockForm.value = {
      id: null,
      stockNo: newStockNo,
      name: "",
      category: "",
      quantity: 0,
      unit: "",
      location: "",
      lastUpdated: today,
      minimumStock: 0,
      status: "Mencukupi"
    };
    isStockModalOpen.value = true;
  };

  // Open modal for viewing stock
  const viewStockItem = (item) => {
    isViewingStock.value = true;
    isEditingStock.value = false;
    stockForm.value = { ...item };
    isStockModalOpen.value = true;
  };

  // Open modal for editing stock
  const editStockItem = (item) => {
    isViewingStock.value = false;
    isEditingStock.value = true;
    stockForm.value = { ...item };
    isStockModalOpen.value = true;
  };

  // Close stock modal
  const closeStockModal = () => {
    isStockModalOpen.value = false;
  };

  // Submit stock form
  const submitStockForm = () => {
    // Update status based on quantity and minimum stock
    stockForm.value.status = stockForm.value.quantity >= stockForm.value.minimumStock ? "Mencukupi" : "Kurang";
    
    if (isEditingStock.value) {
      // Update existing item
      const index = stockData.value.findIndex((i) => i.id === stockForm.value.id);
      if (index !== -1) {
        stockData.value[index] = { ...stockForm.value };
      }
    } else {
      // Add new item
      stockData.value.push({
        ...stockForm.value,
        id: Date.now() // Temporary ID
      });
    }
    closeStockModal();
  };

  // Delete stock item
  const deleteStockItem = (id) => {
    if (confirm('Adakah anda pasti untuk memadamkan rekod ini?')) {
      const index = stockData.value.findIndex((i) => i.id === id);
      if (index !== -1) {
        stockData.value.splice(index, 1);
      }
    }
  };

  // Handle stock FormKit form submission
  const handleStockSubmit = (values) => {
    stockForm.value = {
      ...stockForm.value,
      ...values
    };
    submitStockForm();
  };

  // Stock categories for filtering
  const stockCategories = ["Semua", "Alat Ganti", "Elektrik", "Elektronik", "Peralatan Deck", "Lain-lain"];
  const selectedStockCategory = ref("Semua");

  // Filtered stock data
  const filteredStockData = computed(() => {
    if (selectedStockCategory.value === "Semua") {
      return stockData.value;
    }
    return stockData.value.filter(item => item.category === selectedStockCategory.value);
  });

  // Equipment Modal state
  const isEquipmentModalOpen = ref(false);
  const isViewingEquipment = ref(false);
  const isEditingEquipment = ref(false);
  const equipmentForm = ref({
    id: null,
    equipmentNo: "",
    name: "",
    category: "",
    manufacturer: "",
    model: "",
    serialNumber: "",
    installationDate: "",
    status: "Beroperasi"
  });

  // Open modal for adding equipment
  const openAddEquipmentModal = () => {
    isViewingEquipment.value = false;
    isEditingEquipment.value = false;
    const today = new Date().toISOString().split('T')[0];
    const newEquipmentNo = `EQ-${new Date().getFullYear()}-${String(shipEquipmentData.value.length + 1).padStart(3, '0')}`;
    
    equipmentForm.value = {
      id: null,
      equipmentNo: newEquipmentNo,
      name: "",
      category: "",
      manufacturer: "",
      model: "",
      serialNumber: "",
      installationDate: today,
      status: "Beroperasi"
    };
    isEquipmentModalOpen.value = true;
  };

  // Open modal for viewing equipment
  const viewEquipmentItem = (item) => {
    isViewingEquipment.value = true;
    isEditingEquipment.value = false;
    equipmentForm.value = { ...item };
    isEquipmentModalOpen.value = true;
  };

  // Open modal for editing equipment
  const editEquipmentItem = (item) => {
    isViewingEquipment.value = false;
    isEditingEquipment.value = true;
    equipmentForm.value = { ...item };
    isEquipmentModalOpen.value = true;
  };

  // Close equipment modal
  const closeEquipmentModal = () => {
    isEquipmentModalOpen.value = false;
  };

  // Submit equipment form
  const submitEquipmentForm = () => {
    if (isEditingEquipment.value) {
      // Update existing item
      const index = shipEquipmentData.value.findIndex((i) => i.id === equipmentForm.value.id);
      if (index !== -1) {
        shipEquipmentData.value[index] = { ...equipmentForm.value };
      }
    } else {
      // Add new item
      shipEquipmentData.value.push({
        ...equipmentForm.value,
        id: Date.now() // Temporary ID
      });
    }
    closeEquipmentModal();
  };

  // Delete equipment item
  const deleteEquipmentItem = (id) => {
    if (confirm('Adakah anda pasti untuk memadamkan rekod ini?')) {
      const index = shipEquipmentData.value.findIndex((i) => i.id === id);
      if (index !== -1) {
        shipEquipmentData.value.splice(index, 1);
      }
    }
  };

  // Handle equipment FormKit form submission
  const handleEquipmentSubmit = (values) => {
    equipmentForm.value = {
      ...equipmentForm.value,
      ...values
    };
    submitEquipmentForm();
  };

  // Equipment categories for filtering
  const equipmentCategories = ["Semua", "Elektronik", "Pendorongan", "Persenjataan", "Komunikasi", "Lain-lain"];
  const selectedCategory = ref("Semua");

  // Filtered equipment data
  const filteredEquipmentData = computed(() => {
    if (selectedCategory.value === "Semua") {
      return shipEquipmentData.value;
    }
    return shipEquipmentData.value.filter(item => item.category === selectedCategory.value);
  });

  // PMS Modal state
  const isMaintenanceModalOpen = ref(false);
  const isViewingMaintenance = ref(false);
  const isEditingMaintenance = ref(false);
  const maintenanceForm = ref({
    id: null,
    type: "AD",
    year: new Date().getFullYear(),
    location: "",
    description: ""
  });

  // Open modal for adding maintenance
  const openAddMaintenanceModal = () => {
    isViewingMaintenance.value = false;
    maintenanceForm.value = {
      id: null,
      type: "AD",
      year: new Date().getFullYear(),
      location: "",
      description: ""
    };
    isMaintenanceModalOpen.value = true;
  };

  // Open modal for viewing maintenance
  const viewMaintenanceItem = (item) => {
    isViewingMaintenance.value = true;
    isEditingMaintenance.value = false;
    maintenanceForm.value = { ...item };
    isMaintenanceModalOpen.value = true;
  };

  // Open modal for editing maintenance
  const editMaintenanceItem = (item) => {
    isViewingMaintenance.value = false;
    isEditingMaintenance.value = true;
    maintenanceForm.value = { ...item };
    isMaintenanceModalOpen.value = true;
  };

  // Close maintenance modal
  const closeMaintenanceModal = () => {
    isMaintenanceModalOpen.value = false;
  };

  // Submit maintenance form
  const submitMaintenanceForm = () => {
    if (isEditingMaintenance.value) {
      // Update existing item
      const index = pmsData.value.findIndex((i) => i.id === maintenanceForm.value.id);
      if (index !== -1) {
        pmsData.value[index] = { ...maintenanceForm.value };
      }
    } else {
      // Add new item
      pmsData.value.push({
        ...maintenanceForm.value,
        id: Date.now() // Temporary ID
      });
    }
    closeMaintenanceModal();
  };

  // Handle maintenance FormKit form submission
  const handleMaintenanceSubmit = (values) => {
    maintenanceForm.value = {
      ...maintenanceForm.value,
      ...values
    };
    submitMaintenanceForm();
  };

  // Current start year (for 10-year view)
  const startYear = ref(2024);

  // Next decade
  const nextDecade = () => {
    startYear.value += 10;
  };

  // Previous decade
  const prevDecade = () => {
    startYear.value -= 10;
  };

  // Get maintenance items for a specific year
  const getYearMaintenanceItems = (year) => {
    return pmsData.value.filter(item => item.year === year);
  };

  // Get current year
  const currentYear = new Date().getFullYear();

  // Next year
  const nextYear = () => {
    currentYear.value++;
  };

  // Previous year
  const prevYear = () => {
    currentYear.value--;
  };

  // Get months
  const months = ["Januari", "Februari", "Mac", "April", "Mei", "Jun", "Julai", "Ogos", "September", "Oktober", "November", "Disember"];

  // Get maintenance items for a specific month
  const getMonthMaintenanceItems = (monthIndex) => {
    return pmsData.value.filter(item => {
      const startDate = new Date(item.startDate);
      const endDate = new Date(item.endDate);
      const monthStart = new Date(currentYear.value, monthIndex, 1);
      const monthEnd = new Date(currentYear.value, monthIndex + 1, 0);
      
      return (
        (startDate <= monthEnd && endDate >= monthStart) || 
        (startDate >= monthStart && startDate <= monthEnd) ||
        (endDate >= monthStart && endDate <= monthEnd)
      );
    });
  };

  // Get days in a month
  const getDaysInMonth = (year, month) => {
    return new Date(year, month + 1, 0).getDate();
  };

  // Crew data
  const crewData = ref([
    {
      id: 1,
      noTentera: "T12345",
      name: "Kapt. Ahmad bin Ismail",
      position: "Kapten Kapal",
      joinDate: "2020-06-15",
      endDate: "2024-06-14",
      status: "Aktif",
      contactNo: "012-3456789",
      email: "ahmad.ismail@tldm.mil.my",
      tindakan: "lihat",
      training: [
        { id: 1, name: "Kursus Kepimpinan Maritim", date: "2021-03-15", duration: "2 minggu", location: "Akademi TLDM, Lumut" },
        { id: 2, name: "Latihan Pengurusan Krisis", date: "2022-06-10", duration: "1 minggu", location: "Pusat Latihan TLDM, Kuantan" }
      ],
      serviceRecord: [
        { id: 1, position: "Pegawai Navigasi", vessel: "KD LEKIU", startDate: "2015-06-10", endDate: "2018-05-20" },
        { id: 2, position: "Pegawai Eksekutif", vessel: "KD JEBAT", startDate: "2018-06-01", endDate: "2020-06-10" }
      ],
      education: [
        { id: 1, qualification: "Ijazah Sarjana Muda Kejuruteraan Maritim", institution: "Universiti Pertahanan Nasional Malaysia", year: "2010" },
        { id: 2, qualification: "Diploma Pengajian Maritim", institution: "Akademi TLDM", year: "2008" }
      ]
    },
    {
      id: 2,
      noTentera: "T23456",
      name: "Lt. Kdr. Siti binti Hassan",
      position: "Pegawai Eksekutif",
      joinDate: "2021-03-10",
      endDate: "2025-03-09",
      status: "Aktif",
      contactNo: "013-4567890",
      email: "siti.hassan@tldm.mil.my",
      tindakan: "lihat",
      training: [
        { id: 1, name: "Kursus Pengurusan Strategik", date: "2021-09-20", duration: "3 minggu", location: "Akademi TLDM, Lumut" },
        { id: 2, name: "Latihan Operasi Bersama", date: "2022-11-05", duration: "2 minggu", location: "Pangkalan TLDM, Kota Kinabalu" }
      ],
      serviceRecord: [
        { id: 1, position: "Pegawai Komunikasi", vessel: "KD KASTURI", startDate: "2016-03-15", endDate: "2019-04-30" },
        { id: 2, position: "Pegawai Eksekutif", vessel: "KD KEDAH", startDate: "2019-05-15", endDate: "2021-03-01" }
      ],
      education: [
        { id: 1, qualification: "Ijazah Sarjana Muda Sains Komputer", institution: "Universiti Malaya", year: "2012" },
        { id: 2, qualification: "Diploma Pengajian Pertahanan", institution: "Akademi TLDM", year: "2009" }
      ]
    },
    {
      id: 3,
      noTentera: "T34567",
      name: "Lt. Mohd Rizal bin Abdullah",
      position: "Pegawai Navigasi",
      joinDate: "2022-01-20",
      endDate: "2026-01-19",
      status: "Aktif",
      contactNo: "014-5678901",
      email: "mohd.rizal@tldm.mil.my",
      tindakan: "lihat",
      training: [
        { id: 1, name: "Kursus Navigasi Lanjutan", date: "2022-05-10", duration: "4 minggu", location: "Akademi TLDM, Lumut" }
      ]
    },
    {
      id: 4,
      noTentera: "T45678",
      name: "PW I Ravi a/l Chandran",
      position: "Ketua Jurutera",
      joinDate: "2019-11-05",
      endDate: "2023-11-04",
      status: "Cuti",
      contactNo: "015-6789012",
      email: "ravi.chandran@tldm.mil.my",
      tindakan: "lihat",
      training: [
        { id: 1, name: "Kursus Penyelenggaraan Enjin", date: "2020-02-15", duration: "3 minggu", location: "Pusat Teknikal TLDM, Lumut" },
        { id: 2, name: "Latihan Sistem Pendorongan", date: "2021-07-20", duration: "2 minggu", location: "Pusat Teknikal TLDM, Lumut" },
        { id: 3, name: "Kursus Pengurusan Kejuruteraan", date: "2022-09-12", duration: "1 minggu", location: "Akademi TLDM, Lumut" }
      ]
    },
    {
      id: 5,
      noTentera: "T56789",
      name: "PW II Tan Mei Ling",
      position: "Ketua Komunikasi",
      joinDate: "2020-08-15",
      endDate: "2024-08-14",
      status: "Aktif",
      contactNo: "016-7890123",
      email: "tan.meiling@tldm.mil.my",
      tindakan: "lihat",
      training: [
        { id: 1, name: "Kursus Sistem Komunikasi Maritim", date: "2021-04-05", duration: "3 minggu", location: "Pusat Latihan TLDM, Kuantan" },
        { id: 2, name: "Latihan Keselamatan Siber", date: "2022-08-15", duration: "1 minggu", location: "Pusat Siber TLDM, Kuala Lumpur" }
      ]
    },
    {
      id: 6,
      noTentera: "T67890",
      name: "BM Iskandar bin Zulkifli",
      position: "Juruteknik Elektronik",
      joinDate: "2021-05-20",
      endDate: "2025-05-19",
      status: "Aktif",
      contactNo: "017-8901234",
      email: "iskandar.zulkifli@tldm.mil.my",
      tindakan: "lihat",
      training: [
        { id: 1, name: "Kursus Asas Elektronik Kapal", date: "2021-08-10", duration: "4 minggu", location: "Pusat Teknikal TLDM, Lumut" }
      ]
    }
  ]);

  // Crew Modal state
  const isCrewModalOpen = ref(false);
  const isViewingCrew = ref(false);
  const isEditingCrew = ref(false);
  const crewForm = ref({
    id: null,
    noTentera: "",
    name: "",
    position: "",
    joinDate: "",
    endDate: "",
    status: "Aktif",
    contactNo: "",
    email: "",
    tindakan: "lihat",
    training: [],
    serviceRecord: [],
    education: []
  });

  // Open modal for adding crew
  const openAddCrewModal = () => {
    isViewingCrew.value = false;
    isEditingCrew.value = false;
    const today = new Date().toISOString().split('T')[0];
    const fourYearsLater = new Date();
    fourYearsLater.setFullYear(fourYearsLater.getFullYear() + 4);
    
    crewForm.value = {
      id: null,
      noTentera: "",
      name: "",
      position: "",
      joinDate: today,
      endDate: fourYearsLater.toISOString().split('T')[0],
      status: "Aktif",
      contactNo: "",
      email: "",
      tindakan: "lihat",
      training: [],
      serviceRecord: [],
      education: []
    };
    isCrewModalOpen.value = true;
  };

  // Open modal for viewing crew
  const viewCrewItem = (item) => {
    isViewingCrew.value = true;
    isEditingCrew.value = false;
    crewForm.value = { ...item };
    isCrewModalOpen.value = true;
  };

  // Open modal for editing crew
  const editCrewItem = (item) => {
    isViewingCrew.value = false;
    isEditingCrew.value = true;
    crewForm.value = { ...item };
    isCrewModalOpen.value = true;
  };

  // Close crew modal
  const closeCrewModal = () => {
    isCrewModalOpen.value = false;
  };

  // Submit crew form
  const submitCrewForm = () => {
    if (isEditingCrew.value) {
      // Update existing item
      const index = crewData.value.findIndex((i) => i.id === crewForm.value.id);
      if (index !== -1) {
        crewData.value[index] = { ...crewForm.value };
      }
    } else {
      // Add new item
      crewData.value.push({
        ...crewForm.value,
        id: Date.now() // Temporary ID
      });
    }
    closeCrewModal();
  };

  // Delete crew item
  const deleteCrewItem = (id) => {
    if (confirm('Adakah anda pasti untuk memadamkan rekod ini?')) {
      const index = crewData.value.findIndex((i) => i.id === id);
      if (index !== -1) {
        crewData.value.splice(index, 1);
      }
    }
  };

  // Handle crew FormKit form submission
  const handleCrewSubmit = (values) => {
    crewForm.value = {
      ...crewForm.value,
      ...values
    };
    submitCrewForm();
  };

  // Crew departments for filtering
  const crewDepartments = ["Semua", "Pemerintah", "Eksekutif", "Navigasi", "Kejuruteraan", "Komunikasi", "Elektronik", "Lain-lain"];
  const selectedCrewDepartment = ref("Semua");

  // Filtered crew data
  const filteredCrewData = computed(() => {
    if (selectedCrewDepartment.value === "Semua") {
      return crewData.value;
    }
    return crewData.value.filter(item => item.department === selectedCrewDepartment.value);
  });

  // Print crew item
  const printCrewItem = (item) => {
    // Implement print functionality
    console.log("Printing crew item:", item);
  };

  // Ship/Bot data
  const shipData = ref([
    { id: 1, name: 'KD KEDAH', type: 'Kapal', pennantNumber: 'F171' },
    { id: 2, name: 'KD PAHANG', type: 'Kapal', pennantNumber: 'F172' },
    { id: 3, name: 'KD KELANTAN', type: 'Kapal', pennantNumber: 'F173' },
    { id: 4, name: 'KD SELANGOR', type: 'Kapal', pennantNumber: 'F174' },
    { id: 5, name: 'BOT TUNDA 1', type: 'Bot', pennantNumber: 'BT001' },
    { id: 6, name: 'BOT TUNDA 2', type: 'Bot', pennantNumber: 'BT002' },
    { id: 7, name: 'BOT MALIM 1', type: 'Bot', pennantNumber: 'BM001' },
    { id: 8, name: 'BOT MALIM 2', type: 'Bot', pennantNumber: 'BM002' }
  ]);

  // Computed property for ship options
  const shipOptions = computed(() => {
    return shipData.value.map(ship => ({
      label: `${ship.name} (${ship.pennantNumber})`,
      value: ship.id
    }));
  });

  // Job Card data
  const jobCardData = ref([
    {
      BIL: 1,
      'JENIS ASET': 'Sistem Radar',
      'KAPAL/BOT': 'KD KEDAH',
      'Pengguna Terakhir': 'KD Kedah',
      'TARIKH ROSAK': '2024-03-15',
      'PEMOHON': 'Kapt. Razak',
      'AMOUN': 'RM 15,000',
      'TINDAKAN': '1'
    },
    {
      BIL: 2,
      'JENIS ASET': 'Enjin Utama',
      'KAPAL/BOT': 'KD PAHANG',
      'Pengguna Terakhir': 'KD Kedah',
      'TARIKH ROSAK': '2024-04-02',
      'PEMOHON': 'Kapt. Razak',
      'AMOUN': 'RM 45,000',
      'TINDAKAN': '2'
    },
    {
      BIL: 3,
      'JENIS ASET': 'Sistem Komunikasi',
      'Pengguna Terakhir': 'KD Kedah',
      'TARIKH ROSAK': '2024-04-10',
      'PEMOHON': 'Lt. Mohd Rizal',
      'AMOUN': 'RM 12,500',
      'TINDAKAN': '3'
    },
    {
      BIL: 4,
      'JENIS ASET': 'Sistem Sonar',
      'Pengguna Terakhir': 'KD Kedah',
      'TARIKH ROSAK': '2024-04-15',
      'PEMOHON': 'Kapt. Razak',
      'AMOUN': 'RM 22,300',
      'TINDAKAN': '4'
    },
    {
      BIL: 5,
      'JENIS ASET': 'Sistem Pendorongan',
      'Pengguna Terakhir': 'KD Kedah',
      'TARIKH ROSAK': '2024-04-18',
      'PEMOHON': 'Lt. Mohd Rizal',
      'AMOUN': 'RM 38,750',
      'TINDAKAN': '5'
    }
  ]);

  // Computed property for asset options
  const assetOptions = computed(() => {
    return shipEquipmentData.value.map(equipment => ({
      label: `${equipment.name} (${equipment.equipmentNo})`,
      value: equipment.equipmentNo
    }));
  });

  // Add Job Card Modal state
  const isAddJobCardModalOpen = ref(false);
  const newJobCardForm = ref({
    'JENIS ASET': '',
    'KAPAL/BOT': '',
    'Pengguna Terakhir': 'KD Kedah',
    'TARIKH ROSAK': new Date().toISOString().split('T')[0],
    'PEMOHON': '',
    'AMOUN': '',
    'reportType': 'DEFECT',
    'location': 'PANGKALAN TLDM LUMUT',
    'mainSystem': '',
    'runningHours': '',
    'manufacturer': '',
    'serialNo': '',
    'remarks': '',
    'selectedShipId': null
  });

  // Open add job card modal
  const openAddJobCardModal = () => {
    newJobCardForm.value = {
      'JENIS ASET': '',
      'KAPAL/BOT': '',
      'Pengguna Terakhir': 'KD Kedah',
      'TARIKH ROSAK': new Date().toISOString().split('T')[0],
      'PEMOHON': '',
      'AMOUN': '',
      'reportType': 'DEFECT',
      'location': 'PANGKALAN TLDM LUMUT',
      'mainSystem': '',
      'runningHours': '',
      'manufacturer': '',
      'serialNo': '',
      'remarks': '',
      'selectedShipId': null
    };
    isAddJobCardModalOpen.value = true;
  };

  // Close add job card modal
  const closeAddJobCardModal = () => {
    isAddJobCardModalOpen.value = false;
  };

  // Submit new job card
  const submitNewJobCard = () => {
    // Create a new job card entry
    const newJobCard = {
      id: Date.now(),
      BIL: jobCardData.value.length + 1,
      ...newJobCardForm.value
    };
    
    // Add to the job cards array
    jobCardData.value.push(newJobCard);
    
    // Close the modal
    closeAddJobCardModal();
  };

  // Add Job Card view modal state
  const isViewJobCardModalOpen = ref(false);
  const selectedJobCard = ref(null);

  // Open view job card modal
  const openViewJobCardModal = (jobCard) => {
    selectedJobCard.value = jobCard;
    isViewJobCardModalOpen.value = true;
  };

  // Close view job card modal
  const closeViewJobCardModal = () => {
    isViewJobCardModalOpen.value = false;
  };
</script>

<template>
  <div class="p-6 space-y-6">  
    <div class="space-y-6">
        <!-- Statistics Cards Row -->
        <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
          <div class="bg-white rounded-lg shadow p-6">
              <div class="flex items-center justify-between">
              <div>
                  <p class="text-sm text-gray-500">Jumlah Kad Kerja</p>
                  <h3 class="text-2xl font-bold">5</h3>
              </div>
              <div class="p-3 bg-blue-100 rounded-full">
                  <Icon class="text-primary" name="mdi:invoice-list-outline"></Icon>
              </div>
              </div>
          </div>

          <div class="bg-white rounded-lg shadow p-6">
              <div class="flex items-center justify-between">
              <div>
                  <p class="text-sm text-gray-500">Jumlah Bajet</p>
                  <h3 class="text-2xl font-bold">RM 125,000</h3>
              </div>
              <div class="p-3 bg-blue-100 rounded-full">
                  <Icon class="text-primary" name="f7:money-dollar-circle"></Icon>
              </div>
              </div>
          </div>

          <div class="bg-white rounded-lg shadow p-6">
              <div class="flex items-center justify-between">
              <div>
                  <p class="text-sm text-gray-500">Kad Kerja Selesai</p>
                  <h3 class="text-2xl font-bold">2</h3>
              </div>
              <div class="p-3 bg-blue-100 rounded-full">
                  <Icon class="text-primary" name="material-symbols:library-add-check-outline-rounded"></Icon>
              </div>
              </div>
          </div>

          <div class="bg-white rounded-lg shadow p-6">
              <div class="flex items-center justify-between">
              <div>
                  <p class="text-sm text-gray-500">Kad Kerja Tertunggak</p>
                  <h3 class="text-2xl font-bold text-red-600">2</h3>
              </div>
              <div class="p-3 bg-red-100 rounded-full">
                  <Icon class="text-primary" name="ic:outline-warning-amber"></Icon>
              </div>
              </div>
          </div>            
        </div>
        
        <!-- Approval Status Cards Row -->
        <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4">
          <div class="bg-white rounded-lg shadow p-6">
              <div class="flex items-center justify-between">
              <div>
                  <p class="text-sm text-gray-500">Menunggu Kelulusan MN/ZM</p>
                  <h3 class="text-2xl font-bold text-yellow-600">1</h3>
              </div>
              <div class="p-3 bg-red-100 rounded-full">
                  <Icon class="text-primary" name="hugeicons:validation-approval"></Icon>
              </div>
              </div>
          </div>

          <div class="bg-white rounded-lg shadow p-6">
              <div class="flex items-center justify-between">
              <div>
                  <p class="text-sm text-gray-500">Menunggu Kelulusan KJPKP</p>
                  <h3 class="text-2xl font-bold text-yellow-600">2</h3>
              </div>
              <div class="p-3 bg-red-100 rounded-full">
                  <Icon class="text-primary" name="hugeicons:validation-approval"></Icon>
              </div>
              </div>
          </div>

          <div class="bg-white rounded-lg shadow p-6">
              <div class="flex items-center justify-between">
              <div>
                  <p class="text-sm text-gray-500">Menunggu Pembayaran</p>
                  <h3 class="text-2xl font-bold text-yellow-600">3</h3>
              </div>
              <div class="p-3 bg-red-100 rounded-full">
                  <Icon class="text-primary" name="tabler:moneybag-move"></Icon>
              </div>
              </div>
          </div>

            
        </div>
        
        <!-- Job Card List Section -->
        <rs-card>
            <template #header>
            <div class="flex justify-between items-center p-2">
                <h2 class="text-lg font-semibold">Senarai Kad Kerja</h2>
                <rs-button variant="primary" @click="openAddJobCardModal">
                  <Icon name="material-symbols:add" />
                  Tambah Kad Kerja
                </rs-button>
            </div>
            </template>
            
            <div class="p-4">
            <!-- Job Card Table -->
            <rs-table
                :data="jobCardData"
                :columns="[
                { key: 'BIL', label: 'BIL' },
                { key: 'JENIS ASET', label: 'JENIS ASET' },
                { key: 'KAPAL/BOT', label: 'KAPAL/BOT' },
                { key: 'TARIKH ROSAK', label: 'TARIKH ROSAK' },
                { key: 'PEMOHON', label: 'PEMOHON' },
                { key: 'AMOUN', label: 'JUMLAH (RM)' },
                { key: 'TINDAKAN', label: 'TINDAKAN' }
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
                defaultSort: { column: 'BIL', direction: 'asc' }
                }"
                advanced
            >
                <template v-slot:TINDAKAN="row">
                <div class="flex gap-2">
                    <button variant="primary" size="sm" @click="openViewJobCardModal(row.value)" class="text-blue-600 hover:text-blue-800">
                      <Icon class="text-primary" name="weui:eyes-on-outlined"></Icon>
                      <!-- Lihat -->
                    </button>
                    <!-- <rs-button variant="warning" size="sm">Edit</rs-button>
                    <rs-button variant="danger" size="sm">Padam</rs-button> -->
                </div>
                </template>
            </rs-table>      
            </div>
        </rs-card>
    </div>
    

    <!-- Add Job Card Modal -->
    <rs-modal v-model="isAddJobCardModalOpen" size="lg">
      <template #header>
        <h3 class="text-lg font-semibold">Tambah Kad Kerja Baru</h3>
      </template>
      
      <template #body>
        <div class="p-4">
          <FormKit type="form" :value="newJobCardForm" @submit="submitNewJobCard">
            <!-- Report Type Section -->
            <div class="mb-4">
              <label class="block text-sm font-medium text-gray-700 mb-2">Jenis Laporan</label>
              <div class="flex items-center space-x-4">
                <FormKit
                  type="radio"
                  name="reportType"
                  value="DEFECT"
                  label="KEROSAKAN"
                />
                <FormKit
                  type="radio"
                  name="reportType"
                  value="OSL"
                  label="OSL"
                />
              </div>
            </div>
            
            <!-- Basic Information Section -->
            <div class="bg-gray-50 p-4 rounded-lg mb-4">
              <h4 class="font-medium text-gray-700 mb-3">Maklumat Asas</h4>
              
              <div class="grid grid-cols-2 gap-4 mb-4">
                <FormKit
                  type="select"
                  name="selectedShipId"
                  label="Pilih Kapal/Bot"
                  :options="shipOptions"
                  placeholder="Pilih kapal atau bot"
                  validation="required"
                  @input="(shipId) => {
                    const selectedShip = shipData.value.find(s => s.id === shipId);
                    if (selectedShip) {
                      newJobCardForm['KAPAL/BOT'] = selectedShip.name;
                    }
                  }"
                />
                
                <FormKit
                  type="select"
                  name="selectedAsset"
                  label="Pilih Aset"
                  :options="assetOptions"
                  placeholder="Pilih aset dari senarai"
                  validation="required"
                  @input="(equipmentNo) => {
                    const selectedEquipment = shipEquipmentData.value.find(e => e.equipmentNo === equipmentNo);
                    if (selectedEquipment) {
                      newJobCardForm['JENIS ASET'] = selectedEquipment.name;
                      newJobCardForm.mainSystem = selectedEquipment.category;
                      newJobCardForm.manufacturer = selectedEquipment.manufacturer;
                      newJobCardForm.serialNo = selectedEquipment.serialNumber;
                    }
                  }"
                />
              </div>
              
              <div class="grid grid-cols-2 gap-4">
                <FormKit
                  type="text"
                  name="mainSystem"
                  label="Sistem Utama / Peralatan"
                  placeholder="Contoh: RADAR SISTEM"
                  validation="required"
                  :disabled="true"
                />
                
                <FormKit
                  type="date"
                  name="TARIKH ROSAK"
                  label="Tarikh Rosak"
                  validation="required"
                />
              </div>
              
              <div class="grid grid-cols-2 gap-4">
                <FormKit
                  type="text"
                  name="PEMOHON"
                  label="Pemohon"
                  placeholder="Contoh: Kapt. Razak"
                  validation="required"
                />
              </div>
            </div>
            
            <!-- Technical Information Section -->
            <div class="bg-gray-50 p-4 rounded-lg mb-4">
              <h4 class="font-medium text-gray-700 mb-3">Maklumat Teknikal</h4>
              
              <div class="grid grid-cols-2 gap-4 mb-4">
                <FormKit
                  type="text"
                  name="location"
                  label="Lokasi"
                  placeholder="Contoh: PANGKALAN TLDM LUMUT"
                  validation="required"
                />
                
                <FormKit
                  type="number"
                  name="runningHours"
                  label="Jam Operasi"
                  placeholder="Contoh: 1250"
                />
              </div>
              
              <div class="grid grid-cols-2 gap-4">
                <FormKit
                  type="text"
                  name="manufacturer"
                  label="Pengeluar / Model"
                  placeholder="Contoh: Thales"
                />
                
                <FormKit
                  type="text"
                  name="serialNo"
                  label="No. Siri"
                  placeholder="Contoh: TH-R5670-892"
                />
              </div>
            </div>
            
            <!-- Cost & Details Section -->
            <div class="bg-gray-50 p-4 rounded-lg mb-4">
              <h4 class="font-medium text-gray-700 mb-3">Kos & Keterangan</h4>
              
              <FormKit
                type="text"
                name="AMOUN"
                label="Anggaran Kos"
                placeholder="Contoh: RM 15,000"
                validation="required"
              />
              
              <FormKit
                type="textarea"
                name="remarks"
                label="Keterangan Kerosakan"
                placeholder="Sila masukkan keterangan terperinci mengenai kerosakan"
                validation="required"
                :rows="4"
              />
            </div>
            
            <!-- Attachment Section -->
            <div class="bg-gray-50 p-4 rounded-lg">
              <h4 class="font-medium text-gray-700 mb-3">Lampiran</h4>
              
              <FormKit
                type="file"
                name="attachment"
                label="Lampirkan Dokumen"
                accept=".pdf,.doc,.docx,.jpg,.png"
                help="Sila lampirkan dokumen sokongan (PDF, Word, atau imej)"
              />
            </div>
          </FormKit>
        </div>
      </template>
      
      <template #footer>
        <div class="flex justify-end gap-2">
          <!-- <rs-button variant="secondary" @click="closeAddJobCardModal">Batal</rs-button>
          <rs-button variant="primary" @click="submitNewJobCard">Simpan</rs-button> -->
        </div>
      </template>
    </rs-modal>

    <!-- View Job Card Modal -->
    <rs-modal v-model="isViewJobCardModalOpen" size="lg">
      <template #header>
        <h3 class="text-lg font-semibold">Maklumat Kad Kerja</h3>
      </template>
      
      <template #body>
        <div v-if="selectedJobCard" class="p-4">
          <!-- Document Header -->
          <div class="text-center mb-4">
            <h2 class="text-xl font-bold">BORANG LAPORAN KEROSAKAN</h2>
            <p class="text-sm">JC-2024-{{ selectedJobCard.BIL.toString().padStart(3, '0') }}</p>
          </div>
          
          <!-- Report Type Section -->
          <div class="border border-gray-800 mb-4">
            <table class="w-full">
              <tr class="border-b border-gray-800">
                <td class="p-2 border-r border-gray-800 font-medium w-1/5">JENIS LAPORAN:</td>
                <td class="p-2">
                  <div class="flex items-center space-x-4">
                    <div class="flex items-center space-x-2">
                      <span>KEROSAKAN</span>
                      <div class="w-5 h-5 border border-gray-800 flex items-center justify-center">
                        <span class="text-lg" v-if="selectedJobCard.reportType === 'DEFECT'">✓</span>
                      </div>
                    </div>
                    <div class="flex items-center space-x-2">
                      <span>OSL</span>
                      <div class="w-5 h-5 border border-gray-800 flex items-center justify-center">
                        <span class="text-lg" v-if="selectedJobCard.reportType === 'OSL'">✓</span>
                      </div>
                    </div>
                  </div>
                </td>
              </tr>
            </table>
          </div>
          
          <!-- Vessel Information Section -->
          <div class="border border-gray-800 mb-4">
            <table class="w-full">
              <tr class="border-b border-gray-800">
                <td class="p-2 border-r border-gray-800 font-medium w-1/3">Kapal Maritim</td>
                <td class="p-2">: {{ selectedJobCard['KAPAL/BOT'] }}</td>
                <td class="p-2 border-l border-gray-800 font-medium w-1/5">Jam Operasi</td>
                <td class="p-2">{{ selectedJobCard.runningHours || 'N/A' }}</td>
              </tr>
              <tr class="border-b border-gray-800">
                <td class="p-2 border-r border-gray-800 font-medium">Pangkalan Maritim</td>
                <td class="p-2">: {{ selectedJobCard.location || 'PANGKALAN TLDM LUMUT' }}</td>
                <td class="p-2 border-l border-gray-800 font-medium">Pengeluar / Model</td>
                <td class="p-2">{{ selectedJobCard.manufacturer || 'N/A' }}</td>
              </tr>
              <tr class="border-b border-gray-800">
                <td class="p-2 border-r border-gray-800 font-medium">Lokasi</td>
                <td class="p-2">: {{ selectedJobCard.location || 'PANGKALAN TLDM LUMUT' }}</td>
                <td class="p-2 border-l border-gray-800 font-medium">No. Siri</td>
                <td class="p-2">{{ selectedJobCard.serialNo || 'N/A' }}</td>
              </tr>
              <tr>
                <td class="p-2 border-r border-gray-800 font-medium">Sistem Utama / Peralatan</td>
                <td colspan="3" class="p-2">: {{ selectedJobCard['JENIS ASET'] }}</td>
              </tr>
            </table>
          </div>
          
          <!-- Description Section -->
          <div class="border border-gray-800 mb-4">
            <table class="w-full">
              <tr>
                <td class="p-2 border-r border-gray-800 font-medium w-1/3">Penerangan</td>
                <td class="p-2">: {{ selectedJobCard.remarks || 'Tiada penerangan terperinci.' }}</td>
              </tr>
            </table>
          </div>
          
          <!-- Details Section -->
          <div class="border border-gray-800 mb-4">
            <table class="w-full">
              <tr class="border-b border-gray-800">
                <td class="p-2 border-r border-gray-800 font-medium w-1/3">Tarikh Rosak</td>
                <td class="p-2">: {{ selectedJobCard['TARIKH ROSAK'] }}</td>
              </tr>
              <tr class="border-b border-gray-800">
                <td class="p-2 border-r border-gray-800 font-medium">Pemohon</td>
                <td class="p-2">: {{ selectedJobCard['PEMOHON'] }}</td>
              </tr>
              <tr>
                <td class="p-2 border-r border-gray-800 font-medium">Anggaran Kos</td>
                <td class="p-2">: <span class="font-medium text-blue-600">{{ selectedJobCard['AMOUN'] }}</span></td>
              </tr>
            </table>
          </div>
          
          <!-- Report By / Verified By Section -->
          <div class="border border-gray-800 mb-4">
            <table class="w-full">
              <tr class="border-b border-gray-800">
                <td class="p-2 border-r border-gray-800 font-medium w-1/3">Dilaporkan Oleh</td>
                <td class="p-2">Disahkan Oleh:</td>
              </tr>
              <tr class="border-b border-gray-800">
                <td class="p-2 border-r border-gray-800 font-medium text-xs">(PEGAWAI TEKNIKAL)</td>
                <td class="p-2 font-medium text-xs">(PEGAWAI KAPAL)</td>
              </tr>
              <tr class="border-b border-gray-800">
                <td class="p-2 border-r border-gray-800">Tandatangan: <span class="italic">[Signed]</span></td>
                <td class="p-2">Tandatangan: <span class="italic">[Signed]</span></td>
              </tr>
              <tr class="border-b border-gray-800">
                <td class="p-2 border-r border-gray-800">Nama: {{ selectedJobCard['PEMOHON'] }}</td>
                <td class="p-2">Nama: _________________</td>
              </tr>
              <tr class="border-b border-gray-800">
                <td class="p-2 border-r border-gray-800">Jawatan: PEGAWAI TEKNIKAL</td>
                <td class="p-2">Jawatan: PEGAWAI KAPAL</td>
              </tr>
              <tr>
                <td class="p-2 border-r border-gray-800">Tarikh: {{ selectedJobCard['TARIKH ROSAK'] }}</td>
                <td class="p-2">Tarikh: {{ selectedJobCard['TARIKH ROSAK'] }}</td>
              </tr>
            </table>
          </div>
          
          <!-- Status Section -->
          <div class="border border-gray-800 mb-4">
            <table class="w-full">
              <tr class="border-b border-gray-800 bg-gray-100">
                <td colspan="2" class="p-2 font-bold">STATUS SEMASA</td>
              </tr>
              <tr>
                <td class="p-2 border-r border-gray-800 font-medium w-1/4">Status</td>
                <td class="p-2">: 
                  <rs-badge
                    :variant="'info'"
                  >
                    Dalam Proses
                  </rs-badge>
                </td>
              </tr>
            </table>
          </div>
          
          <!-- Attachment Section -->
          <div class="border border-gray-800 mb-4">
            <table class="w-full">
              <tr class="border-b border-gray-800 bg-gray-100">
                <td colspan="2" class="p-2 font-bold">LAMPIRAN</td>
              </tr>
              <tr>
                <td class="p-2">
                  <div class="flex items-center gap-3 p-3 bg-gray-50 rounded-lg">
                    <div class="bg-blue-100 p-2 rounded-lg">
                      <i class="fas fa-file-pdf text-2xl text-blue-600"></i>
                    </div>
                    <div class="flex-grow">
                      <div class="flex items-center justify-between">
                        <div>
                          <p class="font-medium">Laporan Kerosakan.pdf</p>
                          <p class="text-sm text-gray-500">2.5 MB</p>
                        </div>
                        <div>
                          <rs-button variant="primary" size="sm" class="px-3">
                            <i class="fas fa-download mr-1"></i> Muat Turun
                          </rs-button>
                        </div>
                      </div>
                    </div>
                  </div>
                </td>
              </tr>
            </table>
          </div>
          
          <!-- Page Number -->
          <div class="text-center mt-4">
            <div class="inline-block bg-gray-700 text-white px-8 py-1 rounded-full">
              <span class="mr-2">Halaman</span>
              <span class="mr-2">1</span>
              <span class="mr-2">/</span>
              <span>1</span>
            </div>
          </div>
        </div>
      </template>
      
      <template #footer>
        <div class="flex justify-end gap-2">
          <rs-button variant="secondary" @click="closeViewJobCardModal">Tutup</rs-button>
          <rs-button variant="primary">
            <i class="fas fa-print mr-1"></i> Cetak Laporan
          </rs-button>
        </div>
      </template>
    </rs-modal>
  </div>
</template>
