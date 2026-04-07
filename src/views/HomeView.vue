<script lang="ts" setup>
import { reactive, toRaw, ref, computed } from 'vue'
import type { UnwrapRef } from 'vue'
import { UserOutlined, LockOutlined } from '@ant-design/icons-vue'
import type { FormProps } from 'ant-design-vue'
import dayjs, { Dayjs } from 'dayjs'
import { FrownOutlined, SmileOutlined } from '@ant-design/icons-vue'
import { notification } from 'ant-design-vue'
import { CustomerServiceOutlined, CommentOutlined } from '@ant-design/icons-vue'

// Form 1
interface FormState {
  name: string
  delivery: boolean
  type: string[]
  resource: string
  desc: string
}
const formState: UnwrapRef<FormState> = reactive({
  name: '',
  delivery: false,
  type: [],
  resource: '',
  desc: '',
})
const onSubmit = () => {
  console.log('submit!', structuredClone(toRaw(formState)))
  resetForm()
}
const resetForm = () => {
  formState.name = ''
  formState.delivery = false
  formState.type = []
  formState.resource = ''
  formState.desc = ''
}
const labelCol = { span: 4 }
const wrapperCol = { span: 14 }

// Form 2
interface FormState2 {
  user: string
  password: string
}
const formState2: UnwrapRef<FormState2> = reactive({
  user: '',
  password: '',
})
const handleFinish: FormProps['onFinish'] = (values) => {
  console.log(values, formState)
}
const handleFinishFailed: FormProps['onFinishFailed'] = (errors) => {
  console.log(errors)
}
// Date Picker
const dateFormatList = ['DD/MM/YYYY', 'DD/MM/YY']
const dateValue = ref<Dayjs>(dayjs('07/04/2026', dateFormatList[0]))
const dateStatus = computed(() => {
  const today = dayjs()
  if (dateValue.value.isBefore(today, 'day')) {
    return 'error'
  }
  if (dateValue.value.isSame(today, 'day')) {
    return 'warning'
  }
  return ''
})
// Rate
const rateStar = ref<number>(2.5)
// Slider
const sliderValue = ref<number>(0)
const minSlider = ref<number>(0)
const maxSlider = ref<number>(10)

const preColor = computed(() => {
  const mid = +((maxSlider.value - minSlider.value) / 2).toFixed(5)
  return sliderValue.value >= mid ? '' : 'red'
})

const nextColor = computed(() => {
  const mid = +((maxSlider.value - minSlider.value) / 2).toFixed(5)
  return sliderValue.value >= mid ? 'green' : ''
})
// Switch
const state = reactive({
  checked1: false,
})
// Notification
type NotificationType = 'success' | 'info' | 'warning' | 'error' | 'open'

const openNotificationWithIcon = (type: NotificationType) => {
  let message = ''

  if (type === 'success') {
    message = 'Bạn đã theo dõi truyện thành công'
  } else if (type === 'error') {
    message = 'Bạn đã bỏ theo dõi truyện thành công'
  }

  notification[type]({
    message,
  })
}
</script>

<template>
  <!-- FORM 1 -->
  <div class="container">
    <div class="pt-5">
      <a-form :model="formState" :label-col="labelCol" :wrapper-col="wrapperCol">
        <a-form-item>
          <div class="flex items-center gap-4">
            <span class="font-semibold w-40 shrink-0">Activity Name:</span>
            <a-input v-model:value="formState.name" />
          </div>
        </a-form-item>
        <a-form-item>
          <div class="flex items-center gap-4">
            <span class="font-semibold w-40 shrink-0">Instant delivery:</span>
            <a-switch v-model:checked="formState.delivery" />
          </div>
        </a-form-item>
        <a-form-item>
          <div class="flex items-center gap-4">
            <span class="font-semibold w-40 shrink-0">Activity Type:</span>
            <a-checkbox-group v-model:value="formState.type">
              <a-checkbox value="1" name="type">Online</a-checkbox>
              <a-checkbox value="2" name="type">Promotion</a-checkbox>
              <a-checkbox value="3" name="type">Offline</a-checkbox>
            </a-checkbox-group>
          </div>
        </a-form-item>
        <a-form-item>
          <div class="flex items-center gap-4">
            <span class="font-semibold w-40 shrink-0">Resources:</span>
            <a-radio-group v-model:value="formState.resource">
              <a-radio value="1">Sponsor</a-radio>
              <a-radio value="2">Venue</a-radio>
            </a-radio-group>
          </div>
        </a-form-item>
        <a-form-item>
          <div class="flex items-center gap-4">
            <span class="font-semibold w-40 shrink-0">Activity Description:</span>
            <a-textarea v-model:value="formState.desc" />
          </div>
        </a-form-item>
        <a-form-item :wrapper-col="{ span: 14, offset: 0 }">
          <a-button type="primary" @click="onSubmit">Create</a-button>
          <a-button style="margin-left: 10px">Cancel</a-button>
        </a-form-item>
      </a-form>
    </div>
  </div>
  <!-- FORM 2 -->
  <div class="container">
    <div class="mt-10">
      <a-form
        layout="inline"
        :model="formState2"
        @finish="handleFinish"
        @finishFailed="handleFinishFailed"
      >
        <a-form-item>
          <a-input v-model:value="formState2.user" placeholder="Username">
            <template #prefix><UserOutlined style="color: rgba(0, 0, 0, 0.25)" /></template>
          </a-input>
        </a-form-item>
        <a-form-item>
          <a-input v-model:value="formState2.password" type="password" placeholder="Password">
            <template #prefix><LockOutlined style="color: rgba(0, 0, 0, 0.25)" /></template>
          </a-input>
        </a-form-item>
        <a-form-item>
          <a-button
            type="primary"
            html-type="submit"
            :disabled="formState2.user === '' || formState2.password === ''"
          >
            Log in
          </a-button>
        </a-form-item>
      </a-form>
    </div>
  </div>
  <!-- DATE PICKER -->
  <div class="container">
    <div class="mt-10">
      <a-date-picker :status="dateStatus" v-model:value="dateValue" :format="dateFormatList" />
    </div>
  </div>
  <!-- RATE -->
  <div class="container">
    <div class="mt-10">
      <a-rate v-model:value="rateStar" allow-half />
    </div>
  </div>
  <!-- SLIDER -->
  <div class="container">
    <div class="icon-wrapper mt-10">
      <frown-outlined :style="{ color: preColor }" />
      <a-slider v-model:value="sliderValue" :min="minSlider" :max="maxSlider" />
      <smile-outlined :style="{ color: nextColor }" />
    </div>
  </div>
  <!-- SWITCH -->
  <div class="container">
    <div class="mt-10">
      <a-switch v-model:checked="state.checked1" checked-children="Tắt" un-checked-children="Bật" />
    </div>
  </div>
  <!-- NOTIFICATION -->
  <div class="container">
    <div class="mt-10 flex gap-5">
      <a-button @click="() => openNotificationWithIcon('success')">Theo dõi</a-button>
      <a-button @click="() => openNotificationWithIcon('error')">Bỏ theo dõi</a-button>
    </div>
  </div>
  <!-- FLOAT BUTTON -->
  <div class="container">
    <div class="mt-10">
      <a-float-button-group trigger="click" type="primary" :style="{ right: '24px' }">
        <template #icon>
          <CustomerServiceOutlined />
        </template>
        <a-float-button />
        <a-float-button>
          <template #icon>
            <CommentOutlined />
          </template>
        </a-float-button>
      </a-float-button-group>
      <a-float-button-group trigger="hover" type="primary" :style="{ right: '94px' }">
        <template #icon>
          <CustomerServiceOutlined />
        </template>
        <a-float-button />
        <a-float-button>
          <template #icon>
            <CommentOutlined />
          </template>
        </a-float-button>
      </a-float-button-group>
    </div>
  </div>
</template>

<style scoped>
.icon-wrapper {
  position: relative;
  padding: 0px 30px;
}

.icon-wrapper .anticon {
  position: absolute;
  top: -2px;
  width: 16px;
  height: 16px;
  line-height: 1;
  font-size: 16px;
  color: rgba(0, 0, 0, 0.25);
}

.icon-wrapper .anticon:first-child {
  left: 0;
}

.icon-wrapper .anticon:last-child {
  right: 0;
}
</style>
