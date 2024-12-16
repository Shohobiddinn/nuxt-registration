<template>
  <div class="container flex flex-col items-center justify-center h-screen">
    <div class="relative h-10 w-full min-w-[200px] max-w-[400px]">
      <form @submit.prevent="Submit">
        <input id="inputField" ref="inputField" v-model="inputValue" placeholder="" @input="onInputChange"
          class="peer h-full w-full rounded-[7px] border border-blue-gray-200 bg-transparent px-3 py-2.5 font-sans text-sm font-normal text-blue-gray-700 outline outline-0 transition-all placeholder-shown:border placeholder-shown:border-blue-gray-200 placeholder-shown:border-t-blue-gray-200 focus:border-2 focus:border-pink-500 focus:border-t-transparent focus:outline-0 disabled:border-0 disabled:bg-blue-gray-50" />
        <label for="inputField"
          class="before:content[' '] after:content[' '] pointer-events-none absolute left-0 -top-1.5 flex h-full w-full select-none text-[11px] font-normal leading-tight text-blue-gray-400 transition-all before:pointer-events-none before:mt-[6.5px] before:mr-1 before:box-border before:block before:h-1.5 before:w-2.5 before:rounded-tl-md before:border-t before:border-l before:border-blue-gray-200 before:transition-all after:pointer-events-none after:mt-[6.5px] after:ml-1 after:box-border after:block after:h-1.5 after:w-2.5 after:flex-grow after:rounded-tr-md after:border-t after:border-r after:border-blue-gray-200 after:transition-all peer-placeholder-shown:text-sm peer-placeholder-shown:leading-[3.75] peer-placeholder-shown:text-blue-gray-500 peer-placeholder-shown:before:border-transparent peer-placeholder-shown:after:border-transparent peer-focus:text-[11px] peer-focus:leading-tight peer-focus:text-pink-500 peer-focus:before:border-t-2 peer-focus:before:border-l-2 peer-focus:before:border-pink-500 peer-focus:after:border-t-2 peer-focus:after:border-r-2 peer-focus:after:border-pink-500 peer-disabled:text-transparent peer-disabled:before:border-transparent peer-disabled:after:border-transparent peer-disabled:peer-placeholder-shown:text-blue-gray-500">
          Kontakt ma'lumotlari:
        </label>
        <p v-if="errorMessage" style="color: red;">{{ errorMessage }}</p>
        <button type="submit"
          class="middle none mt-2 center mr-4 rounded-lg bg-blue-500 py-3 px-6 font-sans text-xs font-bold uppercase text-white shadow-md shadow-blue-500/20 transition-all hover:shadow-lg hover:shadow-blue-500/40 focus:opacity-[0.85] focus:shadow-none active:opacity-[0.85] active:shadow-none disabled:pointer-events-none disabled:opacity-50 disabled:shadow-none"
          data-ripple-light="true">
          Button
        </button>
      </form>
    </div>
  </div>
</template>

<script setup lang="ts">
import Cleave from "cleave.js";
import "cleave.js/dist/addons/cleave-phone.i18n"; // Davlat formatlarini yuklash
import { parsePhoneNumberFromString, isValidPhoneNumber } from "libphonenumber-js";
import { ref, onMounted, onBeforeUnmount, watch } from "vue";

const inputValue = ref("");
const errorMessage = ref("");
const cleaveInstance = ref<Cleave | null>(null);
const isPhone = ref(false);

const initInputMask = () => {
  if (cleaveInstance.value) {
    cleaveInstance.value.destroy();
    cleaveInstance.value = null;
  }
  if (isPhone.value) {
    cleaveInstance.value = new Cleave(inputField.value as HTMLInputElement, {
      phone: true,
      phoneRegionCode: "auto", // Foydalanuvchi davlat kodi aniqlanadi
    });
  }
};

const validateInput = () => {
  if (isPhone.value) {
    const phoneNumber = parsePhoneNumberFromString(inputValue.value);
    if (phoneNumber && isValidPhoneNumber(inputValue.value)) {
      errorMessage.value = "";
    } else {
      errorMessage.value = "Telefon raqam noto'g'ri.";
    }
  } else {
    const emailPattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/; // Email uchun to'g'ri regex
    if (emailPattern.test(inputValue.value)) {
      errorMessage.value = "";
    } else {
      errorMessage.value = "Email noto'g'ri.";
    }
  }
};


const onInputChange = () => {
  const isCurrentlyPhone = inputValue.value.startsWith("+");
  if (isCurrentlyPhone !== isPhone.value) {
    isPhone.value = isCurrentlyPhone;
    initInputMask();
  }
  validateInput();
};

const inputField = ref<HTMLInputElement | null>(null);

onMounted(() => {
  initInputMask();
});

onBeforeUnmount(() => {
  if (cleaveInstance.value) {
    cleaveInstance.value.destroy();
  }
});

watch(inputValue, onInputChange);
const Submit = () => {
  alert(inputValue.value)
}

</script>
