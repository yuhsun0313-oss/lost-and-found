<script setup>
import { computed, ref } from 'vue'

const keyword = ref('')
const category = ref('全部')
const showForm = ref(false)

const categories = [
  '全部',
  '電子產品',
  '文具',
  '生活用品',
  '其他',
]

const items = ref([
  {
    id: 1,
    title: '白色無線耳機盒',
    category: '電子產品',
    location: '電腦教室',
    date: '2026-10-10',
    type: 'found',
    image:
      'https://images.unsplash.com/photo-1606220945770-b5b6c2c55bf1?w=600',
  },
  {
    id: 2,
    title: '黑色筆袋',
    category: '文具',
    location: '圖書館',
    date: '2026-10-09',
    type: 'lost',
    image:
      'https://images.unsplash.com/photo-1455390582262-044cdead277a?w=600',
  },
  {
    id: 3,
    title: '藍色水壺',
    category: '生活用品',
    location: '體育館',
    date: '2026-10-08',
    type: 'found',
    image:
      'https://images.unsplash.com/photo-1602143407151-7111542de6e8?w=600',
  },
])

// 新增表單用的雙向綁定資料
const formTitle = ref('')
const formCategory = ref('電子產品')
const formLocation = ref('')
const formDate = ref(new Date().toISOString().slice(0, 10))
const formType = ref('found')
const formImage = ref('')

const filteredItems = computed(() => {
  return items.value.filter((item) => {
    const matchesKeyword =
      `${item.title} ${item.location}`
        .toLowerCase()
        .includes(keyword.value.toLowerCase())

    const matchesCategory =
      category.value === '全部' ||
      item.category === category.value

    return matchesKeyword && matchesCategory
  })
})

function submitItem() {
  // 檢查必填欄位
  if (!formTitle.value.trim() || !formLocation.value.trim()) {
    alert('請填寫物品名稱與地點！')
    return
  }

  // 將新物品加到列表最前方
  items.value.unshift({
    id: Date.now(),
    title: formTitle.value,
    category: formCategory.value,
    location: formLocation.value,
    date: formDate.value,
    type: formType.value,
    image: formImage.value.trim(),
  })

  // 重置表單並關閉彈窗
  formTitle.value = ''
  formLocation.value = ''
  formImage.value = ''
  showForm.value = false
}
</script>

<template>
  <div class="min-h-screen bg-slate-50 text-slate-800">
    <!-- 導覽列 -->
    <header class="border-b border-slate-200 bg-white">
      <div
        class="mx-auto flex max-w-6xl items-center
        justify-between px-5 py-4"
      >
        <a href="/" class="text-xl font-black text-indigo-600">
          ◈ 尋物雷達
        </a>

        <button
          class="rounded-xl bg-indigo-600 px-4 py-2
          text-sm font-bold text-white hover:bg-indigo-700"
          @click="showForm = true"
        >
          + 刊登物品
        </button>
      </div>
    </header>

    <main class="mx-auto max-w-6xl px-5 py-10">
      <!-- 主視覺 -->
      <section
        class="rounded-3xl bg-indigo-600 px-6 py-12
        text-white sm:px-12"
      >
        <p class="mb-3 text-sm font-bold tracking-widest text-indigo-200">
          CAMPUS LOST & FOUND
        </p>

        <h1 class="text-3xl font-black leading-tight sm:text-5xl">
          遺失的物品，<br />
          讓我們幫你找回。
        </h1>

        <p class="mt-4 max-w-lg text-sm leading-7 text-indigo-100">
          搜尋校園失物，或刊登拾獲物品。
          未來將透過 AI 影像比對，推薦可能的失物配對。
        </p>

        <div class="mt-7 flex max-w-xl flex-col gap-3 sm:flex-row">
          <input
            v-model="keyword"
            type="text"
            placeholder="搜尋耳機、水壺、圖書館..."
            class="min-w-0 flex-1 rounded-xl bg-white px-4
            py-3 text-slate-800 outline-none
            focus:ring-4 focus:ring-indigo-300"
          />

          <button
            class="rounded-xl bg-white px-6 py-3
            font-bold text-indigo-700 hover:bg-indigo-50"
            @click="category = '全部'"
          >
            搜尋物品
          </button>
        </div>
      </section>

      <!-- 分類 -->
      <section class="mt-10">
        <div class="flex flex-wrap gap-2">
          <button
            v-for="item in categories"
            :key="item"
            class="rounded-full border px-4 py-2 text-sm font-semibold
            transition"
            :class="
              category === item
                ? 'border-indigo-600 bg-indigo-600 text-white'
                : 'border-slate-200 bg-white text-slate-600 hover:border-indigo-300'
            "
            @click="category = item"
          >
            {{ item }}
          </button>
        </div>
      </section>

      <!-- 清單標題 -->
      <section class="mt-8">
        <div class="mb-5 flex items-end justify-between">
          <div>
            <h2 class="text-2xl font-black">校園失物列表</h2>
            <p class="mt-1 text-sm text-slate-500">
              找找看，有沒有你正在尋找的物品。
            </p>
          </div>

          <span class="text-sm text-slate-500">
            {{ filteredItems.length }} 筆紀錄
          </span>
        </div>

        <!-- 物品卡片 -->
        <div class="grid grid-cols-1 gap-5 sm:grid-cols-2 lg:grid-cols-3">
          <article
            v-for="item in filteredItems"
            :key="item.id"
            class="overflow-hidden rounded-2xl border border-slate-200
            bg-white shadow-sm transition hover:-translate-y-1
            hover:shadow-lg"
          >
            <div class="relative h-52 bg-slate-100">
              <img
                v-if="item.image"
                :src="item.image"
                :alt="item.title"
                class="h-full w-full object-cover"
              />

              <div
                v-else
                class="flex h-full items-center justify-center
                text-slate-400"
              >
                尚無物品照片
              </div>

              <span
                class="absolute left-3 top-3 rounded-full px-3 py-1
                text-xs font-bold"
                :class="
                  item.type === 'found'
                    ? 'bg-emerald-100 text-emerald-700'
                    : 'bg-amber-100 text-amber-700'
                "
              >
                {{ item.type === 'found' ? '已拾獲' : '正在尋找' }}
              </span>
            </div>

            <div class="p-5">
              <p class="text-xs font-semibold text-indigo-600">
                {{ item.category }}
              </p>

              <h3 class="mt-2 text-lg font-bold">
                {{ item.title }}
              </h3>

              <div class="mt-4 space-y-2 text-sm text-slate-500">
                <p>📍 {{ item.location }}</p>
                <p>📅 {{ item.date }}</p>
              </div>

              <button
                class="mt-5 w-full rounded-xl border border-indigo-200
                py-2.5 text-sm font-bold text-indigo-700
                hover:bg-indigo-50"
                @click="keyword = item.title"
              >
                查看／搜尋相似物品
              </button>
            </div>
          </article>
        </div>

        <p
          v-if="filteredItems.length === 0"
          class="py-16 text-center text-slate-500"
        >
          找不到符合條件的物品，試試其他關鍵字。
        </p>
      </section>
    </main>

    <!-- 真正的刊登表單彈窗 -->
    <div
      v-if="showForm"
      class="fixed inset-0 z-10 flex items-center
      justify-center bg-slate-900/50 p-5"
      @click.self="showForm = false"
    >
      <div class="w-full max-w-lg rounded-2xl bg-white p-6 shadow-xl max-h-[90vh] overflow-y-auto">
        <h2 class="text-xl font-black">刊登失物 / 拾獲物品</h2>
        <p class="mt-1 text-sm text-slate-500">
          填寫詳細資訊，讓失主或拾獲人更容易找到。
        </p>

        <div class="mt-5 space-y-4">
          <!-- 類型選擇 -->
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1">類型</label>
            <div class="grid grid-cols-2 gap-3">
              <button
                type="button"
                class="py-2.5 rounded-xl text-sm font-bold border transition"
                :class="formType === 'found' ? 'border-emerald-600 bg-emerald-50 text-emerald-700' : 'border-slate-200 text-slate-600'"
                @click="formType = 'found'"
              >
                🟢 我撿到了 (已拾獲)
              </button>
              <button
                type="button"
                class="py-2.5 rounded-xl text-sm font-bold border transition"
                :class="formType === 'lost' ? 'border-amber-600 bg-amber-50 text-amber-700' : 'border-slate-200 text-slate-600'"
                @click="formType = 'lost'"
              >
                🟠 我弄丟了 (正在尋找)
              </button>
            </div>
          </div>

          <!-- 物品名稱 -->
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1">物品名稱 *</label>
            <input
              v-model="formTitle"
              type="text"
              placeholder="例如：黑色筆電包、學生證"
              class="w-full rounded-xl border border-slate-200 px-4 py-2.5 text-sm outline-none focus:border-indigo-600"
            />
          </div>

          <!-- 分類選擇 -->
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1">分類</label>
            <select
              v-model="formCategory"
              class="w-full rounded-xl border border-slate-200 px-4 py-2.5 text-sm outline-none focus:border-indigo-600 bg-white"
            >
              <option v-for="cat in categories.filter(c => c !== '全部')" :key="cat" :value="cat">
                {{ cat }}
              </option>
            </select>
          </div>

          <!-- 發現地點 -->
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1">地點 *</label>
            <input
              v-model="formLocation"
              type="text"
              placeholder="例如：圖書館 3 樓、體育館外"
              class="w-full rounded-xl border border-slate-200 px-4 py-2.5 text-sm outline-none focus:border-indigo-600"
            />
          </div>

          <!-- 日期 -->
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1">日期</label>
            <input
              v-model="formDate"
              type="date"
              class="w-full rounded-xl border border-slate-200 px-4 py-2.5 text-sm outline-none focus:border-indigo-600"
            />
          </div>

          <!-- 圖片網址 -->
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1">圖片網址 (選填)</label>
            <input
              v-model="formImage"
              type="text"
              placeholder="貼上圖片網址（例如 Unsplash 圖片連結）"
              class="w-full rounded-xl border border-slate-200 px-4 py-2.5 text-sm outline-none focus:border-indigo-600"
            />
          </div>
        </div>

        <div class="mt-6 flex gap-3">
          <button
            class="flex-1 rounded-xl bg-indigo-600 py-3
            font-bold text-white hover:bg-indigo-700"
            @click="submitItem"
          >
            確認送出
          </button>

          <button
            class="flex-1 rounded-xl border border-slate-200 py-3
            font-bold text-slate-600 hover:bg-slate-100"
            @click="showForm = false"
          >
            取消
          </button>
        </div>
      </div>
    </div>
  </div>
</template>