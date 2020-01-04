<script>
import CalendarDay from './CalendarDay';
import Grid from './Grid';
import {
  propOrDefaultMixin,
  childMixin,
  safeScopedSlotMixin,
} from '@/utils/mixins';

export default {
  name: 'CalendarPane',
  mixins: [propOrDefaultMixin, childMixin, safeScopedSlotMixin],
  render(h) {
    // Header
    const header =
      this.safeScopedSlot('header', this.page) ||
      h(
        'div',
        {
          class: ['vc-header', this.theme.header],
        },
        [
          // Header title
          h(
            'div',
            {
              class: `vc-title-layout align-${this.titlePosition}`,
            },
            [
              h('form', { class: 'vc-title' }, [
                h(
                  'span',
                  { class: ['vc-title-month', this.theme.titleMonth] },
                  [
                    h(
                      'select',
                      {
                        class: 'vc-title-select',
                        attrs: {
                          tabindex: 0,
                        },
                        domProps: {
                          value: this.page.month,
                        },
                        on: {
                          change: this.onMonthSelect,
                        },
                      },
                      [
                        this.monthItems.map(m =>
                          h(
                            'option',
                            {
                              attrs: {
                                value: m.month,
                              },
                            },
                            [m.label],
                          ),
                        ),
                      ],
                    ),
                    this.monthLabel,
                  ],
                ),
                h('span', { class: ['vc-title-year', this.theme.titleYear] }, [
                  h(
                    'select',
                    {
                      class: 'vc-title-select',
                      attrs: {
                        tabindex: 1,
                      },

                      domProps: {
                        value: this.page.year,
                      },
                      on: {
                        change: this.onYearSelect,
                      },
                    },
                    [
                      this.yearItems.map(y =>
                        h(
                          'option',
                          {
                            attrs: {
                              value: y.year,
                            },
                          },
                          [y.label],
                        ),
                      ),
                    ],
                  ),
                  h('span', {}, this.yearLabel),
                ]),
              ]),
            ],
          ),
        ],
      );

    // Weeks
    const weeks = h(
      Grid,
      {
        class: 'vc-weeks',
        props: {
          rows: 7,
          columns: 7,
          columnWidth: '1fr',
          disableFocus: true,
        },
      },
      [
        ...this.weekdayLabels.map((wl, i) =>
          h(
            'div',
            {
              key: i + 1,
              class: ['vc-weekday', this.theme.weekdays],
            },
            [wl],
          ),
        ),
        ...this.page.days.map(day =>
          h(CalendarDay, {
            attrs: {
              ...this.$attrs,
              day,
            },
            on: {
              ...this.$listeners,
            },
            scopedSlots: this.$scopedSlots,
            key: day.id,
            ref: 'days',
            refInFor: true,
          }),
        ),
      ],
    );

    return h(
      'div',
      {
        class: 'vc-pane',
        ref: 'pane',
      },
      [header, weeks],
    );
  },
  props: {
    page: Object,
    titlePosition: String,
    navVisibility: String,
    canMove: {
      type: Function,
      default: () => true,
    },
  },
  computed: {
    navVisibility_() {
      return this.propOrDefault('navVisibility', 'navVisibility');
    },
    navPlacement() {
      switch (this.titlePosition) {
        case 'left':
          return 'bottom-start';
        case 'right':
          return 'bottom-end';
        default:
          return 'bottom';
      }
    },
    weekdayLabels() {
      return this.locale
        .getWeekdayDates()
        .map(d => this.format(d, this.masks.weekdays));
    },
    monthItems() {
      // const { month: thisMonth, year: thisYear } = pageForDate(new Date());
      return this.locale.getMonthDates().map((d, i) => {
        const month = i + 1;
        return {
          month,
          label: this.locale.format(d, this.masks.navMonths),
          // ariaLabel: this.locale.format(d, 'MMMM YYYY'),
          // isActive: month === this.month && this.yearIndex === this.year,
          // isCurrent: month === thisMonth && this.yearIndex === thisYear,
          // isDisabled: !this.validator({ month, year: this.yearIndex }),
          // click: () => this.monthClick(month),
        };
      });
    },
    monthLabel() {
      return this.monthItems.find(mi => mi.month === this.page.month).label;
    },
    yearItems() {
      // const { _, year: thisYear } = pageForDate(new Date());
      // const startYear = this.yearGroupIndex * _yearGroupCount;
      // const endYear = startYear + _yearGroupCount;
      const items = [];
      const startYear = 1900;
      const endYear = 3000;
      for (let year = startYear; year < endYear; year += 1) {
        items.push({
          year,
          label: year,
          ariaLabel: year,
          // isActive: year === this.year,
          // isCurrent: year === thisYear,
          // isDisabled: !this.validator({ month: this.month, year }),
          // click: () => this.yearClick(year),
        });
      }
      return items;
    },
    yearLabel() {
      return this.yearItems.find(mi => mi.year === this.page.year).label;
    },
  },
  methods: {
    onMonthSelect(e) {
      const month = Number.parseInt(e.target.value, 10);
      const year = this.page.year;
      this.move({ month, year });
    },
    onYearSelect(e) {
      const month = this.page.month;
      const year = Number.parseInt(e.target.value, 10);
      this.move({ month, year });
    },
    move(page) {
      this.$emit('update:page', page);
    },
    refresh() {
      this.$refs.days.forEach(d => d.refresh());
    },
  },
};
</script>

<style lang="postcss" scoped>
.vc-pane {
  flex-grow: 1;
  flex-shrink: 1;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: stretch;
}

.vc-horizontal-divider {
  align-self: center;
}

.vc-header {
  flex-shrink: 0;
  display: flex;
  align-items: stretch;
  user-select: none;
  padding: var(--header-padding);
  &.align-left {
    order: -1;
    justify-content: flex-start;
  }
  &.align-right {
    order: 1;
    justify-content: flex-end;
  }
}

.vc-title-layout {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-grow: 1;
  &.align-left {
    justify-content: flex-start;
  }
  &.align-right {
    justify-content: flex-end;
  }
}

.vc-title {
  padding: var(--title-padding);
}

.vc-title-month,
.vc-title-year {
  position: relative;
  user-select: none;
  white-space: nowrap;
  & select {
    pointer-events: auto;
    cursor: pointer;
    position: absolute;
    width: 100%;
    height: 100%;
    opacity: 0;
  }
}

.vc-title-year {
  margin-left: 4px;
}

.vc-weekday {
  display: flex;
  justify-content: center;
  align-items: center;
  flex: 1;
  padding: var(--weekday-padding);
  cursor: default;
  user-select: none;
}

.vc-weeks {
  flex-shrink: 1;
  flex-grow: 1;
  padding: var(--weeks-padding);
}
</style>
