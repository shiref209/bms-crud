<template>
  <v-app>
    <v-data-table
      :headers="headers"
      :items="employees"
      disable-filtering
      disable-sort
      mobile-breakpoint="600"
      class="elevation-1"
    >
      <template v-slot:top>
        <v-toolbar flat>
          <v-toolbar-title>BMS</v-toolbar-title>
          <v-divider class="mx-4" inset vertical></v-divider>
          <v-spacer></v-spacer>
          <v-dialog v-model="dialog" max-width="500px">
            <template v-slot:activator="{ on, attrs }">
              <v-btn color="primary" dark class="mb-2" v-bind="attrs" v-on="on">
                Add New Employee
              </v-btn>
            </template>
            <v-card>
              <v-card-title>
                <span class="text-h5">{{ formTitle }}</span>
              </v-card-title>
              <!-- Editing and Adding new employees Modal -->
              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col cols="12" sm="6" md="4">
                      <ValidationProvider
                        name="Full name"
                        rules="required|alpha_spaces"
                        v-slot="{ errors, valid }"
                      >
                        <v-text-field
                          v-model="editedItem.name"
                          label="Full name"
                          :error-messages="errors"
                          :success="valid"
                          required
                        >
                          <span>{{ errors[0] }}</span>
                        </v-text-field>
                      </ValidationProvider>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <ValidationProvider
                        name="Age"
                        rules="required|digits:2"
                        v-slot="{ errors, valid }"
                      >
                        <v-text-field
                          v-model="editedItem.age"
                          label="Age"
                          :error-messages="errors"
                          :success="valid"
                        >
                          <span>{{ errors[0] }}</span>
                        </v-text-field>
                      </ValidationProvider>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <ValidationProvider
                        name="Phone number"
                        :rules="{
                          regex: /^01[0125][0-9]{8}$/gm,
                          required: true,
                        }"
                        v-slot="{ errors, valid }"
                      >
                        <v-text-field
                          v-model="editedItem.phone"
                          label="Phone number"
                          :error-messages="errors"
                          :success="valid"
                          required
                        >
                          <span>{{ errors[0] }}</span>
                        </v-text-field>
                      </ValidationProvider>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <ValidationProvider
                        name="Role"
                        rules="alpha_spaces|required"
                        v-slot="{ errors, valid }"
                      >
                        <v-text-field
                          v-model="editedItem.role"
                          label="Role"
                          :error-messages="errors"
                          :success="valid"
                          required
                        >
                          <span>{{ errors[0] }}</span>
                        </v-text-field>
                      </ValidationProvider>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <ValidationProvider
                        name="Start working at"
                        rules="required"
                        v-slot="{ errors }"
                      >
                        <v-text-field
                          v-model="editedItem.started_date"
                          label="Start working at"
                          :error-messages="errors"
                          required
                          type="month"
                        >
                          <span>{{errors[0] }}</span>
                        </v-text-field>
                      </ValidationProvider>
                    </v-col>
                  </v-row>
                </v-container>
              </v-card-text>

              <v-card-actions>
                <v-spacer></v-spacer>
                <ActionButton
                  color="blue darken-1"
                  text
                  :onClick="close"
                  value="Cancel"
                />
                <ActionButton
                  color="blue darken-1"
                  text
                  :onClick="save"
                  value="save"
                />
              </v-card-actions>
            </v-card>
          </v-dialog>
          <!-- Action Dialog for deleting user -->
          <v-dialog v-model="dialogDelete" max-width="500px">
            <v-card>
              <v-card-title class="text-h6"
                >Are you sure you want to permanently delete this
                employee?</v-card-title
              >
              <v-card-actions>
                <v-spacer></v-spacer>
                <ActionButton
                  color="blue darken-1"
                  text
                  :onClick="closeDelete"
                  value="Cancel"
                />
                <ActionButton
                  color="blue darken-1"
                  text
                  :onClick="deleteItemConfirm"
                  value="Ok"
                />
                <v-spacer></v-spacer>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-toolbar>
      </template>
      <!-- Action Buttons rendered inside the table -->
      <template v-slot:item.edit="{ item }">
        <v-btn @click="editItem(item)" outlined color="#FF9100">
          <v-icon small class="mr-2" color="#FF9100"> mdi-pencil </v-icon>
        </v-btn>
      </template>
      <template v-slot:item.delete="{ item }">
        <v-btn @click="deleteItem(item)" outlined color="#EF5350">
          <v-icon small color="#EF5350"> mdi-delete </v-icon>
        </v-btn>
      </template>
      <template v-slot:item.record="{ item }">
        <v-btn outlined color="#3949AB">
          View
          <v-icon small color="#3949AB"> mdi-export </v-icon>
        </v-btn>
      </template>
      <template v-slot:item.arrival_time="{ item }">
        <TimePicker item="item" />
      </template>
      <template v-slot:item.leaving_time="{ item }">
        <TimePicker :timeValue="item.leaving_time" />
      </template>
      <template v-slot:item.started_date="{ item }">
        <DatePicker :dateValue="item.started_date" />
      </template>
      <template v-slot:item.fired="{ item }">
        <v-icon v-if="item.fired === 0" color="#388E3C">
          mdi-check-circle
        </v-icon>
        <v-icon v-else color="#FF1744"> mdi-close-circle </v-icon>
      </template>

      <template v-slot:no-data>
        <ErrorButton :reload="initialize" />
      </template>
    </v-data-table>
  </v-app>
</template>

<script>
import TimePicker from "./TimePicker.vue";
import ErrorButton from "./ErrorButton.vue";
import EditingDialog from "./EditingDialog.vue";
import DatePicker from "./DatePicker.vue";
import moment from "moment";
import ActionButton from "./ActionButton.vue";
import {
  ValidationProvider,
  ValidationObserver,
} from "vee-validate/dist/vee-validate.full.esm";

export default {
  components: {
    TimePicker,
    ErrorButton,
    EditingDialog,
    DatePicker,
    ActionButton,
    ValidationProvider,
    ValidationObserver,
  },
  data: function () {
    return {
      dialog: false,
      dialogDelete: false,
      headers: [
        {
          text: "ID",
          align: "start",
          sortable: false,
          value: "id",
        },
        { text: "Name", value: "name" },
        { text: "Age", value: "age" },
        { text: "Phone number", value: "phone" },
        { text: "Role", value: "role" },
        { text: "Starting working in", value: "started_date" },
        { text: "Still working", value: "fired" },
        { text: "Edit", value: "edit" },
        { text: "Delete", value: "delete" },
        { text: "Staff record", value: "record" },
        { text: "Arrival Time", value: "arrival_time" },
        { text: "Leaving Time", value: "leaving_time" },
      ],
      employees: [],
      editedIndex: -1,
      editedItem: {
        name: "",
        age: 0,
        phone: 0,
        started_date: "",
        fired: 0,
        id: 0,
        role: "",
        leaving_time: "22:00",
      },
      defaultItem: {
        name: "",
        age: 0,
        phone: 0,
        started_date: "",
        fired: 0,
        id: 0,
        role: "",
        leaving_time: "22:00",
      },
    };
  },
  computed: {
    formTitle() {
      return this.editedIndex === -1 ? "New Employee" : "Edit Employee";
    },
  },
  watch: {
    dialog(val) {
      val || this.close();
    },
    dialogDelete(val) {
      val || this.closeDelete();
    },
  },
  created() {
    // Fetching the data
    this.initialize();
  },
  methods: {
    // Fetching data from local Json file mimicking the api call from DB
    // For editing the staff_members.json file, it will be found in public directory
    async initialize() {
      try {
        const response = await fetch("staff_members.json");
        let employeesData = await response.json();

        this.employees = await employeesData.map((item, index) => {
          // adding id property to every employee
          item.id = index + 1;

          // Calculating age of employee from birth date
          const date = moment(new Date());
          item.age = date.diff(item.birth_date, "years");
          const formattedWorkingDate = moment(item.started_date).format(
            "MMMM,YYYY"
          );
          // item.started_date=formattedWorkingDate
          return { ...item };
        });
      } catch (error) {
        throw new Error(error);
      }
    },
    editItem(item) {
      this.editedIndex = this.employees.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialog = true;
    },
    deleteItem(item) {
      this.editedIndex = this.employees.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialogDelete = true;
    },
    deleteItemConfirm() {
      this.employees.splice(this.editedIndex, 1);
      this.closeDelete();
    },
    close() {
      this.dialog = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },
    closeDelete() {
      this.dialogDelete = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },
    save() {
      if (this.editedIndex > -1) {
        Object.assign(this.employees[this.editedIndex], this.editedItem);
      } else {
        this.editedItem.id = this.employees.length + 1;
        this.employees.push(this.editedItem);
      }
      this.close();
    },
  },
};
</script>
