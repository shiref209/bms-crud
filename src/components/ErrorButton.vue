<template>
        <v-card-text>
        Error occurred, Please contact the provider if the problem still persists
        <v-btn
        :loading="loading"
       dark
        color="#E53935"
        class="ma-1"
        @click="remove"
      >
        Try Again
      </v-btn>
      </v-card-text>
</template>
<script>
  export default {
    name:"ErrorButton",
    props:{
        reload:Function
    },
    data: () => ({ loading: false }),

    methods: {
      async remove () {
        this.loading = true
        await new Promise(resolve => {
            this.reload();
            setTimeout(resolve, 3000)})
       
        this.loading = false
      },
    },
  }
</script>