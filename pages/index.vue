<template>
  <div class="bg-gray-900 min-h-screen flex justify-center">
    <div class="bg-gray-50 w-96 shadow-xl text-2xl">
      <section
        class="rounded shadow-xl m-2 p-2 justify-center flex"
        :class="[isSolved ? 'bg-green-500' : 'bg-gray-500 ']"
      >
        <span
          v-for="(encodedChar, charIndex) in codeWordSplit"
          :key="encodedChar + charIndex"
          class="relative min-w-[2rem]"
        >
          <transition
            enter-from-class="scale-150 opacity-0"
            leave-to-class="scale-50 opacity-0"
            enter-active-class="transition duration-300 absolute"
            leave-active-class="transition duration-300 absolute"
          >
            <span v-if="characterISDecoded(encodedMap[encodedChar])" key="1">{{
              decodedMap[encodedMap[encodedChar]]
            }}</span>
            <span v-else key="2">
              {{ encodedMap[encodedChar] }}
            </span>
          </transition>
        </span>
      </section>
      <section class="grid grid-cols-2 gap-4 place-items-center m-4">
        <span
          v-for="(encodedChar, decodedChar, charIndex) in encodedMap"
          :key="(encodedChar, decodedChar, charIndex)"
        >
          <span class="rounded-l-xl p-2 bg-gray-500 shadow-xl w-12">
            {{ encodedChar }}
          </span>
          <input
            type="text"
            v-model="decodedMap[encodedChar]"
            maxlength="1"
            class="
              rounded-r-xl
              p-2
              bg-gray-900
              text-gray-50
              w-12
              transition
              duration-500
            "
            :class="[
              characterISCorrectlyDecoded(encodedChar, decodedMap[encodedChar])
                ? 'bg-green-500'
                : '',
            ]"
            @input="assembleDecodedWord()"
          />
        </span>
      </section>
      <button
        @click="
          getHint();
          tapHighlight($event);
        "
        class="rounded-full p-2 m-2 transition"
        :disabled="!hintAvailable"
        :class="[hintAvailable ? 'bg-blue-500' : 'bg-gray-300 text-gray-500']"
      >
        Hinweis
      </button>
    <div>Errate das Wort. Alle Buchstaben sind groß geschrieben.</div>
    </div>
    <full-page v-if="isSolved">
      <h1 class="font-2xl font-bold">Super!</h1>
      Gib mir ein neues Wort. Schwierigkeit:
      <div>
        <button
          @click="
            tapHighlight($event);
            initWithRandomWord('easy');
          "
          class="rounded-full p-2 m-2 transition bg-green-100"
        >
          einfach
        </button>
        <button
          @click="
            tapHighlight($event);
            initWithRandomWord('hard');
          "
          class="rounded-full p-2 m-2 transition bg-red-100"
        >
          schwer
        </button>
      </div>
      <div>nein, nein, nein, gibt mir lieber mehr</div>
      <button
        @click="
          tapHighlight($event);
          $refs.confetti.fireConfetti();
        "
        class="rounded-full p-2 m-2 transition bg-blue-500"
      >
        Konfetti!
      </button>
      <confetti :confettiCount="4" ref="confetti" />
    </full-page>
  </div>
</template>

<script>
import Confetti from "~/components/Confetti.vue";
import FullPage from "~/components/FullPage.vue";

export default {
  components: { Confetti, FullPage },
  name: "IndexPage",
  data() {
    return {
      hintAvailable: true,
      mode: "easy",
      codeWords: {
        hard: ["NINJAGO", "KLETTERN", "MATHEMATIK"],
        easy: ["HI", "HALLO", "EIS"],
      },
      codeWord: "hi",
      decodedWord: "",
      isSolved: false,
      emojiTOUnicodeDict: {
        glassOFMilk: 0x1f95b,
        redHeart: 0x2764,
        faceHeart: 0x1f970,
        faceStarts: 0x1f929,
        oneHundred: 0x1f4af,
        explosion: 0x1f4a5,
        thumbsUp: 0x1f44d,
        brain: 0x1f9e0,
        personTippingHand: 0x1f481,
        guard: 0x1f482,
        ninja: 0x1f977,
        monkey: 0x1f435,
        serviceDog: 0x1f9ba,
        unicorn: 0x1f984,
        pigNose: 0x1f43d,
        sloth: 0x1f9a5,
        babyChick: 0x1f425,
        searl: 0x1f9ad,
        cherryBlossom: 0x1f338,
        cactus: 0x1f335,
        apple: 0x1f34e,
        chili: 0x1f336,
        burger: 0x1f354,
        spiegelEI: 0x1f373,
        beach: 0x1f3d6,
        slide: 0x1f6dd,
        rainbow: 0x1f308,
      },
      encodedMap: {},
      decodedMap: {},
      codeWordSplit: [],
    };
  },
  methods: {
    initWithRandomWord(difficulty) {
      const codeWords = this.codeWords[difficulty];
      const randomIndex = Math.floor(Math.random() * codeWords.length);
      const newWord = codeWords[randomIndex];
      this.codeWord = newWord;
      this.init();
    },
    getHint() {
      if (!this.hintAvailable) {
        return;
      } else {
        this.hintAvailable = false;
      }
      const encodedKeys = Object.entries(this.encodedMap);
      const randomIndex = Math.floor(Math.random() * encodedKeys.length);
      const hintArray = encodedKeys[randomIndex];
      const hintKey = hintArray[1];
      const hintValue = hintArray[0];
      console.log(hintKey, hintValue);
      this.decodedMap[hintKey] = hintValue;
    },
    assembleDecodedWord() {
      this.decodedWord = "";
      this.codeWordSplit.forEach((originalChar) => {
        const encodedChar = this.encodedMap[originalChar];
        const decodedChar = this.decodedMap[encodedChar];
        if (encodedChar && decodedChar) {
          this.decodedWord += decodedChar;
        }
      });
      this.isSolved = this.decodedWord === this.codeWord;
    },
    tapHighlight(e) {
      e.target.classList.toggle("opacity-75");
      setTimeout(() => e.target.classList.toggle("opacity-75"), 100);
      e.target.classList.toggle("scale-90");
      setTimeout(() => e.target.classList.toggle("scale-90"), 100);
    },
    resetState() {
      this.decodedMap = {};
      this.encodedMap = {};
      this.isSolved = false;
      this.hintAvailable = true;
    },
    characterISDecoded(char) {
      const decodedVal = this.decodedMap[char];
      const isDecoded = Boolean(decodedVal);
      return isDecoded;
    },
    characterISCorrectlyDecoded(encodedChar, decodedChar) {
      // encodedChar = emoji
      // decodedChar = letter
      const correctEncoding = this.encodedMap[decodedChar];
      return correctEncoding === encodedChar;
    },
    shuffle(a) {
      for (let i = a.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [a[i], a[j]] = [a[j], a[i]];
      }
      return a;
    },
    init() {
      this.resetState();
      this.createMaps();
      this.codeWordSplit = this.codeWord.split("");
    },
    createMaps() {
      const randomizedEmojis = this.shuffle(
        Object.values(this.emojiTOUnicodeDict)
      );
      this.codeWord.split("").forEach((char, charIndex) => {
        if (char === " ") {
          this.encodedMap[char] = " ";
        } else {
          this.encodedMap[char] = String.fromCodePoint(
            randomizedEmojis[charIndex]
          );
        }
      });
    },
  },
  mounted() {
    this.initWithRandomWord("easy");
  },
};
</script>
