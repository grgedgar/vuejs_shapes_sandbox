<template>
  <div>
    <div class="container">
      <span>Translate from / Перекласти з</span>
      <select id="selectedSourceLanguage" @change="selectSourceLanguage()">
      <option value="auto">Auto detect</option>
      <option v-for="(value, name) in languagesList" :value="value" :key="value">{{name}}</option>
      </select>
      &nbsp; &nbsp;
      <span>Translate to / Перекласти на</span>
      <select id="selectedTargetLanguage" @change="selectTargetLanguage()">
        <option v-for="(value, name) in languagesList" :value="value" :key="value">{{name}}</option>
      </select>
    </div>
    <div class="container">
      <textarea class="source-text-container" v-model="sourceText" placeholder="Введіть текст для перекладу" type='text' ref='sourceText' defaultValue="" v-on:keyup="translateText"></textarea>
      <div id="targetText" class="target-text-container">{{translatedText}}</div>
    </div>
    <div class="container">
      <div id="detected-language"><i>{{autoDetectedLanguage}}</i></div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  mounted() {
  },
  methods: {
    selectSourceLanguage() {
      const e = document.getElementById('selectedSourceLanguage');
      this.selectedSourceLanguage = e.options[e.selectedIndex].value;
      this.translateText();
    },
    selectTargetLanguage() {
      const e = document.getElementById('selectedTargetLanguage');
      this.selectedTargetLanguage = e.options[e.selectedIndex].value;
      this.translateText();
    },
    translateText() {
      this.translatedText = '';
      this.autoDetectedLanguage = '';
      if (this.sourceText.length < 1) {
        return;
      }
      const encodedParams = new URLSearchParams();
      encodedParams.append('q', this.sourceText);
      encodedParams.append('target', this.selectedTargetLanguage);
      const a = document.getElementById('selectedSourceLanguage');
      if (a.options[a.selectedIndex].text !== 'Auto detect') {
        encodedParams.append('source', this.selectedSourceLanguage);
      }
      const options = {
        method: 'POST',
        url: 'https://google-translate1.p.rapidapi.com/language/translate/v2',
        headers: {
          'content-type': 'application/x-www-form-urlencoded',
          'X-RapidAPI-Host': 'google-translate1.p.rapidapi.com',
          'X-RapidAPI-Key': '0366e2ca84msh163ce4d4ecb607dp14133cjsn3aad52d5b602',
        },
        data: encodedParams,
      };
      this.$axios.request(options).then((response) => {
        if (!this.selectedSourceLanguage) {
          const e = document.getElementById('selectedSourceLanguage');
          if (e.options[e.selectedIndex].text === 'Auto detect') {
            this.autoDetectedLanguage = `Detected language — ${response.data.data.translations[0].detectedSourceLanguage}`;
          }
        }
        this.translatedText = response.data.data.translations[0].translatedText;
      }).catch((error) => {
        console.error(error);
      });
    },
  },
  data() {
    return {
      languagesList: {
        Ukrainian: 'uk',
        Amharic: 'am',
        Arabic: 'ar',
        Basque: 'eu',
        Bengali: 'bn',
        English: 'en',
        'Portuguese Brazil': 'pt-BR',
        Bulgarian: 'bg',
        Catalan: 'ca',
        Cherokee: 'chr',
        Croatian: 'hr',
        Czech: 'cs',
        Danish: 'da',
        Dutch: 'nl',
        Estonian: 'et',
        Filipino: 'fil',
        Finnish: 'fi',
        French: 'fr',
        German: 'de',
        Greek: 'el',
        Gujarati: 'gu',
        Hebrew: 'iw',
        Hindi: 'hi',
        Hungarian: 'hu',
        Icelandic: 'is',
        Indonesian: 'id',
        Italian: 'it',
        Japanese: 'ja',
        Kannada: 'kn',
        Korean: 'ko',
        Latvian: 'lv',
        Lithuanian: 'lt',
        Malay: 'ms',
        Malayalam: 'ml',
        Marathi: 'mr',
        Norwegian: 'no',
        Polish: 'pl',
        'Portuguese Portugal': 'pt-PT',
        Romanian: 'ro',
        Russian: 'ru',
        Serbian: 'sr',
        'Chinese PRC': 'zh-CN',
        Slovak: 'sk',
        Slovenian: 'sl',
        Spanish: 'es',
        Swahili: 'sw',
        Swedish: 'sv',
        Tamil: 'ta',
        Telugu: 'te',
        Thai: 'th',
        'Chinese Taiwan': 'zh-TW',
        Turkish: 'tr',
        Urdu: 'ur',
        Vietnamese: 'vi',
        Welsh: 'cy',
      },
      selectedSourceLanguage: '',
      selectedTargetLanguage: 'uk',
      translatedText: '',
      sourceText: '',
      autoDetectedLanguage: '',
      lastCall: 0,
    };
  },
};
</script>

<style>
*{
  font-family: 'Verdana', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
.container{
  text-align: center;
  margin-top: 17vh;
  display: flex;
  align-items: center;
  justify-content: center;
  height: 10vh;
}
select {
  margin: 0 10px;
}
.source-text-container, .target-text-container{
  margin: 0 20px;
  padding: 5px;
  width: 80vh;
  height: 35vh;
  border: 1px solid #2c3e50;
}
</style>
