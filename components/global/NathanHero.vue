<template>
    <div>
     Data is:
     <p>{{ data }}</p>
    </div>
</template>

<script>
export default {
  name: 'NathanHero',
  props: {
    data: {
      type: Object,
      required: true,
    },
  },
};
</script>