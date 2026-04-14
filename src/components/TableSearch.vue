<template>
    <el-form :model="formData" >
        <el-form-item v-for="item in props.formItem" :key="item.prop">
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
        <el-form-item>
            <el-button type="primary" @click="handleSearch">查询</el-button>
            <el-button @click="handleReset">重置</el-button>
        </el-form-item>
    </el-form>
</template>

<script setup>

import { reactive } from 'vue'


const emit = defineEmits(['Search'])

const props = defineProps({
  formItem: {
    type: Array,
    default: () => []
  }
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

const handleReset = () => {
}
</script>