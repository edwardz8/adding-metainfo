<template>
  <h1>{{ title }}</h1>
  <div v-html="contents"></div>
  <comment v-if="error === false">
    <p>I am a slot content</p>
  </comment>
  <button @click="count++">{{ count }}</button>
  <form>
  <span>{{ phoneNumber }}</span>
  </form>
</template>

<script setup>
import useFetch from "@/composables/use-fetch";
import { throwError } from "@/modules/utils";

const title = ref(null);
const contents = ref(null);
const count = ref(1);
const phoneNumber = ref();

const { data, error, message, status } = await useFetch(`/wp-json/api/v1/page/about`);
if (error) {
  throwError(JSON.stringify(message), status);
}

title.value = data.title;
contents.value = data.contents;

if (data.phone_number) {
  phoneNumber.value = data.phone_number;
}
useHead({
  title: data.meta.primary.title,
  meta: [
    {
      name: "description",
      content: data.meta.primary.description,
    },
    {
      property: "og:title",
      content: data.meta.open_graph.title,
    },
    {
      property: "og:description",
      content: data.meta.open_graph.description,
    },
    {
      property: "og:image",
      content: data.meta.open_graph.image,
    },
  ],
});
</script>
