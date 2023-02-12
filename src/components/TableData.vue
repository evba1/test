<template>
  <div id="app">
    <div class="container pt-5">
      <div class="d-grid gap-4 d-md-flex justify-content-md-start">
        <div class="btn-group" role="group" aria-label="Currencies">
          <input
            type="radio"
            class="btn-check"
            name="currency"
            id="btn-currency-usd"
            autocomplete="off"
            value="USD"
            v-model="currency"
          />
          <label class="btn btn-outline-primary" for="btn-currency-usd"
            >USD</label
          >

          <input
            type="radio"
            class="btn-check"
            name="currency"
            id="btn-currency-eur"
            autocomplete="off"
            value="EUR"
            v-model="currency"
          />
          <label class="btn btn-outline-primary" for="btn-currency-eur"
            >EUR</label
          >

          <input
            type="radio"
            class="btn-check"
            name="currency"
            id="btn-currency-cad"
            autocomplete="off"
            value="CAD"
            v-model="currency"
          />
          <label class="btn btn-outline-primary" for="btn-currency-cad"
            >CAD</label
          >
        </div>
        <div class="btn-group" role="group" aria-label="Years">
          <input
            type="checkbox"
            class="btn-check"
            id="btn-years-5"
            autocomplete="off"
            :value="5"
            v-model="years"
          />
          <label class="btn btn-outline-primary" for="btn-years-5">5 YRS</label>

          <input
            type="checkbox"
            class="btn-check"
            id="btn-years-10"
            autocomplete="off"
            :value="10"
            v-model="years"
          />
          <label class="btn btn-outline-primary" for="btn-years-10"
            >10 YRS</label
          >

          <input
            type="checkbox"
            class="btn-check"
            id="btn-years-40"
            autocomplete="off"
            :value="40"
            v-model="years"
          />
          <label class="btn btn-outline-primary" for="btn-years-40"
            >40 YRS</label
          >
        </div>
        <div class="btn-group" role="group" aria-label="Display">
          <input
            type="radio"
            class="btn-check"
            name="display"
            id="btn-display-spread"
            autocomplete="off"
            value="Spread"
            v-model="display"
          />
          <label class="btn btn-outline-primary" for="btn-display-spread"
            >Spread</label
          >

          <input
            type="radio"
            class="btn-check"
            name="display"
            id="btn-display-yield"
            autocomplete="off"
            value="Yield"
            v-model="display"
          />
          <label class="btn btn-outline-primary" for="btn-display-yield"
            >Yield</label
          >

          <input
            type="radio"
            class="btn-check"
            name="display"
            id="btn-display-3ml"
            autocomplete="off"
            value="3MLSpread"
            v-model="display"
          />
          <label class="btn btn-outline-primary" for="btn-display-3ml"
            >3MLSpread</label
          >
        </div>
      </div>
      <div class="mt-3 mb-3">
        <input
          type="text"
          class="form-control w-25"
          id="search"
          placeholder="Filter by company name ..."
          v-model="companyName"
        />
      </div>
      <table class="table">
        <thead>
          <tr>
            <th></th>
            <th scope="col" @click="setSort('DateSent')">
              Date Sent
              <i
                :class="[
                  'bi',
                  sort.DateSent ? 'bi-chevron-down' : 'bi-chevron-up',
                ]"
              ></i>
            </th>
            <th scope="col" @click="setSort('Company')">
              Company
              <i
                :class="[
                  'bi',
                  sort.Company ? 'bi-chevron-down' : 'bi-chevron-up',
                ]"
              ></i>
            </th>
            <th scope="col" v-for="year in years" :key="year">
              <div class="row text-center mr-2">
                <div class="col col-12 border-bottom">{{ year }} YRS</div>
                <div
                  class="col col-6"
                  v-for="couponType of couponTypes"
                  :key="couponType"
                >
                  {{ couponType }}
                </div>
              </div>
            </th>
          </tr>
        </thead>
        <tbody class="table-group-divider">
          <tr v-for="(item, index) in filteredItems" :key="index">
            <td>
              <i
                v-if="item.Quote && Object.keys(item.Quote).length > 0"
                class="bi bi-chevron-down"
              ></i>
            </td>
            <td>{{ item.DateSent }}</td>
            <td>{{ item.Company }}</td>
            <td v-for="year in years" :key="year">
              <div class="row text-center">
                <div
                  class="col col-6"
                  v-for="couponType of couponTypes"
                  :key="couponType"
                >
                  {{
                    item.Quote
                      ? item.Quote[year]?.find(
                          (q) => q.CouponType === couponType
                        )?.[display]
                      : null
                  }}
                </div>
              </div>
            </td>
          </tr>
          <tr class="border">
            <td></td>
            <td></td>
            <td>Average by {{ display }}</td>
            <td v-for="year in years" :key="year">
              <div class="row text-center">
                <div
                  class="col col-6"
                  v-for="couponType of couponTypes"
                  :key="couponType"
                >
                  {{
                    totalRow && totalRow[year] && totalRow[year][couponType]
                      ? Math.round(
                          totalRow[year][couponType].value /
                            totalRow[year][couponType].rows
                        )
                      : null
                  }}
                </div>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
export default {
  name: "TableData",
  data() {
    return {
      currency: "USD",
      years: [5],
      display: "Spread",
      couponTypes: ["FIX", "FRN"],
      companyName: "",
      sort: {},
      items: [
        {
          Id: "395356",
          DateSent: "2020-12-30",
          Company: "Openlane",
          Preferred: "1",
          Quote: [
            {
              Amount: 500,
              Currency: "USD",
              Years: 5,
              CouponType: "FIX",
              Spread: 50,
              Yield: 0.873,
              "3MLSpread": 86,
            },
            {
              Amount: 500,
              Currency: "CAD",
              Years: 10,
              CouponType: "FIX",
              Spread: 50,
              Yield: 1.209,
              "3MLSpread": 13,
            },
            {
              Amount: 500,
              Currency: "EUR",
              Years: 4,
              CouponType: "FIX",
              Spread: 35,
              Yield: -0.136,
              "3MLSpread": 49,
            },
            {
              Amount: 500,
              Currency: "EUR",
              Years: 8,
              CouponType: "FIX",
              Spread: 60,
              Yield: 0.248,
              "3MLSpread": 81,
            },
            {
              Amount: 500,
              Currency: "EUR",
              Years: 12,
              CouponType: "FIX",
              Spread: 90,
              Yield: 0.721,
              "3MLSpread": 117,
            },
            {
              Amount: 500,
              Currency: "USD",
              Years: 10,
              CouponType: "FIX",
              Spread: 90,
              Yield: 1.828,
              "3MLSpread": 88,
            },
            {
              Amount: 500,
              Currency: "USD",
              Years: 40,
              CouponType: "FIX",
              Spread: 130,
              Yield: 2.965,
              "3MLSpread": 152,
            },
            {
              Amount: 500,
              Currency: "EUR",
              Years: 12,
              CouponType: "FRN",
              Spread: 92,
              Yield: 0.718,
              "3MLSpread": 112,
            },
            {
              Amount: 500,
              Currency: "USD",
              Years: 10,
              CouponType: "FRN",
              Spread: 94,
              Yield: 1.826,
              "3MLSpread": 86,
            },
            {
              Amount: 500,
              Currency: "USD",
              Years: 40,
              CouponType: "FRN",
              Spread: 128,
              Yield: 2.962,
              "3MLSpread": 150,
            },
          ],
        },
        {
          Id: "395347",
          DateSent: "2020-12-30",
          Company: "Yearin",
          Preferred: "0",
          Quote: [
            {
              Amount: 500,
              Currency: "USD",
              Years: 10,
              CouponType: "FIX",
              Spread: 75,
              Yield: 1.678,
              "3MLSpread": 74,
            },
          ],
        },
        {
          Id: "395284",
          DateSent: "2020-12-18",
          Company: "Condax",
          Preferred: "0",
          Quote: [
            {
              Amount: 300,
              Currency: "USD",
              Years: 10,
              CouponType: "FIX",
              Spread: 100,
              Yield: null,
              "3MLSpread": null,
            },
          ],
        },
        {
          Id: null,
          DateSent: null,
          Company: "Opentech",
          Preferred: "1",
          Quote: null,
        },
        {
          Id: null,
          DateSent: null,
          Company: "Golddex",
          Preferred: "1",
          Quote: null,
        },
        {
          Id: null,
          DateSent: null,
          Company: "Warephase",
          Preferred: "1",
          Quote: null,
        },
        {
          Id: null,
          DateSent: null,
          Company: "Donware",
          Preferred: "1",
          Quote: null,
        },
        {
          Id: null,
          DateSent: null,
          Company: "Faxquote",
          Preferred: "1",
          Quote: null,
        },
      ],
    };
  },
  computed: {
    otherDisplayList() {
      return ["Spread", "Yield", "3MLYield"].filter((d) => d !== this.display);
    },
    filteredItems() {
      const self = this;

      // eslint-disable-next-line vue/no-side-effects-in-computed-properties
      const sorted = self.items
        .sort((a, b) => new Date(b.DateSent) - new Date(a.DateSent))
        .sort((a, b) => Number(b.Preferred) - Number(a.Preferred))
        .sort((a, b) => {
          const sortKeys = Object.keys(self.sort);
          const sortKey = sortKeys.length ? sortKeys[0] : "";
          if (sortKey === "Company") {
            if (self.sort[sortKey]) {
              return b.Company.localeCompare(a.Company);
            } else {
              return a.Company.localeCompare(b.Company);
            }
          }

          if (sortKey === "DateSent") {
            if (self.sort[sortKey]) {
              return new Date(b.DateSent) - new Date(a.DateSent);
            } else {
              return new Date(a.DateSent) - new Date(b.DateSent);
            }
          }
          return 0;
        })
        .sort((a, b) => {
          if (a.Quote === null) {
            return 1;
          }
          if (b.Quote === null) {
            return -1;
          }
          return 0;
        });

      return sorted
        .map((item) => {
          return {
            ...item,
            Quote: item.Quote
              ? item.Quote.filter((quote) => quote.Currency === self.currency)
              : null,
          };
        })
        .map((item) => {
          return {
            ...item,
            Quote: item.Quote
              ? item.Quote.filter((quote) =>
                  self.years.includes(quote.Years)
                ).reduce(function (r, a) {
                  r[a.Years] = r[a.Years] || [];
                  r[a.Years].push(a);
                  return r;
                }, Object.create(null))
              : null,
          };
        })
        .filter((i) => {
          if (this.companyName.length >= 3) {
            return (
              i.Company.toLowerCase().indexOf(
                this.companyName.toLowerCase()
              ) !== -1
            );
          } else {
            return i;
          }
        });
    },
    totalRow() {
      const totalsRow = {};
      const self = this;
      this.filteredItems.forEach((item) => {
        self.years.forEach((year) => {
          if (item.Quote && item.Quote[year] && item.Quote[year].length > 0) {
            item.Quote[year].forEach((quote) => {
              if (!totalsRow[year]) {
                totalsRow[year] = {};
              }
              if (!totalsRow[year][quote.CouponType]) {
                totalsRow[year][quote.CouponType] = {
                  value: 0,
                  rows: 0,
                  average: 0,
                };
              }

              if (quote[self.display]) {
                totalsRow[year][quote.CouponType].value += quote[self.display];
                totalsRow[year][quote.CouponType].rows += 1;
              }
            });
          }
        });
      });
      return totalsRow;
    },
  },
  methods: {
    setSort(columnName) {
      if (!this.sort[columnName]) {
        this.sort = {
          [columnName]: false,
        };
      }
      this.sort[columnName] = !this.sort[columnName];
    },
  },
};
</script>
