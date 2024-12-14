<script setup>
const username = ref('')

</script>
<template>
    <pre>{{ username }}</pre>
    <EmailInput v-model="username" />
</template>