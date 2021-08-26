<template>
  <v-main id="main" class="pa-0 ma-0">
    <v-container class="main_body d-flex flex-column justify-space-between">
      <div class="px-sm-4 px-3">
        <v-row align="center" class="header py-lg-5">
          <v-col cols="6" class="text-left px-0">
            <img
              src="~/assets/img/cello_logo_transparent.png"
              width="210"
              height="65"
              alt="logo"
              class="logo-img"
            />
          </v-col>
          <v-col
            cols="6"
            class="d-flex px-0 align-center justify-end user-avatar"
          >
            <v-btn class="mx-2" fab small color="success">
              <v-icon color="grey darken-4" dense>
                mdi-account
              </v-icon>
            </v-btn>

            <v-btn
              tile
              outlined
              color="white"
              class="ml-sm-4 ml-1 px-sm-7 py-sm-4 px-4 py-2 login-btn"
              >LOGIN</v-btn
            >
          </v-col>
        </v-row>
      </div>
      <div class="wizard-panel">
        <v-stepper
          v-model="curr"
          vertical
          color="red"
          class="d-flex flex-column justify-space-between"
        >
          <div class="step-point-line">
            <v-stepper-step
              v-for="(step, n) in questions"
              :key="n"
              :complete="stepComplete(n + 1)"
              :step="n + 1"
              :color="stepStatus(n + 1)"
              complete-icon=" "
              class="py-0 pr-0"
            >
            </v-stepper-step>
            <nav />
          </div>
          <v-stepper-content
            v-for="(step, n) in questions"
            :key="'key-' + n"
            :step="n + 1"
            class="my-0"
          >
            <div class="question-panel">
              <v-row justify="center" class="question-mark-panel">
                <img
                  src="~/assets/img/Q-mark.png"
                  class="question-mark"
                  alt="question-mark"
                />
              </v-row>
              <perfect-scrollbar>
                <div class="mt-5 question-panel__text-area">
                  <v-row class="question-area mx-0" justify="center">
                    <h2 class="mt-sm-5">
                      {{ step.question }}
                    </h2>
                  </v-row>
                  <v-row class="hint-area mx-0" justify="center">
                    <p class="mt-sm-7">
                      {{ step.hint }}
                    </p>
                  </v-row>
                </div>
              </perfect-scrollbar>
            </div>
            <div
              class="answer-panel d-flexq text-center flex-column"
              justify="center"
            >
              <input
                v-model="answers[n]"
                placeholder="Type your answer here"
                class="row px-5 py-2 mb-sm-12 mt-sm-10 mt-0 mb-7 text-center"
                @keyup="keypress"
                ref="answerInput"
              />
              <v-btn
                v-if="n + 1 < questions.length"
                color="success"
                tile
                class="px-sm-7 py-sm-5 px-5 py-3 text-capitalize"
                depressed
                @click="nextStep(n + 2)"
                >Submit</v-btn
              >
              <v-btn
                v-else
                color="success"
                class="px-sm-7 py-sm-5 px-5 py-3 text-capitalize"
                tile
                depressed
                @click="done()"
                >Finish</v-btn
              >
            </div>
          </v-stepper-content>
        </v-stepper>
      </div>
      <div class="px-sm-4 footer-panel">
        <v-row class="footer flex-column my-0 px-sm-0 px-4">
          <v-divider color="white" />
          <v-col cols="12" class="px-0">
            <v-row>
              <v-col cols="6" class="text-left py-sm-2 py-0">
                <h3 class="timer white--text text-center">
                  <div id="timer" class="d-flex">
                    <span id="minutes" class="min-box pr-3"
                      >{{ minutes }}
                      <p>min</p></span
                    >
                    <span id="seconds" class="sec-box pl-3"
                      >{{ seconds }}
                      <p>secs</p></span
                    >
                  </div>
                </h3>
              </v-col>
              <v-col cols="6" class="text-right py-sm-2 py-0 pr-10">
                <img
                  src="~/assets/img/footer_logo_transparent.png"
                  width="100"
                  height="100"
                  class="footer-logo"
                  alt="footer-logo"
                />
              </v-col>
            </v-row>
          </v-col>
        </v-row>
      </div>
      <v-alert
        tile
        prominent
        min-height="50"
        min-width="350"
        type="success"
        origin="bottom-right"
        transition="slide-x-reverse-transition"
        mode="in-out"
        class="submit-alert"
        ref="submitAlert"
        :value="alertState"
      >
        Your answers submitted
      </v-alert>
      <v-alert
        tile
        prominent
        min-height="50"
        min-width="350"
        type="warning"
        origin="bottom-right"
        transition="slide-x-reverse-transition"
        mode="in-out"
        class="warn-alert"
        :value="warnAlertState"
      >
        Please type answer
      </v-alert>
      <v-dialog v-model="dialog" width="500px" max-width="90%">
        <v-card color="red" class="px-5 py-3">
          <p v-for="(answer, n) in answers" :key="n" class="mt-5 white--text">
            Answer{{ n + 1 }} : {{ answer }}
          </p>
        </v-card>
      </v-dialog>
    </v-container>
  </v-main>
</template>

<script>
import { PerfectScrollbar } from "vue2-perfect-scrollbar";
import "vue2-perfect-scrollbar/dist/vue2-perfect-scrollbar.css";

// Vue.use(PerfectScrollbar);

export default {
  components: {
    PerfectScrollbar
  },
  data: function() {
    return {
      curr: 1,
      alertState: false,
      warnAlertState: false,
      timer: null,
      totalTime: 15 * 60,
      resetButton: false,
      title: "Let the countdown begin!!",
      answers: [],
      dialog: false,
      questions: [
        {
          question: "1: How will a decentralized aptent taciti sociosqu?",
          hint:
            "Duis hendrerit nisi ut purus semper, sit amet sodales ipsum tincidunt."
        },
        {
          question: "2: How will a decentralized aptent taciti sociosqu?",
          hint:
            "Duis hendrerit nisi ut purus semper, sit amet sodales ipsum tincidunt."
        },
        {
          question: "3: How will a decentralized aptent taciti sociosqu?",
          hint:
            "Duis hendrerit nisi ut purus semper, sit amet sodales ipsum tincidunt."
        },
        {
          question: "4: How will a decentralized aptent taciti sociosqu?",
          hint:
            "Duis hendrerit nisi ut purus semper, sit amet sodales ipsum tincidunt."
        },
        {
          question: "5: How will a decentralized aptent taciti sociosqu?",
          hint:
            "Duis hendrerit nisi ut purus semper, sit amet sodales ipsum tincidunt."
        }
      ]
    };
    s;
  },
  mounted() {
    this.startTimer();
  },
  computed: {
    minutes() {
      const minutes = Math.floor(this.totalTime / 60);
      return this.padTime(minutes);
    },
    seconds() {
      const seconds = this.totalTime - this.minutes * 60;
      return this.padTime(seconds);
    }
  },
  methods: {
    keypress(e) {
      if (e.keyCode === 13) {
        console.log(this.curr, this.questions.length);
        if (this.curr == this.questions.length) {
          this.done();
        } else {
          this.nextStep(this.curr + 1);
        }
      }
    },
    handle(value) {
      this.curr = value;
    },
    stepComplete(step) {
      return this.curr > step;
    },
    stepStatus(step) {
      return this.curr > step ? "rgb(100,214,121)" : "rgb(242,288,75)";
    },
    done() {
      if (!this.answers[this.curr - 1]) {
        this.warnAlertState = true;
        setTimeout(() => {
          this.warnAlertState = false;
        }, 3000);
      } else {
        this.alertState = true;
        this.dialog = true;
        setTimeout(() => {
          this.alertState = false;
          this.curr = 1;
          this.dialog = false;
          this.answers = [];
          this.resetTimer();
          this.startTimer();
        }, 3000);
      }
    },
    nextStep(step) {
      if (!this.answers[this.curr - 1]) {
        this.warnAlertState = true;
        setTimeout(() => {
          this.warnAlertState = false;
        }, 3000);
      } else {
        this.curr = step;
        this.resetTimer();
        this.startTimer();
      }
    },
    startTimer() {
      this.timer = setInterval(() => this.countdown(), 1000);
      this.resetButton = true;
      this.title = "Greatness is within sight!!";
    },
    stopTimer() {
      clearInterval(this.timer);
      this.timer = null;
      this.resetButton = true;
      this.title = "Never quit, keep going!!";
    },
    resetTimer() {
      this.totalTime = 15 * 60;
      clearInterval(this.timer);
      this.timer = null;
      this.resetButton = false;
      this.title = "Let the countdown begin!!";
    },
    padTime(time) {
      return (time < 10 ? "0" : "") + time;
    },
    countdown() {
      if (this.totalTime >= 1) {
        this.totalTime--;
      } else {
        this.totalTime = 0;
        this.resetTimer();
      }
    }
  }
};
</script>

<style>
.v-stepper__step.v-stepper__step--inactive > span {
  background: rgb(130, 129, 129) !important;
}

.theme--light.v-stepper .v-stepper__step__step {
  color: transparent !important;
}

.v-stepper__step__step {
  width: 14px !important;
  height: 14px !important;
  min-width: 14px !important;
  margin-right: 0 !important;
}
</style>

<style scoped>
#main {
  background: url(~/assets/img/celo_dequest_bg_centred.jpg);
  background-size: 100% 100%;
  height: 100vh;
  width: 100%;
}
.main_body {
  height: 100%;
}
.v-application--is-ltr .v-stepper--vertical .v-stepper__content {
  margin: -8px 36px -16px -36px;
  border-left: 0 !important;
}
.v-sheet.v-stepper {
  background: transparent;
}
.timer {
  font-size: 1.5rem;
  width: 90px;
}
.timer-rate {
  margin-top: -10px;
  font-size: 1.2rem;
  width: 90px;
}
.footer-logo {
  margin-top: -50px;
  position: relative;
  z-index: 10;
}
.submit-alert,
.warn-alert {
  position: absolute;
  bottom: 0;
  right: 0;
  z-index: 11;
}
.ps {
  height: 250px;
}
.v-application--is-ltr .theme--light.v-stepper--vertical .v-stepper__content {
  position: absolute;
  width: 100%;
  top: 45%;
  transform: translate(0, -50%);
}
.wizard-panel {
  flex: 1;
}
.wizard-panel > div {
  height: 100%;
  background: none;
  box-shadow: none !important;
}

.v-stepper__step {
  flex-direction: row-reverse;
}
.v-stepper__step {
  z-index: 10;
}
.btn-submit {
  z-index: 10;
}
.question-panel,
.answer-panel {
  position: relative;
  z-index: 10;
}
.question-mark-panel {
  margin-top: 20px;
}
.question-area h2 {
  width: 50%;
  font-size: 45px;
  color: white;
  text-align: center;
}
.hint-area p {
  width: 50%;
  color: white;
  text-align: center;
  font-size: 18px;
}
.answer-panel input {
  font-family: EBGaramond;
  font-size: 25px;
  margin: auto;
  color: rgb(180, 179, 179);
  border-bottom: 2px solid rgb(134, 136, 138);
}
.answer-panel button {
  font-family: Jost;
  font-size: 20px;
}
.login-btn {
  font-family: Jost;
}
.answer-panel input:focus-visible {
  outline: none;
}
.step-point-line {
  padding-top: 100px;
  padding-right: 50px;
  position: relative;
  height: 100%;
  display: flex;
  justify-content: space-between;
  flex-direction: column;
  padding-bottom: 100px;
}
.question-mark-panel img {
  max-width: 105px;
}
.step-point-line nav {
  height: calc(100% - 200px);
  width: 2px;
  background-color: rgb(106, 109, 112);
  position: absolute;
  top: 100px;
  right: 56px;
}
.min-box,
.sec-box {
  font-family: Jost;
  font-size: 28px;
}
.min-box > p,
.sec-box > p {
  font-size: 20px;
  line-height: 50%;
}
.question-area > *,
.hint-area > * {
  font-family: EBGaramond;
}
@media (max-width: 1263px) {
  .question-area h2 {
    width: 80%;
  }
}

@media (max-width: 940px) {
  .v-application--is-ltr .theme--light.v-stepper--vertical .v-stepper__content {
    padding-right: 30px;
  }
}

@media (max-width: 768px) {
  .footer-panel {
    display: anone;
  }
  .v-application--is-ltr .theme--light.v-stepper--vertical .v-stepper__content {
    top: 50%;
  }
  .question-mark-panel {
    width: 20%;
    margin: auto;
  }
  .question-mark-panel img {
    width: 100%;
  }
  .logo-img {
    width: 70%;
    min-width: 150px;
    height: auto;
  }
  .user-avatar img {
    width: 40px;
    height: 40px;
  }
  .main_body {
    padding-bottom: 0;
  }
  .footer-logo {
    width: 25%;
    height: auto;
    min-width: 60px;
    margin-top: -30px;
  }
  .min-box,
  .sec-box {
    font-size: 25px;
  }
  .min-box > p,
  .sec-box > p {
    font-size: 15px;
  }
}

@media (max-width: 650px) {
  .step-point-line {
    padding-right: 30px;
  }
  .step-point-line nav {
    right: 36px;
  }
  .v-application--is-ltr .theme--light.v-stepper--vertical .v-stepper__content {
    padding-right: 0px;
  }
  .question-mark-panel {
    width: 25%;
  }
  .ps {
    height: 210px;
  }
}

@media (max-width: 540px) {
  .question-mark-panel {
    width: 20%;
  }
  .question-area h2 {
    width: 90%;
    font-size: 35px;
  }
  .hint-area p {
    width: 75%;
    font-size: 15px;
  }
  .answer-panel input {
    font-size: 20px;
  }
  .answer-panel button {
    font-size: 17px;
  }
}

@media (max-width: 360px) {
  .question-area h2 {
    font-size: 30px;
  }
  .step-point-line {
    padding-right: 10px;
  }
  .step-point-line nav {
    right: 16px;
  }
}
</style>
