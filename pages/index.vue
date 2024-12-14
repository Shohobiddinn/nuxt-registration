
<template>
  <div>
    <label for="inputField">Kontakt ma'lumotlari:</label>
    <input id="inputField" ref="inputField" v-model="inputValue" class="w-full input-md px-0"
      placeholder="Telefon raqam yoki email kiriting" @input="onInputChange" />
    <p v-if="errorMessage" style="color: red;">{{ errorMessage }}</p>
  </div>
</template>

<script>
import Cleave from "cleave.js";
import "cleave.js/dist/addons/cleave-phone.i18n"; // Davlat formatlarini yuklash
import { parsePhoneNumberFromString, isValidPhoneNumber } from "libphonenumber-js";

export default {
  data() {
    return {
      inputValue: "",
      errorMessage: "",
      cleaveInstance: null,
      isPhone: false,
    };
  },
  mounted() {
    this.initInputMask();
  },
  beforeDestroy() {
    if (this.cleaveInstance) {
      this.cleaveInstance.destroy();
    }
  },
  methods: {
    initInputMask() {
      if (this.cleaveInstance) {
        this.cleaveInstance.destroy();
        this.cleaveInstance = null;
      }
      if (this.isPhone) {
        this.cleaveInstance = new Cleave(this.$refs.inputField, {
          phone: true,
          phoneRegionCode: "auto", // Foydalanuvchi davlat kodi aniqlanadi
        });
      }
    },
    onInputChange() {
      const isCurrentlyPhone = this.inputValue.startsWith("+");
      if (isCurrentlyPhone !== this.isPhone) {
        this.isPhone = isCurrentlyPhone;
        this.initInputMask();
      }
      this.validateInput();
    },
    validateInput() {
      if (this.isPhone) {
        const phoneNumber = parsePhoneNumberFromString(this.inputValue);
        if (phoneNumber && isValidPhoneNumber(this.inputValue)) {
          this.errorMessage = "";
        } else {
          this.errorMessage = "Telefon raqam noto'g'ri.";
        }
      } else {
        const emailPattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/; // Email uchun regex
        if (emailPattern.test(this.inputValue)) {
          this.errorMessage = "";
        } else {
          this.errorMessage = "Email noto'g'ri.";
        }
      }
    },
  },
};
</script>
