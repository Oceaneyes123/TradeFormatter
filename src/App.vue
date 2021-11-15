<template>
  <v-app>
    <v-container class="fill-height">
      <v-row justify="center">
        <v-col cols="5" class="d-flex flex-column">
          <div class="headline font-weight-bold blue--text mb-5 text-center">
            TRADE FORMATTER
          </div>
          <v-textarea outlined v-model="message"></v-textarea>
          <v-btn color="primary" class="mx-auto" @click="format()"
            >Format</v-btn
          >
          <v-card class="pa-5" color="blue lighten-3 mt-7 rounded-xl" flat>
            <div>{{ symbol }}</div>
            <div>{{ direction }}</div>
            <div>{{ entryPrice }}</div>
            <div>{{ stopLoss }}</div>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
  </v-app>
</template>

<script>
  import axios from "axios";
  export default {
    name: "App",

    data: () => ({
      //
      message: "",
      list_of_symbols: [
        "GOLD",
        "XAUUSD",
        "AUDCAD",
        "AUDCHF",
        "AUDJPY",
        "AUDNZD",
        "AUDUSD",
        "CADCHF",
        "CADJPY",
        "CHFJPY",
        "EURAUD",
        "EURCAD",
        "EURCHF",
        "EURGBP",
        "EURJPY",
        "EURNZD",
        "EURUSD",
        "GBPAUD",
        "GBPCAD",
        "GBPCHF",
        "GBPJPY",
        "GBPNZD",
        "GBPUSD",
        "NZDCAD",
        "NZDJPY",
        "NZDUSD",
        "USDCAD",
        "USDCHF",
        "USDJPY",
        "XAUUSD",
      ],
      token: "2049698541:AAFkyDh73K2ymXXxFi7T2YEOT8ZjIrIc2wI",
      chat_id: "-1001255977074",
      symbol: "",
      sentence: "",
      direction: "",
      stopLoss: 0,
      takeProfit: 0,
      entryPrice: 0,
      finalNumbers: [],
    }),

    methods: {
      format() {
        this.sentence = this.message;
        for (var i = 0; i < this.list_of_symbols.length; i++) {
          var tempSymbol = this.list_of_symbols[i];

          //tempsymbol to lower case
          tempSymbol = tempSymbol.toLowerCase();

          //if tempSymbol is present in the sentence, set symbol to tempSymbol
          if (this.sentence.toLowerCase().includes(tempSymbol)) {
            this.symbol = tempSymbol;
          }
        }

        var numbers = this.sentence.match(/\d+\.?\d*/g);

        if (this.sentence.toLowerCase().includes("buy")) {
          this.direction = "buy";
        } else if (this.sentence.toLowerCase().includes("sell")) {
          this.direction = "sell";
        }

        if (this.direction == "buy") {
          numbers.sort(function (a, b) {
            return a - b;
          });
        } else {
          numbers.sort(function (a, b) {
            return b - a;
          });
        }

        if (this.symbol == "xauusd" || this.symbol == "gold") {
          for (var a = 0; a < numbers.length; a++) {
            if (
              numbers[a] != 1 &&
              numbers[a] != 2 &&
              numbers[a] != 3 &&
              numbers[a] != 4
            ) {
              this.finalNumbers.push(numbers[i]);
            }
          }
        } else {
          for (var b = 0; b < numbers.length; b++) {
            if (numbers[b].includes(".")) {
              this.finalNumbers.push(numbers[b]);
            }
          }
        }

        this.stopLoss = this.finalNumbers[0];

        //check if sentence contains dash
        if (this.sentence.toLowerCase().includes("-")) {
          this.entryPrice = this.finalNumbers[2];
        } else {
          this.entryPrice = this.finalNumbers[1];
        }

        this.symbol = this.symbol.toUpperCase();

        //if symbol is XAUUSD change to GOLD
        if (this.symbol == "XAUUSD") {
          this.symbol = "GOLD";
        }

        //direction to upper case
        this.direction = this.direction.toUpperCase();

        console.log(
          this.symbol,
          this.entryPrice,
          this.stopLoss,
          this.direction
        );

        //send data through axios
        axios
          .post(
            "https://api.telegram.org/bot" +
              this.token +
              "/sendMessage?chat_id=" +
              this.chat_id +
              "&text=" +
              this.symbol +
              "%0a" +
              this.direction +
              "%0a" +
              this.entryPrice +
              "%0a" +
              this.stopLoss
          )
          .then(function (response) {
            console.log(response);
          })
          .catch(function (error) {
            console.log(error);
          });
      },
    },
  };
</script>
