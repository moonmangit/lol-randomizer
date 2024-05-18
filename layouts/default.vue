<template>
  <div
    class="bg-gradient-to-t from-slate-900 bg-slate-800 text-slate-300 w-screen h-screen overflow-x-hidden"
  >
    <nav class="container mx-auto h-[80px]">
      <div class="flex justify-between items-center p-4 h-full">
        <NuxtLink to="/">
          <h1 class="font-bold text-xl">Randomizer</h1>
        </NuxtLink>
        <ul class="flex gap-x-2">
          <li>
            <NuxtLink to="/about">About</NuxtLink>
          </li>
        </ul>
      </div>
    </nav>
    <main class="container mx-auto px-4">
      <div class="min-h-[calc(100dvh-80px-2rem)] relative">
        <slot />
      </div>
      <footer class="h-[2rem] text-center text-slate-600">
        All rights reserved to LeeSongSin coperation
      </footer>
    </main>
  </div>
</template>

<script lang="ts" setup></script>

<style></style>
