<template>
    <el-dialog
        title="文章详情"
        v-model="dialogVisible"
        width="50%"
        @close="handleClose"
    >
        <el-form :model="formData" ref="formRef" :rules="rules" label-width="120px" >
            <el-form-item label="文章标题" prop="title">
                <el-input v-model="formData.title" placeholder="请输入文章标题" maxlength="200" show-word-limit clearable />
            </el-form-item>
            <el-form-item label="所属分类" prop="categoryId">
                <el-select v-model="formData.categoryId" placeholder="请选择分类">
                    <el-option v-for="item in categories" :key="item.value" :label="item.label" :value="item.value" />
                </el-select>
            </el-form-item>
            <el-form-item label="文章摘要" prop="summary">
                <el-input type="textarea" v-model="formData.summary" placeholder="请输入文章摘要(可选)" maxlength="1000" show-word-limit :rows="4" />
            </el-form-item>
            <el-form-item label="标签" prop="tags">
                <el-select v-model="formData.tagArray" multiple placeholder="请选择文章标签" style="width: 100%;">
                    <el-option v-for="item in commonTags" :key="item" :label="item" :value="item" />
                </el-select>
            </el-form-item>
            <el-form-item label="封面图片">
                <div class="cover-upload">
                    <el-upload
                        class="image-upload"
                        action="#"
                        :before-upload="beforeUpload"
                        :http-request="handleUploadRequest"
                        accept="image/*"
                        :show-file-list = "false"
                    >
                        <div v-if="!imgUrl" class="cover-placeholder">
                            <p>点击上传封面</p>
                        </div>
                        <img v-else :src="imgUrl" alt="封面图片" class="cover-image">
                    </el-upload>
                    <div v-if="imgUrl">
                        <el-button type="danger" @click="deleteImg" >移除封面</el-button>
                    </div>
                </div>
            </el-form-item>
            <el-form-item label="文章内容" prop="content">
                <RichTextEditor
                    v-model="formData.content"
                    placeholder="请输入文件内容，支持富文本"
                    :maxCharCount="5000"
                    @change="handleContentChange"
                    @created="handleEditorCreated"
                    min-height="400px"
                    />
            </el-form-item>
        </el-form>
        <div v-if="btnPreview">
            <h3>内容预览</h3>
            <div v-html="formData.content"></div>
        </div>
        <template #footer>
            <el-button @click="btnPreview = !btnPreview">{{ btnPreview ? "隐藏预览" : "预览效果" }}</el-button>
            <el-button @click="handleClose">取消</el-button>
            <el-button @click="handleSubmit" :loading="loading">创建文章</el-button>
        </template>
    </el-dialog>
</template>

<script setup>
import { ElMessage } from 'element-plus'
import { computed,ref,reactive } from 'vue'
import { fileUpload, createArticle } from '@/api/login'
import { fileBaseUrl } from '@/config/index.js'
import RichTextEditor from '@/components/RichTextEditor.vue'
import { login } from '../api/login'

// form

const formData = ref({
    "title": "",
    "content": "",
    "coverImage": "",
    "categoryId": 1,
    "summary": "",
    "tags": "",
    "id": ""
})



const props = defineProps({
    modelValue: {
        type:Boolean,
        default: false
    },
    categories: {
        type:Array,
        default:() => []
    }
})

const emit = defineEmits(['update:modelValue', 'success'])
const dialogVisible = computed({
    get() {
        return props.modelValue
    },
    set(val) {
        emit('update:modelValue',val)
    }
})

const handleClose = () => {}

const rules = reactive({
    title: [
        { required: true, message: '请输入文章标题' ,trigger:'blur'},
        { min:1, message: '文章标题不可小于2个字符', trigger:'blur'}
    ],
    categoryId: [
        { required: true, message: '请选择分类' ,trigger:'blur'}
    ],
    content: [
        { required: true, message: '请输入文章内容' ,trigger:'blur'},
        { min:1, max:5000, message: '文章内容不可超过5000字符', trigger:'blur'}
    ],
})

// 文章标签
const commonTags = [ '情绪管理', '焦虑', '抑郁', '压力', '睡眠', '冥想', '正念', '放松', '心理健康', '自我成长', '人际关系', '工作压力', '学习方法', '生活技巧' ]


// 上传相关

const imgUrl = ref('')

const beforeUpload = (file) => {
    const isImage = file.type.startsWith('image/')
    const isLt5M = file.size / 1024 / 1024 < 5
    if(!isImage){
        ElMessage.error('上传封面图片，请选择图片文件')
        return
    }
    if(!isLt5M){
        ElMessage.error('上传封面图片，图片大小不能超过5M')
        return
    }
    return true
}

const handleUploadRequest = async ({ file }) => {
    const uuid = crypto.randomUUID()

    const imgRes = await fileUpload(file, {
        businessId: uuid
    })
    
    imgUrl.value = fileBaseUrl + imgRes.filePath
    formData.value.coverImage = imgRes.filePath
    console.log(imgUrl.value)
}

const deleteImg = () => {
    imgUrl.value = ""
    formData.coverImage = ""
}

// 富文本相关
const handleContentChange = ({ html }) => {
    formData.content = html
}

const handleEditorCreated = (editor) => {
    if(formData.content) {
        editor.setHtml(formData.content)
    }
}

// 预览
const btnPreview = ref(false)


// 创建文章
const formRef = ref()
const loading = ref(false)
const handleSubmit = () => {
    formRef.value.validate((valid, fields) => {
        if(valid){
            loading.value = true
        }else{
            return
        }
        // console.log(formData.value, 'formData')
        const submitData = {
            ...formData.value,
            tags: formData.value.tagArray.join(',')
        }
        delete submitData.tagArray

        console.log(submitData, 'submitData')
        createArticle(submitData).then(res => {
            loading.value = false
            emit('success')
        })

    })
}
</script>


<style lang="scss" scoped>

.cover-placeholder {
    width: 200px;
    height: 120px;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    color: #8b949e;
    background: #f6f8fa;
}

.cover-image {
    width: 200px;
    height: 100px;
    display: block;
}
</style>