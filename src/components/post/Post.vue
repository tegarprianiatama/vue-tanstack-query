<script lang="ts">
import { defineComponent } from 'vue'
import { useQuery } from '@tanstack/vue-query'

import type { Post } from '../../type/Post'

const fetcher = async (id: number): Promise<Post> =>
  await fetch(`https://jsonplaceholder.typicode.com/posts/${id}`).then((response) =>
    response.json()
  )

export default defineComponent({
  name: 'PostDetails',
  props: {
    postId: {
      type: Number,
      required: true
    }
  },
  emits: ['setPostId'],
  setup(props) {
    const { isPending, isError, isFetching, data, error } = useQuery({
      queryKey: ['post', props.postId],
      queryFn: () => fetcher(props.postId)
    })

    return { isPending, isError, isFetching, data, error }
  }
})
</script>

<template>
  <h1>Post {{ postId }}</h1>
  <a @click="$emit('setPostId', -1)" href="#"> Back </a>
  <div v-if="isPending" class="update">Loading...</div>
  <div v-else-if="isError">An error has occurred: {{ error }}</div>
  <div v-else-if="data">
    <h1>{{ data.title }}</h1>
    <div>
      <p>{{ data.body }}</p>
    </div>
    <div v-if="isFetching" class="update">Background Updating...</div>
  </div>
</template>

<style scoped>
.update {
  font-weight: bold;
  color: green;
}
</style>
