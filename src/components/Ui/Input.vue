<script setup>
const props = defineProps({
  modelValue: [String, Number],
  placeholder: String,
  error: [String, Boolean],
  number: Boolean,
  disabled: Boolean,
  required: {
    type: Boolean,
    default: true
  }
});

const emit = defineEmits(['update:modelValue']);

function handleInput(e) {
  const input = e.target.value;
  if (props.number) {
    return emit('update:modelValue', !input ? undefined : parseFloat(input));
  }
  emit('update:modelValue', input);
}
</script>

<template>
  <UiButton
    class="text-left w-full mb-2 flex px-3"
    :class="{ 'border-red': error }"
  >
    <div class="text-color mr-2">
      <slot name="label" />
    </div>
    <div v-if="$slots.selected" class="flex-auto"><slot name="selected" /></div>
    <input
      v-else
      :value="modelValue"
      @input="handleInput"
      :placeholder="placeholder"
      :type="number ? 'number' : 'text'"
      :disabled="disabled"
      class="input flex-auto"
      :required="required"
    />
    <slot name="info" />
    <UiTooltip class="inline-block" :text="error">
      <span v-if="error" class="float-right link-color">
        <Icon name="warning" class="!text-red p-1 block pt-2 mt-1 -mr-1" />
      </span>
    </UiTooltip>
  </UiButton>
</template>
