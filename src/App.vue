<script setup lang="ts">
import { onMounted, ref } from "vue";

declare global {
  interface Window {
    YT: any;
    onYouTubeIframeAPIReady: () => void;
  }
}

const LANGUAGES = {
  spanish: { label: "Español", videoId: "Io5mt83nCcU" },
  english: { label: "English", videoId: "KuHOkF51X8Q" },
  portuguese: { label: "Português", videoId: "9XT4CU-Ar2M" },
  french: { label: "Français", videoId: "iEpJwprxDdk" },
} as const;

type Lang = keyof typeof LANGUAGES;

const currentLang = ref<Lang | undefined>();
const isReady = ref(false);
const player = ref<any>();

function changeLanguage(lang: Lang) {
  isReady.value = false;

  const { videoId } = LANGUAGES[lang];

  player.value.loadVideoById(videoId);
  currentLang.value = lang;

  isReady.value = true;
}

onMounted(() => {
  const tag = document.createElement("script");
  tag.src = "https://www.youtube.com/iframe_api";
  const firstScriptTag = document.getElementsByTagName("script")[0];
  firstScriptTag.parentNode?.insertBefore(tag, firstScriptTag);

  window.onYouTubeIframeAPIReady = () => {
    player.value = new window.YT.Player("youtube-player", {
      height: "100%",
      width: "100%",
      events: {
        onReady: () => {
          changeLanguage("spanish");
        },
      },
      playerVars: {
        autoplay: 1,
        controls: 1,
        color: "white",
        enablejsapi: 1,
        fs: 1,
        modestbranding: 1,
        rel: 0,
        showinfo: 0,
      },
    });
  };
});
</script>

<template>
  <div>
    <div
      class="bg-gray-50 aspect-video max-w-[800px] h-full w-full m-auto rounded-lg relative shadow-xl"
    >
      <div
        v-show="!isReady"
        class="bg-gray-100 flex items-center justify-center w-full h-full rounded-lg absolute"
      >
        <div
          class="animate-spin rounded-full h-12 w-12 border-4 border-blue-500 border-t-transparent"
        ></div>
      </div>

      <div id="youtube-player" class="rounded-lg"></div>
    </div>

    <div class="flex flex-wrap gap-2 justify-center mt-4">
      <button
        v-for="(obj, lang) of LANGUAGES"
        class="px-6 py-2 rounded-full font-medium transition-colors"
        :class="[
          currentLang === lang
            ? 'bg-blue-600 text-white'
            : 'bg-gray-100 text-gray-700 hover:bg-gray-200',
        ]"
        @click="changeLanguage(lang)"
      >
        {{ obj.label }}
      </button>
    </div>
  </div>
</template>
