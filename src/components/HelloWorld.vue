<template>
  <v-container>
    <v-data-table
      v-model="selected"
      :headers="headers"
      :items="matchScores"
      item-key="team_name"
      show-select
      class="elevation-1"
      :sort-by.sync="sortBy"
      :sort-desc.sync="sortDesc"
      :loading="loading"
      loading-text="Updating the result..."
      :search="search"
      fixed-header
      height="500px"
      light
      flat
    >
      <template v-slot:top>
        <v-toolbar flat>
          <v-toolbar-title>Match Score</v-toolbar-title>
          <v-divider class="mx-4" inset vertical></v-divider>
          <v-spacer></v-spacer>
          <v-dialog v-model="dialog" max-width="500px">
            <template v-slot:activator="{ on, attrs }">
              <v-row>
                <v-col cols="9">
                  <v-text-field
                    v-model="search"
                    append-icon="mdi-magnify"
                    label="Search"
                    single-line
                    hide-details
                  ></v-text-field>
                </v-col>
                <v-col cols="3" float="right">
                  <v-btn color="success" class="mb-2" v-bind="attrs" v-on="on">
                    New Team
                  </v-btn>
                </v-col>
              </v-row>
            </template>
            <v-card>
              <v-card-title class="headline grey lighten-2">
                ADD NEW TEAM
              </v-card-title>
              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col>
                      <v-text-field
                        v-model="editedItem.team_name"
                        label="Team Name"
                      ></v-text-field>
                    </v-col>
                  </v-row>
                </v-container>
              </v-card-text>

              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="close">
                  Cancel
                </v-btn>
                <v-btn color="blue darken-1" text @click="save"> Save </v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-toolbar>
      </template>
    </v-data-table>
    <v-dialog persistent v-model="dialog1" max-width="550px">
      <v-card>
        <v-card-title class="headline grey lighten-2">
          Select the winner
        </v-card-title>
        <div class="px-6 pt-4 red--text">
          *** Don't select anything and click save in case of tie ****
        </div>
        <v-card-text>
          <v-container>
            <v-radio-group v-model="radioGroup">
              <v-radio
                v-for="(val, index) in selected"
                :key="index"
                :label="` ${val.team_name}`"
                :value="index"
              ></v-radio>
            </v-radio-group>
          </v-container>
        </v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="close1"> Cancel </v-btn>
          <v-btn color="blue darken-1" text @click="save1"> Save </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-snackbar v-model="snackbar">
      {{ text }}

      <template v-slot:action="{ attrs }">
        <v-btn color="pink" text v-bind="attrs" @click="snackbar = false">
          Close
        </v-btn>
      </template>
    </v-snackbar>
  </v-container>
</template>

<script>
import axios from "axios";
export default {
  name: "HelloWorld",
  watch: {
    selected(val) {
      if (val.length == 2) {
        this.dialog1 = true;
        this.radioGroup = -1;
      }
    },
  },
  mounted() {
    this.loading = true;
    axios.get(this.url + "teams").then((res) => {
      this.matchScores = res.data;
      this.loading = false;
    });
  },
  data: () => ({
    url: "https://cryptic-beach-94850.herokuapp.com/",
    singleSelect: false,
    dialog1: false,
    sortBy: "score",
    loading: false,
    snackbar: false,
    text: "",
    sortDesc: true,
    search: "",
    selected: [],
    radioGroup: "",
    headers: [
      {
        text: "Team name",
        align: "start",
        sortable: false,
        value: "team_name",
      },
      { text: "Wins", value: "wins" },
      { text: "Loss", value: "losses" },
      { text: "Ties", value: "ties" },
      { text: "Score", value: "score" },
    ],
    editedIndex: -1,
    editedItem: {
      team_name: "",
    },
    defaultItem: {
      team_name: "",
    },
    matchScores: [],
    dialog: false,
  }),
  methods: {
    showErrorMessage(msg) {
      this.loading = false;
      this.snackbar = true;
      this.text = msg;
    },
    save() {
      this.loading = true;
      axios
        .post(this.url + "addNewTeam", this.editedItem)
        .then((res) => {
          this.loading = false;
          this.matchScores = res.data;
        })
        .catch((err) => {
          this.showErrorMessage(err.response.data.message);
        });
      this.close();
    },
    close1() {
      this.dialog1 = false;
      this.radioGroup = "";
      this.selected = [];
    },
    save1() {
      this.dialog1 = false;
      let payload = {};
      if (this.radioGroup != -1) {
        payload.winner = this.selected[this.radioGroup].team_name;
        payload.loser = this.selected[!this.radioGroup ? 1 : 0].team_name;
      } else {
        payload.ties = this.selected.map((v) => v.team_name);
      }
      this.loading = true;
      axios
        .post(this.url + "matchResult", payload)
        .then((res) => {
          this.matchScores = res.data;
          this.loading = false;
        })
        .catch((err) => {
          this.showErrorMessage(err.response.data.message);
        });
      this.close1();
    },
    close() {
      this.dialog = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },
  },
};
</script>


<style scoped>
thead.v-data-table-header {
  display: none;
}
</style>