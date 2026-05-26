<script setup>

    defineProps({

        todo: {
            type: Object,
        }
    })

</script>

<template>
    <li>{{ todo.text }}</li>
</template>