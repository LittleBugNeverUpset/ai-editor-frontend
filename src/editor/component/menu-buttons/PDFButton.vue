<template>
  <div>
    <a-popover placement="bottom" trigger="click">
      <template #content>
        <ul class="dropdown">
          <li class="dropdown__opeartion" @click="insertLocalPdf">
            <CloudUploadOutlined style="margin-right: 5px" />
            上传本地PDF
          </li>
        </ul>
      </template>
      <a-tooltip placement="top">
        <template #title>
          <span>PDF</span>
        </template>
        <div class="tools__button">
          <FilePdfOutlined style="font-size: 16px; font-weight: 600" />
        </div>
      </a-tooltip>
    </a-popover>
    <InsertPDF
      ref="insertRef"
      :options="{ title: '插入PDF地址', placeholder: 'URL of PDF', headers }"
      @emitInsert="handleEmit"
    >
      <a-form-item label="插入类型" name="type">
        <a-radio-group v-model:value="type" button-style="solid">
          <a-radio-button :value="1">附件形式</a-radio-button>
          <a-radio-button :value="2">内容形式</a-radio-button>
        </a-radio-group>
      </a-form-item>
    </InsertPDF>
    <UploadPDF ref="uploadRef" :options="{ title: '上传PDF' }" @emitUpload="handleEmit"></UploadPDF>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { validateUrl } from '@/utils/pattern.ts'
import { CloudUploadOutlined, DisconnectOutlined, FilePdfOutlined } from '@ant-design/icons-vue'
import InsertPDF from './insert-model/index.vue'
import UploadPDF from './upload-model/index.vue'

const emit = defineEmits(['emitPdf'])
const props = defineProps(['editor'])

const insertRef = ref()
const uploadRef = ref()

const headers = [
  {
    formItem: {
      name: 'url',
      rules: [{ required: true, validator: validateUrl, trigger: 'blur' }]
    },
    component: {
      name: 'input',
      placeholder: 'URL of PDF'
    }
  }
]

const type = ref(1)
const handleEmit = (forms) => {
  const { url } = forms
  if (type.value === 1) {
    props.editor.commands.insertContent({
      type: 'text',
      text: `附件:${url}`,
      marks: [
        {
          type: 'link',
          attrs: {
            href: url
          }
        }
      ]
    })
  }
  if (type.value === 2) {
    props.editor.chain().focus().setIframe({ src: url }).run()
  }
  uploadRef.value.closeModal()
  insertRef.value.closeModal()
}

const insertLocalPdf = () => {
  type.value = 2
  uploadRef.value.showModal()
}
</script>

<style lang="scss" scoped>
.dropdown {
  &__opeartion {
    padding: 5px 0;
    cursor: pointer;
    transition: 0.2s;

    &:hover {
      color: #409eff;
    }
  }
}
</style>
