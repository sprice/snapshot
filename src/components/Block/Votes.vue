<script setup>
import { ref, computed, watch, toRefs } from 'vue';
import { getChoiceString } from '@/helpers/utils';
import { useProfiles } from '@/composables/useProfiles';
import { useWeb3 } from '@/composables/useWeb3';

const props = defineProps({
  space: Object,
  proposal: Object,
  votes: Object,
  loaded: Boolean,
  strategies: Object
});

const format = getChoiceString;

const { votes } = toRefs(props);
const { web3 } = useWeb3();

const showAllVotes = ref(false);
const authorIpfsHash = ref('');
const modalReceiptOpen = ref(false);

const web3Account = computed(() => web3.value.account);

const visibleVotes = computed(() =>
  showAllVotes.value ? sortVotesUserFirst() : sortVotesUserFirst().slice(0, 10)
);
const titles = computed(() =>
  props.strategies.map(strategy => strategy.params.symbol)
);

function isZero() {
  if (!props.loaded) return true;
  if (props.votes.length > 0) return true;
}

function openReceiptModal(vote) {
  authorIpfsHash.value = vote.id;
  // this.relayerIpfsHash = vote.relayerIpfsHash;
  modalReceiptOpen.value = true;
}

function sortVotesUserFirst() {
  const votes = props.votes;
  if (votes.map(vote => vote.voter).includes(web3Account.value)) {
    votes.unshift(
      votes.splice(
        votes.findIndex(item => item.voter === web3Account.value),
        1
      )[0]
    );
    return votes;
  }
  return votes;
}

const { profiles, addressArray } = useProfiles();

watch(votes, () => {
  addressArray.value = votes.value.map(vote => vote.voter);
});
</script>

<template>
  <Block
    v-if="isZero()"
    :title="$t('votes')"
    :counter="votes.length"
    :slim="true"
    :loading="!loaded"
  >
    <div
      v-for="(vote, i) in visibleVotes"
      :key="i"
      :style="i === 0 && 'border: 0 !important;'"
      class="px-4 py-3 border-t flex"
    >
      <User
        :profile="profiles[vote.voter]"
        :address="vote.voter"
        :space="space"
        class="column"
      />
      <div class="flex-auto text-center link-color">
        <UiTooltip
          class="inline-block"
          :text="format(proposal, vote.choice)"
          :show="format(proposal, vote.choice).length > 24"
        >
          <span class="text-center link-color">
            {{ _shorten(format(proposal, vote.choice), 24) }}
          </span>
        </UiTooltip>
      </div>

      <div class="column text-right link-color">
        <UiTooltip
          class="inline-block"
          :text="
            vote.scores
              .map((score, index) => `${_n(score)} ${titles[index]}`)
              .join(' + ')
          "
        >
          {{ `${_n(vote.balance)} ${_shorten(space.symbol, 'symbol')}` }}
        </UiTooltip>
        <a
          @click="openReceiptModal(vote)"
          target="_blank"
          class="ml-2 text-color"
          title="Receipt"
        >
          <Icon name="signature" />
        </a>
      </div>
    </div>
    <a
      v-if="!showAllVotes && votes.length > 10"
      @click="showAllVotes = true"
      class="
        px-4
        py-3
        border-t
        text-center
        block
        header-bg
        rounded-b-none
        md:rounded-b-md
      "
    >
      {{ $t('seeMore') }}
    </a>
    <teleport to="#modal">
      <ModalReceipt
        :open="modalReceiptOpen"
        @close="modalReceiptOpen = false"
        :authorIpfsHash="authorIpfsHash"
      />
    </teleport>
  </Block>
</template>
