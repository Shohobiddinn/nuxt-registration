<template>
    <div>
      <label for="inputField">Kontakt ma'lumotlari component:</label>
      <input
        id="inputField"
        ref="inputField"
        v-model="localValue"
        class="w-full input-md px-0"
        placeholder="Telefon raqam yoki email kiriting"
        @input="onInputChange"
      />
      <p v-if="errorMessage" style="color: red;">{{ errorMessage }}</p>
    </div>
  </template>
  
  <script setup lang="ts">
  import { ref, watch, onMounted, onBeforeUnmount } from "vue";
  import Cleave from "cleave.js";
  import "cleave.js/dist/addons/cleave-phone.i18n";
  import { parsePhoneNumberFromString, isValidPhoneNumber } from "libphonenumber-js";
  
  interface Props {
    modelValue: string;
  }
  
  const props = defineProps<Props>();
  const emit = defineEmits(["update:modelValue"]);
  
  const inputField = ref<HTMLInputElement | null>(null);
  const localValue = ref(props.modelValue);
  const errorMessage = ref<string>("");
  let cleaveInstance: Cleave | null = null;
  const isPhone = ref(false);
  
  const initInputMask = () => {
    if (cleaveInstance) {
      cleaveInstance.destroy();
      cleaveInstance = null;
    }
    if (isPhone.value && inputField.value) {
      cleaveInstance = new Cleave(inputField.value, {
        phone: true,
        phoneRegionCode: "auto",
      });
    }
  };
  
  const validateInput = () => {
    if (isPhone.value) {
      const phoneNumber = parsePhoneNumberFromString(localValue.value || "");
      if (phoneNumber && isValidPhoneNumber(localValue.value || "")) {
        errorMessage.value = "";
      } else {
        errorMessage.value = "Telefon raqam noto'g'ri.";
      }
    } else {
      const emailPattern = /^[^\\s@]+@[^\\s@]+\\.[^\\s@]+$/;
      if (emailPattern.test(localValue.value || "")) {
        errorMessage.value = "";
      } else {
        errorMessage.value = "Email noto'g'ri.";
      }
    }
  };
  
  const onInputChange = () => {
    const isCurrentlyPhone = localValue.value.startsWith("+");
    if (isCurrentlyPhone !== isPhone.value) {
      isPhone.value = isCurrentlyPhone;
      initInputMask();
    }
    validateInput();
    emit("update:modelValue", localValue.value);
  };
  
  watch(
    () => props.modelValue,
    (newValue) => {
      localValue.value = newValue;
    }
  );
  
  onMounted(() => {
    initInputMask();
  });
  
  onBeforeUnmount(() => {
    if (cleaveInstance) {
      cleaveInstance.destroy();
    }
  });
  </script>
  
  <style scoped>
  /* Add your styles here if necessary */
  </style>
  