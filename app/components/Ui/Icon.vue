<template>
  <Icon v-bind="props" />
</template>

<script lang="ts" setup>
import { defineProps } from 'vue';
import {Icon,type IconifyIconProps } from '@iconify/vue';

const props = defineProps<IconifyIconProps>();
</script>

<style></style>
