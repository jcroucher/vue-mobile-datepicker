<template>
  <div class="home">
    <button @click="openDate">选择日期</button>
    <Datepicker
      :visible.sync="isShow"
      @change="onChange"
      @cancel="onCancel"
    />
  </div>
</template>

<script>
import Datepicker from '@/components/datepicker'
export default {
  name: 'home',
  components: {
    Datepicker
  },
  data () {
    return {
      isShow: false
    }
  },
  methods: {
    openDate () {
      this.isShow = true
    },
    onChange (date) {
      console.log(date)
    },
    onCancel () {
      console.log('cancel')
    }
  }
}
</script>
