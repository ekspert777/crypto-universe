<template>
  <h1 class="mb-4 text-center text-3xl font-extrabold text-gray-900 dark:text-white md:text-5xl lg:text-6xl"><span
      class="text-transparent bg-clip-text bg-gradient-to-r to-orange-600 from-red-500">Crypto Universe</span> by
    Starling
  </h1>
  <cu-table>
    <cu-table-head>
      <cu-table-head-cell>Module</cu-table-head-cell>
      <cu-table-head-cell>Status</cu-table-head-cell>
    </cu-table-head>
    <cu-table-body>
      <cu-table-row v-for="module in availableModules" :key="module">
        <cu-table-cell>{{ module }}</cu-table-cell>
        <cu-table-cell>
          <div class="flex items-center">
            <div v-if="moduleEnabled(module)" class="h-2.5 w-2.5 rounded-full bg-green-500 me-2"></div>
            <div v-else class="h-2.5 w-2.5 rounded-full bg-red-500 me-2"></div>
            {{ moduleEnabled(module) ? 'Enabled' : 'Disabled' }}
          </div>
        </cu-table-cell>
      </cu-table-row>
    </cu-table-body>
  </cu-table>
</template>

<script setup>
import { ref, onMounted, getCurrentInstance } from 'vue'
import { loadModuleData } from '@/utils'
import { initFlowbite } from 'flowbite'
import {
  CuTable,
  CuTableHead,
  CuTableHeadCell,
  CuTableBody,
  CuTableRow,
  CuTableCell
} from '@/components/cu'

const availableModules = ref([])
const modules = ref({})

const module = ref('crypto_universe')

const { proxy } = getCurrentInstance()

const loadDefaults = async () => {
  await loadModuleData(proxy, module.value, 'instructions', 'js', (data) => {
    if (!Object.hasOwn(data, 'modules')) return

    modules.value = data.modules
  })

  await loadModuleData(proxy, module.value, 'configs', 'js', (data) => {
    if (!Object.hasOwn(data, 'available_modules')) return

    availableModules.value = data.available_modules
  })
}

const moduleEnabled = (module) => {
  return modules.value[module] ? modules.value[module].enabled : false
}

onMounted(async () => {
  initFlowbite()
  await loadDefaults()
})
</script>
