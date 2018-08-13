<template>
  <div class="btn-group" name="HeaderSettings">
    <button class="btn btn-default dropdown-toggle" ref="dropdownBtn" type="button">
      <i class="fa" :class="[processingCls || 'fa-cog']"></i>
      <span class="caret"></span>
    </button>
    <div class="dropdown-menu clearfix" :style="drpMenuStyle">
      <div class="-col-group-container">
        <column-group v-for="(columns, groupName) in colGroups"
          ref="colGroups" :key="groupName"
          :group-name="groupName" :columns="columns">
        </column-group>
      </div>
      <div class="clearfix" style="margin: 10px 0">
        <div class="btn-group btn-group-sm pull-right">
          <button class="btn btn-default" type="button" @click="apply()">
            {{ $i18nForDatatable('Apply') }}
          </button>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import ColumnGroup from './ColumnGroup.vue'
import groupBy from 'lodash/groupBy'

export default {
  name: 'HeaderSettings',
  components: { ColumnGroup },
  props: {
    columns: { type: Array, required: true },
    supportBackup: { type: Boolean, required: true }
  },
  data () {
    const origSettings = JSON.stringify(this.columns)
    return {
      origSettings,
      processingCls: '',
    }
  },
  created () {
    
  },
  mounted () {
    // control dropdown manually (refers to http://jsfiddle.net/rj3k550m/3)
    const $el = $(this.$el)
    $(this.$refs.dropdownBtn).on('click', this.toggle)
    $(document).on('click', e => {
      $(e.target).closest($el).length || $el.removeClass('open')
    })
  },
  computed: {
    colGroups () {
      return groupBy(
        this.columns.filter(col => col.label || col.title),
        'group'
      )
    },
    drpMenuStyle () {
      const w = Object.keys(this.colGroups).length * 150
      return {
        padding: '10px 10px 0',
        width: `${w + 25}px`,
        left: `-${w - 25}px`
      }
    }
  },
  methods: {
    apply (alsoBackup) {
      this.toggle()
      this.$refs.colGroups.forEach(colGroup => { colGroup.apply() })
    },
    toggle () {
      $(this.$el).toggleClass('open')
    },
    showProcessing () {
      ['fa-spinner fa-pulse', 'fa-check', ''].forEach((cls, idx) => {
        setTimeout(() => {
          this.processingCls = cls
        }, idx * 1000)
      })
    }
  }
}
</script>
<style>
.-col-group-container {
  border-bottom: 1px solid #ddd;
}
</style>
