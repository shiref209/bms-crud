<template>
  <v-btn color="color" text @click="onClick">
    {{ value }}
  </v-btn>
</template>

<script>
export default {
  name: "ActionButton",

  props: ["value", "color", "onClick"],
};
</script>
