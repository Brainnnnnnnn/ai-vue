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
    </el-form>
</template>

<script setup>

import { reactive } from 'vue'

const formData = reactive({})



const props = defineProps({
  formItem: {
    type: Array,
    default: () => []
  }
})

const isComp = (comp) => {
    return {
        input: 'el-input',
        select: 'el-select'
    }[comp]
}
</script>