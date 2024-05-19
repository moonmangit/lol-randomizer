<template>
  <div class="space-y-10">
    <!-- randomer -->
    <div v-if="!pending">
      <div class="h-[calc(100dvh-400px)] flex items-center justify-center">
        <Transition name="roll">
          <div v-if="rollPreview" class="">
            <div class="space-y-10">
              <h1 class="text-center text-3xl">{{ rollPreview.name }}</h1>
              <img
                :src="rollPreview.image"
                class="size-52 rounded-xl duration-700 z-[100] relative"
                :class="{
                  'blur-lg': rollPending,
                }"
              />
            </div>
            <div
              class="fixed inset-0 mix-blend-screen pointer-events-none opacity-30"
            >
              <img
                :src="rollPreview.image"
                class="w-full h-full object-cover blur-3xl"
                alt=""
              />
            </div>
          </div>
        </Transition>
      </div>
      <!-- winner -->
      <Transition name="winner">
        <div v-if="rollWinnerDisplay" class="fixed inset-0 z-[999] bg-white">
          <img
            :src="rollPreview?.fullSplash"
            class="w-full h-full object-cover"
            alt=""
          />
          <div
            class="fixed inset-x-0 bottom-0 p-10 container mx-auto text-center z-[10000] space-y-4"
          >
            <h1 class="text-3xl lg:text-9xl text-white">
              <AppTypeIn :content="rollPreview?.name || ''" />
            </h1>
            <p
              class="text-lg lg:text-5xl animate__animated animate__fadeInDown animate__delay-1s"
            >
              {{ rollPreview?.title }}
            </p>
          </div>
          <div
            class="fixed inset-0 bg-gradient-to-t from-slate-800 to-slate-100 mix-blend-multiply"
          ></div>
        </div>
      </Transition>
    </div>
    <!-- rolling grid -->
    <Transition name="right-drawer">
      <div
        v-if="!pending && viewGridRolling"
        class="fixed inset-y-0 right-0 bg-slate-950 p-4 !m-0 z-[100] w-screen lg:w-[min(500px,50dvw)] overflow-y-auto overflow-x-hidden duration-300 space-y-4"
      >
        <section class="flex justify-between items-center">
          <button
            @click="closeGridRolling()"
            class="text-2xl btn-ghost rounded-full inline-block lg:hidden"
          >
            <Icon name="mdi:chevron-right"></Icon>
          </button>
          <h1 class="mx-auto">Champion Pools</h1>
        </section>
        <p v-if="pools.length > 0" class="text-center text-indigo-500">
          <span class="bg-indigo-500 text-white px-2 rounded-full"
            >{{ ((1 / pools.length) * 100).toFixed(1) }}%</span
          >
          chance to get a champion
        </p>
        <ul class="grid grid-cols-[repeat(auto-fill,minmax(50px,1fr))]">
          <li
            v-for="champ in allChampions"
            :key="champ.id"
            class="aspect-square duration-150 relative group"
            :class="{
              'opacity-20':
                filterTags &&
                !filterTags.every((tag) => champ.tags.includes(tag)),
              active: rollPreview && rollPreview.id === champ.id,
            }"
          >
            <img
              :src="champ.image"
              class="w-full h-full object-cover group-[.active]:scale-150 origin-center group-[.active]:z-10 relative duration-300"
            />
            <!-- only me here -->
            <Transition name="roll">
              <div
                v-if="pools.length === 1 && pools[0].id === champ.id"
                class="absolute bg-white text-slate-900 p-2 px-3 rounded-xl rounded-br-none z-10 bottom-[calc(100%-1rem)] right-[calc(100%-1rem)] whitespace-nowrap"
              >
                Haha it's only me here.
              </div>
            </Transition>
          </li>
        </ul>
      </div>
    </Transition>
    <!-- action -->
    <Transition name="roll">
      <div v-if="!rollPending" class="flex justify-center">
        <div
          class="space-y-5 flex flex-col items-center justify-center max-w-[80dvw]duration-300"
          :class="{
            'opacity-40 cursor-not-allowed pointer-events-none': rollPending,
          }"
        >
          <p v-if="!rollable" class="text-red-500">
            {{
              pools.length === 0
                ? "No champion to roll"
                : `${pools[0].name}: "Only me in the pool"`
            }}
          </p>
          <ul class="flex gap-2 flex-wrap justify-center px-8">
            <li v-for="tag in allChampionsTags" :key="tag">
              <button
                class="p-3 px-4 rounded-full bg-slate-900 hover:bg-slate-800 duration-300 border border-transparent hover:border-indigo-600"
                @click="toggleFilterTag(tag)"
                :class="{
                  '!bg-indigo-600 text-white -translate-y-2':
                    filterTags && filterTags.includes(tag),
                }"
              >
                {{ tag }}
              </button>
            </li>
          </ul>
          <div class="flex items-center justify-center gap-x-8">
            <button class="btn-ghost rounded-full">
              <Icon name="mdi:cog"></Icon>
            </button>
            <button
              type="button"
              class="size-20 bg-indigo-600 text-2xl rounded-full ring-0 ring-indigo-600/20 hover:ring-8 duration-300 group relative"
              @click.prevent="roll"
              :class="{
                'opacity-40 cursor-not-allowed pointer-events-none': !rollable,
              }"
            >
              <Icon
                name="mdi:dice"
                class="group-hover:scale-150 duration-300 group-hover:-translate-y-2 group-hover:rotate-180"
                :class="{
                  'animate-spin': rollPending,
                }"
              ></Icon>
              <p
                class="absolute bottom-2 text-sm text-center w-full translate-y-1/2 opacity-0 group-hover:opacity-100 group-hover:translate-y-0 duration-300 pointer-events-none"
              >
                Roll
              </p>
            </button>
            <button
              @click.prevent="toggleGridRolling()"
              class="btn-ghost rounded-full"
              :class="{
                'btn-ghost--active': viewGridRolling,
                'rotate-180': viewGridRolling,
              }"
            >
              <Icon v-if="!viewGridRolling" name="mdi:eye"></Icon>
              <Icon v-else name="mdi:eye-off"></Icon>
            </button>
          </div>
        </div>
      </div>
    </Transition>
  </div>
</template>

<script lang="ts" setup>
// https://ddragon.leagueoflegends.com/cdn/img/champion/splash/Aatrox_0.jpg
// https://ddragon.leagueoflegends.com/cdn/14.10.1/img/champion/Aatrox.png
// https://ddragon.leagueoflegends.com/cdn/14.10.1/data/en_US/champion.json

const { data, pending } = useAsyncData(async () => {
  const res = await $fetch(
    "https://ddragon.leagueoflegends.com/cdn/14.10.1/data/en_US/champion.json"
  );
  return res;
});

type Champ = {
  name: string;
  id: string;
  tags: string[];
  title: string;
  image: string;
  fullSplash: string;
};

// Raw
const allChampions = computed<Champ[]>(() => {
  if (!data.value) return [];
  const res: Champ[] = [];
  Object.entries((data.value as any).data).forEach(([key, value]) => {
    res.push({
      name: key,
      id: (value as any).id,
      tags: (value as any).tags,
      title: (value as any).title,
      image: `https://ddragon.leagueoflegends.com/cdn/14.10.1/img/champion/${
        (value as any).image.full
      }`,
      fullSplash: `https://ddragon.leagueoflegends.com/cdn/img/champion/splash/${key}_0.jpg`,
    });
  });
  return res;
});
const allChampionsTags = computed(() => {
  const res = new Set<string>();
  allChampions.value.forEach((champ) => {
    champ.tags.forEach((tag) => {
      res.add(tag);
    });
  });
  return Array.from(res);
});

// Filter
const filterTags = ref<string[] | null>(null);
function toggleFilterTag(tag: string) {
  if (!filterTags.value) {
    filterTags.value = [tag];
  } else {
    const idx = filterTags.value.indexOf(tag);
    if (idx === -1) {
      filterTags.value.push(tag);
    } else {
      filterTags.value.splice(idx, 1);
    }
  }
  resetRoll();
}
const pools = computed(() => {
  // Need to mached all tags
  if (!filterTags.value) return allChampions.value;
  const filtered = allChampions.value.filter((champ) => {
    if (filterTags.value) {
      return filterTags.value.every((tag) => champ.tags.includes(tag));
    }
  });
  return filtered || [];
});

// View
const viewGridRolling = ref<boolean>(false);
function toggleGridRolling() {
  viewGridRolling.value = !viewGridRolling.value;
}
function closeGridRolling() {
  viewGridRolling.value = false;
}

// Roll
const rollIndex = ref<number>(-1);
const rollPreview = computed(() => {
  if (rollIndex.value === -1) return null;
  return pools.value[rollIndex.value];
});
const rollInterval = ref<NodeJS.Timeout | null>(null);
const rollPending = ref<boolean>(false);
const rollWinnerDisplay = ref<boolean>(false);
const rollable = computed(() => pools.value.length > 1);
function resetRoll() {
  rollIndex.value = -1;
}
function roll() {
  if (rollPending.value) return;
  rollPending.value = true;
  if (rollInterval.value) {
    clearInterval(rollInterval.value);
  }
  rollInterval.value = setInterval(() => {
    rollIndex.value = Math.floor(Math.random() * pools.value.length);
  }, 150);
  // Stop
  setTimeout(() => {
    if (rollInterval.value) {
      clearInterval(rollInterval.value);
    }
    rollPending.value = false;
    setTimeout(() => {
      rollWinnerDisplay.value = true;
      setTimeout(() => {
        rollWinnerDisplay.value = false;
      }, 3000);
    }, 750);
  }, 2000);
}
</script>

<style scoped></style>
