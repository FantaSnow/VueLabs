<script setup lang="ts">
import axios from "axios";
import { ref, computed, watch } from 'vue';

const columns = [
  { key: 'id', label: '#' },
  { key: 'user_name', label: 'Автор' },
  { key: 'category_title', label: 'Категорія' },
  { key: 'title', label: 'Заголовок' },
  { key: 'published_at', label: 'Дата публікації' },
  { key: 'actions', label: 'Дії' }
];

const page = ref(1);
const perPage = ref(8); // Wrap perPage in ref to make it reactive
const posts = ref<Post[]>([]);
const total = ref<number>(0); // Initialize total as 0

interface User {
  name: string;
}

interface Category {
  id: number;
  title: string;
}

interface Post {
  id: number;
  user: User;
  category: Category;
  title: string;
  published_at: string;
}

const getPosts = async () => {
  try {
    const response = await axios.get(`http://127.0.0.1:8000/api/blog/posts`, {
      params: {
        page: page.value,
        per_page: perPage.value
      }
    });
    posts.value = response.data.data;
    total.value = response.data.total;
    console.log(response);
  } catch (error) {
    console.error('Error fetching posts:', error);
  }
};

watch([page, perPage], getPosts, { immediate: true });

const rows = computed(() => {
  return posts.value.map((post) => ({
    id: post.id,
    user_name: post.user.name,
    category_title: post.category.title,
    title: post.title,
    published_at: post.published_at,
    category_id: post.category.id
  }));
});

const items = (post: Post) => [
  [ {
    label: 'Деталі',
    icon: 'i-heroicons-information-circle-20-solid',
    click: () => window.location.href = (`/posts/${post.id}`)
  }]
];
</script>

<template>
  <UTable :columns="columns" :rows="rows" :total="total" >
    <template #category_title-data="{ row }">
        {{ row.category_title }}
    </template>
    <template #actions-data="{ row }">
      <UDropdown :items="items(row)">
        <UButton color="gray" variant="ghost" icon="i-heroicons-ellipsis-horizontal-20-solid" />
      </UDropdown>
    </template>
  </UTable>
  <div>
    <UPagination v-model="page" :total="total" :page-count="perPage" />
  </div>
</template>
