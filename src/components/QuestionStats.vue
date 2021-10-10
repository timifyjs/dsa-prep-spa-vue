<template>
  <div class="container">
    <q-chip color="positive" text-color="white" icon="speed">
      {{ diffLevel }}
    </q-chip>
    <q-chip color="dark" text-color="white" icon="thumb_up">
      {{ likes }}
    </q-chip>
    <q-chip color="dark" text-color="white" icon="thumb_down">
      {{ dislikes }}
    </q-chip>
    <q-icon
      :color="isFav ? 'pink' : 'white'"
      size="sm"
      :name="isFav ? 'favorite' : 'favorite_border'"
    />
  </div>
</template>

<script>
export default {
  props: {
    diffLevel: {
      type: String,
      required: true
    },
    likes: {
      type: Number,
      required: true
    },
    dislikes: {
      type: Number,
      required: true
    },
    isFav: {
      type: Boolean,
      required: true
    }
  },
  data() {
    return {};
  }
};
</script>

<style scoped>
.container {
    width: 300px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.diffLevel {
}
</style>
