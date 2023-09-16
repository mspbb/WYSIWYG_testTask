<template>
  <main>
    <div class="wrapper">
      <div class="controlPanel">
        <button v-on:click="backStep">&#8634;</button>
        <button v-on:click="nextStep">&#8635;</button>
        <button v-on:click="addH1">T</button>
        <button v-on:click="addParagraph">tt</button>
        <div>
          <button v-on:click="showAddImgPanel">Картинка</button>
          <div class="imgUploadPanel" v-if="panelShow">
            <input name="" v-model="imgUrl" />
            <button v-on:click="addImg">OK</button>
          </div>
        </div>
        <button v-on:click="copyHTML">скопировать HTML</button>
      </div>

      <div
        ref="testArea"
        id="textArea"
        contenteditable="true"
        spellcheck="false"
        v-on:click="textFocus"
      >
        <!-- &nbsp; -->
      </div>
    </div>
  </main>
</template>

<script>
/* import { useMutationObserver } from "vue"; */

export default {
  name: "App",
  data() {
    return {
      textBackHistory: [""],
      textNextHistory: [],

      panelShow: false,

      imgUrl: "",
      textArea: "",
      text: "",
      stopMutationObserver: false,
    };
  },

  methods: {
    textFocus() {
      if (window.getSelection()) {
        this.stopMutationObserver = false;
        let select = window.getSelection();
        this.text = select;
      }
    },

    backStep() {
      this.stopMutationObserver = true;
      let lastNumber = this.textBackHistory.length;
      if (lastNumber >= 2) {
        this.$refs.testArea.innerHTML = this.textBackHistory[lastNumber - 2];
        let last = this.textBackHistory.pop();
        this.textNextHistory.push(last);
      } else {
        this.textBackHistory = [""];
      }
    },

    nextStep() {
      this.stopMutationObserver = true;
      let lastNumber = this.textNextHistory.length - 1;
      if (lastNumber > -1) {
        this.$refs.testArea.innerHTML = this.textNextHistory[lastNumber];
        let last = this.textNextHistory.pop();
        this.textBackHistory.push(last);
      } else {
        return;
      }
    },

    addH1() {
      this.stopMutationObserver = false;

      const p = document.createElement("h1");
      if (this.text) {
        const s = this.text;

        if (s.rangeCount) {
          let range = s.getRangeAt(0).cloneRange();
          range.surroundContents(p);
          s.removeAllRanges();
          /* s.addRange(range); */
          /* s.deleteFromDocument(); */
        }
      }
    },
    addParagraph() {
      this.stopMutationObserver = false;

      const p = document.createElement("p");
      p.style.color = "#d57474";
      if (this.text) {
        const s = this.text;

        if (s.rangeCount) {
          let range = s.getRangeAt(0).cloneRange();
          range.surroundContents(p);
          s.removeAllRanges();
        }
      }
    },

    showAddImgPanel() {
      if (!this.panelShow) {
        this.panelShow = true;
      } else {
        this.panelShow = false;
      }
    },
    addImg() {
      this.stopMutationObserver = false;

      this.$refs.testArea.focus();
      const s = window.getSelection(this.$refs.testArea);
      let range = s.getRangeAt(0).cloneRange();

      let newNode = document.createElement("img");
      newNode.classList.add("controlPanel__img");
      newNode.src = this.imgUrl;

      if (range.endContainer.parentNode.id == "textArea") {
        range.insertNode(newNode);
      } else {
        range.insertNode(newNode);
      }
    },

    copyHTML() {
      let copyHTMLtext = this.$refs.testArea.innerHTML;
      navigator.clipboard.writeText(copyHTMLtext);
    },
  },

  mounted() {
    new MutationObserver((mutationsList, observer) => {
      if (this.stopMutationObserver) {
        return;
      }
      this.textBackHistory.push(this.$refs.testArea.innerHTML);
    }).observe(this.$refs.testArea, {
      childList: true,
      characterData: true,
      subtree: true,
    });

    this.$refs.testArea.focus();
  },
};
</script>

<style src="./assets/style.css"></style>
