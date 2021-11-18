<template>
  <v-app>
    <v-container class="fill-height">
      <v-row justify="center">
        <v-col cols="10" md="5">
          <div class="mb-2">
            Lets buy Eurjpy @129.961 - 129.761 TP 1 : 130.161 TP 2 : 130.361
            SL❗️ 129.361
          </div>
          <div class="mb-2">
            GOLD SELL NOW @ 1860.50-1862.50 sl @ 1864.50 tp 1 : 1858.50 tp 2 :
            1855.50 tp 3 : 1850.50
          </div>
          <div class="mb-2">
            AUDNZD SELL SL 1.04700 (25 pips) TP1 1.04300 (20 pips) TP2 1.04100
            (40 pips) TP3 1.03900 (60 pips) TP4 1.03500 (100 pips)
          </div>
          <div class="mb-6">
            EURAUD BUY NOW @ 1.55250- @ 1.55000 TP 1 : 1.55450 TP 2 : 1.55750 TP
            3 : 1.56250 TP 4 : HOLD SL : 1.54800 Use suitable lot sizes based on
            your capital. Money management is key.
          </div>

          <div class="headline font-weight-bold blue--text mb-5 text-center">
            TRADE FORMATTER
          </div>
          <v-textarea outlined v-model="message" class="mb-7"></v-textarea>
          <div class="d-flex justify-center">
            <v-btn
              @click="recover = true"
              :color="recover ? 'green' : ''"
              class="mr-2"
              >RECOVER</v-btn
            >
            <v-btn color="primary" class="ml-2" @click="format()">Format</v-btn>
          </div>
          <!-- <v-card
            class="pa-5"
            color="blue lighten-3 mt-7 rounded-xl"
            flat
            ref="text"
          >
            <div>{{ symbol }}</div>
            <div>{{ direction }}</div>
            <div>{{ entryPrice }}</div>
            <div>{{ stopLoss }}</div>
          </v-card>
          <v-btn color="primary" class="mx-auto mt-5" @click="copyText()"
            >COPY</v-btn
          > -->
        </v-col>
        <v-col cols="10" md="5">
          <v-card
            color="blue lighten-5"
            class="pa-5"
            max-height="500"
            style="overflow-y: scroll"
          >
            <div :key="deleteRefreshKey">
              <div v-for="(trade, i) in trades" :key="i">
                <v-container>
                  <v-row>
                    <v-col col="5" class="d-flex flex-column">
                      <v-card width="100%" class="pa-5 mb-3 d-flex">
                        <div>
                          <div>{{ trade.symbol }}</div>
                          <div>{{ trade.direction }}</div>
                          <div>{{ trade.entryPrice }}</div>
                          <div>{{ trade.stopLoss }}</div>
                          <div>{{ trade.recover }}</div>
                        </div>
                      </v-card>
                      <v-btn
                        class="mb-2 mx-auto white--text"
                        color="red darken-1"
                        @click="deleteTrade(trade)"
                      >
                        DELETE
                      </v-btn>
                    </v-col>
                    <v-col class="d-flex flex-column">
                      <v-btn
                        class="mb-2"
                        color="blue lighten-1"
                        @click="performCommand('HALF', trade)"
                      >
                        HALF
                      </v-btn>
                      <v-btn
                        class="mb-2"
                        color="teal lighten-3"
                        @click="performCommand('TAKE PROFIT', trade)"
                      >
                        TAKE PROFIT
                      </v-btn>
                      <v-btn
                        class="mb-2"
                        color="yellow lighten-3"
                        @click="performCommand('BREAKEVEN', trade)"
                      >
                        BREAKEVEN
                      </v-btn>
                      <v-btn
                        class="mb-2"
                        color="orange lighten-3"
                        @click="performCommand('CLOSE', trade)"
                      >
                        CLOSE PENDING
                      </v-btn>
                      <v-btn
                        class="mb-2"
                        color="red lighten-1"
                        @click="performCommand('CUTLOSS', trade)"
                      >
                        CUTLOSS
                      </v-btn>
                      <v-btn class="mb-2" color="green darken-1">
                        REOPEN
                      </v-btn>
                    </v-col>
                  </v-row>
                </v-container>
                <v-divider></v-divider>
              </div>
            </div>
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
      trades: [],
      deleteRefreshKey: 0,

      recover: false,

      // baseApiUrl: "http://localhost:3000/trades",
      baseApiUrl: "https://trade-api.vercel.app/trades",
    }),

    mounted() {
      this.getTrades();
    },

    methods: {
      getTrades() {
        console.log("getTrades");
        axios
          .get(this.baseApiUrl + "/all")
          .then((response) => {
            console.log(response);
            this.trades = [];
            //loop through response and add to trades array
            for (let i = 0; i < response.data.length; i++) {
              if ("recover" in response.data[i]) {
                this.trades.push(response.data[i]);
              }
            }
          })
          .catch((error) => {
            console.log(error);
          });
      },

      deleteTrade(trade) {
        console.log(trade.id);
        //axios delete
        axios
          .delete(`${this.baseApiUrl}/${trade.id}`)
          .then((response) => {
            console.log(response);
            console.log("deleted");
            this.getTrades();
          })
          .catch((error) => {
            console.log(error);
          });

        //remove trade from trades
        //  this.trades.splice(i, 1)
      },

      performCommand(command, trade) {
        console.log("sending");
        const { symbol, direction } = trade;
        //axios post to close pending
        axios
          .post(this.baseApiUrl, {
            command: command,
            direction: direction,
            symbol: symbol,
          })
          .then((response) => {
            console.log(response);
          })
          .catch((error) => {
            console.log(error);
          });
      },

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

        console.log(numbers);

        if (this.symbol == "xauusd" || this.symbol == "gold") {
          for (var x = 0; x < numbers.length; x++) {
            if (
              numbers[x] != 1 &&
              numbers[x] != 2 &&
              numbers[x] != 3 &&
              numbers[x] != 4
            ) {
              this.finalNumbers.push(numbers[x]);
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

        // this.trades.push({
        //   symbol: this.symbol,
        //   entryPrice: this.entryPrice,
        //   stopLoss: this.stopLoss,
        //   direction: this.direction,
        // });

        this.finalNumbers = [];

        // axios.post(this.baseApiUrl, "HI").then((response) => {
        //   console.log(response);
        // }).catch((error) => {
        //   console.log(error);
        // });

        axios
          .post(this.baseApiUrl, {
            symbol: this.symbol,
            entryPrice: this.entryPrice,
            stopLoss: this.stopLoss,
            direction: this.direction,
            recover: this.recover,
          })
          .then((response) => {
            this.getTrades();
            this.recover = false;
          })
          .catch(function (error) {
            console.log(error);
          });

        //send data through axios
        // axios
        //   .post(
        //     "https://api.telegram.org/bot" +
        //       this.token +
        //       "/sendMessage?chat_id=" +
        //       this.chat_id +
        //       "&text=" +
        //       this.symbol +
        //       "%0a" +
        //       this.direction +
        //       "%0a" +
        //       this.entryPrice +
        //       "%0a" +
        //       this.stopLoss
        //   )
        //   .then(function (response) {
        //     console.log(response);
        //   })
        //   .catch(function (error) {
        //     console.log(error);
        //   });
      },
    },
  };
</script>
