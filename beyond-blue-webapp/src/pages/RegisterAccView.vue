<script setup>
import { currentUserStore } from '@/store';
import { ref } from 'vue'
import { useRouter } from 'vue-router';
import {getAuth, createUserWithEmailAndPassword} from "firebase/auth"


const data = ref({
  password: '',
  email: '',
  confirmPassword: '',
  isAdmin: false,
  adminCode: ''
})

const errors = ref({
  password: null,
  email: null,
  confirmPassword: null,
  adminCode: null
})
const router = useRouter()

const submitForm = () => {
  validatePassword(true)
  validateConfirmPassword(true)
  register()
  if (!errors.value.password && !errors.value.confirmPassword) {
    clearForm()
  }
  router.push('/')
}

const clearForm = () => {
  data.value = {
    password: '',
    email: '',
    confirmPassword: ''
  }
}

const validatePassword = (blur) => {
  const password = data.value.password
  const minLength = 8
  const hasUppercase = /[A-Z]/.test(password)
  const hasLowercase = /[a-z]/.test(password)
  const hasNumber = /\d/.test(password)
  const hasSpecialChar = /[!@#$%^&*(),.?":{}|<>]/.test(password)

  if (password.length < minLength) {
    if (blur) errors.value.password = `Password must be at least ${minLength} characters long.`
  } else if (!hasUppercase) {
    if (blur) errors.value.password = 'Password must contain at least one uppercase letter.'
  } else if (!hasLowercase) {
    if (blur) errors.value.password = 'Password must contain at least one lowercase letter.'
  } else if (!hasNumber) {
    if (blur) errors.value.password = 'Password must contain at least one number.'
  } else if (!hasSpecialChar) {
    if (blur) errors.value.password = 'Password must contain at least one special character.'
  } else {
    errors.value.password = null
  }
}

const validateConfirmPassword = (blur) => {
  if (data.value.confirmPassword != data.value.password) {
    if (blur) errors.value.confirmPassword = `Password must be the same`
  } else {
    errors.value.confirmPassword = null
  }
}

const validateEmail = (blur) => {
  if (data.value.email.length < 6) {
    if (blur) errors.value.email = 'Email is incorrect format'
  } else {
    errors.value.email = null
  }
}

const validateAdminCode = (blur) => {
  if (data.value.adminCode.length < 6) {
    if (blur) errors.value.adminCode = 'Code is wrong format'
  } else {
    errors.value.adminCode = null
  }
}

const auth = getAuth()
const register = () => {
  createUserWithEmailAndPassword(auth, data.value.email, data.value.password)
    .then(() => {
      window.alert("Registration success")
      router.push("/login")
    }).catch(() => {
      window.alert("Registration failed")
    })
}
</script>

<template>
    <div>
      <form @submit.prevent="submitForm" class="w-100 mx-auto form-container mt-5 p-5 border">
        <div class="mb-3">
          <label for="emailInput" class="form-label">Email address</label>
          <input type="email" class="form-control" id="emailInput" @blur="() => validateEmail(true)" @input="() => validateEmail(false)" v-model="data.email" required>
          <div v-if="errors.email" class="text-danger">{{ errors.email }}</div>
        </div>
        <div class="mb-3">
          <label for="passwordInput" class="form-label">Password</label>
          <input type="password" class="form-control" id="passwordInput" @blur="() => validatePassword(true)" @input="() => validatePassword(false)" v-model="data.password" required>
          <div v-if="errors.password" class="text-danger">{{ errors.password }}</div>
        </div>
        <div class="mb-3">
          <label for="confirmPasswordInput" class="form-label">Confirm password</label>
          <input type="password" class="form-control" id="confirmPasswordInput" @blur="() => validateConfirmPassword(true)" @input="() => validateConfirmPassword(false)" v-model="data.confirmPassword" required>
          <div v-if="errors.confirmPassword" class="text-danger">{{ errors.confirmPassword }}</div>
        </div>
        <div class="mb-3">
          <div class="form-check">
            <input class="form-check-input" type="checkbox" id="isAdminCheckbox" v-model="data.isAdmin">
            <label class="form-check-label" for="isAdminCheckbox">Is Admin</label>
          </div>
        </div>
        <div v-if="data.isAdmin" class="mb-3">
          <label for="adminCodeInput" class="form-label">Admin Code</label>
          <input type="password" class="form-control" id="adminCodeInput" @blur="() => validateAdminCode(true)" @input="() => validateAdminCode(false)" v-model="data.adminCode" required>
          <div v-if="errors.adminCode" class="text-danger">{{ errors.adminCode }}</div>
        </div>
        <div class="d-grid">
          <button type="submit" class="btn btn-primary" :disabled="accountError">Create account</button>
        </div>
      </form>
    </div>
  </template>

<style>
    .form-container {
        max-width: 60%;
    }
</style>