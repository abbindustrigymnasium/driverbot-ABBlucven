<template>
  <v-app id="content">
    <v-toolbar app id="toolbar">
      <v-toolbar-title class="headline text-uppercase">
        <span>Driver</span>
        <span class="font-weight-light">Bot</span>
      </v-toolbar-title>

      <v-spacer></v-spacer>
      <v-btn color="blue lighten-2" dark @click.stop="dialog = true" span class="black--text">Anslutningsalternativ</v-btn>
      <v-btn color="pink lighten-3" target="_blank" @click="Switch=!Switch">
        <span v-if="Switch!==true" class="mr-2">Buttons 1</span>
        <span v-else class="mr-2">Buttons 2</span>
      </v-btn>
    </v-toolbar>

    <v-content>
      <v-row align="center" justify="center">
        <v-dialog v-model="dialog" max-width="290" height="290">
          <v-card>
            <v-card-title class="headline">Anslutning till Server</v-card-title>

            <v-card-text style="height: 300px;">
              <v-text-field v-model="name" label="Name" filled></v-text-field>

              <v-text-field v-model="password" label="MQTT Password" filled></v-text-field>

              <v-text-field v-model="port" label="Port" filled></v-text-field>
              <v-text-field v-model="adress" label="Adress" filled></v-text-field>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>

              <v-btn color="orange lighten-3" text @click="save()">Spara</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>

        <div>
          <Logger />

          <Buttons v-if="Switch" />
          <Buttons2 v-else />
        </div>
      </v-row>
    </v-content>
  </v-app>
</template>

<script>
import Buttons2 from "./components/Buttonsv2";
import Buttons from "./components/Buttons";
import Logger from "./components/Logger";

export default {
  name: "App",
  components: {
    Buttons,
    Buttons2,
    Logger
  },
  data() {
    return {
      Switch: false,
      dialog: false,
      name: "",
      password: "",
      port: "",
      adress: ""
    };
  },
  created() {
    let User = this.$store.getters.GetUser;
    this.name = User.name;
    this.password = User.password;
    this.port = User.port;
    this.adress = User.adress;
  },

  methods: {
    save() {
      let User = {
        name: this.name,
        password: this.password,
        port: this.port,
        adress: this.adress
      };

      this.$store.dispatch("Save", User);

      this.dialog = false;
    }
  },
  computed: {}
};
</script>
<style scoped>
.img {
  width: 40%;
  height: 40%;
  margin: auto;
  padding: 10px;
}
#logger {
  /* position: fixed;
  bottom: 0;
  right: 0; */
  width: 300px;
  height: 300px;
  border: black dotted 2px;

  word-wrap: break-word;
}

/* https://beautifuldingbats.com/pattern-generator/ https://css-tricks.com/using-svg/ */
#toolbar {
  background-image: url("https://mumintrolletartblog.files.wordpress.com/2012/01/fc3a4rgglad-bakgrund.jpg");
}
#content {
  background-image: url("data:image/jpeg;base64,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");
}
</style>
