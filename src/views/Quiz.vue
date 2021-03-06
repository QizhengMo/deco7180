<template>
  <v-container id="root">
    <MyHeader></MyHeader>

    <v-container v-if="questions.length != 0" id="main">
      <div v-if="!finished">
        <div id="quiz-title" class="content-box">
          <h2>Quiz</h2>
          <h2>Current Score: {{ currentScore }}</h2>
        </div>

        <div id="quiz-questions" class="content-box">
          <h2>
            Q{{ currentQuestion }}: {{ questions[currentQuestion - 1][2] }}
          </h2>

          <v-btn block class="white--text" color="#03999e" v-on:click="answerA">
            {{ questions[currentQuestion - 1][0] }}
          </v-btn>

          <v-btn block class="white--text" color="#03999e" v-on:click="answerB">
            {{ questions[currentQuestion - 1][1] }}
          </v-btn>
        </div>
      </div>

      <div v-else>
        <div id="quiz-result" class="content-box">
          <h1>Congratulations!</h1>
          <h2>Your Score: {{ currentScore }} / 8</h2>
          <h2>Your Highest Score: {{ highScore }}</h2>

          <v-btn
          class="white--text"
          color="#03999e"
          x-large
          @click="retry"
          >
            Retry
          </v-btn>
        </div>
      </div>
    </v-container>
  </v-container>
</template>


<script>
import MyHeader from "../components/MyHeader";

function shuffle(a) {
  for (let i = a.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [a[i], a[j]] = [a[j], a[i]];
  }
  return a;
}

function myRandom(range, notMe) {
  var result = Math.floor(Math.random() * Math.floor(range));
  if (result != notMe) {
    return result;
  } else {
    return myRandom(range, notMe);
  }
}

export default {
  name: "About",

  data: () => ({
    currentQuestion: 1,
    currentScore: 0,
    highScore: 0,
    finished: false,
  }),

  computed: {
    arts() {
      return this.$store.getters.arts().length != 0
        ? this.$store.getters.arts()
        : [];
    },

    /**
     * Generate questions for the quiz, this will work as computed data
     * because VUE will cache the computed result if the dependency remain the same
     */
    questions() {
      if (this.arts.length == 0) {
        return [];
      } else {
        let questions = [];
        for (var i = 0; i < this.arts.length; i++) {
          var correctIndex = Math.floor(Math.random() * Math.floor(2));
          var question = [];
          question[correctIndex] = `${this.arts[i]["Artist"]}`;
          question[1 - correctIndex] = `${this.arts[myRandom(this.arts.length, i)]["Artist"]}`;
          question[2] = `Who is the [ARTIST] of ${this.arts[i]["Item_title"]}?`;
          question[3] = correctIndex;

          questions.push(question);

          correctIndex = Math.floor(Math.random() * Math.floor(2));
          question = [];
          question[correctIndex] = `${this.arts[i]["Material"]}`;
          question[1 - correctIndex] = `${this.arts[myRandom(this.arts.length, i)]["Material"]}`;
          question[2] = `What is the [MATERIAL] of ${this.arts[i]["Item_title"]}?`;
          question[3] = correctIndex;

          questions.push(question);

          correctIndex = Math.floor(Math.random() * Math.floor(2));
          question = [];
          question[correctIndex] = `${this.arts[i]["Installed"]}`;
          question[1 - correctIndex] = `${Math.floor(Math.random() * Math.floor(120)) + 1900}`;
          question[2] = `Which is the installation [YEAR] of ${this.arts[i]["Item_title"]}?`;
          question[3] = correctIndex;

          questions.push(question);
        }
        shuffle(questions);
        return questions;
      }
    },
  },

  mounted() {
    if (this.arts.length == 0) {
      this.$store.dispatch("fetchArts");
    }
  },

  methods: {
    answerA() {
      if (this.questions[this.currentQuestion][3] === 0) {
        this.currentScore += 1;
      }
      this.currentQuestion += 1;
      this.checkFinished();
    },

    answerB() {
      if (this.questions[this.currentQuestion][3] === 1) {
        this.currentScore += 1;
      }
      this.currentQuestion += 1;
      this.checkFinished();

    },

    checkFinished() {
      if (this.currentQuestion === 9) {
        this.finished = true;
        if (this.currentScore > this.highScore) {
          this.highScore = this.currentScore;
        }
      }
    },
    retry() {
      shuffle(this.questions);
      this.currentQuestion = 1;
      this.currentScore = 0;
      this.finished = false;
    }
  },

  components: {
    MyHeader,
  },
};
</script>



<style scoped>
#root {
  height: 100vh;
  width: 100vw;
  min-width: 100vw;
  min-height: 100vh;
  padding: 0px;
  margin: 0px;
  background-image: url("../assets/about.jpg");
  background-position: center;
  background-size: cover;
}

#main {
  height: 100%;
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: start;
  align-items: center;
  padding-top: 15vh;
}

.content-box {
  width: 50vw;
  display: flex;
  align-items: center;
  justify-content: center;
  background: white;
  box-shadow: 10px 10px #03999e;
  border: solid 2px #03999e;
}

#quiz-title {
  height: 10vh;
  padding-left: 2em;
  padding-right: 2em;
  justify-content: space-between;
}

#quiz-questions {
  height: 50vh;
  margin-top: 5vh;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  padding: 30px;
}

#quiz-title h1 {
  color: black;
}

#quiz-result {
  height: 30vh;
  margin-top: 5vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 30px;

}
button {
  margin-top: 50px;
}
</style>