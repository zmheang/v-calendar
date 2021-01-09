<template>
  <div>
    <v-date-picker
      mode="datetime"
      v-model="date"
      :available-dates="availableDates"
      timezone="utc"
      is-range
    />
    <div>
      {{ date }}
    </div>
  </div>
</template>

<script>
// I can't pick date range etc.
// From: Tue May 18 2021 14:00:00 GMT+0200 (Central European Summer Time)
// To: Thu May 27 2021 14:00:00 GMT+0200 (Central European Summer Time)
export default {
  githubTitle: 'Date picker between available dates',
  data() {
    return {
      date: new Date(2021, 4, 5),
      availableDates: [
        {
          start: new Date('2021-05-01T00:00:00Z'), // Sat May 01 2021 00:00:00 GMT+0200
          end: new Date('2021-05-20T00:00:00Z'), // Thu May 20 2021 00:00:00 GMT+0200
        },
        {
          start: '2021-05-21T00:00:00', // Fri May 21 2021 00:00:00 GMT+0200
          end: '2021-06-12T00:00:00', // Tue Jun 15 2021 00:00:00 GMT+0200
        },
      ],
    };
  },
};
</script>
