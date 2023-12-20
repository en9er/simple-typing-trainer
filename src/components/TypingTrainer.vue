<template>
  <div class="typing-trainer">
    <input
      type="file"
      @change="handleFileChange"
      v-if="!isTypingStarted && !isTypingCompleted"
      accept=".txt"
    />
    <div v-show="isTypingStarted || isTypingCompleted" class="typing-container">
      <h1>{{ displayWord }}</h1>
      <div class="input-container">
        <span
          v-for="(letter, index) in currentWord"
          :key="index"
          :class="getLetterClass(index)"
        >
          {{ letter === ' ' ? '⎵' : letter }}
        </span>
        <input
          v-model="typedText"
          @input="checkTyping"
          ref="textInput"
          class="invisible-input"
        />
      </div>
      <p v-if="isTypingCompleted" class="completion-message">{{ completionMessage }}</p>
      <div class="buttons">
        <button v-if="isTypingCompleted" @click="restart">Restart</button>
        <button @click="chooseNewFile">Choose New File</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      words: [],
      currentWordIndex: 0,
      currentWord: "",
      typedText: "",
      isTypingStarted: false
    };
  },
  computed: {
    isTypingCompleted() {
      return this.isTypingStarted && this.currentWordIndex === this.words.length;
    },
    completionMessage() {
      return this.isTypingCompleted ? "Congratulations! You completed the typing trainer." : "";
    },
    displayWord() {
      return this.isTypingCompleted ? "" : this.currentWord;
    }
  },
  methods: {
    handleFileChange(event) {
      const file = event.target.files[0];
      if (file) {
        this.readFile(file);
        this.isTypingStarted = true;
      }
    },
    async readFile(file) {
      try {
        const content = await this.readFileContent(file);
        this.words = content.split("\n").filter(word => word.trim() !== "");
        this.setNextWord();
      } catch (error) {
        console.error("Error reading file:", error);
      }
    },
    readFileContent(file) {
      return new Promise((resolve, reject) => {
        const reader = new FileReader();
        reader.onload = event => resolve(event.target.result);
        reader.onerror = error => reject(error);
        reader.readAsText(file);
      });
    },
    setNextWord() {
      this.currentWord = this.words[this.currentWordIndex] + '\n';
      this.typedText = "";
      this.$refs.textInput.focus();
    },
    checkTyping() {
      if (this.typedText === this.currentWord.trim()) {
        this.currentWordIndex++;
        if (this.currentWordIndex < this.words.length) {
          this.setNextWord();
        }
      }
    },
    getLetterClass(index) {
      return {
        correct: this.typedText[index] === this.currentWord[index],
        incorrect: this.typedText[index] && this.typedText[index] !== this.currentWord[index]
      };
    },
    restart() {
      this.currentWordIndex = 0;
      this.setNextWord();
    },
    chooseNewFile() {
      this.isTypingStarted = false;
      this.words = [];
      this.currentWordIndex = 0;
      this.currentWord = "";
      this.typedText = "";
    }
  }
};
</script>

<style scoped>
.typing-trainer {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100vh;
}

.typing-container {
  max-width: 600px;
  text-align: center;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 10px;
  background-color: #f9f9f9;
}

h1 {
  font-size: 24px;
  margin-bottom: 10px;
}

.input-container {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 20px;
}

span {
  display: inline-block;
  width: 24px;
  text-align: center;
  font-size: 18px;
  font-weight: bold;
  margin-right: 4px;
}

.invisible-input {
  opacity: 0;
  width: 0;
  height: 0;
  overflow: hidden;
  border: none;
  outline: none;
}

.correct {
  color: green;
}

.incorrect {
  color: red;
}

.completion-message {
  margin-top: 20px;
  color: green;
  font-weight: bold;
}

.buttons {
  display: flex;
  justify-content: center;
}

button {
  margin-top: 10px;
  margin-right: 10px;
  padding: 10px 20px;
  font-size: 16px;
  cursor: pointer;
  background-color: #4caf50;
  color: #fff;
  border: none;
  border-radius: 5px;
  transition: background-color 0.3s;
}

button:hover {
  background-color: #45a049;
}
</style>
