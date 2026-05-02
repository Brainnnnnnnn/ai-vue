<template>
    <el-dialog
        title="文章详情"
        v-model="dialogVisible"
        width="50%"
        @close="handleClose"
    >
        <el-form :model="formData" ref="formRef" label-width="120px">
            <el-form-item label="文章标题" prop="title">
                <el-input v-model="formData.title" placeholder="请输入文章标题" maxlength="200" show-word-limit clearable />
            </el-form-item>

        </el-form>

    </el-dialog>
</template>

<script setup>
import { computed,ref,reactive } from 'vue'

// form

const formData = ref({
    "title": "",
    "content": "",
    "coverImage": "",
    "categoryId": 0,
    "summary": "",
    "tags": "",
    "id": ""
})



const props = defineProps({
    modelValue: {
        type:Boolean,
        default: false
    }
})

const emit = defineEmits(['update:modelValue'])
const dialogVisible = computed({
    get() {
        return props.modelValue
    },
    set(val) {
        emit('update:modelValue',val)
    }
})

const handleClose = () => {}
</script>
