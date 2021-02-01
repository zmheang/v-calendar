<template>
  <div>
    <v-date-picker
      v-model="date"
      :disabled-dates="disabledDates"
      :drag-attribute="disabledAttribute"
      :select-attribute="disabledAttribute"
    />
    <v-date-picker
      v-model="range"
      :disabled-dates="disabledDates"
      :drag-attribute="disabledAttribute"
      :select-attribute="disabledAttribute"
      is-range
    />
    <v-calendar :attributes="attributes" :min-date="minDate" is-range />
  </div>
</template>

<script>
export default {
  githubTitle: 'Disabled attributes',
  data() {
    return {
      date: new Date(),
      range: null,
      minDate: new Date(2021, 0, 15),
      disabledDates: [new Date(2021, 0, 15)],
      dragAttribute: {
        disabled: {
          highlight: 'red',
        },
      },
      selectAttribute: {
        disabled: {
          highlight: 'red',
        },
      },
      disabledAttribute: {
        disabled: {
          highlight: 'red',
          // hidden: true,
        },
      },
      attributes: [
        {
          highlight: true,
          dates: [{ start: new Date(2021, 0, 10), end: new Date(2021, 0, 20) }],
          disabled: {
            highlight: 'red',
            isHidden: true,
          },
        },
      ],
    };
  },
};
</script>
