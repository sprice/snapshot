<script>
export default {
  props: { open: Boolean, authorIpfsHash: String, relayerIpfsHash: Object },
  emits: ['close']
};
</script>

<template>
  <UiModal :open="open" @close="$emit('close')">
    <template v-slot:header>
      <h3>{{ $t('receipt') }}</h3>
    </template>
    <div class="m-4 mb-0 p-4 border rounded-md link-color">
      <div class="flex">
        <span v-text="$t('author')" class="flex-auto text-color mr-1" />
        <a :href="_getUrl(authorIpfsHash)" target="_blank" class="link-color">
          #{{ authorIpfsHash.slice(0, 7) }}
          <Icon name="external-link" class="ml-1" />
        </a>
      </div>
      <div v-if="relayerIpfsHash" class="flex">
        <span v-text="$t('relayer')" class="flex-auto text-color mr-1" />
        <a :href="_getUrl(relayerIpfsHash)" target="_blank" class="link-color">
          #{{ relayerIpfsHash.slice(0, 7) }}
          <Icon name="external-link" class="ml-1" />
        </a>
      </div>
    </div>
    <div class="m-4">
      <a
        href="https://app.mycrypto.com/verify-message"
        target="_blank"
        class="mb-2 block"
      >
        <UiButton class="button-outline w-full">
          {{ $t('verifyOnMycrypto') }}
          <Icon name="external-link" class="ml-1" />
        </UiButton>
      </a>
    </div>
  </UiModal>
</template>
