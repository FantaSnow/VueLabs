<script setup lang="ts">

interface User {
  name: string;
}

interface Category {
  title: string;
}

interface Post {
  id: number;
  user: User;
  category: Category;
  title: string;
  excerpt: string;
  content_raw: string;
  is_published: boolean;
  slug: string;
}

const route = useRoute();
const postId = ref<number | null>(null);
const post = ref<Post | null>(null);

const getPost = async (id: number) => {
  try {
    const response = await $fetch<Post>(`http://127.0.0.1:8000/api/blog/posts/${id}`);
    post.value = response;
  } catch (error) {
    console.error('Error fetching post:', error);
  }
};

onMounted(() => {
  const id = parseInt(route.params.id as string, 10);
  if (!isNaN(id)) {
    postId.value = id;
    getPost(id);
  } else {
    console.error('Invalid post ID');
  }
});

const items = [{
  label: 'Content',
  icon: 'i-heroicons-information-circle',
  slot: 'content'
}]
</script>

<template>
  <div>
    <div class="p-5 m-auto mt-2 flex">
      <nuxt-link class=" bg-green-500 hover:bg-green-700 text-white font-bold py-2 px-4 rounded m-auto" to="/posts">Усі статті</nuxt-link>
    </div>
    <div class="flex flex-row justify-evenly my-2 w-1/2 m-auto text-center">
      <UCard class="h-full">
        <template #header>
          <p>Post ID: {{ postId }}</p>
        </template>
        <ul>
          <li class="border-b-2 my-3 pb-3">Назва статті: {{post?.title}}</li>
          <li class="border-b-2 my-3 pb-3">Категорія: {{post?.category?.title}}</li>
          <li class="border-b-2 my-3 pb-3">Автор: {{post?.user?.name}}</li>
          <li class="border-b-2 my-3 pb-3">Опубліковано: {{post?.is_published ? 'Так' : 'Ні'}}</li>
          <li class="border-b-2 my-3 pb-3">Уривок: {{post?.excerpt}}</li>
          <li class="border-b-2 my-3 pb-3">Слаг: {{post?.slug}}</li>
          <li class="text-justify">Зміст статті: {{post?.content_raw}}</li>
        </ul>
      </UCard>
    </div>
  </div>
</template>