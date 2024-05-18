<template>
  <div>
    <NuxtLayout>
      <NuxtPage />
    </NuxtLayout>
  </div>
</template>

<script setup lang="ts">
useSeoMeta({
  title: "League Champion Randomizer - Discover Your Next Champion",
  description:
    "Explore and discover new League of Legends champions with our randomizer tool. Find detailed information on champion stats, abilities, and lore.",
  ogTitle: "League Champion Randomizer - Discover Your Next Champion",
  ogDescription:
    "Explore and discover new League of Legends champions with our randomizer tool. Find detailed information on champion stats, abilities, and lore.",
  twitterTitle: "League Champion Randomizer - Discover Your Next Champion",
  twitterDescription:
    "Explore and discover new League of Legends champions with our randomizer tool. Find detailed information on champion stats, abilities, and lore.",
  twitterCard: "summary",
});

useHead({
  htmlAttrs: {
    lang: "en",
  },
  link: [
    {
      rel: "icon",
      type: "image/png",
      href: "/favicon.png",
    },
  ],
});
</script>
