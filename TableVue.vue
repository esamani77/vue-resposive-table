<template>
  <section class="w-100 ">
    <table class="m-0 p-0 m-w-100" :class="[classList]">
      <thead>
        <tr :class="headVariant">
          <th
            scope="col"
            v-for="field in fields"
            :key="field.key"
            :class="field.thClass"
          >
            <div>{{ field.label }}</div>
          </th>
        </tr>
      </thead>
      <tbody v-if="!busy">
        <tr v-for="item in items" :class="[hover ? 'hover' : '']">
          <td
            scope="row"
            :data-label="field.label"
            v-for="field in fields"
            :key="field.key"
            :class="[field.tdClass]"
          >
            <slot :name="field.key" v-bind:item="item" v-if="field.formatter">
              <!-- {{ field.formatter(item[field.key], field.key, item) }} -->
              <span
                v-if="
                  field.key.includes('.') &&
                    item[field.key.slice(0, field.key.indexOf('.'))] !== null
                "
              >
                {{
                  field.formatter(
                    item[field.key.slice(0, field.key.indexOf("."))][
                      field.key.slice(field.key.indexOf(".") + 1)
                    ],
                    field.key,
                    item
                  )
                }}
              </span>
              <span v-else>{{
                field.formatter(item[field.key], field.key, item)
              }}</span>
            </slot>
            <slot :name="field.key" class="py-2" v-bind:item="item" v-else>
              <span>{{ item[field.key] }}</span>
            </slot>
          </td>
        </tr>
      </tbody>
      <tbody v-else>
        <tr :class="[hover ? 'hover' : '']">
          <td align="center" :colspan="fields.length">
            <slot name="empty">
              <i class="fa fa-circle-o-notch fa-spin"></i>
            </slot>
          </td>
        </tr>
      </tbody>
    </table>
    <div
      v-if="items.length === 0 && !busy"
      class="w-100 text-center bg-white py-3"
      :class="[hover ? 'hover' : '']"
    >
      <slot name="empty"> داده‌ای وجود ندارد.</slot>
    </div>
    <div></div>
  </section>
</template>

<script>
import loading from "./loading.vue";

export default {
  components: { loading },
  props: {
    fields: {
      type: Array,
      default: []
    },
    items: {
      type: Array,
      default: []
    },
    rtl: {
      type: Boolean,
      default: false
    },
    theme: {
      type: String,
      default: ""
    },

    busy: {
      type: Boolean,
      default: false
    },
    rows: {
      type: Number,
      default: 5
    },
    classList: {
      type: String,
      default: ""
    },
    hover: {
      type: Boolean,
      default: false
    },
    headVariant: {
      type: String,
      default: ""
    },
    showEmpty: {
      type: Boolean,
      default: false
    }
  },
  mounted() {
    // setTimeout(() => {
    //   console.log("hi", this.items[5]);
    //   console.log(
    //     this.getNested(this.items[5], "factor.payed_at")[
    //       "factor.payed_at".slice("factor.payed_at".lastIndexOf(".") + 1)
    //     ]
    //   );
    // }, 2000);
  },
  methods: {
    getNested(item, key) {
      // console.log(item, key);
      // console.log(1, key.includes("."));
      if (key.includes(".")) {
        // console.log(key.slice(0, key.indexOf(".")));
        let cut = key.slice(0, key.indexOf("."));
        // console.log(cut, item, item[cut]);
        // console.log(item.factor);
        item = item[cut];
        // console.log(key.slice(key.indexOf(".") + 1));
        key = key.slice(key.indexOf(".") + 1);

        // console.log(5, item, key);

        if (key.slice(key.indexOf(".") + 1).includes(".")) {
          // console.log("if");
          // console.log(
          //   item[key.slice(0, key.indexOf("."))],
          //   key.slice(key.indexOf(".") + 1)
          // );
          return this.getNested(
            item[key.slice(0, key.indexOf("."))],
            key.slice(key.indexOf(".") + 1)
          );
        } else {
          // console.log("else");
          // console.log(item, key);
          // console.log(item[key]);
          // console.log(item[key.slice(0, key.indexOf("."))]);
          // return item[key.slice(0, key.indexOf("."))];
          return item[key];
        }
      }
    }
  }
};
</script>

<style scoped>
.m-w-100 {
  min-width: 100%;
}

@media screen and (max-width: 600px) {
  th,
  td {
    background: #fff;
    padding: 0px;
  }

  table {
    width: 100%;
  }

  table thead {
    display: none;
  }

  table tr,
  table td {
    border-bottom: 1px solid #eee;
  }
  table td:last-child {
    border-bottom: 0;
  }
  table tr {
    margin-bottom: 8px;
    border-top: 15px solid;
    border-bottom: 15px solid;
    border-color: transparent;
  }

  table td {
    display: flex;
    align-items: center;
  }
  table td span {
    text-align: center;
    margin: 10px;
  }

  table td::before {
    padding: 10px;
    background: #eee;
    content: attr(data-label);
    font-weight: bold;
    width: 120px;
    min-width: 120px;
  }
}

@keyframes aniHorizontal {
  0% {
    opacity: 1;
    background-position: -100% 0;
  }
  95% {
    opacity: 1;
  }
  100% {
    opacity: 1;
    background-position: 100% 0;
  }
}

.hover {
  transition: all 0.5s ease;
  cursor: default;
}
.hover:hover {
  background-color: rgba(0, 0, 0, 0.025);
}

.empty {
  text-align: center;
  padding: 1rem 0;
  border: 1px solid rgb(240, 240, 240);
  border-top: none;
  background: rgb(240, 240, 240);
}
</style>
