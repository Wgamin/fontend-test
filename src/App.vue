<template>
  <div class="min-h-screen bg-gray-50 flex font-sans text-gray-900 relative">
    
    <!-- 1. GIAO DIỆN ĐĂNG NHẬP -->
    <div v-if="!token" class="flex-1 flex items-center justify-center p-6 bg-indigo-50">
      <div class="max-w-md w-full bg-white rounded-2xl shadow-xl p-8 border border-gray-100">
        <div class="text-center mb-8">
          <div class="inline-flex items-center justify-center w-16 h-16 bg-indigo-600 rounded-xl mb-4 shadow-lg shadow-indigo-200">
            <svg class="w-8 h-8 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="12 14l9-5-9-5-9 5 9 5z"/><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="12 14l9-5-9-5-9 5 9 5zm0 0l6.16-3.422a12.083 12.083 0 01.665 6.479A11.952 11.952 0 0012 20.055a11.952 11.952 0 00-6.824-2.998 12.078 12.078 0 01.665-6.479L12 14z"/></svg>
          </div>
          <h1 class="text-2xl font-bold">Học viện EduManager</h1>
          <p class="text-gray-500 text-sm">Vui lòng đăng nhập để tiếp tục</p>
        </div>

        <div class="space-y-4">
          <div>
            <label class="block text-xs font-semibold text-gray-600 uppercase mb-1">Email</label>
            <input v-model="loginForm.email" type="email" class="w-full px-4 py-3 bg-gray-50 border border-gray-200 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:bg-white transition-all outline-none" placeholder="admin@example.com">
          </div>
          <div>
            <label class="block text-xs font-semibold text-gray-600 uppercase mb-1">Mật khẩu</label>
            <input v-model="loginForm.password" type="password" class="w-full px-4 py-3 bg-gray-50 border border-gray-200 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:bg-white transition-all outline-none" placeholder="••••••••">
          </div>
          <button @click="handleLogin" :disabled="loading" class="w-full py-3 bg-indigo-600 hover:bg-indigo-700 text-white font-bold rounded-lg shadow-lg shadow-indigo-200 transition-all disabled:opacity-50 mt-4">
            {{ loading ? 'Đang xử lý...' : 'Đăng nhập' }}
          </button>
          <p v-if="error" class="text-red-500 text-xs text-center font-medium mt-2">{{ error }}</p>
        </div>
        
        <div class="mt-8 pt-6 border-t border-gray-100 grid grid-cols-3 gap-2 text-[10px] text-center text-gray-400 font-bold uppercase">
          <div class="cursor-pointer hover:text-indigo-600" @click="loginForm.email='admin@example.com'; loginForm.password='password'">Admin</div>
          <div class="cursor-pointer hover:text-indigo-600" @click="loginForm.email='teacher@example.com'; loginForm.password='password'">Teacher</div>
          <div class="cursor-pointer hover:text-indigo-600" @click="loginForm.email='parent@example.com'; loginForm.password='password'">Parent</div>
        </div>
      </div>
    </div>

    <!-- 2. GIAO DIỆN DASHBOARD CHÍNH -->
    <template v-else>
      <aside class="w-64 bg-indigo-900 text-indigo-100 hidden md:flex flex-col shadow-2xl">
        <div class="p-6">
          <h2 class="text-xl font-bold flex items-center gap-2">
            <span class="w-8 h-8 bg-indigo-500 rounded flex items-center justify-center text-sm text-white">EM</span>
            EduManager
          </h2>
        </div>
        
        <nav class="flex-1 px-4 space-y-1">
          <a @click="currentTab = 'dashboard'" :class="tabClass('dashboard')" class="flex items-center gap-3 px-4 py-3 rounded-lg cursor-pointer transition-colors">
            <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path d="M3 12l2-2m0 0l7-7 7 7M5 10v10a1 1 0 001 1h3m10-11l2 2m-2-2v10a1 1 0 01-1 1h-3m-6 0a1 1 0 001-1v-4a1 1 0 011-1h2a1 1 0 011 1v4a1 1 0 001 1m-6 0h6"/></svg>
            Tổng quan
          </a>
          <template v-if="user?.role === 'admin'">
            <div class="pt-4 pb-2 text-xs font-bold text-indigo-400 uppercase px-4">Quản trị</div>
            <a @click="currentTab = 'teachers'" :class="tabClass('teachers')" class="flex items-center gap-3 px-4 py-3 rounded-lg cursor-pointer transition-colors">
              <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path d="M12 4.354a4 4 0 110 5.292M15 21H3v-1a6 6 0 0112 0v1zm0 0h6v-1a6 6 0 00-9-5.197M13 7a4 4 0 11-8 0 4 4 0 018 0z"/></svg>
              Giáo viên
            </a>
            <a @click="currentTab = 'subjects'" :class="tabClass('subjects')" class="flex items-center gap-3 px-4 py-3 rounded-lg cursor-pointer transition-colors">
              <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path d="M12 6.253v13m0-13C10.832 5.477 9.246 5 7.5 5S4.168 5.477 3 6.253v13C4.168 18.477 5.754 18 7.5 18s3.332.477 4.5 1.253m0-13C13.168 5.477 14.754 5 16.5 5c1.747 0 3.332.477 4.5 1.253v13C19.832 18.477 18.247 18 16.5 18c-1.746 0-3.332.477-4.5 1.253"/></svg>
              Môn học
            </a>
          </template>
          <div class="pt-4 pb-2 text-xs font-bold text-indigo-400 uppercase px-4">Học tập</div>
          <a v-if="user?.role === 'admin' || user?.role === 'teacher'" @click="currentTab = 'classes'" :class="tabClass('classes')" class="flex items-center gap-3 px-4 py-3 rounded-lg cursor-pointer transition-colors">
            <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path d="M19 21V5a2 2 0 00-2-2H7a2 2 0 00-2 2v16m14 0h2m-2 0h-5m-9 0H3m2 0h5M9 7h1m-1 4h1m4-4h1m-1 4h1m-5 10v-5a1 1 0 011-1h2a1 1 0 011 1v5m-4 0h4"/></svg>
            Lớp học
          </a>
          <a v-if="user?.role === 'admin' || user?.role === 'parent'" @click="currentTab = 'students'" :class="tabClass('students')" class="flex items-center gap-3 px-4 py-3 rounded-lg cursor-pointer transition-colors">
            <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path d="M16 7a4 4 0 11-8 0 4 4 0 018 0zM12 14a7 7 0 00-7 7h14a7 7 0 00-7-7z"/></svg>
            {{ user?.role === 'parent' ? 'Con cái' : 'Học sinh' }}
          </a>
        </nav>
        <div class="p-4 bg-indigo-950">
          <div class="flex items-center gap-3 mb-4 text-white">
            <div class="w-10 h-10 bg-indigo-500 rounded-full flex items-center justify-center font-bold uppercase">{{ user?.name.charAt(0) }}</div>
            <div class="overflow-hidden">
              <p class="text-sm font-bold truncate">{{ user?.name }}</p>
              <p class="text-[10px] text-indigo-400 truncate uppercase">{{ user?.role }}</p>
            </div>
          </div>
          <button @click="handleLogout" class="w-full py-2 bg-indigo-800 hover:bg-red-600 text-xs font-bold rounded transition-colors text-white uppercase">Đăng xuất</button>
        </div>
      </aside>

      <main class="flex-1 flex flex-col overflow-hidden bg-white">
        <header class="h-16 bg-white border-b border-gray-200 flex items-center justify-between px-8 z-10">
          <h2 class="text-lg font-bold capitalize text-gray-800">{{ currentTab === 'dashboard' ? 'Bảng điều khiển' : currentTab }}</h2>
          <div class="flex items-center gap-4">
            <span class="text-xs text-gray-400">Trạng thái: Online</span>
            <div class="w-2 h-2 bg-green-500 rounded-full"></div>
          </div>
        </header>

        <div class="flex-1 overflow-auto p-8">
          <div v-if="currentTab === 'dashboard'" class="grid grid-cols-1 md:grid-cols-3 gap-6">
            <div class="bg-indigo-50 p-6 rounded-2xl border border-indigo-100 flex items-center gap-4">
              <div class="w-12 h-12 bg-white text-indigo-600 rounded-xl flex items-center justify-center shadow-sm"><svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path d="M12 4.354a4 4 0 110 5.292M15 21H3v-1a6 6 0 0112 0v1zm0 0h6v-1a6 6 0 00-9-5.197M13 7a4 4 0 11-8 0 4 4 0 018 0z"/></svg></div>
              <div><p class="text-xs text-indigo-400 font-bold uppercase">Giáo viên</p><p class="text-2xl font-black text-indigo-900">{{ dataList.length }}</p></div>
            </div>
          </div>

          <div v-else class="bg-white rounded-2xl border border-gray-100 shadow-sm overflow-hidden">
            <div class="p-6 border-b border-gray-50 flex items-center justify-between">
              <div>
                <h3 class="font-bold text-xl text-gray-800">Danh mục {{ currentTab }}</h3>
                <p class="text-xs text-gray-400">Tổng cộng {{ dataList.length }} bản ghi</p>
              </div>
              <div class="flex gap-2">
                <button @click="fetchData" class="p-2 hover:bg-gray-100 rounded-lg transition-all text-gray-500"><svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"/></svg></button>
                <button v-if="user?.role === 'admin' && currentTab === 'teachers'" @click="openCreateTeacher" class="px-5 py-2.5 bg-indigo-600 hover:bg-indigo-700 text-white text-xs font-bold rounded-xl transition-all shadow-lg shadow-indigo-100">+ Thêm Giáo viên</button>
              </div>
            </div>
            
            <div class="overflow-x-auto">
              <table class="w-full text-left">
                <thead class="bg-gray-50/50 text-[10px] uppercase font-bold text-gray-400 border-b border-gray-100">
                  <tr>
                    <th class="px-6 py-4">ID</th>
                    <template v-if="currentTab === 'teachers'">
                      <th class="px-6 py-4">Họ tên</th><th class="px-6 py-4">Email</th><th class="px-6 py-4">Số điện thoại</th><th class="px-6 py-4">Chuyên môn</th>
                    </template>
                    <template v-if="currentTab === 'subjects'">
                      <th class="px-6 py-4">Tên môn học</th><th class="px-6 py-4">Mô tả</th>
                    </template>
                    <template v-if="currentTab === 'classes'">
                      <th class="px-6 py-4">Lớp học</th><th class="px-6 py-4 text-right">Học phí</th>
                    </template>
                    <template v-if="currentTab === 'students'">
                      <th class="px-6 py-4">Tên học sinh</th><th class="px-6 py-4 text-center">Hành động</th>
                    </template>
                    <th v-if="currentTab !== 'students'" class="px-6 py-4 text-center">Thao tác</th>
                  </tr>
                </thead>
                <tbody class="divide-y divide-gray-50">
                  <tr v-for="item in dataList" :key="item.id" class="group hover:bg-gray-50/50 transition-colors">
                    <td class="px-6 py-4 text-gray-400 font-mono text-xs">#{{ item.id }}</td>
                    <template v-if="currentTab === 'teachers'">
                      <td class="px-6 py-4 font-bold text-gray-800">{{ item.name }}</td>
                      <td class="px-6 py-4 text-indigo-600 text-xs font-medium">{{ item.email }}</td>
                      <td class="px-6 py-4 text-gray-500 text-sm">{{ item.phone }}</td>
                      <td class="px-6 py-4"><span class="px-2.5 py-1 bg-indigo-50 text-indigo-600 rounded-lg text-xs font-medium">{{ item.specialization }}</span></td>
                      <td class="px-6 py-4 text-center">
                        <div class="flex items-center justify-center gap-2 opacity-0 group-hover:opacity-100 transition-opacity">
                          <button @click="openEditTeacher(item)" class="p-1.5 text-blue-600 hover:bg-blue-50 rounded-lg"><svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z"/></svg></button>
                          <button @click="handleDeleteTeacher(item.id)" class="p-1.5 text-red-600 hover:bg-red-50 rounded-lg"><svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"/></svg></button>
                        </div>
                      </td>
                    </template>
                    <template v-if="currentTab === 'subjects'">
                      <td class="px-6 py-4 font-bold text-gray-800">{{ item.name }}</td>
                      <td class="px-6 py-4 text-gray-500 italic text-xs">{{ item.description || 'Chưa có mô tả' }}</td>
                      <td class="px-6 py-4 text-center text-gray-300 italic">...</td>
                    </template>
                    <template v-if="currentTab === 'classes'">
                      <td class="px-6 py-4 font-bold text-indigo-600">{{ item.name }}</td>
                      <td class="px-6 py-4 text-right font-black text-gray-700">{{ item.base_price?.toLocaleString() }}đ</td>
                      <td class="px-6 py-4 text-center text-gray-300 italic">...</td>
                    </template>
                    <template v-if="currentTab === 'students'">
                      <td class="px-6 py-4 font-bold text-gray-800">{{ item.name }}</td>
                      <td class="px-6 py-4 text-center">
                        <button class="text-xs font-bold text-indigo-600 hover:bg-indigo-50 px-3 py-1.5 rounded-lg border border-indigo-100">Chi tiết con</button>
                      </td>
                    </template>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>
      </main>
    </template>

    <!-- 3. MODAL FORM GIÁO VIÊN -->
    <div v-if="showTeacherModal" class="fixed inset-0 bg-black/60 backdrop-blur-sm flex items-center justify-center z-50 p-4">
      <div class="bg-white w-full max-w-md rounded-2xl shadow-2xl overflow-hidden animate-in fade-in zoom-in duration-200">
        <div class="p-6 border-b border-gray-100 flex justify-between items-center bg-gray-50/50">
          <h3 class="font-bold text-xl text-gray-800">{{ isEditing ? 'Cập nhật Giáo viên' : 'Thêm Giáo viên mới' }}</h3>
          <button @click="showTeacherModal = false" class="text-gray-400 hover:text-gray-600"><svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path d="M6 18L18 6M6 6l12 12"/></svg></button>
        </div>
        <div class="p-6 space-y-4">
          <div>
            <label class="block text-xs font-bold text-gray-500 uppercase mb-1.5">Họ và tên</label>
            <input v-model="teacherForm.name" type="text" class="w-full px-4 py-2.5 bg-gray-50 border border-gray-200 rounded-xl focus:ring-2 focus:ring-indigo-500 outline-none transition-all" placeholder="Nguyễn Văn A">
          </div>
          <div>
            <label class="block text-xs font-bold text-gray-500 uppercase mb-1.5">Email (Tài khoản)</label>
            <input v-model="teacherForm.email" type="email" class="w-full px-4 py-2.5 bg-gray-50 border border-gray-200 rounded-xl focus:ring-2 focus:ring-indigo-500 outline-none transition-all" placeholder="teacher@example.com">
          </div>
          <div>
            <label class="block text-xs font-bold text-gray-500 uppercase mb-1.5">
              {{ isEditing ? 'Mật khẩu mới (Để trống nếu không đổi)' : 'Mật khẩu' }}
            </label>
            <input v-model="teacherForm.password" type="password" class="w-full px-4 py-2.5 bg-gray-50 border border-gray-200 rounded-xl focus:ring-2 focus:ring-indigo-500 outline-none transition-all" placeholder="••••••••">
          </div>
          <div>
            <label class="block text-xs font-bold text-gray-500 uppercase mb-1.5">Số điện thoại</label>
            <input v-model="teacherForm.phone" type="text" class="w-full px-4 py-2.5 bg-gray-50 border border-gray-200 rounded-xl focus:ring-2 focus:ring-indigo-500 outline-none transition-all" placeholder="0123 456 789">
          </div>
          <div>
            <label class="block text-xs font-bold text-gray-500 uppercase mb-1.5">Chuyên môn</label>
            <input v-model="teacherForm.specialization" type="text" class="w-full px-4 py-2.5 bg-gray-50 border border-gray-200 rounded-xl focus:ring-2 focus:ring-indigo-500 outline-none transition-all" placeholder="Toán, Lý, Hóa...">
          </div>
        </div>
        <div class="p-6 bg-gray-50/80 border-t border-gray-100 flex gap-3">
          <button @click="showTeacherModal = false" class="flex-1 py-3 text-sm font-bold text-gray-500 hover:bg-gray-100 rounded-xl transition-all font-sans">Hủy</button>
          <button @click="handleSaveTeacher" :disabled="loading" class="flex-1 py-3 bg-indigo-600 hover:bg-indigo-700 text-white text-sm font-bold rounded-xl shadow-lg shadow-indigo-100 transition-all disabled:opacity-50">
            {{ isEditing ? 'Cập nhật ngay' : 'Tạo mới' }}
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, watch } from 'vue';
import { apiFetch } from './api/fetch';

const token = ref(localStorage.getItem('access_token'));
const user = ref<any>(null);
const currentTab = ref('dashboard');
const dataList = ref<any[]>([]);
const loading = ref(false);
const error = ref('');

const loginForm = ref({ email: '', password: '' });

const showTeacherModal = ref(false);
const isEditing = ref(false);
const editingId = ref<number | null>(null);
const teacherForm = ref({
  name: '',
  email: '',
  password: '',
  phone: '',
  specialization: ''
});

const tabClass = (tab: string) => {
  return currentTab.value === tab 
    ? 'bg-indigo-700 text-white font-bold shadow-lg shadow-indigo-900/50' 
    : 'hover:bg-indigo-800 text-indigo-300';
};

const handleLogin = async () => {
  loading.value = true;
  error.value = '';
  try {
    const data = await apiFetch('/login', {
      method: 'POST',
      body: JSON.stringify(loginForm.value)
    });
    localStorage.setItem('access_token', data.access_token);
    token.value = data.access_token;
    user.value = data.user;
    currentTab.value = 'dashboard';
  } catch (err: any) {
    error.value = err.message || 'Sai thông tin đăng nhập';
  } finally {
    loading.value = false;
  }
};

const handleLogout = async () => {
  try { await apiFetch('/logout', { method: 'POST' }); } catch {}
  localStorage.removeItem('access_token');
  token.value = null;
  user.value = null;
  dataList.value = [];
};

const fetchData = async () => {
  if (currentTab.value === 'dashboard') return;
  let endpoint = `/${currentTab.value}`;
  if (currentTab.value === 'students' && user.value?.role === 'parent') {
    endpoint = '/my-children';
  }
  try {
    const data = await apiFetch(endpoint);
    dataList.value = data.data || data;
  } catch (err) {
    dataList.value = [];
  }
};

const openCreateTeacher = () => {
  isEditing.value = false;
  editingId.value = null;
  teacherForm.value = { name: '', email: '', password: '', phone: '', specialization: '' };
  showTeacherModal.value = true;
};

const openEditTeacher = (teacher: any) => {
  isEditing.value = true;
  editingId.value = teacher.id;
  teacherForm.value = { 
    name: teacher.name, 
    email: teacher.email,
    password: '', // Để trống khi sửa
    phone: teacher.phone, 
    specialization: teacher.specialization
  };
  showTeacherModal.value = true;
};

const handleSaveTeacher = async () => {
  if (!teacherForm.value.name || !teacherForm.value.email || (!isEditing.value && !teacherForm.value.password)) {
    alert('Vui lòng điền đầy đủ các thông tin bắt buộc!');
    return;
  }
  loading.value = true;
  try {
    const method = isEditing.value ? 'PUT' : 'POST';
    const endpoint = isEditing.value ? `/teachers/${editingId.value}` : '/teachers';
    await apiFetch(endpoint, {
      method,
      body: JSON.stringify(teacherForm.value)
    });
    showTeacherModal.value = false;
    await fetchData(); 
    alert(isEditing.value ? 'Cập nhật thành công!' : 'Thêm mới thành công!');
  } catch (err: any) {
    alert('Lỗi: ' + err.message);
  } finally {
    loading.value = false;
  }
};

const handleDeleteTeacher = async (id: number) => {
  if (!confirm('Xóa giáo viên này?')) return;
  try {
    await apiFetch(`/teachers/${id}`, { method: 'DELETE' });
    await fetchData();
    alert('Đã xóa!');
  } catch (err: any) {
    alert('Lỗi: ' + err.message);
  }
};

watch(currentTab, () => fetchData());
onMounted(async () => {
  if (token.value) {
    try {
      const data = await apiFetch('/me');
      user.value = data.user;
      fetchData();
    } catch { handleLogout(); }
  }
});
</script>

<style>
/* Sử dụng CSS thuần cho scrollbar để tránh lỗi @apply trong v4 */
::-webkit-scrollbar { width: 4px; }
::-webkit-scrollbar-track { background: #f8fafc; }
::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 10px; }
::-webkit-scrollbar-thumb:hover { background: #94a3b8; }

.animate-in {
  animation: modal-in 0.3s ease-out;
}

@keyframes modal-in {
  from { opacity: 0; transform: scale(0.95) translateY(10px); }
  to { opacity: 1; transform: scale(1) translateY(0); }
}
</style>
