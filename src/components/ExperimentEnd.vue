<script setup lang="ts">
import { settings } from '@/settings';
import { computed } from 'vue';
import { useSerialPositionStore } from '@/store/store';
import { useClipboard } from '@vueuse/core';

const useStore = useSerialPositionStore();
const { copy } = useClipboard({ legacy: true });

function getExperimentIdentifier() {
  const groupOrder = useStore.getGroupOrder;
  const sessionOrder = useStore.getSessionOrder;

  // 穿插groupOrder 与 sessionOrder
  const identifier = [];
  for (let i = 0; i < groupOrder.length; i++) {
    identifier.push(`${sessionOrder[i]}${groupOrder[i]}`);
  }
  return identifier.join('-');
}

const experimentIdentifier = computed(() => getExperimentIdentifier());

function handleNext() {
  copy(experimentIdentifier.value)
    .catch(e => {
      alert(
        'コピーに失敗しました。番号をメモして，フォームに入力してください。'
      );
      console.error(e);
    })
    .then(() => {
      window.open(settings.formUrl, '_blank');
    });
}
</script>

<template>
  <div class="flex columns-2 items-center">
    <div class="flex flex-col gap-8 px-8 text-2xl">
      <h1 class="font-bold text-4xl">実験課題はこれで終わります</h1>
      <p>あなたの 12 桁の確認番号は：{{ getExperimentIdentifier() }}</p>
      <p>「コピー」ボタンを押して，クリップボードにコピーしてください</p>
      <p>この後，課題中に思い出した刺激を入力してもらいます</p>
      <p>下のボタンをを押して入力フォームを提出してください</p>
      <button
        class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded max-w-2xl"
        @click="handleNext"
      >
        確認番号をコピーして入力フォームに移動する
      </button>
      <p>
        ボタンで入力フォームに移動できない場合には，<br />
        リンクをクリックし，またはQRコードをスキャンして<br />
        フォームに入力してください：<br />
        <a
          class="underline text-yellow-500"
          :href="settings.formUrl"
          target="_blank"
          >{{ settings.formUrl }}</a
        >
      </p>
    </div>
    <img class="qr-code-image" :src="settings.qrCodeImgUrl" alt="QRコード" />
  </div>
</template>

<style scoped lang="scss">
img.qr-code-image {
  width: 360px;
  max-width: 48vw;
  height: 100%;
  aspect-ratio: 1 / 1;
}
</style>
