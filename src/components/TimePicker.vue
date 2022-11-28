<template>
  <v-menu
    ref="menu"
    v-model="menu2"
    :close-on-content-click="false"
    :nudge-right="40"
    :return-value.sync="time"
    transition="scale-transition"
    offset-y
    max-width="290px"
    min-width="290px"
  >
    <template v-slot:activator="{ on, attrs }">
      <p>
        {{ time }}
      </p>
      <v-icon v-model="time" color="#3949AB" v-bind="attrs" v-on="on" right>
        mdi-clock-time-four-outline
      </v-icon>
    </template>
    <v-time-picker
      v-if="menu2"
      v-model="time"
      full-width
      scrollable
      @click:minute="$refs.menu.save(time)"
    ></v-time-picker>
  </v-menu>
</template>

<script>
export default {
  name: "TimePicker",
  props: {
    timeValue: {
      type: String,
      default: null,
    },
  },
  data() {
    return {
      time: this.timeValue,
      menu2: false,
    };
  },
};
</script>

<!-- <template >
  
  <v-menu
    ref="menu"
    v-model="menu2"
    :close-on-content-click="false"
    :nudge-right="40"
    :return-value.sync="time"
    transition="scale-transition"
    offset-y
    max-width="290px"
    min-width="290px"
  >
    <template v-slot:activator="{ on, attrs }">
      <p>
    {{time}}
  </p>
      <v-text-field
        v-model="time"
        label="Click to change time"
        prepend-icon="mdi-clock-time-four-outline"
        readonly
        v-bind="attrs"
        v-on="on"
      ></v-text-field>
    </template>
    <v-time-picker
      v-if="menu2"
      v-model="time"
      full-width
      scrollable
      @click:minute="$refs.menu.save(time)"
    ></v-time-picker>
  </v-menu>
</template>

<script>
export default {
  name: "TimePicker",
  props:{
    timeValue:{
      type:String,
      default:null,
    }
  },
  data() {
    return {
      time:this.timeValue,
      menu2: false,
    };
  },
};
</script> -->
