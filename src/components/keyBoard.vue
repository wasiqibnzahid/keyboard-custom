<template>
  <div>
    <div class="m-auto w-[50%] min-w-[250px]">
      <textarea
        ref="mainTextArea"
        v-model="text"
        @focus="showKeyboard = true"
        placeholder="Enter text here..."
        class="block resize-none p-2.5 w-full text-sm min-h-[150px] text-gray-900 bg-gray-50 rounded-lg border ring-gray-500 outline-none border-gray-500 dark:text-white"
        @input="handleInput(text)"
      ></textarea>
    </div>
    <div class="flex justify-end w-[50%] mx-auto mt-6">
      <button
        @click="showKeyboard = !showKeyboard"
        class="outline-none rounded-lg bg-gray-500 text-white py-3 px-6 hover:bg-gray-600 duration-200 active:bg-gray-600 focus:ring focus:ring-blue-300"
      >
        Toggle Keyboard
      </button>
    </div>

    <!-- Keyboard coded here -->
    <div
      class="flex flex-col py-6 min-w-[250px] fixed h-[300px] left-1/2 translate-x-[-50%] duration-500 px-3 bg-gray-700 rounded-t-lg"
      :class="
        showKeyboard
          ? 'bottom-0 opacity-100'
          : 'pointer-events-none bottom-[-30%] opacity-0'
      "
    >
      <div
        class="w-full rounded-md px-3 gap-1 mb-3 flex justify-between"
        v-for="(row, indexRow) in keyboardKeys"
        :key="indexRow"
      >
        <div
          v-for="(key, index) in row"
          :key="index"
          @mouseup="removeColor"
          @mousedown="setColor"
          @mouseleave="removeColor"
          class="text-white min-w-[40px] cursor-pointer hover:ring duration-200 hover:ring-blue-300 rounded-lg px-3 relative py-2 font-bold bg-gray-800"
          :class="
            key.type !== 'function' && caps && shift
              ? ''
              : key.type !== 'function' && (caps || shift)
              ? 'capitalize'
              : key.value === 'space'
              ? 'min-w-[60%]'
              : ''
          "
          :ref="shift && key.alt ? key.alt : key.value"
          @click="key?.type === 'function' ? functionKey(key) : addText(key)"
        >
          <!-- @mouseup="key.value === 'shift' ? shiftOff() : ''" -->
          <!-- @mousedown="key.value === 'shift' ? shiftOn() : ''" -->
          {{ shift && key.alt ? key.alt : key.value }}
          <div
            v-show="
              (key.value == 'capslock' && caps) ||
              (key.value === 'shift' && shift)
            "
            class="absolute w-1 h-1 top-[10%] right-[10%] rounded-full bg-white"
          ></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "KeyboardComponent",
  mounted() {
    document.onkeyup = this.handleKeyUp;
    document.onkeydown = this.handleKeyDown;
  },
  data() {
    return {
      caps: false,
      showKeyboard: true,
      keyboardKeys: [
        [
          {
            value: "`",
            alt: "~",
          },
          {
            value: "1",
            alt: "!",
          },
          {
            value: "2",
            alt: "@",
          },
          {
            value: "3",
            alt: "#",
          },
          {
            value: "4",
            alt: "$",
          },
          {
            value: "5",
            alt: "%",
          },
          {
            value: "6",
            alt: "^",
          },
          {
            value: "7",
            alt: "&",
          },
          {
            value: "8",
            alt: "*",
          },
          {
            value: "9",
            alt: "(",
          },
          {
            value: "0",
            alt: ")",
          },
          {
            value: "-",
            alt: "_",
          },
          {
            value: "=",
            alt: "+",
          },
          {
            value: "backspace",
            type: "function",
          },
        ],
        [
          {
            value: "tab",
            type: "function",
          },
          {
            value: "q",
          },
          {
            value: "w",
          },
          {
            value: "e",
          },
          {
            value: "r",
          },
          {
            value: "t",
          },
          {
            value: "y",
          },
          {
            value: "u",
          },
          {
            value: "i",
          },
          {
            value: "o",
          },
          {
            value: "p",
          },
          {
            value: "[",
            alt: "{",
          },
          {
            value: "]",
            alt: "}",
          },
        ],
        [
          {
            value: "capslock",
            type: "function",
          },
          {
            value: "a",
          },
          {
            value: "s",
          },
          {
            value: "d",
          },
          {
            value: "f",
          },
          {
            value: "g",
          },
          {
            value: "h",
          },
          {
            value: "j",
          },
          {
            value: "k",
          },
          {
            value: "l",
          },
          {
            value: ";",
            alt: ":",
          },
          {
            value: "'",
            alt: '"',
          },
          {
            value: "enter",
            type: "function",
          },
        ],
        [
          {
            value: "shift",
            type: "function",
          },
          {
            value: "z",
          },
          {
            value: "x",
          },
          {
            value: "c",
          },
          {
            value: "v",
          },
          {
            value: "b",
          },
          {
            value: "n",
          },
          {
            value: "m",
          },
          {
            value: ",",
            alt: "<",
          },
          {
            value: ".",
            alt: ">",
          },
          {
            value: "/",
            alt: "?",
          },
          {
            value: "shift",
            type: "function",
          },
        ],
        [
          {
            value: "ctrl",
            type: "function",
          },

          {
            value: "win",
            type: "function",
          },
          {
            value: "alt",
            type: "function",
          },
          {
            value: "space",
            type: "function",
          },
          {
            value: "alt",
            type: "function",
          },
          {
            value: "ctrl",
            type: "function",
          },
        ],
      ],
      text: "",
      shift: false,
      activeKeys: [],
    };
  },
  methods: {
    handleInput(text) {
      if (text.length < 1) {
        return;
      }
      let a = text.split("");
      if ((this.caps && !this.shift) || (!this.caps && this.shift)) {
        a[a.length - 1] = a[a.length - 1].toUpperCase();
        this.text = a.join("");
      }
    },
    setColor(e) {
      e.target.style.backgroundColor = "rgba(31, 41, 55, 0.5)";
    },
    removeColor(e) {
      e.target.style.backgroundColor = "";
    },
    addText(key) {
      if (document.activeElement != this.$refs.mainTextArea) {
        this.$refs.mainTextArea.focus();
      }
      if (this.shift && key.alt) {
        this.text = this.text + key.alt;
      } else if (this.caps && this.shift) {
        this.text = this.text + key.value;
      } else if (this.caps || this.shift) {
        this.text = this.text + key.value.toUpperCase();
      } else {
        this.text = this.text + key.value;
      }
    },
    functionKey(key) {
      if (document.activeElement !== this.$refs.mainTextArea) {
        this.$refs.mainTextArea.focus();
      }
      if (key.value === "capslock") {
        this.caps = !this.caps;
      } else if (key.value === "backspace") {
        if (this.text.length > 0) {
          this.text = this.text.slice(0, -1);
        }
      } else if (key.value === "shift") {
        this.shift = !this.shift;
      } else if (key.value === "enter") {
        this.text = this.text + "\n";
      } else if (key.value === "space") {
        this.text = this.text + " ";
      }
    },
    handleKeyDown(e) {
      if (e.getModifierState("CapsLock")) {
        this.caps = true;
      }
      if (e.key.toLowerCase() == "capslock") {
        this.caps = !this.caps;
      }
      if (e.key.toLowerCase() == "shift") {
        this.shift = true;
      }
      if (e.code === "Space") {
        if (!this.activeKeys.includes("space")) {
          this.activeKeys.push("space");
          this.$refs["space"][0].style.backgroundColor = "rgba(31, 41, 55, 0.5)";
        }
      } else if (e.key == "Control") {
        const items = this.$refs["ctrl"];
        items.forEach((x) => {
          x.style.backgroundColor = "rgba(31, 41, 55, 0.5)";
        });
      } else {
        if (!this.$refs[e.key.toLowerCase()]) {
          return;
        }
        const items = this.$refs[e.key.toLowerCase()];
        items.forEach((x) => {
          x.style.backgroundColor = "rgba(31, 41, 55, 0.5)";
        });
        if (!this.activeKeys.includes(e.key.toLowerCase())) {
          this.activeKeys.push(e.key.toLowerCase());
        }
      }
    },
    handleKeyUp(e) {
      if (e.key.toLowerCase() == "shift") {
        this.shift = false;
      }
      if (e.code === "Space") {
        const items = this.$refs["space"];
        items.forEach((x) => {
          x.style.backgroundColor = "";
        });
        let index = this.activeKeys.findIndex((x) => {
          return x === "space";
        });
        if (index > -1) {
          this.activeKeys.splice(index, 1);
        }
      } else if (e.key == "Control") {
        const items = this.$refs["ctrl"];
        items.forEach((x) => {
          x.style.backgroundColor = "";
        });
      } else {
        if (!this.$refs[e.key.toLowerCase()]) {
          return;
        }
        const items = this.$refs[e.key.toLowerCase()];
        items.forEach((x) => {
          x.style.backgroundColor = "";
        });
        let index = this.activeKeys.findIndex((x) => {
          return x === e.key.toLowerCase();
        });
        if (index > -1) {
          this.activeKeys.splice(index, 1);
        }
      }
    },
  },
};
</script>
