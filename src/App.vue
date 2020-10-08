<template>
  <div id="app">
    <label for="text">
      読み上げるテキスト<br />
      <input type="text" style="width: 100%; height: 2em" v-model="text" />
    </label>
    <label
      >読み上げる音声
      <select v-model="selectedVoiceIndex" style="width: 100%; height: 2em">
        <option v-for="(voice, index) in voices" :value="index" :key="index">
          {{ voice.name }}({{ voice.lang }})
        </option>
      </select>
    </label>
    <button @click="onButtonClick" style="width: 100%; height: 2em">
      読み上げる
    </button>
    <p>{{ errorMessage }}</p>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      text: "Sample text",
      selectedVoiceIndex: 0,
      voices: [],
      errorMessage: ""
    };
  },
  mounted: function() {
    if (
      typeof speechSynthesis !== "undefined" &&
      speechSynthesis.onvoiceschanged !== undefined
    ) {
      window.speechSynthesis.onvoiceschanged = () => this.onVoiceChanged();
    } else {
      this.onVoiceChanged();
    }
  },
  methods: {
    onVoiceChanged() {
      if (typeof speechSynthesis === "undefined") {
        this.$data.errorMessage = "speechSynthesis is undefined";
        return;
      }

      const voices = speechSynthesis.getVoices();
      this.$data.voices = voices;
      this.$data.selectedVoiceIndex = 0;
    },
    onButtonClick() {
      if (typeof speechSynthesis === "undefined") {
        this.$data.errorMessage = "speechSynthesis is undefined";
        return;
      }
      const utterance = new SpeechSynthesisUtterance(this.$data.text);
      utterance.voice = this.$data.voices[this.$data.selectedVoiceIndex];
      speechSynthesis.speak(utterance);
    }
  }
};
</script>
