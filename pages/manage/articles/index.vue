<script setup lang="ts">
import { ArticleItem } from "~/utils/types";
import ManageListTable from "~/comps/manage-list-table.vue";

const searchTag = reactive(new Set<string>());
const toggleTag = (tag: string) => {
  if (searchTag.has(tag)) {
    searchTag.delete(tag);
  } else {
    searchTag.add(tag);
  }
};
const registryFilter = (customFilter) => {
  watch(searchTag, () =>
    customFilter((item: ArticleItem) => {
      return (
        !searchTag.size || (item.tags.length && Array.from(searchTag).every(tag => item.tags.includes(tag)))
      );
    })
  );
};
const searchFn = (item: ArticleItem, s: string) => item.title.includes(s);
</script>

<template>
  <div class="manage-article">
    <manage-list-table
      col-prefix="article-"
      :registry-filter="registryFilter"
      :show-filter="!!searchTag.size"
      :search-fn="searchFn"
    >
      <template #filter>
        <div class="filter-tag">
          <the-tag
            v-for="tag in searchTag"
            :key="tag"
            :active="searchTag.has(tag)"
            @click="toggleTag(tag)"
          >
            {{ tag }}
          </the-tag>
        </div>
      </template>
      <template #title="{ data: title, dataUrl }">
        <nuxt-link :to="dataUrl">
          {{ title }}
        </nuxt-link>
      </template>
      <template #tags="{ data: tags }">
        <div>
          <the-tag
            v-for="tag in tags"
            :key="tag"
            :active="searchTag.has(tag)"
            @click="toggleTag(tag)"
          >
            {{ tag }}
          </the-tag>
        </div>
      </template>
    </manage-list-table>
  </div>
</template>

<style lang="scss">
.manage-article {
  .filter-tag {
    .common-tag {
      margin-right: 4px;
    }
  }

  .article-title {
    flex-basis: 40%;
    font-weight: bold;
    font-size: 15px;
  }

  .article-tags {
    flex-basis: 20%;

    .common-tag {
      margin-right: 4px;
      margin-bottom: 4px;
    }
  }
}
</style>
