<template>
  <v-col cols="12" sm="6" md="4">
    <v-dialog
      ref="dialog"
      v-model="modal"
      :return-value.sync="date"
      persistent
      width="290px"
    >
      <template v-slot:activator="{ on, attrs }">
        <!-- <v-text-field
      v-model="date"
      label="Picker in dialog"
      prepend-icon="mdi-calendar"
      readonly
      v-bind="attrs"
      v-on="on"
    ></v-text-field> -->
        <p>{{ formattedDate() }}</p>
        <v-icon v-bind="attrs" v-on="on" v-model="date">mdi-calendar</v-icon>
      </template>
      <v-date-picker
        v-model="date"
        type="month"
        scrollable
        active-picker="MONTH"
        event-color="blue"
      >
        <v-spacer></v-spacer>
        <v-btn text color="primary" @click="modal = false"> Cancel </v-btn>
        <v-btn text color="primary" @click="$refs.dialog.save(date)">
          OK
        </v-btn>
      </v-date-picker>
    </v-dialog>
  </v-col>
</template>

<script>
import moment from "moment";
export default {
  name: "DatePicker",
  data: function () {
    return {
      date: this.dateValue,
      modal: false,
      formattedDate: () =>
        this.date ? moment(this.date).format("MMMM,YYYY") : "",
    };
  },
  props: {
    dateValue: {
      type: String,
      default: null,
    },
  },
};
</script>
