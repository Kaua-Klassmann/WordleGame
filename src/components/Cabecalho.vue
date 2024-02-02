<template>
    <nav class="d-flex">
        <img src="@/assets/Logo.svg" alt="Logo" class="logo ms-5">
    </nav>
</template>

<script>
export default {
    name: "Cabecalho"
}
</script>

<style scoped>
nav {
    height: 8vh;
    border-bottom: 1px solid #585858;
    align-items: center;
}
.logo{
    height: 45px;
    width: 45px;
}
</style>