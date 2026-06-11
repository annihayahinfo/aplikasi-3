import React, { useState, useEffect, useMemo } from 'react';

// =========================================================================
// GLOBAL HELPER FUNCTIONS (Didefinisikan di awal agar aman dari hoisting)
// =========================================================================

const formatDateString = (dateStr) => {
  if (!dateStr) return '-';
  const months = [
    'Januari', 'Februari', 'Maret', 'April', 'Mei', 'Juni', 
    'Juli', 'Agustus', 'September', 'Oktober', 'November', 'Desember'
  ];
  try {
    const d = new Date(dateStr);
    if (isNaN(d.getTime())) return dateStr;
    return `${d.getDate()} ${months[d.getMonth()]} ${d.getFullYear()}`;
  } catch (e) {
    return dateStr;
  }
};

// =========================================================================
// DATA INITIALIZATION & CONSTANTS
// =========================================================================

const DEFAULT_CLASSES = ['Kelas 1A', 'Kelas 1B', 'Kelas 2A', 'Kelas 3A', 'Kelas 4A', 'Kelas 5A', 'Kelas 6A'];
const DEFAULT_YEARS = ['2021/2022', '2022/2023', '2023/2024', '2024/2025', '2025/2026'];

const INITIAL_STUDENTS = [
  {
    id: 'std-1',
    nama: 'Ahmad Faiz Al-Fatih',
    nis: '21220101',
    nisn: '0123456781',
    kelas: 'Kelas 6A',
    status: 'Aktif',
    tahunAjaran: '2025/2026',
    jenisKelamin: 'Laki-laki',
    foto: '',
    tempatLahir: 'Karawang',
    tanggalLahir: '2014-05-12'
  },
  {
    id: 'std-2',
    nama: 'Siti Aisyah Humaira',
    nis: '21220102',
    nisn: '0123456782',
    kelas: 'Kelas 6A',
    status: 'Lulus',
    tahunAjaran: '2024/2025',
    jenisKelamin: 'Perempuan',
    foto: '',
    tempatLahir: 'Karawang',
    tanggalLahir: '2014-09-20',
    noIjazah: 'DN-01/D-SD/24/0001234',
    tanggalLulus: '2025-06-15',
    nilaiRataIjazah: '88.5'
  },
  {
    id: 'std-3',
    nama: 'Muhammad Rizky',
    nis: '22230201',
    nisn: '0134567891',
    kelas: 'Kelas 5A',
    status: 'Mutasi Masuk',
    tahunAjaran: '2024/2025',
    jenisKelamin: 'Laki-laki',
    foto: '',
    tempatLahir: 'Bekasi',
    tanggalLahir: '2015-02-15',
    tanggalMasuk: '2024-07-10',
    sekolahAsal: 'SD Negeri Karawang 02',
    alamatAsal: 'Jl. Ahmad Yani No. 12, Karawang'
  },
  {
    id: 'std-4',
    nama: 'Fatima Az-Zahra',
    nis: '23240301',
    nisn: '0145678912',
    kelas: 'Kelas 3A',
    status: 'Mutasi Keluar',
    tahunAjaran: '2024/2025',
    jenisKelamin: 'Perempuan',
    foto: '',
    tempatLahir: 'Bandung',
    tanggalLahir: '2017-07-08',
    tanggalKeluar: '2025-01-05',
    sekolahTujuan: 'SDIT Al-Irsyad',
    alamatTujuan: 'Jl. Siliwangi No. 45, Bandung'
  },
  {
    id: 'std-5',
    nama: 'Abdurrahman Wahid',
    nis: '21220103',
    nisn: '0123456783',
    kelas: 'Kelas 6A',
    status: 'Lulus',
    tahunAjaran: '2024/2025',
    jenisKelamin: 'Laki-laki',
    foto: '',
    tempatLahir: 'Karawang',
    tanggalLahir: '2014-08-04',
    noIjazah: 'DN-01/D-SD/24/0001235',
    tanggalLulus: '2025-06-15',
    nilaiRataIjazah: '91.2'
  }
];

const INITIAL_GRADES = {
  'std-1': {
    'Kelas 6A_Ganjil': { PPKN: 85, PAI: 90, 'B.Indo': 88, MTK: 82, IPAS: 86, SBDP: 85, PJOK: 90, 'B.Sunda': 80, 'B.Inggris': 85 },
    'Kelas 6A_Genap': { PPKN: 87, PAI: 92, 'B.Indo': 89, MTK: 85, IPAS: 88, SBDP: 87, PJOK: 92, 'B.Sunda': 82, 'B.Inggris': 87 }
  },
  'std-2': {
    'Kelas 1A_Ganjil': { PPKN: 80, PAI: 85, 'B.Indo': 82, MTK: 80, IPA: 85, IPS: 80, SBDP: 85, PJOK: 85, 'B.Sunda': 80, 'B.Inggris': 82 },
    'Kelas 6A_Ganjil': { PPKN: 90, PAI: 95, 'B.Indo': 92, MTK: 88, IPA: 90, IPS: 89, SBDP: 90, PJOK: 95, 'B.Sunda': 85, 'B.Inggris': 90 },
    'Kelas 6A_Genap': { PPKN: 92, PAI: 96, 'B.Indo': 94, MTK: 90, IPA: 92, IPS: 91, SBDP: 92, PJOK: 96, 'B.Sunda': 87, 'B.Inggris': 92 }
  },
  'std-5': {
    'Kelas 6A_Ganjil': { PPKN: 90, PAI: 92, 'B.Indo': 90, MTK: 92, IPA: 90, IPS: 88, SBDP: 90, PJOK: 90, 'B.Sunda': 85, 'B.Inggris': 88 },
    'Kelas 6A_Genap': { PPKN: 92, PAI: 95, 'B.Indo': 92, MTK: 94, IPA: 92, IPS: 90, SBDP: 91, PJOK: 92, 'B.Sunda': 87, 'B.Inggris': 90 }
  }
};

const INITIAL_ATTENDANCE = {
  'std-1': {
    'Kelas 6A_Ganjil': { sakit: 1, izin: 2, alfa: 0 },
    'Kelas 6A_Genap': { sakit: 0, izin: 1, alfa: 0 }
  },
  'std-2': {
    'Kelas 6A_Ganjil': { sakit: 0, izin: 0, alfa: 0 },
    'Kelas 6A_Genap': { sakit: 1, izin: 0, alfa: 0 }
  }
};

const INITIAL_NOTES = {
  'std-1': {
    'Kelas 6A_Ganjil': 'Ananda Faiz memiliki motivasi belajar yang tinggi, pertahankan prestasi di bidang matematika.',
    'Kelas 6A_Genap': 'Selamat atas kelulusannya! Tetap rajin beribadah dan belajar di jenjang SMP.'
  },
  'std-2': {
    'Kelas 6A_Ganjil': 'Siti sangat tekun dalam membaca dan menghafal Al-Qur\'an. Prestasi akademiknya luar biasa.',
    'Kelas 6A_Genap': 'Prestasi yang sangat membanggakan! Pertahankan karakter mulia dan terus berprestasi.'
  }
};

// =========================================================================
// MAIN COMPONENT DEFINITION
// =========================================================================

export default function App() {
  const [isAuthenticated, setIsAuthenticated] = useState(true); 
  const [userRole, setUserRole] = useState('admin'); 
  const [usernameInput, setUsernameInput] = useState('');
  const [passwordInput, setPasswordInput] = useState('');
  const [loginError, setLoginError] = useState('');

  // Core School Database State (With LocalStorage Hydration)
  const [students, setStudents] = useState(() => {
    const saved = localStorage.getItem('annihayah_students');
    return saved ? JSON.parse(saved) : INITIAL_STUDENTS;
  });
  const [grades, setGrades] = useState(() => {
    const saved = localStorage.getItem('annihayah_grades');
    return saved ? JSON.parse(saved) : INITIAL_GRADES;
  });
  const [attendance, setAttendance] = useState(() => {
    const saved = localStorage.getItem('annihayah_attendance');
    return saved ? JSON.parse(saved) : INITIAL_ATTENDANCE;
  });
  const [notes, setNotes] = useState(() => {
    const saved = localStorage.getItem('annihayah_notes');
    return saved ? JSON.parse(saved) : INITIAL_NOTES;
  });
  const [classes, setClasses] = useState(() => {
    const saved = localStorage.getItem('annihayah_classes');
    return saved ? JSON.parse(saved) : DEFAULT_CLASSES;
  });
  const [years, setYears] = useState(() => {
    const saved = localStorage.getItem('annihayah_years');
    return saved ? JSON.parse(saved) : DEFAULT_YEARS;
  });

  // Navigation / UI states
  const [activeTab, setActiveTab] = useState('dashboard');
  const [studentSearch, setStudentSearch] = useState('');
  const [statusFilter, setStatusFilter] = useState('Semua');
  const [classFilter, setClassFilter] = useState('Semua');
  const [sortField, setSortField] = useState('nama');
  const [sortDirection, setSortDirection] = useState('asc');

  // Input Student Modal / Form State
  const [showStudentModal, setShowStudentModal] = useState(false);
  const [editingStudent, setEditingStudent] = useState(null);
  const [studentForm, setStudentForm] = useState({
    nama: '', nis: '', nisn: '', kelas: '', status: 'Aktif', tahunAjaran: '', jenisKelamin: 'Laki-laki', foto: '',
    tempatLahir: '', tanggalLahir: '',
    noIjazah: '', tanggalLulus: '', nilaiRataIjazah: '',
    tanggalMasuk: '', sekolahAsal: '', alamatAsal: '',
    tanggalKeluar: '', sekolahTujuan: '', alamatTujuan: ''
  });

  // Custom Notification & Confirmation Modal States
  const [toast, setToast] = useState({ show: false, message: '', type: 'info' });
  const [confirmDialog, setConfirmDialog] = useState({ show: false, message: '', onConfirm: null });

  // Custom alert & confirm functions
  const triggerToast = (message, type = 'info') => {
    setToast({ show: true, message, type });
    setTimeout(() => {
      setToast(prev => ({ ...prev, show: false }));
    }, 4000);
  };

  const triggerConfirm = (message, onConfirmAction) => {
    setConfirmDialog({
      show: true,
      message,
      onConfirm: () => {
        onConfirmAction();
        setConfirmDialog({ show: false, message: '', onConfirm: null });
      }
    });
  };

  // Input Grades / Attendance Form State
  const [selectedStudentGradeId, setSelectedStudentGradeId] = useState('');
  const [selectedGradeClass, setSelectedGradeClass] = useState('');
  const [selectedSemester, setSelectedSemester] = useState('Ganjil');

  // Settings page dynamic states
  const [newClassName, setNewClassName] = useState('');
  const [newYearName, setNewYearName] = useState('');

  // Active print state
  const [printStudentId, setPrintStudentId] = useState(null);
  const [printClassSem, setPrintClassSem] = useState('');

  // Save to LocalStorage helpers
  useEffect(() => {
    localStorage.setItem('annihayah_students', JSON.stringify(students));
  }, [students]);
  useEffect(() => {
    localStorage.setItem('annihayah_grades', JSON.stringify(grades));
  }, [grades]);
  useEffect(() => {
    localStorage.setItem('annihayah_attendance', JSON.stringify(attendance));
  }, [attendance]);
  useEffect(() => {
    localStorage.setItem('annihayah_notes', JSON.stringify(notes));
  }, [notes]);
  useEffect(() => {
    localStorage.setItem('annihayah_classes', JSON.stringify(classes));
  }, [classes]);
  useEffect(() => {
    localStorage.setItem('annihayah_years', JSON.stringify(years));
  }, [years]);

  // Load SheetJS dynamically on demand for Excel
  const [sheetJsLoaded, setSheetJsLoaded] = useState(false);
  useEffect(() => {
    const script = document.createElement('script');
    script.src = 'https://cdn.jsdelivr.net/npm/xlsx@0.18.5/dist/xlsx.full.min.js';
    script.async = true;
    script.onload = () => setSheetJsLoaded(true);
    document.body.appendChild(script);
  }, []);

  // Handle Login
  const handleLogin = (e) => {
    e.preventDefault();
    if (usernameInput === 'admin' && passwordInput === 'admin123') {
      setIsAuthenticated(true);
      setUserRole('admin');
      setLoginError('');
      triggerToast('Berhasil masuk sebagai Administrator!', 'success');
    } else if (usernameInput === 'guru' && passwordInput === 'guru123') {
      setIsAuthenticated(true);
      setUserRole('guru');
      setLoginError('');
      triggerToast('Berhasil masuk sebagai Guru!', 'success');
    } else {
      setLoginError('Username atau Password salah!');
      triggerToast('Otentikasi gagal. Silakan coba lagi.', 'error');
    }
  };

  const handleLogout = () => {
    setIsAuthenticated(false);
    setUsernameInput('');
    setPasswordInput('');
    triggerToast('Anda telah keluar dari sistem.', 'info');
  };

  // Helper to determine curriculum subjects based on academic year
  const getSubjectsForYear = (yearString) => {
    if (!yearString) return [];
    const parts = yearString.split('/');
    if (parts.length === 0) return [];
    const startYear = parseInt(parts[0], 10);
    if (startYear >= 2024) {
      return ['PPKN', 'PAI', 'B.Indo', 'MTK', 'IPAS', 'SBDP', 'PJOK', 'B.Sunda', 'B.Inggris'];
    } else {
      return ['PPKN', 'PAI', 'B.Indo', 'MTK', 'IPA', 'IPS', 'SBDP', 'PJOK', 'B.Sunda', 'B.Inggris'];
    }
  };

  // Dashboard Stats Calculations
  const stats = useMemo(() => {
    const total = students.length;
    const aktif = students.filter(s => s.status === 'Aktif').length;
    const lulus = students.filter(s => s.status === 'Lulus').length;
    const mutasiMasuk = students.filter(s => s.status === 'Mutasi Masuk').length;
    const mutasiKeluar = students.filter(s => s.status === 'Mutasi Keluar').length;
    const laki = students.filter(s => s.jenisKelamin === 'Laki-laki').length;
    const perempuan = students.filter(s => s.jenisKelamin === 'Perempuan').length;

    return { total, aktif, lulus, mutasiMasuk, mutasiKeluar, laki, perempuan };
  }, [students]);

  // Filter & Sort Students
  const filteredStudents = useMemo(() => {
    return students
      .filter(s => {
        const searchLower = studentSearch.toLowerCase();
        const matchesSearch = s.nama.toLowerCase().includes(searchLower) ||
          s.nis.includes(studentSearch) ||
          (s.nisn && s.nisn.includes(studentSearch)) ||
          (s.tempatLahir && s.tempatLahir.toLowerCase().includes(searchLower));
        const matchesStatus = statusFilter === 'Semua' || s.status === statusFilter;
        const matchesClass = classFilter === 'Semua' || s.kelas === classFilter;
        return matchesSearch && matchesStatus && matchesClass;
      })
      .sort((a, b) => {
        let valA = a[sortField] || '';
        let valB = b[sortField] || '';
        if (typeof valA === 'string') {
          return sortDirection === 'asc'
            ? valA.localeCompare(valB)
            : valB.localeCompare(valA);
        }
        return sortDirection === 'asc' ? valA - valB : valB - valA;
      });
  }, [students, studentSearch, statusFilter, classFilter, sortField, sortDirection]);

  const requestSort = (field) => {
    let direction = 'asc';
    if (sortField === field && sortDirection === 'asc') {
      direction = 'desc';
    }
    setSortField(field);
    setSortDirection(direction);
  };

  // Student Form Handlers
  const handleOpenAddModal = () => {
    setEditingStudent(null);
    setStudentForm({
      nama: '', nis: '', nisn: '', kelas: classes[0] || '', status: 'Aktif', tahunAjaran: years[years.length - 1] || '', jenisKelamin: 'Laki-laki', foto: '',
      tempatLahir: '', tanggalLahir: '',
      noIjazah: '', tanggalLulus: '', nilaiRataIjazah: '',
      tanggalMasuk: '', sekolahAsal: '', alamatAsal: '',
      tanggalKeluar: '', sekolahTujuan: '', alamatTujuan: ''
    });
    setShowStudentModal(true);
  };

  const handleOpenEditModal = (student) => {
    setEditingStudent(student.id);
    setStudentForm({ ...student });
    setShowStudentModal(true);
  };

  const handleSaveStudent = (e) => {
    e.preventDefault();
    if (!studentForm.nama || !studentForm.nis || !studentForm.nisn) {
      triggerToast("Harap lengkapi Nama, NIS, dan NISN siswa!", "error");
      return;
    }

    if (editingStudent) {
      setStudents(prev => prev.map(s => s.id === editingStudent ? { ...studentForm, id: editingStudent } : s));
      triggerToast(`Profil ${studentForm.nama} berhasil diperbarui!`, 'success');
    } else {
      const newStudent = {
        ...studentForm,
        id: 'std-' + Date.now()
      };
      setStudents(prev => [...prev, newStudent]);
      triggerToast(`Siswa ${studentForm.nama} berhasil diregistrasi!`, 'success');
    }
    setShowStudentModal(false);
  };

  const handleDeleteStudent = (id) => {
    const student = students.find(s => s.id === id);
    triggerConfirm(`Apakah Anda yakin ingin menghapus data induk siswa ${student?.nama || ''}? Semua data nilai & absensi juga akan dihapus permanen.`, () => {
      setStudents(prev => prev.filter(s => s.id !== id));
      const updatedGrades = { ...grades };
      delete updatedGrades[id];
      setGrades(updatedGrades);

      const updatedAtt = { ...attendance };
      delete updatedAtt[id];
      setAttendance(updatedAtt);

      const updatedNotes = { ...notes };
      delete updatedNotes[id];
      setNotes(updatedNotes);
      triggerToast('Data siswa berhasil dihapus.', 'success');
    });
  };

  const handleImageChange = (e) => {
    const file = e.target.files[0];
    if (file) {
      const reader = new FileReader();
      reader.onloadend = () => {
        setStudentForm(prev => ({ ...prev, foto: reader.result }));
      };
      reader.readAsDataURL(file);
    }
  };

  // Download Excel Template for Students
  const handleDownloadStudentTemplate = () => {
    if (!sheetJsLoaded || !window.XLSX) return triggerToast("Sistem Excel sedang dimuat, mohon tunggu sebentar...", "info");

    const templateColumns = [
      {
        'Nama Siswa': 'Ahmad Fauzi',
        'NIS': '22230101',
        'NISN': '0134567801',
        'Tempat Lahir': 'Karawang',
        'Tanggal Lahir (YYYY-MM-DD)': '2015-04-12',
        'Jenis Kelamin (Laki-laki/Perempuan)': 'Laki-laki',
        'Kelas': 'Kelas 1A',
        'Tahun Ajaran': '2025/2026',
        'Status (Aktif/Lulus/Mutasi Masuk/Mutasi Keluar)': 'Aktif'
      }
    ];

    const ws = window.XLSX.utils.json_to_sheet(templateColumns);
    const wb = window.XLSX.utils.book_new();
    window.XLSX.utils.book_append_sheet(wb, ws, "Template_Siswa");
    window.XLSX.writeFile(wb, "Format_Impor_Siswa_SDIT_ANNIHAYAH.xlsx");
    triggerToast("Template impor data siswa berhasil diunduh.", "success");
  };

  // Import Students from Excel File
  const handleImportStudentExcel = (e) => {
    const file = e.target.files[0];
    if (!file) return;
    if (!sheetJsLoaded || !window.XLSX) return triggerToast("Sistem Excel sedang dimuat, mohon tunggu...", "info");

    const reader = new FileReader();
    reader.onload = (event) => {
      try {
        const data = new Uint8Array(event.target.result);
        const workbook = window.XLSX.read(data, { type: 'array' });
        const sheetName = workbook.SheetNames[0];
        const worksheet = workbook.Sheets[sheetName];
        const jsonData = window.XLSX.utils.sheet_to_json(worksheet);

        let importedCount = 0;
        let updatedCount = 0;
        const newStudents = [...students];

        jsonData.forEach(row => {
          const rawNis = String(row['NIS'] || '').trim();
          const rawNisn = String(row['NISN'] || '').trim();
          const namaSiswa = row['Nama Siswa'];

          if (namaSiswa && rawNis) {
            const existingIdx = newStudents.findIndex(s => s.nis === rawNis);
            const studentData = {
              nama: namaSiswa,
              nis: rawNis,
              nisn: rawNisn,
              tempatLahir: row['Tempat Lahir'] || '',
              tanggalLahir: row['Tanggal Lahir (YYYY-MM-DD)'] || '',
              jenisKelamin: row['Jenis Kelamin (Laki-laki/Perempuan)'] === 'Perempuan' ? 'Perempuan' : 'Laki-laki',
              kelas: row['Kelas'] || classes[0],
              tahunAjaran: row['Tahun Ajaran'] || years[years.length - 1],
              status: row['Status (Aktif/Lulus/Mutasi Masuk/Mutasi Keluar)'] || 'Aktif',
              foto: ''
            };

            if (existingIdx > -1) {
              newStudents[existingIdx] = { ...newStudents[existingIdx], ...studentData };
              updatedCount++;
            } else {
              newStudents.push({
                ...studentData,
                id: 'std-' + Date.now() + Math.random().toString(36).substr(2, 4)
              });
              importedCount++;
            }
          }
        });

        setStudents(newStudents);
        triggerToast(`Berhasil memproses berkas: ${importedCount} siswa baru diimpor, ${updatedCount} siswa diperbarui.`, 'success');
      } catch (err) {
        triggerToast("Gagal membaca file Excel: " + err.message, "error");
      }
      e.target.value = null; 
    };
    reader.readAsArrayBuffer(file);
  };

  // Download Excel Template for Grades
  const handleDownloadTemplate = () => {
    if (!sheetJsLoaded || !window.XLSX) return triggerToast("Sistem Excel sedang dimuat, mohon tunggu sebentar...", "info");
    
    const currentClass = classFilter === 'Semua' ? classes[0] : classFilter;
    const currentStudents = students.filter(s => s.kelas === currentClass);
    
    if (currentStudents.length === 0) return triggerToast(`Tidak ada siswa di ${currentClass}. Harap tambahkan siswa terlebih dahulu.`, "error");

    const sampleSubj = getSubjectsForYear(currentStudents[0].tahunAjaran || '2024/2025');

    const dataTemplate = currentStudents.map(s => {
      const row = {
        'NIS': s.nis,
        'Nama Siswa': s.nama,
      };
      sampleSubj.forEach(subj => { row[subj] = ''; });
      row['Sakit'] = 0;
      row['Izin'] = 0;
      row['Alpa'] = 0;
      row['Catatan Guru'] = '';
      return row;
    });

    const ws = window.XLSX.utils.json_to_sheet(dataTemplate);
    const wb = window.XLSX.utils.book_new();
    window.XLSX.utils.book_append_sheet(wb, ws, "Format_Nilai");
    window.XLSX.writeFile(wb, `Format_Impor_Nilai_${currentClass}_${selectedSemester}.xlsx`);
    triggerToast("Berkas format impor nilai berhasil diunduh.", "success");
  };

  // Import grades from Excel sheet
  const handleImportLegerExcel = (e) => {
    const file = e.target.files[0];
    if (!file) return;
    if (!sheetJsLoaded || !window.XLSX) return triggerToast("Sistem Excel sedang dimuat...", "info");

    const reader = new FileReader();
    reader.onload = (event) => {
      try {
        const data = new Uint8Array(event.target.result);
        const workbook = window.XLSX.read(data, { type: 'array' });
        const sheetName = workbook.SheetNames[0];
        const worksheet = workbook.Sheets[sheetName];
        const jsonData = window.XLSX.utils.sheet_to_json(worksheet);

        const newGrades = { ...grades };
        const newAttendance = { ...attendance };
        const newNotes = { ...notes };

        let successCount = 0;
        const currentClass = classFilter === 'Semua' ? classes[0] : classFilter;
        const classSemKey = `${currentClass}_${selectedSemester}`;

        jsonData.forEach(row => {
          const student = students.find(s => s.nis == row['NIS']);
          if (student) {
            const studentId = student.id;
            const subjects = getSubjectsForYear(student.tahunAjaran);
            
            const studentScores = {};
            subjects.forEach(subj => {
              if (row[subj] !== undefined) studentScores[subj] = Number(row[subj]);
            });
            if (!newGrades[studentId]) newGrades[studentId] = {};
            newGrades[studentId][classSemKey] = studentScores;

            if (!newAttendance[studentId]) newAttendance[studentId] = {};
            newAttendance[studentId][classSemKey] = {
              sakit: parseInt(row['Sakit']) || 0,
              izin: parseInt(row['Izin']) || 0,
              alfa: parseInt(row['Alpa']) || 0
            };

            if (!newNotes[studentId]) newNotes[studentId] = {};
            newNotes[studentId][classSemKey] = row['Catatan Guru'] || '';

            successCount++;
          }
        });

        setGrades(newGrades);
        setAttendance(newAttendance);
        setNotes(newNotes);
        triggerToast(`Berhasil mengimpor data nilai untuk ${successCount} siswa di ${currentClass} - Semester ${selectedSemester}`, 'success');
      } catch (err) {
        triggerToast("Gagal memproses file Excel: " + err.message, "error");
      }
      e.target.value = null; 
    };
    reader.readAsArrayBuffer(file);
  };

  const activeStudentForGrades = useMemo(() => {
    return students.find(s => s.id === selectedStudentGradeId) || null;
  }, [selectedStudentGradeId, students]);

  const currentClassSemesterKey = `${selectedGradeClass}_${selectedSemester}`;

  const currentGradesForEdit = useMemo(() => {
    if (!selectedStudentGradeId || !selectedGradeClass) return {};
    return grades[selectedStudentGradeId]?.[currentClassSemesterKey] || {};
  }, [selectedStudentGradeId, selectedGradeClass, selectedSemester, grades]);

  const currentAttendanceForEdit = useMemo(() => {
    if (!selectedStudentGradeId || !selectedGradeClass) return { sakit: 0, izin: 0, alfa: 0 };
    return attendance[selectedStudentGradeId]?.[currentClassSemesterKey] || { sakit: 0, izin: 0, alfa: 0 };
  }, [selectedStudentGradeId, selectedGradeClass, selectedSemester, attendance]);

  const currentNoteForEdit = useMemo(() => {
    if (!selectedStudentGradeId || !selectedGradeClass) return '';
    return notes[selectedStudentGradeId]?.[currentClassSemesterKey] || '';
  }, [selectedStudentGradeId, selectedGradeClass, selectedSemester, notes]);

  const handleSaveAcademicRecord = (subjectGrades, attObj, noteStr) => {
    if (!selectedStudentGradeId || !selectedGradeClass) {
      triggerToast("Pilih siswa dan kelas terlebih dahulu!", "error");
      return;
    }

    setGrades(prev => {
      const studentGrades = prev[selectedStudentGradeId] || {};
      return {
        ...prev,
        [selectedStudentGradeId]: {
          ...studentGrades,
          [currentClassSemesterKey]: subjectGrades
        }
      };
    });

    setAttendance(prev => {
      const studentAtt = prev[selectedStudentGradeId] || {};
      return {
        ...prev,
        [selectedStudentGradeId]: {
          ...studentAtt,
          [currentClassSemesterKey]: attObj
        }
      };
    });

    setNotes(prev => {
      const studentNotes = prev[selectedStudentGradeId] || {};
      return {
        ...prev,
        [selectedStudentGradeId]: {
          ...studentNotes,
          [currentClassSemesterKey]: noteStr
        }
      };
    });

    triggerToast("Data Nilai Rapor, Absensi, dan Catatan Guru berhasil disimpan!", 'success');
  };

  const handleAddClass = (e) => {
    e.preventDefault();
    if (newClassName && !classes.includes(newClassName)) {
      setClasses(prev => [...prev, newClassName].sort());
      setNewClassName('');
      triggerToast(`Kelas ${newClassName} berhasil ditambahkan!`, 'success');
    }
  };

  const handleDeleteClass = (clsName) => {
    triggerConfirm(`Apakah Anda yakin ingin menghapus ${clsName}?`, () => {
      setClasses(prev => prev.filter(c => c !== clsName));
      triggerToast(`Kelas ${clsName} berhasil dihapus.`, 'success');
    });
  };

  const handleAddYear = (e) => {
    e.preventDefault();
    if (newYearName && !years.includes(newYearName)) {
      setYears(prev => [...prev, newYearName]);
      setNewYearName('');
      triggerToast(`Tahun Ajaran ${newYearName} berhasil ditambahkan!`, 'success');
    }
  };

  const handleDeleteYear = (yrName) => {
    triggerConfirm(`Apakah Anda yakin ingin menghapus Tahun Ajaran ${yrName}?`, () => {
      setYears(prev => prev.filter(y => y !== yrName));
      triggerToast(`Tahun Ajaran ${yrName} berhasil dihapus.`, 'success');
    });
  };

  const handleExportJSON = () => {
    const fullDB = {
      students,
      grades,
      attendance,
      notes,
      classes,
      years
    };
    const blob = new Blob([JSON.stringify(fullDB, null, 2)], { type: 'application/json' });
    const url = URL.createObjectURL(blob);
    const link = document.createElement('a');
    link.href = url;
    link.download = `backup_database_sdit_annihayah_${Date.now()}.json`;
    link.click();
    URL.revokeObjectURL(url);
    triggerToast("File cadangan database (.json) berhasil diunduh.", "success");
  };

  const handleImportJSON = (e) => {
    const file = e.target.files[0];
    if (!file) return;

    const reader = new FileReader();
    reader.onload = (event) => {
      try {
        const imported = JSON.parse(event.target.result);
        if (imported.students && imported.grades && imported.classes && imported.years) {
          setStudents(imported.students);
          setGrades(imported.grades);
          setAttendance(imported.attendance || {});
          setNotes(imported.notes || {});
          setClasses(imported.classes);
          setYears(imported.years);
          triggerToast("Database SDIT ANNIHAYAH Berhasil Dipulihkan Sepenuhnya!", 'success');
        } else {
          triggerToast("Format berkas cadangan tidak valid!", 'error');
        }
      } catch (err) {
        triggerToast("Gagal membaca berkas: " + err.message, 'error');
      }
    };
    reader.readAsText(file);
  };

  const handleExportExcel = () => {
    if (!sheetJsLoaded || !window.XLSX) {
      triggerToast("Sedang memuat sistem pendukung Excel. Silakan coba kembali dalam beberapa detik.", "info");
      return;
    }

    try {
      const wb = window.XLSX.utils.book_new();

      const studentRows = students.map(s => {
        const base = {
          'Nama Siswa': s.nama,
          'NIS': s.nis,
          'NISN': s.nisn,
          'Tempat Lahir': s.tempatLahir || '-',
          'Tanggal Lahir': s.tanggalLahir || '-',
          'Kelas Sekarang': s.kelas,
          'Tahun Ajaran': s.tahunAjaran,
          'Status': s.status,
          'Jenis Kelamin': s.jenisKelamin,
        };
        if (s.status === 'Lulus') {
          return {
            ...base,
            'No Ijazah': s.noIjazah,
            'Tanggal Lulus': s.tanggalLulus,
            'Rata-rata Nilai': s.nilaiRataIjazah
          };
        } else if (s.status === 'Mutasi Masuk') {
          return {
            ...base,
            'Tanggal Masuk': s.tanggalMasuk,
            'Sekolah Asal': s.sekolahAsal,
            'Alamat Asal': s.alamatAsal
          };
        } else if (s.status === 'Mutasi Keluar') {
          return {
            ...base,
            'Tanggal Keluar': s.tanggalKeluar,
            'Sekolah Tujuan': s.sekolahTujuan,
            'Alamat Tujuan': s.alamatTujuan
          };
        }
        return base;
      });
      const wsStudents = window.XLSX.utils.json_to_sheet(studentRows);
      window.XLSX.utils.book_append_sheet(wb, wsStudents, "Data Induk Siswa");

      const gradeRows = [];
      students.forEach(s => {
        const studentGrades = grades[s.id] || {};
        Object.keys(studentGrades).forEach(classSemKey => {
          const mapelScores = studentGrades[classSemKey] || {};
          const att = attendance[s.id]?.[classSemKey] || { sakit: 0, izin: 0, alfa: 0 };
          const noteText = notes[s.id]?.[classSemKey] || '';

          gradeRows.push({
            'NIS': s.nis,
            'Nama Siswa': s.nama,
            'Kelas & Semester': classSemKey,
            ...mapelScores,
            'Sakit': att.sakit,
            'Izin': att.izin,
            'Alpa': att.alfa,
            'Catatan Guru': noteText
          });
        });
      });
      if (gradeRows.length > 0) {
        const wsGrades = window.XLSX.utils.json_to_sheet(gradeRows);
        window.XLSX.utils.book_append_sheet(wb, wsGrades, "Rekap Nilai Rapor");
      }

      window.XLSX.writeFile(wb, `Laporan_Administrasi_SDIT_ANNIHAYAH_${Date.now()}.xlsx`);
      triggerToast("Seluruh database berhasil diekspor ke file Excel.", "success");
    } catch (error) {
      triggerToast("Gagal mengekspor data ke Excel: " + error.message, 'error');
    }
  };

  const handleOpenPrintPreview = (studentId, classSem) => {
    setPrintStudentId(studentId);
    setPrintClassSem(classSem);
  };

  // =========================================================================
  // VIEW RENDERER DISPATCHER (Mencegah galat objek anak React)
  // =========================================================================
  
  const renderMainTabContent = () => {
    if (activeTab.startsWith('alumni-detail-')) {
      const alumniId = activeTab.replace('alumni-detail-', '');
      const s = students.find(x => x.id === alumniId);
      if (!s) return <p className="p-6">Data Alumni tidak ditemukan.</p>;

      const studentGrades = grades[s.id] || {};
      const semesters = ['Ganjil', 'Genap'];

      return (
        <div className="space-y-6">
          <div className="flex items-center justify-between border-b pb-4">
            <div>
              <button
                onClick={() => setActiveTab('riwayatalumni')}
                className="text-emerald-700 font-semibold hover:underline text-sm flex items-center gap-1 mb-2"
              >
                ← Kembali ke Pusat Alumni
              </button>
              <h2 className="text-2xl font-bold text-slate-800"> Rekam Jejak Nilai Kumulatif</h2>
              <p className="text-sm text-slate-500">Portfolio Akademik Siswa dari Kelas 1 sampai Kelas 6.</p>
            </div>
            <div className="text-right">
              <span className="block text-xs uppercase text-slate-400 font-bold">Rata-Rata Kelulusan</span>
              <span className="text-3xl font-black text-emerald-800">{s.nilaiRataIjazah || '-'}</span>
            </div>
          </div>

          <div className="bg-emerald-50 border border-emerald-100 rounded-2xl p-5 flex flex-col md:flex-row gap-6">
            <div className="w-24 h-24 bg-white border-2 border-emerald-200 rounded-full flex items-center justify-center text-4xl overflow-hidden self-center shadow">
              {s.foto ? <img src={s.foto} alt="Foto" className="w-full h-full object-cover" /> : '🎓'}
            </div>
            <div className="grid grid-cols-1 md:grid-cols-2 gap-4 flex-1 text-sm">
              <div>
                <p className="text-xs text-emerald-700 font-bold">Nama Lengkap</p>
                <p className="font-extrabold text-slate-800 text-base">{s.nama}</p>
                <p className="text-xs text-slate-500 mt-1">NIS / NISN: {s.nis} / {s.nisn}</p>
                <p className="text-xs text-slate-500">Lahir: {s.tempatLahir || '-'}, {s.tanggalLahir ? formatDateString(s.tanggalLahir) : '-'}</p>
              </div>
              <div>
                <p className="text-xs text-emerald-700 font-bold">Informasi Ijazah</p>
                <p className="font-bold text-slate-800">Nomor: {s.noIjazah || '-'}</p>
                <p className="text-xs text-slate-500">Tanggal Kelulusan: {formatDateString(s.tanggalLulus)}</p>
              </div>
            </div>
          </div>

          <div className="space-y-4">
            <h3 className="font-bold text-slate-800 text-lg">📁 Rincian Transkrip Nilai per Semester</h3>
            <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
              {classes.map(cls => {
                return semesters.map(sem => {
                  const key = `${cls}_${sem}`;
                  const record = studentGrades[key];
                  if (!record) return null;

                  const subjects = Object.keys(record);
                  const total = subjects.reduce((sum, m) => sum + parseFloat(record[m] || 0), 0);
                  const avg = (total / (subjects.length || 1)).toFixed(1);

                  return (
                    <div key={key} className="bg-white border border-slate-200 rounded-xl p-4 shadow-sm hover:shadow transition">
                      <div className="flex justify-between items-center border-b pb-2 mb-3">
                        <span className="font-extrabold text-emerald-800">{cls} - {sem}</span>
                        <span className="bg-emerald-100 text-emerald-800 text-xs font-bold px-2 py-0.5 rounded-full">
                          Rata-Rata: {avg}
                        </span>
                      </div>
                      <div className="grid grid-cols-2 gap-y-1.5 gap-x-4 text-xs">
                        {subjects.map(subj => (
                          <div key={subj} className="flex justify-between border-b border-dashed border-slate-100 pb-1">
                            <span className="text-slate-500">{subj}</span>
                            <span className="font-bold text-slate-800">{record[subj]}</span>
                          </div>
                        ))}
                      </div>
                    </div>
                  );
                });
              })}
            </div>
          </div>
        </div>
      );
    }

    switch (activeTab) {
      case 'dashboard':
        return (
          <div className="space-y-6">
            <div>
              <h2 className="text-2xl font-bold text-slate-800">Assalamu'alaikum, Admin SDIT ANNIHAYAH 🌸</h2>
              <p className="text-slate-500 text-sm mt-1">Berikut adalah ringkasan statistik administrasi kearsipan sekolah terpusat.</p>
            </div>

            <div className="grid grid-cols-2 lg:grid-cols-3 gap-4">
              <div className="bg-emerald-50 border border-emerald-100 p-4 rounded-xl">
                <p className="text-xs font-bold text-emerald-800 uppercase tracking-wide">Siswa Aktif</p>
                <p className="text-3xl font-extrabold text-emerald-900 mt-1">{stats.aktif} <span className="text-sm font-normal text-emerald-700">anak</span></p>
              </div>
              <div className="bg-blue-50 border border-blue-100 p-4 rounded-xl">
                <p className="text-xs font-bold text-blue-800 uppercase tracking-wide">Total Alumni Lulus</p>
                <p className="text-3xl font-extrabold text-blue-900 mt-1">{stats.lulus} <span className="text-sm font-normal text-blue-700">siswa</span></p>
              </div>
              <div className="bg-purple-50 border border-purple-100 p-4 rounded-xl">
                <p className="text-xs font-bold text-purple-800 uppercase tracking-wide">Mutasi Masuk</p>
                <p className="text-3xl font-extrabold text-purple-900 mt-1">{stats.mutasiMasuk} <span className="text-sm font-normal text-purple-700">siswa</span></p>
              </div>
              <div className="bg-amber-50 border border-amber-100 p-4 rounded-xl">
                <p className="text-xs font-bold text-amber-800 uppercase tracking-wide">Mutasi Keluar</p>
                <p className="text-3xl font-extrabold text-amber-900 mt-1">{stats.mutasiKeluar} <span className="text-sm font-normal text-amber-700">siswa</span></p>
              </div>
              <div className="bg-teal-50 border border-teal-100 p-4 rounded-xl">
                <p className="text-xs font-bold text-teal-800 uppercase tracking-wide">Laki-Laki</p>
                <p className="text-3xl font-extrabold text-teal-900 mt-1">{stats.laki} <span className="text-sm font-normal text-teal-700">siswa</span></p>
              </div>
              <div className="bg-rose-50 border border-rose-100 p-4 rounded-xl">
                <p className="text-xs font-bold text-rose-800 uppercase tracking-wide">Perempuan</p>
                <p className="text-3xl font-extrabold text-rose-900 mt-1">{stats.perempuan} <span className="text-sm font-normal text-rose-700">siswi</span></p>
              </div>
            </div>

            <div className="bg-slate-50 p-5 rounded-xl border border-slate-200">
              <h3 className="text-sm font-bold text-slate-700 mb-4 flex items-center gap-2">
                <span>📈</span> Grafik Komparasi Data Siswa
              </h3>
              <div className="h-64 flex items-end justify-around px-4 pb-4 border-b border-slate-300">
                {[
                  { label: 'Siswa Aktif', value: stats.aktif, color: '#047857' },
                  { label: 'Lulusan', value: stats.lulus, color: '#1d4ed8' },
                  { label: 'Mutasi Masuk', value: stats.mutasiMasuk, color: '#7e22ce' },
                  { label: 'Mutasi Keluar', value: stats.mutasiKeluar, color: '#b45309' },
                ].map((item, idx) => {
                  const maxVal = Math.max(stats.aktif, stats.lulus, stats.mutasiMasuk, stats.mutasiKeluar, 1);
                  const percentHeight = (item.value / maxVal) * 80;

                  return (
                    <div key={idx} className="flex flex-col items-center w-1/5 group">
                      <span className="text-xs font-bold mb-2 text-slate-700 opacity-0 group-hover:opacity-100 transition-opacity bg-white px-2 py-1 rounded shadow border border-slate-100">
                        {item.value} Anak
                      </span>
                      <div
                        style={{ height: `${percentHeight || 8}px`, backgroundColor: item.color }}
                        className="w-full max-w-[40px] rounded-t-md shadow transition-all duration-500 hover:brightness-110"
                      ></div>
                      <span className="text-xs font-semibold text-slate-600 mt-2 text-center break-words w-full">
                        {item.label}
                      </span>
                    </div>
                  );
                })}
              </div>
            </div>
          </div>
        );

      case 'datasiswa':
        return (
          <div className="space-y-6">
            <div className="flex flex-col sm:flex-row sm:items-center sm:justify-between gap-4">
              <div>
                <h2 className="text-xl font-bold text-slate-800">📁 Data Induk Administrasi Siswa</h2>
                <p className="text-slate-500 text-sm">Menampilkan seluruh data arsip siswa, lulusan, dan riwayat mutasi.</p>
              </div>
              
              <div className="flex flex-wrap gap-2">
                <button
                  onClick={handleDownloadStudentTemplate}
                  className="bg-emerald-50 hover:bg-emerald-100 text-emerald-700 border border-emerald-200 font-bold py-2 px-3 rounded-lg text-xs shadow-sm transition flex items-center gap-1.5"
                >
                  <span>📥</span> Unduh Format Excel Siswa
                </button>
                
                <label className="cursor-pointer bg-emerald-700 hover:bg-emerald-600 text-white font-bold py-2 px-3 rounded-lg text-xs shadow transition flex items-center gap-1.5">
                  <span>📤</span> Unggah / Impor Data Siswa
                  <input 
                    type="file" 
                    accept=".xlsx, .xls" 
                    className="hidden" 
                    onChange={handleImportStudentExcel}
                  />
                </label>

                {userRole === 'admin' && (
                  <button
                    onClick={handleOpenAddModal}
                    className="bg-emerald-900 hover:bg-emerald-950 text-white font-bold px-3 py-2 rounded-lg text-xs shadow transition shrink-0"
                  >
                    + Tambah Siswa Baru 🎒
                  </button>
                )}
              </div>
            </div>

            <div className="grid grid-cols-1 sm:grid-cols-3 gap-3 bg-slate-50 p-4 rounded-xl border border-slate-200">
              <div className="relative">
                <label className="block text-xs font-semibold text-slate-500 mb-1">Cari Nama/NIS/NISN/Tgl Lahir</label>
                <input
                  type="text"
                  placeholder="Cari..."
                  value={studentSearch}
                  onChange={(e) => setStudentSearch(e.target.value)}
                  className="w-full px-3 py-1.5 text-sm bg-white border border-slate-300 rounded-lg outline-none focus:ring-1 focus:ring-emerald-500"
                />
              </div>

              <div>
                <label className="block text-xs font-semibold text-slate-500 mb-1">Filter Status</label>
                <select
                  value={statusFilter}
                  onChange={(e) => setStatusFilter(e.target.value)}
                  className="w-full px-3 py-1.5 text-sm bg-white border border-slate-300 rounded-lg outline-none focus:ring-1 focus:ring-emerald-500"
                >
                  <option value="Semua">Semua Status</option>
                  <option value="Aktif">Aktif</option>
                  <option value="Lulus">Lulus</option>
                  <option value="Mutasi Masuk">Mutasi Masuk</option>
                  <option value="Mutasi Keluar">Mutasi Keluar</option>
                </select>
              </div>

              <div>
                <label className="block text-xs font-semibold text-slate-500 mb-1">Filter Kelas Sekarang</label>
                <select
                  value={classFilter}
                  onChange={(e) => setClassFilter(e.target.value)}
                  className="w-full px-3 py-1.5 text-sm bg-white border border-slate-300 rounded-lg outline-none focus:ring-1 focus:ring-emerald-500"
                >
                  <option value="Semua">Semua Kelas</option>
                  {classes.map(c => <option key={c} value={c}>{c}</option>)}
                </select>
              </div>
            </div>

            <div className="overflow-x-auto border border-slate-200 rounded-xl shadow-sm">
              <table className="w-full text-left border-collapse">
                <thead>
                  <tr className="bg-slate-50 text-slate-600 text-xs font-bold uppercase border-b border-slate-200">
                    <th className="px-4 py-3 cursor-pointer select-none" onClick={() => requestSort('nama')}>
                      Siswa {sortField === 'nama' ? (sortDirection === 'asc' ? '▲' : '▼') : ''}
                    </th>
                    <th className="px-4 py-3 cursor-pointer select-none" onClick={() => requestSort('nis')}>
                      Identitas {sortField === 'nis' ? (sortDirection === 'asc' ? '▲' : '▼') : ''}
                    </th>
                    <th className="px-4 py-3 cursor-pointer select-none" onClick={() => requestSort('tempatLahir')}>
                      Tempat, Tanggal Lahir {sortField === 'tempatLahir' ? (sortDirection === 'asc' ? '▲' : '▼') : ''}
                    </th>
                    <th className="px-4 py-3 cursor-pointer select-none" onClick={() => requestSort('kelas')}>
                      Kelas & TA {sortField === 'kelas' ? (sortDirection === 'asc' ? '▲' : '▼') : ''}
                    </th>
                    <th className="px-4 py-3 cursor-pointer select-none" onClick={() => requestSort('status')}>
                      Status {sortField === 'status' ? (sortDirection === 'asc' ? '▲' : '▼') : ''}
                    </th>
                    <th className="px-4 py-3">Informasi Status Khusus</th>
                    {userRole === 'admin' && <th className="px-4 py-3 text-right">Aksi</th>}
                  </tr>
                </thead>
                <tbody className="divide-y divide-slate-100 text-sm text-slate-700">
                  {filteredStudents.length === 0 ? (
                    <tr>
                      <td colSpan="7" className="text-center py-8 text-slate-400 font-medium">
                        Tidak ada data siswa yang cocok dengan pencarian/filter.
                      </td>
                    </tr>
                  ) : (
                    filteredStudents.map(student => (
                      <tr key={student.id} className="hover:bg-slate-50/80 transition-colors">
                        <td className="px-4 py-3">
                          <div className="flex items-center space-x-3">
                            <div className="w-10 h-10 rounded-full bg-slate-100 border border-slate-200 overflow-hidden flex items-center justify-center flex-shrink-0">
                              {student.foto ? (
                                <img src={student.foto} alt="Siswa" className="w-full h-full object-cover" />
                              ) : (
                                <span className="text-lg">{student.jenisKelamin === 'Laki-laki' ? '👦' : '👧'}</span>
                              )}
                            </div>
                            <div>
                              <p className="font-bold text-slate-800">{student.nama}</p>
                              <span className="text-xs text-slate-400 font-medium">{student.jenisKelamin}</span>
                            </div>
                          </div>
                        </td>
                        <td className="px-4 py-3">
                          <p className="text-xs">NIS: <span className="font-semibold text-slate-800">{student.nis}</span></p>
                          <p className="text-xs">NISN: <span className="font-semibold text-slate-800">{student.nisn}</span></p>
                        </td>
                        <td className="px-4 py-3 text-xs">
                          <p className="font-semibold text-slate-800">{student.tempatLahir || '-'}</p>
                          <p className="text-slate-500">{student.tanggalLahir ? formatDateString(student.tanggalLahir) : '-'}</p>
                        </td>
                        <td className="px-4 py-3">
                          <p className="font-semibold text-slate-800">{student.kelas}</p>
                          <span className="text-xs text-slate-400">TA {student.tahunAjaran}</span>
                        </td>
                        <td className="px-4 py-3">
                          <span className={`inline-block px-2 py-0.5 rounded-full text-xs font-bold ${
                            student.status === 'Aktif' ? 'bg-emerald-50 text-emerald-700 border border-emerald-100' :
                            student.status === 'Lulus' ? 'bg-blue-50 text-blue-700 border border-blue-100' :
                            student.status === 'Mutasi Masuk' ? 'bg-purple-50 text-purple-700 border border-purple-100' :
                            'bg-amber-50 text-amber-700 border border-amber-100'
                          }`}>
                            {student.status}
                          </span>
                        </td>
                        <td className="px-4 py-3 text-xs leading-relaxed max-w-xs">
                          {student.status === 'Lulus' && (
                            <div className="text-slate-600 bg-blue-50/40 p-2 rounded border border-blue-100/50">
                              <p>📜 No. Ijazah: <span className="font-bold">{student.noIjazah || '-'}</span></p>
                              <p>📅 Tgl Lulus: {formatDateString(student.tanggalLulus)}</p>
                              <p>⭐ Rata-Rata: <span className="font-bold">{student.nilaiRataIjazah || '-'}</span></p>
                            </div>
                          )}
                          {student.status === 'Mutasi Masuk' && (
                            <div className="text-slate-600 bg-purple-50/40 p-2 rounded border border-purple-100/50">
                              <p>📅 Masuk: {formatDateString(student.tanggalMasuk)}</p>
                              <p>🏫 Asal: <span className="font-bold">{student.sekolahAsal || '-'}</span></p>
                            </div>
                          )}
                          {student.status === 'Mutasi Keluar' && (
                            <div className="text-slate-600 bg-amber-50/40 p-2 rounded border border-amber-100/50">
                              <p>📅 Keluar: {formatDateString(student.tanggalKeluar)}</p>
                              <p>🏫 Tujuan: <span className="font-bold">{student.sekolahTujuan || '-'}</span></p>
                            </div>
                          )}
                          {student.status === 'Aktif' && (
                            <span className="text-slate-400">Tidak ada info tambahan khusus</span>
                          )}
                        </td>
                        {userRole === 'admin' && (
                          <td className="px-4 py-3 text-right">
                            <div className="flex justify-end space-x-1.5">
                              <button
                                onClick={() => handleOpenEditModal(student)}
                                className="p-1.5 bg-slate-100 hover:bg-slate-200 rounded text-xs font-semibold text-slate-700 transition"
                                title="Edit Profil"
                              >
                                ✏️ Edit
                              </button>
                              <button
                                onClick={() => handleDeleteStudent(student.id)}
                                className="p-1.5 bg-rose-50 hover:bg-rose-100 rounded text-xs font-semibold text-rose-600 transition"
                                title="Hapus"
                              >
                                🗑️ Hapus
                              </button>
                            </div>
                          </td>
                        )}
                      </tr>
                    ))
                  )}
                </tbody>
              </table>
            </div>
          </div>
        );

      case 'mutasirekap':
        return (
          <div className="space-y-6">
            <div>
              <h2 className="text-xl font-bold text-slate-800">🔄 Rekapitulasi Mutasi Siswa</h2>
              <p className="text-slate-500 text-sm">Arsip khusus pelacakan mutasi siswa masuk dan mutasi siswa keluar secara terpusat.</p>
            </div>

            <div className="grid grid-cols-1 lg:grid-cols-2 gap-6">
              <div className="border border-slate-200 rounded-xl p-4 bg-white shadow-sm">
                <h3 className="font-bold text-slate-800 text-md border-b pb-2 mb-3 flex items-center justify-between">
                  <span>📥 Siswa Mutasi Masuk</span>
                  <span className="bg-purple-100 text-purple-800 text-xs px-2 py-0.5 rounded-full font-bold">
                    {students.filter(s => s.status === 'Mutasi Masuk').length} Siswa
                  </span>
                </h3>
                <div className="space-y-3 max-h-[400px] overflow-y-auto pr-1">
                  {students.filter(s => s.status === 'Mutasi Masuk').length === 0 ? (
                    <p className="text-sm text-slate-400 text-center py-6">Belum ada data siswa mutasi masuk.</p>
                  ) : (
                    students.filter(s => s.status === 'Mutasi Masuk').map(s => (
                      <div key={s.id} className="p-3 bg-purple-50/50 border border-purple-100 rounded-lg text-xs space-y-1">
                        <p className="font-bold text-sm text-slate-800">{s.nama} ({s.nisn})</p>
                        <p className="text-slate-600">🏫 Sekolah Asal: <span className="font-semibold">{s.sekolahAsal}</span></p>
                        <p className="text-slate-600">📅 Tanggal Masuk: {formatDateString(s.tanggalMasuk)}</p>
                        <p className="text-slate-500 italic">Alamat: {s.alamatAsal}</p>
                      </div>
                    ))
                  )}
                </div>
              </div>

              <div className="border border-slate-200 rounded-xl p-4 bg-white shadow-sm">
                <h3 className="font-bold text-slate-800 text-md border-b pb-2 mb-3 flex items-center justify-between">
                  <span>📤 Siswa Mutasi Keluar</span>
                  <span className="bg-amber-100 text-amber-800 text-xs px-2 py-0.5 rounded-full font-bold">
                    {students.filter(s => s.status === 'Mutasi Keluar').length} Siswa
                  </span>
                </h3>
                <div className="space-y-3 max-h-[400px] overflow-y-auto pr-1">
                  {students.filter(s => s.status === 'Mutasi Keluar').length === 0 ? (
                    <p className="text-sm text-slate-400 text-center py-6">Belum ada data siswa mutasi keluar.</p>
                  ) : (
                    students.filter(s => s.status === 'Mutasi Keluar').map(s => (
                      <div key={s.id} className="p-3 bg-amber-50/50 border border-amber-100 rounded-lg text-xs space-y-1">
                        <p className="font-bold text-sm text-slate-800">{s.nama} ({s.nisn})</p>
                        <p className="text-slate-600">🏫 Sekolah Tujuan: <span className="font-semibold">{s.sekolahTujuan}</span></p>
                        <p className="text-slate-600">📅 Tanggal Keluar: {formatDateString(s.tanggalKeluar)}</p>
                        <p className="text-slate-500 italic">Alamat Tujuan: {s.alamatTujuan}</p>
                      </div>
                    ))
                  )}
                </div>
              </div>
            </div>
          </div>
        );

      case 'inputnilai':
        return (
          <div className="space-y-6">
            <div>
              <h2 className="text-xl font-bold text-slate-800">📝 Pengisian Nilai Rapor & Absensi Terpadu</h2>
              <p className="text-slate-500 text-sm">Silakan pilih siswa aktif, alumni, atau pindahan untuk mengelola nilai semesteran.</p>
            </div>

            <div className="grid grid-cols-1 md:grid-cols-4 gap-4 bg-slate-50 p-4 rounded-xl border border-slate-200">
              <div className="md:col-span-2">
                <label className="block text-xs font-bold text-slate-500 mb-1">Pilih Siswa</label>
                <select
                  value={selectedStudentGradeId}
                  onChange={(e) => {
                    const sId = e.target.value;
                    setSelectedStudentGradeId(sId);
                    const std = students.find(s => s.id === sId);
                    if (std) {
                      setSelectedGradeClass(std.kelas);
                    }
                  }}
                  className="w-full px-3 py-1.5 text-sm bg-white border border-slate-300 rounded-lg focus:ring-1 focus:ring-emerald-500 outline-none"
                >
                  <option value="">-- Pilih Siswa --</option>
                  {students.map(s => (
                    <option key={s.id} value={s.id}>{s.nama} ({s.status} - {s.kelas})</option>
                  ))}
                </select>
              </div>

              <div>
                <label className="block text-xs font-bold text-slate-500 mb-1">Tingkat Kelas Input</label>
                <select
                  value={selectedGradeClass}
                  onChange={(e) => setSelectedGradeClass(e.target.value)}
                  className="w-full px-3 py-1.5 text-sm bg-white border border-slate-300 rounded-lg focus:ring-1 focus:ring-emerald-500 outline-none"
                >
                  <option value="">-- Pilih Kelas --</option>
                  {classes.map(c => <option key={c} value={c}>{c}</option>)}
                </select>
              </div>

              <div>
                <label className="block text-xs font-bold text-slate-500 mb-1">Semester</label>
                <select
                  value={selectedSemester}
                  onChange={(e) => setSelectedSemester(e.target.value)}
                  className="w-full px-3 py-1.5 text-sm bg-white border border-slate-300 rounded-lg focus:ring-1 focus:ring-emerald-500 outline-none"
                >
                  <option value="Ganjil">Ganjil</option>
                  <option value="Genap">Genap</option>
                </select>
              </div>
            </div>

            {activeStudentForGrades && selectedGradeClass ? (
              <GradeFormContainer
                student={activeStudentForGrades}
                classSemesterKey={currentClassSemesterKey}
                initialGrades={currentGradesForEdit}
                initialAttendance={currentAttendanceForEdit}
                initialNote={currentNoteForEdit}
                subjects={getSubjectsForYear(activeStudentForGrades.tahunAjaran)}
                onSave={handleSaveAcademicRecord}
              />
            ) : (
              <div className="text-center py-12 text-slate-400 border-2 border-dashed border-slate-200 rounded-xl">
                <span className="text-4xl">📝</span>
                <p className="mt-2 font-medium">Silakan tentukan Siswa dan Tingkat Kelas untuk mulai mengisi nilai.</p>
              </div>
            )}
          </div>
        );

      case 'legerrapor':
        return (
          <div className="space-y-6">
            <div className="flex flex-col sm:flex-row sm:items-center justify-between gap-4">
              <div>
                <h2 className="text-xl font-bold text-slate-800">📋 Leger Nilai Rapor & Impor Massal</h2>
                <p className="text-slate-500 text-sm">Rekapitulasi nilai per kelas. Gunakan fitur impor Excel untuk pengisian cepat.</p>
              </div>
              
              <div className="flex flex-wrap gap-2">
                <button
                  onClick={handleDownloadTemplate}
                  className="bg-emerald-50 hover:bg-emerald-100 text-emerald-700 border border-emerald-200 font-bold py-2 px-4 rounded-lg text-xs shadow-sm transition flex items-center gap-2"
                >
                  <span>📥</span> Unduh Format Excel
                </button>
                
                <label className="cursor-pointer bg-emerald-700 hover:bg-emerald-600 text-white font-bold py-2 px-4 rounded-lg text-xs shadow transition flex items-center gap-2">
                  <span>📤</span> Unggah/Impor Nilai
                  <input 
                    type="file" 
                    accept=".xlsx, .xls" 
                    className="hidden" 
                    onChange={handleImportLegerExcel}
                  />
                </label>
              </div>
            </div>

            <div className="grid grid-cols-1 md:grid-cols-2 gap-4 bg-emerald-50/50 p-4 rounded-xl border border-emerald-100">
              <div>
                <label className="block text-[10px] font-bold text-emerald-800 uppercase mb-1">Pilih Kelas</label>
                <select
                  value={classFilter}
                  onChange={(e) => setClassFilter(e.target.value)}
                  className="w-full px-3 py-2 text-sm bg-white border border-emerald-200 rounded-lg outline-none focus:ring-1 focus:ring-emerald-500"
                >
                  <option value="Semua">Semua Kelas</option>
                  {classes.map(c => <option key={c} value={c}>{c}</option>)}
                </select>
              </div>

              <div>
                <label className="block text-[10px] font-bold text-emerald-800 uppercase mb-1">Pilih Semester</label>
                <select
                  value={selectedSemester}
                  onChange={(e) => setSelectedSemester(e.target.value)}
                  className="w-full px-3 py-2 text-sm bg-white border border-emerald-200 rounded-lg outline-none focus:ring-1 focus:ring-emerald-500"
                >
                  <option value="Ganjil">Semester Ganjil</option>
                  <option value="Genap">Semester Genap</option>
                </select>
              </div>
            </div>

            <div className="overflow-x-auto border border-slate-200 rounded-xl shadow-sm bg-white">
              <table className="w-full text-left border-collapse">
                <thead>
                  <tr className="bg-slate-50 text-slate-600 text-xs font-bold uppercase border-b border-slate-200">
                    <th className="px-4 py-3">Nama Siswa</th>
                    <th className="px-4 py-3">NIS</th>
                    {getSubjectsForYear(years[years.length - 1]).map(subj => (
                      <th key={subj} className="px-3 py-3 text-center">{subj}</th>
                    ))}
                    <th className="px-4 py-3 text-center bg-slate-100 font-bold">Rata-Rata</th>
                    <th className="px-4 py-3 text-center">Absensi (S-I-A)</th>
                    <th className="px-4 py-3 text-center">Aksi Rapor</th>
                  </tr>
                </thead>
                <tbody className="divide-y divide-slate-100 text-sm text-slate-700">
                  {students
                    .filter(s => s.kelas === (classFilter === 'Semua' ? classes[0] : classFilter))
                    .map(s => {
                      const classSemKey = `${classFilter === 'Semua' ? classes[0] : classFilter}_${selectedSemester}`;
                      const currentSubjGrades = grades[s.id]?.[classSemKey] || {};
                      const attObj = attendance[s.id]?.[classSemKey] || { sakit: 0, izin: 0, alfa: 0 };

                      const activeSubjects = getSubjectsForYear(s.tahunAjaran);
                      const totalScores = activeSubjects.reduce((acc, curr) => acc + (parseFloat(currentSubjGrades[curr]) || 0), 0);
                      const gradesCount = activeSubjects.filter(curr => currentSubjGrades[curr] !== undefined && currentSubjGrades[curr] !== '').length;
                      const avg = gradesCount > 0 ? (totalScores / gradesCount).toFixed(1) : '-';

                      return (
                        <tr key={s.id} className="hover:bg-slate-50/50">
                          <td className="px-4 py-3 font-semibold text-slate-800">{s.nama}</td>
                          <td className="px-4 py-3 text-xs">{s.nis}</td>
                          
                          {getSubjectsForYear(years[years.length - 1]).map(subj => {
                            const score = currentSubjGrades[subj];
                            const isExist = activeSubjects.includes(subj);
                            return (
                              <td key={subj} className="px-3 py-3 text-center">
                                {!isExist ? (
                                  <span className="text-slate-300">-</span>
                                ) : (
                                  <span className={score !== undefined && parseFloat(score) < 70 ? "text-rose-600 font-bold" : "font-medium"}>
                                    {score ?? 'N/A'}
                                  </span>
                                )}
                              </td>
                            );
                          })}

                          <td className="px-4 py-3 text-center bg-slate-50 font-bold text-slate-800">{avg}</td>
                          <td className="px-4 py-3 text-center text-xs">
                            <span className="bg-rose-50 text-rose-700 px-1.5 py-0.5 rounded font-bold">{attObj.sakit}</span> / <span className="bg-amber-50 text-amber-700 px-1.5 py-0.5 rounded font-bold">{attObj.izin}</span> / <span className="bg-red-50 text-red-700 px-1.5 py-0.5 rounded font-bold">{attObj.alfa}</span>
                          </td>
                          <td className="px-4 py-3 text-center">
                            <button
                              onClick={() => handleOpenPrintPreview(s.id, classSemKey)}
                              className="bg-emerald-100 hover:bg-emerald-200 text-emerald-800 text-xs font-bold px-3 py-1 rounded-lg transition"
                            >
                              🖨️ Cetak Rapor
                            </button>
                          </td>
                        </tr>
                      );
                    })}
                </tbody>
              </table>
            </div>
          </div>
        );

      case 'riwayatalumni':
        return (
          <div className="space-y-6">
            <div>
              <h2 className="text-xl font-bold text-slate-800">🎓 Pusat Arsip Riwayat Alumni & Kelulusan</h2>
              <p className="text-slate-500 text-sm">Menampilkan portofolio kumulatif riwayat nilai Kelas 1 hingga Kelas 6 secara otomatis.</p>
            </div>

            <div className="overflow-x-auto border border-slate-200 rounded-xl shadow-sm">
              <table className="w-full text-left border-collapse">
                <thead>
                  <tr className="bg-slate-50 text-slate-600 text-xs font-bold uppercase border-b border-slate-200">
                    <th className="px-4 py-3">Nama Alumni</th>
                    <th className="px-4 py-3">Identitas Ijazah</th>
                    <th className="px-4 py-3 text-center">Rata-Rata Kelulusan</th>
                    <th className="px-4 py-3 text-right">Aksi Riwayat</th>
                  </tr>
                </thead>
                <tbody className="divide-y divide-slate-100 text-sm text-slate-700">
                  {students.filter(s => s.status === 'Lulus').length === 0 ? (
                    <tr>
                      <td colSpan="4" className="text-center py-8 text-slate-400 font-medium">
                        Belum ada arsip siswa berstatus lulus.
                      </td>
                    </tr>
                  ) : (
                    students.filter(s => s.status === 'Lulus').map(s => {
                      return (
                        <tr key={s.id} className="hover:bg-slate-50/50">
                          <td className="px-4 py-3">
                            <p className="font-bold text-slate-800">{s.nama}</p>
                            <span className="text-xs text-slate-400">NISN: {s.nisn}</span>
                          </td>
                          <td className="px-4 py-3 leading-relaxed text-xs">
                            <p className="font-semibold text-slate-700">No. Ijazah: {s.noIjazah || '-'}</p>
                            <p className="text-slate-500">Tanggal Kelulusan: {formatDateString(s.tanggalLulus)}</p>
                          </td>
                          <td className="px-4 py-3 text-center">
                            <span className="text-lg font-extrabold text-emerald-700 bg-emerald-50 px-3 py-1 rounded-full border border-emerald-100">
                              {s.nilaiRataIjazah || '-'}
                            </span>
                          </td>
                          <td className="px-4 py-3 text-right">
                            <button
                              onClick={() => setActiveTab(`alumni-detail-${s.id}`)}
                              className="bg-slate-100 hover:bg-slate-200 text-slate-700 font-bold text-xs px-3 py-1.5 rounded-lg transition"
                            >
                              🔎 Detail Rekam Jejak Nilai
                            </button>
                          </td>
                        </tr>
                      );
                    })
                  )}
                </tbody>
              </table>
            </div>
          </div>
        );

      case 'pengaturan':
        return (
          <div className="space-y-6">
            <div>
              <h2 className="text-xl font-bold text-slate-800">⚙️ Pengaturan Daftar Kelas & Tahun Ajaran</h2>
              <p className="text-slate-500 text-sm">Kelola daftar kelas dan tahun ajaran untuk mempermudah form pengisian.</p>
            </div>

            <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
              <div className="bg-slate-50 p-4 rounded-xl border border-slate-200">
                <h3 className="font-bold text-slate-800 text-md mb-3">🏫 Kelola Kelas</h3>
                <form onSubmit={handleAddClass} className="flex gap-2 mb-4">
                  <input
                    type="text"
                    placeholder="Contoh: Kelas 1C"
                    required
                    value={newClassName}
                    onChange={(e) => setNewClassName(e.target.value)}
                    className="flex-1 px-3 py-1.5 text-sm bg-white border border-slate-300 rounded-lg outline-none"
                  />
                  <button type="submit" className="bg-emerald-700 hover:bg-emerald-600 text-white font-bold px-4 py-1.5 rounded-lg text-xs">
                    Tambah
                  </button>
                </form>
                <div className="flex flex-wrap gap-2">
                  {classes.map(c => (
                    <span key={c} className="inline-flex items-center gap-1.5 bg-white border border-slate-200 px-3 py-1 rounded-full text-xs font-semibold text-slate-700">
                      {c}
                      <button onClick={() => handleDeleteClass(c)} className="text-red-500 font-bold hover:text-red-700">×</button>
                    </span>
                  ))}
                </div>
              </div>

              <div className="bg-slate-50 p-4 rounded-xl border border-slate-200">
                <h3 className="font-bold text-slate-800 text-md mb-3">📅 Kelola Tahun Ajaran</h3>
                <form onSubmit={handleAddYear} className="flex gap-2 mb-4">
                  <input
                    type="text"
                    placeholder="Contoh: 2026/2027"
                    required
                    value={newYearName}
                    onChange={(e) => setNewYearName(e.target.value)}
                    className="flex-1 px-3 py-1.5 text-sm bg-white border border-slate-300 rounded-lg outline-none"
                  />
                  <button type="submit" className="bg-emerald-700 hover:bg-emerald-600 text-white font-bold px-4 py-1.5 rounded-lg text-xs">
                    Tambah
                  </button>
                </form>
                <div className="flex flex-wrap gap-2">
                  {years.map(y => (
                    <span key={y} className="inline-flex items-center gap-1.5 bg-white border border-slate-200 px-3 py-1 rounded-full text-xs font-semibold text-slate-700">
                      {y}
                      <button onClick={() => handleDeleteYear(y)} className="text-red-500 font-bold hover:text-red-700">×</button>
                    </span>
                  ))}
                </div>
              </div>
            </div>
          </div>
        );

      case 'backup':
        return (
          <div className="space-y-6">
            <div>
              <h2 className="text-xl font-bold text-slate-800">💾 Ekspor & Pemulihan Database</h2>
              <p className="text-slate-500 text-sm">Amankan database administrasi kearsipan sekolah Anda secara mandiri.</p>
            </div>

            <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
              <div className="border border-slate-200 rounded-xl p-5 space-y-4">
                <h3 className="font-bold text-slate-800 text-lg flex items-center gap-2">
                  <span>📥</span> Ekspor Database & Laporan Excel
                </h3>
                <p className="text-slate-500 text-xs leading-relaxed">
                  Unduh seluruh file database yang berisi data siswa, riwayat alumni, absensi, hingga seluruh leger nilai rapor. Berkas ini dapat dibuka dan diedit di Microsoft Excel atau diimpor kembali ke aplikasi ini sewaktu-waktu.
                </p>
                <div className="space-y-2">
                  <button
                    onClick={handleExportJSON}
                    className="w-full bg-emerald-700 hover:bg-emerald-600 text-white font-bold py-3 px-4 rounded-xl text-sm shadow transition flex items-center justify-center gap-2"
                  >
                    💾 Cadangkan Database Lengkap (.json)
                  </button>
                  <button
                    onClick={handleExportExcel}
                    className="w-full bg-blue-700 hover:bg-blue-600 text-white font-bold py-3 px-4 rounded-xl text-sm shadow transition flex items-center justify-center gap-2"
                  >
                    📊 Ekspor Data Lengkap ke Excel (.xlsx)
                  </button>
                </div>
              </div>

              <div className="border border-slate-200 rounded-xl p-5 space-y-4">
                <h3 className="font-bold text-slate-800 text-lg flex items-center gap-2">
                  <span>📤</span> Pulihkan / Impor Database
                </h3>
                <p className="text-slate-500 text-xs leading-relaxed">
                  Pilih file cadangan berformat <code className="bg-slate-100 px-1 rounded font-bold">.json</code> yang sudah pernah diunduh sebelumnya untuk memulihkan seluruh riwayat administrasi sekolah secara otomatis.
                </p>
                <div className="bg-slate-50 p-4 rounded-xl border border-dashed border-slate-300 flex flex-col items-center justify-center py-6 text-center">
                  <label className="cursor-pointer bg-white hover:bg-slate-100 border border-slate-300 font-semibold px-4 py-2 rounded-lg text-xs text-slate-700 shadow-sm transition">
                    Pilih Berkas Cadangan
                    <input
                      type="file"
                      accept=".json"
                      onChange={handleImportJSON}
                      className="hidden"
                    />
                  </label>
                  <span className="text-[10px] text-slate-400 mt-2">Hanya menerima format berkas .json cadangan resmi</span>
                </div>
              </div>
            </div>
          </div>
        );

      default:
        return <div className="p-6 text-slate-400 font-semibold">Memuat halaman kearsipan...</div>;
    }
  };

  return (
    <div className="min-h-screen bg-slate-50 text-slate-800 font-sans flex flex-col">
      
      {/* HEADER UTAMA */}
      <header className="bg-emerald-800 text-white shadow-md print:hidden">
        <div className="max-w-7xl mx-auto px-4 py-3 flex flex-wrap justify-between items-center gap-4">
          <div className="flex items-center space-x-3">
            <div className="w-12 h-12 bg-white rounded-full flex items-center justify-center font-bold text-emerald-800 text-2xl shadow-inner">
              🕌
            </div>
            <div>
              <h1 className="text-xl font-bold tracking-wide">SDIT ANNIHAYAH</h1>
              <p className="text-xs text-emerald-100 font-medium">Sistem Informasi Administrasi & Data Induk Sekolah</p>
            </div>
          </div>

          {isAuthenticated ? (
            <div className="flex items-center space-x-4">
              <div className="text-right">
                <span className="block text-sm font-semibold">{userRole === 'admin' ? 'Administrator' : 'Guru Kelas'}</span>
                <span className="block text-xs text-emerald-200">SDIT Annihayah</span>
              </div>
              <button
                onClick={handleLogout}
                className="bg-emerald-700 hover:bg-emerald-600 px-4 py-2 rounded-lg text-sm font-semibold transition"
              >
                Keluar 🚪
              </button>
            </div>
          ) : (
            <span className="text-xs bg-emerald-950 px-3 py-1 rounded-full text-emerald-300">Belum Masuk</span>
          )}
        </div>
      </header>

      {/* LOGIN BOX */}
      {!isAuthenticated ? (
        <div className="flex-1 flex items-center justify-center px-4 py-12">
          <div className="bg-white p-8 rounded-2xl shadow-xl max-w-md w-full border border-slate-100">
            <div className="text-center mb-6">
              <span className="text-5xl">🕌</span>
              <h2 className="text-2xl font-bold text-slate-800 mt-3">Silakan Masuk</h2>
              <p className="text-sm text-slate-500">Gunakan akun Admin atau Guru SDIT Annihayah</p>
            </div>

            {loginError && (
              <div className="bg-red-50 text-red-600 text-sm p-3 rounded-lg mb-4 text-center font-semibold border border-red-200">
                ⚠️ {loginError}
              </div>
            )}

            <form onSubmit={handleLogin} className="space-y-4">
              <div>
                <label className="block text-sm font-medium text-slate-600 mb-1">Username</label>
                <input
                  type="text"
                  required
                  value={usernameInput}
                  onChange={(e) => setUsernameInput(e.target.value)}
                  placeholder="Contoh: admin atau guru"
                  className="w-full px-4 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500 outline-none"
                />
              </div>

              <div>
                <label className="block text-sm font-medium text-slate-600 mb-1">Password</label>
                <input
                  type="password"
                  required
                  value={passwordInput}
                  onChange={(e) => setPasswordInput(e.target.value)}
                  placeholder="Contoh: admin123 atau guru123"
                  className="w-full px-4 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500 outline-none"
                />
              </div>

              <button
                type="submit"
                className="w-full bg-emerald-600 hover:bg-emerald-700 text-white font-bold py-3 rounded-lg transition shadow-md"
              >
                Masuk ke Sistem 🔑
              </button>
            </form>
          </div>
        </div>
      ) : (
        /* UTAMA (SUDAH LOGIN) */
        <div className="flex-1 max-w-7xl w-full mx-auto px-4 py-6 flex flex-col md:flex-row gap-6">
          
          {/* SIDEBAR NAVIGATION */}
          <aside className="w-full md:w-64 flex flex-col space-y-2 print:hidden">
            <div className="bg-white p-4 rounded-xl shadow-sm border border-slate-200/80 mb-2">
              <p className="text-xs uppercase tracking-wider text-slate-400 font-bold mb-1">Petugas Aktif</p>
              <div className="flex items-center space-x-2">
                <div className="w-2.5 h-2.5 rounded-full bg-emerald-500 animate-pulse"></div>
                <span className="text-sm font-semibold text-slate-700">
                  {userRole === 'admin' ? '💼 Kepala Admin' : '👩‍🏫 Wali Kelas / Guru'}
                </span>
              </div>
            </div>

            <button
              onClick={() => { setActiveTab('dashboard'); setPrintStudentId(null); }}
              className={`flex items-center space-x-3 px-4 py-3 rounded-lg text-sm font-semibold transition text-left ${activeTab === 'dashboard' ? 'bg-emerald-700 text-white shadow' : 'bg-white text-slate-700 hover:bg-slate-100 shadow-sm'}`}
            >
              <span>📊</span>
              <span>Dashboard & Statistik</span>
            </button>

            <button
              onClick={() => { setActiveTab('datasiswa'); setPrintStudentId(null); }}
              className={`flex items-center space-x-3 px-4 py-3 rounded-lg text-sm font-semibold transition text-left ${activeTab === 'datasiswa' ? 'bg-emerald-700 text-white shadow' : 'bg-white text-slate-700 hover:bg-slate-100 shadow-sm'}`}
            >
              <span>📁</span>
              <span>Kelola Data Induk</span>
            </button>

            {userRole === 'admin' && (
              <button
                onClick={() => { setActiveTab('mutasirekap'); setPrintStudentId(null); }}
                className={`flex items-center space-x-3 px-4 py-3 rounded-lg text-sm font-semibold transition text-left ${activeTab === 'mutasirekap' ? 'bg-emerald-700 text-white shadow' : 'bg-white text-slate-700 hover:bg-slate-100 shadow-sm'}`}
              >
                <span>🔄</span>
                <span>Rekap Mutasi Masuk/Keluar</span>
              </button>
            )}

            <button
              onClick={() => { setActiveTab('inputnilai'); setPrintStudentId(null); }}
              className={`flex items-center space-x-3 px-4 py-3 rounded-lg text-sm font-semibold transition text-left ${activeTab === 'inputnilai' ? 'bg-emerald-700 text-white shadow' : 'bg-white text-slate-700 hover:bg-slate-100 shadow-sm'}`}
            >
              <span>📝</span>
              <span>Input Nilai, Absen & Catatan</span>
            </button>

            <button
              onClick={() => { setActiveTab('legerrapor'); setPrintStudentId(null); }}
              className={`flex items-center space-x-3 px-4 py-3 rounded-lg text-sm font-semibold transition text-left ${activeTab === 'legerrapor' ? 'bg-emerald-700 text-white shadow' : 'bg-white text-slate-700 hover:bg-slate-100 shadow-sm'}`}
            >
              <span>📋</span>
              <span>Leger Nilai Rapor</span>
            </button>

            <button
              onClick={() => { setActiveTab('riwayatalumni'); setPrintStudentId(null); }}
              className={`flex items-center space-x-3 px-4 py-3 rounded-lg text-sm font-semibold transition text-left ${activeTab === 'riwayatalumni' ? 'bg-emerald-700 text-white shadow' : 'bg-white text-slate-700 hover:bg-slate-100 shadow-sm'}`}
            >
              <span>🎓</span>
              <span>Arsip Kelulusan & Alumni</span>
            </button>

            {userRole === 'admin' && (
              <button
                onClick={() => { setActiveTab('pengaturan'); setPrintStudentId(null); }}
                className={`flex items-center space-x-3 px-4 py-3 rounded-lg text-sm font-semibold transition text-left ${activeTab === 'pengaturan' ? 'bg-emerald-700 text-white shadow' : 'bg-white text-slate-700 hover:bg-slate-100 shadow-sm'}`}
              >
                <span>⚙️</span>
                <span>Pengaturan Kelas & TA</span>
              </button>
            )}

            <button
              onClick={() => { setActiveTab('backup'); setPrintStudentId(null); }}
              className={`flex items-center space-x-3 px-4 py-3 rounded-lg text-sm font-semibold transition text-left ${activeTab === 'backup' ? 'bg-emerald-700 text-white shadow' : 'bg-white text-slate-700 hover:bg-slate-100 shadow-sm'}`}
            >
              <span>💾</span>
              <span>Backup, Restore & Excel</span>
            </button>
          </aside>

          {/* MAIN CONTENT AREA */}
          <main className="flex-1 bg-white p-6 rounded-2xl shadow-sm border border-slate-200/80 min-h-[500px]">
            {/* Memanggil Dispatcher Utama yang Aman untuk Mencegah Child Errors */}
            {renderMainTabContent()}

            {/* PREVIEW RAPOR INDONESIA FORMAL (SIAP CETAK A4) */}
            {printStudentId && printClassSem && (() => {
              const student = students.find(s => s.id === printStudentId);
              if (!student) return <p>Siswa tidak ditemukan.</p>;

              const studentGrades = grades[student.id]?.[printClassSem] || {};
              const attObj = attendance[student.id]?.[printClassSem] || { sakit: 0, izin: 0, alfa: 0 };
              const noteText = notes[student.id]?.[printClassSem] || '';
              const subjects = getSubjectsForYear(student.tahunAjaran);

              const totalScores = subjects.reduce((acc, curr) => acc + (parseFloat(studentGrades[curr]) || 0), 0);
              const gradesCount = subjects.filter(curr => studentGrades[curr] !== undefined && studentGrades[curr] !== '').length;
              const avg = gradesCount > 0 ? (totalScores / gradesCount).toFixed(1) : '-';

              return (
                <div className="space-y-6">
                  <div className="flex flex-wrap gap-2 justify-between items-center bg-slate-100 p-4 rounded-xl print:hidden">
                    <div>
                      <button
                        onClick={() => setPrintStudentId(null)}
                        className="text-slate-600 font-bold hover:underline text-sm"
                      >
                        ← Tutup Preview Rapor
                      </button>
                    </div>
                    <button
                      onClick={() => window.print()}
                      className="bg-emerald-700 hover:bg-emerald-600 text-white font-bold py-2 px-6 rounded-lg text-sm shadow transition"
                    >
                      🖨️ Cetak / Unduh Format PDF Rapor
                    </button>
                  </div>

                  <div className="bg-white border border-slate-300 p-8 max-w-[210mm] mx-auto min-h-[297mm] text-black print:border-0 print:p-0" id="printable-rapor-sheet">
                    <div className="text-center border-b-4 border-double border-slate-800 pb-3 mb-6">
                      <h3 className="text-lg font-bold">YAYASAN AL-ANNIHAYAH KARAWANG</h3>
                      <h2 className="text-xl font-black">SDIT ANNIHAYAH</h2>
                      <p className="text-xs">Alamat: Jl. Syech Quro, Plawad, Kec. Karawang Timur, Karawang, Jawa Barat 41314</p>
                    </div>

                    <h1 className="text-center text-lg font-bold uppercase tracking-wider mb-6">LAPORAN HASIL BELAJAR SISWA (RAPOR)</h1>

                    <div className="grid grid-cols-2 gap-4 text-xs mb-6">
                      <div className="space-y-1">
                        <p><strong>Nama Lengkap:</strong> {student.nama}</p>
                        <p><strong>NIS / NISN:</strong> {student.nis} / {student.nisn}</p>
                        <p><strong>Tempat, Tgl Lahir:</strong> {student.tempatLahir || '-'}, {student.tanggalLahir ? formatDateString(student.tanggalLahir) : '-'}</p>
                        <p><strong>Sekolah:</strong> SDIT ANNIHAYAH</p>
                      </div>
                      <div className="space-y-1 text-right">
                        <p><strong>Kelas / Semester:</strong> {printClassSem.replace('_', ' / Semester ')}</p>
                        <p><strong>Tahun Pelajaran:</strong> {student.tahunAjaran}</p>
                        <p><strong>Jenis Kelamin:</strong> {student.jenisKelamin}</p>
                      </div>
                    </div>

                    <table className="w-full text-left border-collapse border border-black text-xs mb-6">
                      <thead>
                        <tr className="bg-slate-100 border-b border-black">
                          <th className="border border-black px-3 py-2 text-center w-12">No.</th>
                          <th className="border border-black px-3 py-2">Mata Pelajaran</th>
                          <th className="border border-black px-3 py-2 text-center w-24">Nilai Kognitif</th>
                          <th className="border border-black px-3 py-2">Predikat & Keterangan</th>
                        </tr>
                      </thead>
                      <tbody>
                        {subjects.map((subj, idx) => {
                          const val = studentGrades[subj] ?? '-';
                          let predikat = 'C';
                          if (parseFloat(val) >= 90) predikat = 'A (Sangat Baik)';
                          else if (parseFloat(val) >= 80) predikat = 'B (Baik)';
                          else if (parseFloat(val) >= 70) predikat = 'C (Cukup)';
                          else if (parseFloat(val) < 70) predikat = 'D (Kurang)';

                          return (
                            <tr key={subj} className="border-b border-black">
                              <td className="border border-black px-3 py-1.5 text-center">{idx + 1}</td>
                              <td className="border border-black px-3 py-1.5 font-semibold">{subj}</td>
                              <td className="border border-black px-3 py-1.5 text-center font-bold">{val}</td>
                              <td className="border border-black px-3 py-1.5 text-slate-700">{predikat}</td>
                            </tr>
                          );
                        })}
                        <tr className="bg-slate-50 font-bold">
                          <td colSpan="2" className="border border-black px-3 py-2 text-right">Rata-Rata Nilai Akhir:</td>
                          <td className="border border-black px-3 py-2 text-center text-lg text-emerald-800">{avg}</td>
                          <td className="border border-black px-3 py-2"></td>
                        </tr>
                      </tbody>
                    </table>

                    <div className="grid grid-cols-1 md:grid-cols-2 gap-4 text-xs mb-8">
                      <div className="border border-black p-3">
                        <h4 className="font-bold border-b border-black pb-1 mb-2">Keterangan Kehadiran (Semester Ini)</h4>
                        <table className="w-full">
                          <tbody>
                            <tr>
                              <td className="py-1">Sakit (S)</td>
                              <td className="py-1 text-center font-bold">: {attObj.sakit} hari</td>
                            </tr>
                            <tr>
                              <td className="py-1">Izin (I)</td>
                              <td className="py-1 text-center font-bold">: {attObj.izin} hari</td>
                            </tr>
                            <tr>
                              <td className="py-1">Tanpa Keterangan (Alpa)</td>
                              <td className="py-1 text-center font-bold">: {attObj.alfa} hari</td>
                            </tr>
                          </tbody>
                        </table>
                      </div>

                      <div className="border border-black p-3">
                        <h4 className="font-bold border-b border-black pb-1 mb-2">Catatan Guru Wali Kelas</h4>
                        <p className="italic text-slate-800 leading-relaxed min-h-[60px]">
                          {noteText || '"Belum ada catatan pembinaan karakter murid."'}
                        </p>
                      </div>
                    </div>

                    <div className="grid grid-cols-3 text-center text-xs mt-12 pt-8">
                      <div>
                        <p>Mengetahui,</p>
                        <p className="mb-16">Orang Tua / Wali Murid</p>
                        <p className="font-bold border-b border-black inline-block px-4">........................................</p>
                      </div>
                      <div>
                        <p>&nbsp;</p>
                        <p className="mb-16">Wali Kelas</p>
                        <p className="font-bold border-b border-black inline-block px-4">{userRole === 'admin' ? 'Ustadz / Ustadzah' : 'Siti Humairah, S.Pd.'}</p>
                      </div>
                      <div>
                        <p>Karawang, {formatDateString(new Date().toISOString().split('T')[0])}</p>
                        <p className="mb-16">Kepala Sekolah SDIT Annihayah</p>
                        <p className="font-bold border-b border-black inline-block px-4">KH. Dudung, M.Pd.I</p>
                      </div>
                    </div>
                  </div>
                </div>
              );
            })()}
          </main>
        </div>
      )}

      {/* FOOTER */}
      <footer className="bg-slate-100 border-t border-slate-200 py-4 mt-6 print:hidden">
        <p className="text-center text-xs text-slate-500 font-semibold">
          © 2026 SDIT ANNIHAYAH Karawang • Hak Cipta Administrasi Terpadu Dilindungi.
        </p>
      </footer>

      {/* 9. MODAL POPUP TAMBAH/EDIT SISWA LENGKAP */}
      {showStudentModal && (
        <div className="fixed inset-0 bg-black/50 backdrop-blur-sm flex items-center justify-center p-4 z-50 overflow-y-auto">
          <div className="bg-white rounded-2xl shadow-xl max-w-2xl w-full p-6 border border-slate-100 max-h-[90vh] overflow-y-auto">
            <div className="flex justify-between items-center border-b pb-3 mb-4">
              <h3 className="text-lg font-bold text-slate-800">
                {editingStudent ? '✏️ Ubah Data Induk Siswa' : '🎒 Registrasi Siswa Baru'}
              </h3>
              <button onClick={() => setShowStudentModal(false)} className="text-slate-400 hover:text-slate-600 text-2xl font-bold">
                ×
              </button>
            </div>

            <form onSubmit={handleSaveStudent} className="space-y-4">
              <div className="grid grid-cols-1 sm:grid-cols-2 gap-4">
                <div>
                  <label className="block text-xs font-bold text-slate-600 mb-1">Nama Lengkap Siswa *</label>
                  <input
                    type="text"
                    required
                    value={studentForm.nama}
                    onChange={(e) => setStudentForm(prev => ({ ...prev, nama: e.target.value }))}
                    className="w-full px-3 py-1.5 text-sm border border-slate-300 rounded-lg outline-none"
                    placeholder="Nama Lengkap"
                  />
                </div>
                <div>
                  <label className="block text-xs font-bold text-slate-600 mb-1">Jenis Kelamin</label>
                  <select
                    value={studentForm.jenisKelamin}
                    onChange={(e) => setStudentForm(prev => ({ ...prev, jenisKelamin: e.target.value }))}
                    className="w-full px-3 py-1.5 text-sm border border-slate-300 rounded-lg outline-none"
                  >
                    <option value="Laki-laki">Laki-laki</option>
                    <option value="Perempuan">Perempuan</option>
                  </select>
                </div>
              </div>

              <div className="grid grid-cols-1 sm:grid-cols-2 gap-4">
                <div>
                  <label className="block text-xs font-bold text-slate-600 mb-1">Tempat Lahir</label>
                  <input
                    type="text"
                    value={studentForm.tempatLahir || ''}
                    onChange={(e) => setStudentForm(prev => ({ ...prev, tempatLahir: e.target.value }))}
                    className="w-full px-3 py-1.5 text-sm border border-slate-300 rounded-lg outline-none"
                    placeholder="Kabupaten / Kota Lahir"
                  />
                </div>
                <div>
                  <label className="block text-xs font-bold text-slate-600 mb-1">Tanggal Lahir</label>
                  <input
                    type="date"
                    value={studentForm.tanggalLahir || ''}
                    onChange={(e) => setStudentForm(prev => ({ ...prev, tanggalLahir: e.target.value }))}
                    className="w-full px-3 py-1.5 text-sm border border-slate-300 rounded-lg outline-none"
                  />
                </div>
              </div>

              <div className="grid grid-cols-2 gap-4">
                <div>
                  <label className="block text-xs font-bold text-slate-600 mb-1">Nomor Induk Siswa (NIS) *</label>
                  <input
                    type="text"
                    required
                    value={studentForm.nis}
                    onChange={(e) => setStudentForm(prev => ({ ...prev, nis: e.target.value }))}
                    className="w-full px-3 py-1.5 text-sm border border-slate-300 rounded-lg outline-none"
                    placeholder="NIS"
                  />
                </div>
                <div>
                  <label className="block text-xs font-bold text-slate-600 mb-1">NISN *</label>
                  <input
                    type="text"
                    required
                    value={studentForm.nisn}
                    onChange={(e) => setStudentForm(prev => ({ ...prev, nisn: e.target.value }))}
                    className="w-full px-3 py-1.5 text-sm border border-slate-300 rounded-lg outline-none"
                    placeholder="NISN"
                  />
                </div>
              </div>

              <div className="grid grid-cols-2 gap-4">
                <div>
                  <label className="block text-xs font-bold text-slate-600 mb-1">Tingkat Kelas Sekarang</label>
                  <select
                    value={studentForm.kelas}
                    onChange={(e) => setStudentForm(prev => ({ ...prev, kelas: e.target.value }))}
                    className="w-full px-3 py-1.5 text-sm border border-slate-300 rounded-lg outline-none"
                  >
                    {classes.map(c => <option key={c} value={c}>{c}</option>)}
                  </select>
                </div>
                <div>
                  <label className="block text-xs font-bold text-slate-600 mb-1">Tahun Ajaran Masuk/Sekarang</label>
                  <select
                    value={studentForm.tahunAjaran}
                    onChange={(e) => setStudentForm(prev => ({ ...prev, tahunAjaran: e.target.value }))}
                    className="w-full px-3 py-1.5 text-sm border border-slate-300 rounded-lg outline-none"
                  >
                    {years.map(y => <option key={y} value={y}>{y}</option>)}
                  </select>
                </div>
              </div>

              <div>
                <label className="block text-xs font-bold text-slate-600 mb-1">Status Kearsipan</label>
                <select
                  value={studentForm.status}
                  onChange={(e) => setStudentForm(prev => ({ ...prev, status: e.target.value }))}
                  className="w-full px-3 py-1.5 text-sm border border-slate-300 rounded-lg outline-none bg-emerald-50 font-bold text-emerald-800"
                >
                  <option value="Aktif">Aktif</option>
                  <option value="Lulus">Lulus</option>
                  <option value="Mutasi Masuk">Mutasi Masuk</option>
                  <option value="Mutasi Keluar">Mutasi Keluar</option>
                </select>
              </div>

              {studentForm.status === 'Lulus' && (
                <div className="bg-blue-50/50 p-4 rounded-xl border border-blue-100 space-y-3">
                  <h4 className="font-bold text-blue-900 text-xs uppercase tracking-wide">Data Kelulusan</h4>
                  <div className="grid grid-cols-1 sm:grid-cols-3 gap-3">
                    <div>
                      <label className="block text-[10px] font-bold text-blue-700 mb-1">Nomor Seri Ijazah</label>
                      <input
                        type="text"
                        value={studentForm.noIjazah || ''}
                        onChange={(e) => setStudentForm(prev => ({ ...prev, noIjazah: e.target.value }))}
                        className="w-full px-3 py-1.5 text-xs bg-white border border-blue-200 rounded-lg"
                        placeholder="DN-01/..."
                      />
                    </div>
                    <div>
                      <label className="block text-[10px] font-bold text-blue-700 mb-1">Tanggal Kelulusan</label>
                      <input
                        type="date"
                        value={studentForm.tanggalLulus || ''}
                        onChange={(e) => setStudentForm(prev => ({ ...prev, tanggalLulus: e.target.value }))}
                        className="w-full px-3 py-1.5 text-xs bg-white border border-blue-200 rounded-lg"
                      />
                    </div>
                    <div>
                      <label className="block text-[10px] font-bold text-blue-700 mb-1">Rata-Rata Nilai Ijazah</label>
                      <input
                        type="text"
                        value={studentForm.nilaiRataIjazah || ''}
                        onChange={(e) => setStudentForm(prev => ({ ...prev, nilaiRataIjazah: e.target.value }))}
                        className="w-full px-3 py-1.5 text-xs bg-white border border-blue-200 rounded-lg"
                        placeholder="Contoh: 89.5"
                      />
                    </div>
                  </div>
                </div>
              )}

              {studentForm.status === 'Mutasi Masuk' && (
                <div className="bg-purple-50/50 p-4 rounded-xl border border-purple-100 space-y-3">
                  <h4 className="font-bold text-purple-900 text-xs uppercase tracking-wide">Asal Sekolah Mutasi</h4>
                  <div className="grid grid-cols-1 sm:grid-cols-2 gap-3">
                    <div>
                      <label className="block text-[10px] font-bold text-purple-700 mb-1">Tanggal Masuk</label>
                      <input
                        type="date"
                        value={studentForm.tanggalMasuk || ''}
                        onChange={(e) => setStudentForm(prev => ({ ...prev, tanggalMasuk: e.target.value }))}
                        className="w-full px-3 py-1.5 text-xs bg-white border border-purple-200 rounded-lg"
                      />
                    </div>
                    <div>
                      <label className="block text-[10px] font-bold text-purple-700 mb-1">Sekolah Asal</label>
                      <input
                        type="text"
                        value={studentForm.sekolahAsal || ''}
                        onChange={(e) => setStudentForm(prev => ({ ...prev, sekolahAsal: e.target.value }))}
                        className="w-full px-3 py-1.5 text-xs bg-white border border-purple-200 rounded-lg"
                        placeholder="SD Negeri/Swasta..."
                      />
                    </div>
                  </div>
                  <div>
                    <label className="block text-[10px] font-bold text-purple-700 mb-1">Alamat Asal Sekolah</label>
                    <input
                      type="text"
                      value={studentForm.alamatAsal || ''}
                      onChange={(e) => setStudentForm(prev => ({ ...prev, alamatAsal: e.target.value }))}
                      className="w-full px-3 py-1.5 text-xs bg-white border border-purple-200 rounded-lg"
                      placeholder="Kabupaten/Kecamatan..."
                    />
                  </div>
                </div>
              )}

              {studentForm.status === 'Mutasi Keluar' && (
                <div className="bg-amber-50/50 p-4 rounded-xl border border-amber-100 space-y-3">
                  <h4 className="font-bold text-amber-900 text-xs uppercase tracking-wide">Tujuan Mutasi</h4>
                  <div className="grid grid-cols-1 sm:grid-cols-2 gap-3">
                    <div>
                      <label className="block text-[10px] font-bold text-amber-700 mb-1">Tanggal Keluar</label>
                      <input
                        type="date"
                        value={studentForm.tanggalKeluar || ''}
                        onChange={(e) => setStudentForm(prev => ({ ...prev, tanggalKeluar: e.target.value }))}
                        className="w-full px-3 py-1.5 text-xs bg-white border border-amber-200 rounded-lg"
                      />
                    </div>
                    <div>
                      <label className="block text-[10px] font-bold text-amber-700 mb-1">Sekolah Tujuan</label>
                      <input
                        type="text"
                        value={studentForm.sekolahTujuan || ''}
                        onChange={(e) => setStudentForm(prev => ({ ...prev, sekolahTujuan: e.target.value }))}
                        className="w-full px-3 py-1.5 text-xs bg-white border border-amber-200 rounded-lg"
                        placeholder="SD/MI..."
                      />
                    </div>
                  </div>
                  <div>
                    <label className="block text-[10px] font-bold text-amber-700 mb-1">Alamat Sekolah Tujuan</label>
                    <input
                      type="text"
                      value={studentForm.alamatTujuan || ''}
                      onChange={(e) => setStudentForm(prev => ({ ...prev, alamatTujuan: e.target.value }))}
                      className="w-full px-3 py-1.5 text-xs bg-white border border-amber-200 rounded-lg"
                      placeholder="Kabupaten/Kecamatan..."
                    />
                  </div>
                </div>
              )}

              <div>
                <label className="block text-xs font-bold text-slate-600 mb-1">Pas Foto Siswa (Opsional)</label>
                <div className="flex items-center space-x-3">
                  {studentForm.foto && (
                    <img src={studentForm.foto} alt="Preview" className="w-12 h-12 rounded-full object-cover border" />
                  )}
                  <input
                    type="file"
                    accept="image/*"
                    onChange={handleImageChange}
                    className="text-xs text-slate-500"
                  />
                </div>
              </div>

              <div className="flex justify-end gap-2 border-t pt-3">
                <button
                  type="button"
                  onClick={() => setShowStudentModal(false)}
                  className="bg-slate-100 hover:bg-slate-200 text-slate-700 font-semibold px-4 py-2 rounded-lg text-sm transition"
                >
                  Batal
                </button>
                <button
                  type="submit"
                  className="bg-emerald-700 hover:bg-emerald-600 text-white font-bold px-5 py-2 rounded-lg text-sm transition shadow"
                >
                  Simpan Data
                </button>
              </div>
            </form>
          </div>
        </div>
      )}

      {/* 10. NOTIFIKASI TOAST KUSTOM */}
      {toast.show && (
        <div className="fixed bottom-5 right-5 z-[100] max-w-sm w-full bg-slate-900 text-white p-4 rounded-xl shadow-2xl border border-slate-800 animate-slide-in flex items-center gap-3">
          <span className="text-xl">
            {toast.type === 'success' ? '✅' : toast.type === 'error' ? '❌' : 'ℹ️'}
          </span>
          <p className="text-xs font-medium flex-1">{toast.message}</p>
        </div>
      )}

      {/* 11. MODAL DIALOG KONFIRMASI KUSTOM */}
      {confirmDialog.show && (
        <div className="fixed inset-0 bg-black/60 backdrop-blur-sm flex items-center justify-center p-4 z-[90]">
          <div className="bg-white rounded-2xl shadow-xl max-w-sm w-full p-6 border border-slate-100 text-center space-y-4">
            <span className="text-4xl block">❓</span>
            <h3 className="text-md font-bold text-slate-800">Konfirmasi Tindakan</h3>
            <p className="text-xs text-slate-500 leading-relaxed">{confirmDialog.message}</p>
            <div className="flex justify-center gap-2 border-t pt-3">
              <button
                onClick={() => setConfirmDialog({ show: false, message: '', onConfirm: null })}
                className="bg-slate-100 hover:bg-slate-200 text-slate-700 font-semibold px-4 py-1.5 rounded-lg text-xs transition"
              >
                Batal
              </button>
              <button
                onClick={confirmDialog.onConfirm}
                className="bg-rose-600 hover:bg-rose-700 text-white font-bold px-4 py-1.5 rounded-lg text-xs transition shadow"
              >
                Lanjutkan
              </button>
            </div>
          </div>
        </div>
      )}

      {/* STYLING UTILITY FOR ANIMATIONS AND PRINTS */}
      <style>{`
        @keyframes slide-in {
          from { transform: translateY(100px); opacity: 0; }
          to { transform: translateY(0); opacity: 1; }
        }
        .animate-slide-in {
          animation: slide-in 0.3s ease-out forwards;
        }
        @media print {
          body * {
            visibility: hidden;
          }
          #printable-rapor-sheet, #printable-rapor-sheet * {
            visibility: visible;
          }
          #printable-rapor-sheet {
            position: absolute;
            left: 0;
            top: 0;
            width: 100%;
            margin: 0;
            padding: 0;
            border: none;
            box-shadow: none;
          }
          .print\\:hidden {
            display: none !important;
          }
        }
      `}</style>

    </div>
  );
}

// ==========================================
// SUB-COMPONENT: FORM PENGISIAN AKADEMIK
// ==========================================
function GradeFormContainer({ student, classSemesterKey, initialGrades, initialAttendance, initialNote, subjects, onSave }) {
  const [formGrades, setFormGrades] = useState({});
  const [formAttendance, setFormAttendance] = useState({ sakit: 0, izin: 0, alfa: 0 });
  const [formNote, setFormNote] = useState('');

  useEffect(() => {
    const gradesObj = {};
    subjects.forEach(subj => {
      gradesObj[subj] = initialGrades[subj] !== undefined ? initialGrades[subj] : '';
    });
    setFormGrades(gradesObj);
    setFormAttendance(initialAttendance || { sakit: 0, izin: 0, alfa: 0 });
    setFormNote(initialNote || '');
  }, [student, classSemesterKey, initialGrades, initialAttendance, initialNote, subjects]);

  const handleGradeChange = (subj, value) => {
    setFormGrades(prev => ({ ...prev, [subj]: value }));
  };

  const handleAttendanceChange = (field, value) => {
    setFormAttendance(prev => ({ ...prev, [field]: parseInt(value, 10) || 0 }));
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    onSave(formGrades, formAttendance, formNote);
  };

  return (
    <form onSubmit={handleSubmit} className="space-y-6 bg-slate-50 p-5 rounded-2xl border border-slate-200">
      <div className="flex flex-col sm:flex-row sm:items-center justify-between border-b pb-3 gap-2">
        <div>
          <span className="text-xs font-bold text-slate-400 uppercase">Input Aktif</span>
          <h3 className="text-lg font-bold text-emerald-900">{student.nama}</h3>
          <p className="text-xs text-slate-500">TA {student.tahunAjaran} • {classSemesterKey.replace('_', ' Semester ')}</p>
        </div>
        <button
          type="submit"
          className="bg-emerald-700 hover:bg-emerald-600 text-white font-bold px-6 py-2 rounded-xl text-sm shadow transition"
        >
          💾 Simpan Seluruh Rekor Akademik
        </button>
      </div>

      <div className="grid grid-cols-1 lg:grid-cols-3 gap-6">
        
        {/* DAFTAR NILAI MAPEL */}
        <div className="lg:col-span-2 bg-white p-4 rounded-xl shadow-sm border border-slate-200 space-y-4">
          <h4 className="font-extrabold text-slate-800 text-sm border-b pb-1">📚 Input Nilai Kognitif Mata Pelajaran</h4>
          <div className="grid grid-cols-1 sm:grid-cols-2 gap-4">
            {subjects.map(subj => (
              <div key={subj} className="flex items-center justify-between gap-4 border-b pb-1">
                <span className="text-xs font-bold text-slate-600">{subj}</span>
                <input
                  type="number"
                  min="0"
                  max="100"
                  placeholder="0-100"
                  value={formGrades[subj] ?? ''}
                  onChange={(e) => handleGradeChange(subj, e.target.value)}
                  className="w-20 px-2 py-1 text-xs border border-slate-300 rounded text-center font-bold focus:ring-1 focus:ring-emerald-500 outline-none"
                />
              </div>
            ))}
          </div>
        </div>

        {/* ABSENSI & CATATAN */}
        <div className="space-y-6">
          <div className="bg-white p-4 rounded-xl shadow-sm border border-slate-200 space-y-4">
            <h4 className="font-extrabold text-slate-800 text-sm border-b pb-1">📅 Presensi & Ketidakhadiran</h4>
            <div className="space-y-3 text-xs">
              <div className="flex items-center justify-between">
                <span>Sakit (S)</span>
                <input
                  type="number"
                  min="0"
                  value={formAttendance.sakit}
                  onChange={(e) => handleAttendanceChange('sakit', e.target.value)}
                  className="w-16 px-2 py-1 border border-slate-300 rounded text-center"
                />
              </div>
              <div className="flex items-center justify-between">
                <span>Izin (I)</span>
                <input
                  type="number"
                  min="0"
                  value={formAttendance.izin}
                  onChange={(e) => handleAttendanceChange('izin', e.target.value)}
                  className="w-16 px-2 py-1 border border-slate-300 rounded text-center"
                />
              </div>
              <div className="flex items-center justify-between">
                <span>Alpa / Tanpa Ket (A)</span>
                <input
                  type="number"
                  min="0"
                  value={formAttendance.alfa}
                  onChange={(e) => handleAttendanceChange('alfa', e.target.value)}
                  className="w-16 px-2 py-1 border border-slate-300 rounded text-center"
                />
              </div>
            </div>
          </div>

          <div className="bg-white p-4 rounded-xl shadow-sm border border-slate-200 space-y-2">
            <h4 className="font-extrabold text-slate-800 text-sm border-b pb-1">💬 Catatan Pembinaan Guru</h4>
            <textarea
              rows="3"
              placeholder="Berikan saran motivasi perkembangan karakter murid..."
              value={formNote}
              onChange={(e) => setFormNote(e.target.value)}
              className="w-full p-2.5 text-xs border border-slate-300 rounded-lg outline-none focus:ring-1 focus:ring-emerald-500"
            ></textarea>
          </div>
        </div>

      </div>
    </form>
  );
}
