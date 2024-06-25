<!-- eslint-disable vue/multi-word-component-names -->
<script setup>
  import { ref, onMounted } from 'vue'
  import kodams from '../../kodam.json'
  import Loader from '../components/Loader.vue'

  const randomizedName = ref('Kodam')
  const nameInput = ref('')
  const kodamName = ref('')
  const kodamDesc = ref('')
  const localStorageName = 'kodams'
  const isLoading = ref(false)

  const nameArr = ['Johny', 'Rohid', 'Roni', 'Kusno', 'Romi', 'Alexius']

  const randomNumber = (length) => {
    return Math.floor(Math.random() * length)
  }

  const random = () => {
    return nameArr[randomNumber(nameArr.length)]
  }

  onMounted(() => {
    randomizedName.value = random()
    const readLocal = JSON.parse(localStorage.getItem(localStorageName))
    if (!readLocal) {
      localStorage.setItem(localStorageName, JSON.stringify([]))
    }
  })

  const checkLocalStorage = (name) => {
    const readLocal = JSON.parse(localStorage.getItem(localStorageName)) || []
    const inputSanitized = name.replace(/ /g, '')
    if (readLocal.length) {
      const existedData = readLocal.filter((obj) => {
        return obj.name === inputSanitized
      })
      return existedData
    } else {
      return false
    }
  }

  const findKodamById = (id) => {
    const existedKodam = kodams.filter((kodam) => {
      return kodam.id === id
    })

    return existedKodam[0]
  }

  const setLocalStorage = (id) => {
    const readLocal = JSON.parse(localStorage.getItem(localStorageName))

    readLocal.push({
      name: nameInput.value.replace(/ /g, ''),
      kodamId: id
    })

    localStorage.setItem(localStorageName, JSON.stringify(readLocal))
  }

  const submitPromise = () => {
    return new Promise((resolve) => {
      setTimeout(() => {
        const existedData = checkLocalStorage(nameInput.value)

        if (existedData.length) {
          kodamName.value = findKodamById(existedData[0].kodamId).name
          kodamDesc.value = findKodamById(existedData[0].kodamId).description
          resolve()
          return
        }

        const kodam = kodams[randomNumber(kodams.length)]
        kodamName.value = kodam.name
        kodamDesc.value = kodam.description
        setLocalStorage(kodam.id)
        resolve()
      }, 2000)
    })
  }

  const submit = async () => {
    kodamName.value = ''
    kodamDesc.value = ''
    isLoading.value = true
    await submitPromise()
    isLoading.value = false
  }
</script>

<template>
  <div class="w-full bg-gray-700 text-center p-8 rounded-lg shadow">
    <h2 class="text-lg font-semibold mb-3 text-white">Masukan Nama Kamu</h2>
    <form class="flex gap-2 justify-center" @submit.prevent="submit">
      <input
        type="text"
        :placeholder="randomizedName"
        class="bg-gray-800 p-3 rounded border border-transparent focus:outline-none focus:border-red-400 text-gray-50"
        v-model="nameInput"
        required />
      <button type="submit" class="bg-rose-700 text-white p-3 focus:outline-none border-none rounded">Lihat</button>
    </form>
  </div>
  <div v-if="isLoading" class="w-full bg-gray-700 text-center p-8 rounded-lg shadow mt-4 flex justify-center">
    <Loader></Loader>
  </div>
  <div v-if="kodamName" class="w-full bg-gray-700 text-center p-8 box-border rounded-lg shadow mt-4">
    <div>
      <h2 class="p-4 text-xl text-rose-600 font-bold">{{ kodamName }}</h2>
      <p class="text-white">{{ kodamDesc }}</p>
    </div>
  </div>
</template>
