<template>
  <div class="example">
    <v-calendar :attributes="attributes" :min-date="minDate" />
  </div>
</template>

<script>
export default {
  props: {
    disabled: null,
  },
  data() {
    return {
      minDate: new Date(2021, 0, 15),
      attributes: [
        {
          highlight: 'blue',
          dates: new Date(2021, 0, 5),
          disabled: this.disabled,
        },
      ],
    };
  },
};
</script>
