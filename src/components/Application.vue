<template>
    <n-loading-bar-provider>
      <n-dialog-provider>
        <n-notification-provider :placement="'top'">
          <n-message-provider>
            <slot></slot>
          </n-message-provider>
        </n-notification-provider>
      </n-dialog-provider>
    </n-loading-bar-provider>
  </template>
  
  <script setup lang="ts">
  </script>