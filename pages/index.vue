<template>
  <div id="home" class="divcol">
  </div>
</template>

<script>
import computeds from '~/mixins/computeds'

export default {
  name: "HomePage",
  mixins: [computeds],
  data() {
    return {
    }
  },
  head() {
    const title = 'Home';
    return {
      title,
    }
  },
  created() {
    this.$router.push(this.localePath('/profile'))
  },
  methods: {
  }
};
</script>

<style src="~/assets/styles/pages/index.scss" lang="scss" />
