<template>
  <ms-table-search-component v-model="component.operator.value" :component="component" @change="change" v-bind="$attrs" v-on="$listeners">
    <template v-slot="scope">
      <el-date-picker v-model="scope.component.value" v-bind="scope.component.props"
                      :placeholder="$t('commons.date.select_date_time')" size="small"
                      :type="type" :key="type" value-format="timestamp"
                      class="ms-el-date-picker"
                      :range-separator="$t('commons.date.range_separator')"
                      :start-placeholder="$t('commons.date.start_date_time')"
                      :end-placeholder="$t('commons.date.end_date_time')">
      </el-date-picker>
    </template>
  </ms-table-search-component>

</template>

<script>
  import MsTableSearchComponent from "./MsTableSearchComponet";
  import {OPERATORS} from "./search-components"

  export default {
    name: "MsTableSearchDateTimePicker",
    components: {MsTableSearchComponent},
    props: ['component'],
    inheritAttrs: false,
    methods: {
      change(value) {
        if (value === OPERATORS.BETWEEN.value) {
          if (!Array.isArray(this.component.value)) {
            this.component.value = [];
          }
        } else {
          if (Array.isArray(this.component.value)) {
            this.component.value = "";
          }
        }
      }
    },
    computed: {
      type() {
        if (this.component.operator.value === OPERATORS.BETWEEN.value) {
          return "datetimerange";
        } else {
          return "datetime";
        }
      }
    }
  }
</script>

<style scoped>
.ms-el-date-picker >>> .el-date-editor--datetimerange.el-input,
.el-date-editor--datetimerange.el-input__inner {
  width: 100%;
}
</style>
