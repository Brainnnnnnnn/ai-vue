<template>
    <el-form :model="formData" ref="ruleFormRef">
        <el-row :gutter="24">
                <template v-for="item in formItemAttr" :key="item.prop">
                    <el-col v-bind="item.col">
                        <el-form-item :label="item.label" :prop="item.prop">
                            <component :is="isComp(item.comp)" :placeholder="item.placeholder" v-model="formData[item.prop]">
                                <template v-if="item.comp === 'select'">
                                    <el-option label= "全部" value = ""/>
                                    <el-option
                                    v-for="opt in item.options" 
                                    :key="opt.value"
                                    :label = "opt.label" 
                                    :value = "opt.value" />
                                </template>
                            </component>
                        </el-form-item>
                    </el-col>
                </template>
        </el-row>
        <el-row>
            <el-button type="primary" @click="handleSearch">查询</el-button>
            <el-button @click="handleReset(ruleFormRef)">重置</el-button>
        </el-row>
        
    </el-form>
</template>

<script setup>

import { reactive ,computed, ref} from 'vue'


const emit = defineEmits(['Search'])

const props = defineProps({
  formItem: {
    type: Array,
    default: () => []
  }
})

// pull	栅格向左移动格数		0
// xs	<768px 响应式栅格数或者栅格属性对象	 / 	—
// sm	≥768px 响应式栅格数或者栅格属性对象	 / 	—
// md	≥992px 响应式栅格数或者栅格属性对象	 / 	—
// lg	≥1200px 响应式栅格数或者栅格属性对象	 / 	—
// xl	≥1920px 响应式栅格数或者栅格属性对象
const formItemAttr = computed(() => {
    const { formItem } = props
     formItem.forEach(item => {
        item.col = {xs: 24, sm: 12, md: 8, lg: 6, xl: 6}
    })
    return formItem
})

const formData = reactive({})
const isComp = (comp) => {
    return {
        input: 'el-input',
        select: 'el-select'
    }[comp]
}

const handleSearch = () => {
    emit('Search', formData)
}
const ruleFormRef = ref()
const handleReset = (formEl) => {
    if (!formEl) return
    formEl.resetFields()
    emit('Search', formData)
}
</script>