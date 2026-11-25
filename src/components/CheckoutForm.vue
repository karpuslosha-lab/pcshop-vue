<script setup>
import { reactive, ref, computed } from 'vue'
import { formatPrice } from '../utils.js'

// Форма оформлення замовлення з перевіркою полів.
// total — сума до сплати (props), submit — подія з даними клієнта (emit).
defineProps({
  total: { type: Number, required: true },
})

const emit = defineEmits(['submit'])

const form = reactive({
  name: '',
  phone: '',
  delivery: 'np',
  address: '',
})

const touched = reactive({ name: false, phone: false, address: false })
const submitted = ref(false)

const addressLabel = computed(() =>
  form.delivery === 'np' ? 'Місто та відділення Нової пошти' : 'Адреса доставки',
)

// Усі помилки обчислюються автоматично при зміні будь-якого поля.
const errors = computed(() => {
  const result = {}

  const name = form.name.trim()
  if (name.length < 2) {
    result.name = 'Вкажіть ім’я (мінімум 2 літери)'
  } else if (!/^[\p{L}\s'’-]+$/u.test(name)) {
    result.name = 'Ім’я може містити лише літери'
  }

  const digits = form.phone.replace(/\D/g, '')
  if (!/^(380\d{9}|0\d{9})$/.test(digits)) {
    result.phone = 'Введіть номер у форматі +380 50 123 45 67'
  }

  if (form.delivery !== 'pickup' && form.address.trim().length < 5) {
    result.address = 'Вкажіть адресу (мінімум 5 символів)'
  }

  return result
})

function showError(field) {
  return Boolean(errors.value[field]) && (touched[field] || submitted.value)
}

function onSubmit() {
  submitted.value = true
  if (Object.keys(errors.value).length > 0) return

  emit('submit', {
    name: form.name.trim(),
    phone: form.phone.trim(),
    delivery: form.delivery,
    address: form.delivery === 'pickup' ? '' : form.address.trim(),
  })
}
</script>

<template>
  <form class="form" novalidate @submit.prevent="onSubmit">
    <p class="form__total">
      До сплати: <strong>{{ formatPrice(total) }}</strong>
    </p>

    <div class="form__row">
      <label class="field" :class="{ 'field--error': showError('name') }">
        <span class="field__label">Ваше ім’я</span>
        <input
          v-model="form.name"
          type="text"
          autocomplete="name"
          placeholder="Іван Петренко"
          :aria-invalid="showError('name')"
          @blur="touched.name = true"
        />
        <span v-if="showError('name')" class="field__error">{{ errors.name }}</span>
      </label>

      <label class="field" :class="{ 'field--error': showError('phone') }">
        <span class="field__label">Номер телефону</span>
        <input
          v-model="form.phone"
          type="tel"
          autocomplete="tel"
          placeholder="+380 50 123 45 67"
          :aria-invalid="showError('phone')"
          @blur="touched.phone = true"
        />
        <span v-if="showError('phone')" class="field__error">{{ errors.phone }}</span>
      </label>
    </div>

    <label class="field">
      <span class="field__label">Спосіб отримання</span>
      <select v-model="form.delivery">
        <option value="np">Нова пошта (відділення)</option>
        <option value="courier">Кур’єр за адресою</option>
        <option value="pickup">Самовивіз</option>
      </select>
    </label>

    <label
      v-if="form.delivery !== 'pickup'"
      class="field"
      :class="{ 'field--error': showError('address') }"
    >
      <span class="field__label">{{ addressLabel }}</span>
      <input
        v-model="form.address"
        type="text"
        autocomplete="street-address"
        :aria-invalid="showError('address')"
        @blur="touched.address = true"
      />
      <span v-if="showError('address')" class="field__error">{{ errors.address }}</span>
    </label>

    <button class="btn btn--primary btn--wide" type="submit">Підтвердити замовлення →</button>
  </form>
</template>
