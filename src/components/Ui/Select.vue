<script setup>
defineProps({ modelValue: [String, Number], disabled: Boolean });

const emit = defineEmits(['update:modelValue', 'change']);

function handleChange(event) {
  emit('update:modelValue', event.target.value);
  emit('change', event.target.value);
}
</script>

<template>
  <UiButton class="w-full mb-2 px-3 flex items-center overflow-hidden">
    <div class="text-color mr-2 no-shrink">
      <slot name="label" />
    </div>
    <div v-if="$slots.image" class="text-color mr-2 no-shrink">
      <slot name="image" />
    </div>
    <select
      :disabled="disabled"
      :value="modelValue"
      @change="handleChange($event)"
      v-bind:class="{ disabled }"
      class="input flex-auto height-full w-full"
    >
      <slot />
    </select>
  </UiButton>
</template>

<style scoped lang="scss">
.no-shrink {
  flex-shrink: 0;
}
.disabled {
  appearance: none;
}
</style>
