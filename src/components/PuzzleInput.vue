<script lang="ts">
import { defineComponent } from 'vue';

import type { GreensArray, YellowsArray, GraysArray } from '@/solver';

const validLettersRegex = /[^abcdefghijklmnopqrstuvwxyz]/ig;

function cleanLetters(val: string): string {
    if (val && typeof val === "string") {
        return val.toLowerCase().replaceAll(validLettersRegex, "");
    } else {
        return "";
    }
}

export default defineComponent({
    name: "PuzzleInput",
    emits: ['inputChanged', 'inputDone'],
    data() {
        return {
            page: "input",
            debug: false,

            green1: "", green2: "", green3: "", green4: "", green5: "",
            yellow1: "", yellow2: "", yellow3: "", yellow4: "", yellow5: "",
            gray: "",
        }
    },
    computed: {
        greens(): GreensArray {
            return [
                this.parseGreen(this.green1),
                this.parseGreen(this.green2),
                this.parseGreen(this.green3),
                this.parseGreen(this.green4),
                this.parseGreen(this.green5),
            ]
        },
        yellows(): YellowsArray {
            return [
                this.parseYellow(this.yellow1),
                this.parseYellow(this.yellow2),
                this.parseYellow(this.yellow3),
                this.parseYellow(this.yellow4),
                this.parseYellow(this.yellow5),
            ]
        },
        grays(): GraysArray {
            return this.parseGray(this.gray);
        },
        hasInput(): boolean {
            return this.green1 != "" || this.green2 != "" || this.green3 != "" || this.green4 != "" || this.green5 != ""
                || this.yellow1 != "" || this.yellow2 != "" || this.yellow3 != "" || this.yellow4 != "" || this.yellow5 != ""
                || this.gray != "";
        },
    },
    methods: {
        parseGreen(val: string) {
            val = cleanLetters(val);
            if (val) {
                return val;
            } else {
                return null;
            }
        },
        parseYellow(val: string) {
            val = cleanLetters(val);
            if (val) {
                return val.split("");
            } else {
                return [];
            }
        },
        parseGray(val: string) {
            val = cleanLetters(val);
            return val.split("");
        },
        selectAll(event: Event) {
            console.log("selectAll", event);
            if (event.target && event.target instanceof HTMLInputElement) {
                event.target.select();
            }
        },
        cursorEnd(event: Event) {
            console.log("cursorEnd", event);
            if (event.target && event.target instanceof HTMLInputElement) {
                const length = event.target.value.length;
                event.target.setSelectionRange(length, length);
            }

        },
        resetGreen() {
            this.green1 = "";
            this.green2 = "";
            this.green3 = "";
            this.green4 = "";
            this.green5 = "";
        },
        resetYellow() {
            this.yellow1 = "";
            this.yellow2 = "";
            this.yellow3 = "";
            this.yellow4 = "";
            this.yellow5 = "";
        },
        resetGray() {
            this.gray = "";
        },
        inputDone() {
            this.$emit('inputDone');
        }
    },
    watch: {
        greens() {
            this.$emit("inputChanged", this.hasInput, this.greens, this.yellows, this.grays);
        },
        yellows() {
            this.$emit("inputChanged", this.hasInput, this.greens, this.yellows, this.grays);
        },
        grays() {
            this.$emit("inputChanged", this.hasInput, this.greens, this.yellows, this.grays);
        },
    },
});
</script>

<template>
    <form @submit.prevent="inputDone">
        <button hidden type="submit" @click.prevent="inputDone">Submit</button>
        <div class="section green">
            <div class="section__header">
                <h1>Correct Letters</h1>
                <button class="button icon" @click.prevent="resetGreen">
                    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 448 512"><path fill="currentColor" d="M440 64H336l-33.6-44.8A48 48 0 0 0 264 0h-80a48 48 0 0 0-38.4 19.2L112 64H8a8 8 0 0 0-8 8v16a8 8 0 0 0 8 8h18.9l33.2 372.3a48 48 0 0 0 47.8 43.7h232.2a48 48 0 0 0 47.8-43.7L421.1 96H440a8 8 0 0 0 8-8V72a8 8 0 0 0-8-8zM171.2 38.4A16.1 16.1 0 0 1 184 32h80a16.1 16.1 0 0 1 12.8 6.4L296 64H152zm184.8 427a15.91 15.91 0 0 1-15.9 14.6H107.9A15.91 15.91 0 0 1 92 465.4L59 96h330z"></path></svg>
                </button>
            </div>

            <div class="section__body">
                <div class="inputs chars green">
                    <input v-model="green1" class="input char green" maxlength="1" type="text" spellcheck="false" @click="selectAll" />
                    <input v-model="green2" class="input char green" maxlength="1" type="text" spellcheck="false" @click="selectAll" />
                    <input v-model="green3" class="input char green" maxlength="1" type="text" spellcheck="false" @click="selectAll" />
                    <input v-model="green4" class="input char green" maxlength="1" type="text" spellcheck="false" @click="selectAll" />
                    <input v-model="green5" class="input char green" maxlength="1" type="text" spellcheck="false" @click="selectAll" />
                </div>
            </div>
        </div>

        <div class="section yellow">
            <div class="section__header">
                <h1>Misplaced Letters</h1>
                <button class="button icon" @click.prevent="resetYellow">
                    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 448 512"><path fill="currentColor" d="M440 64H336l-33.6-44.8A48 48 0 0 0 264 0h-80a48 48 0 0 0-38.4 19.2L112 64H8a8 8 0 0 0-8 8v16a8 8 0 0 0 8 8h18.9l33.2 372.3a48 48 0 0 0 47.8 43.7h232.2a48 48 0 0 0 47.8-43.7L421.1 96H440a8 8 0 0 0 8-8V72a8 8 0 0 0-8-8zM171.2 38.4A16.1 16.1 0 0 1 184 32h80a16.1 16.1 0 0 1 12.8 6.4L296 64H152zm184.8 427a15.91 15.91 0 0 1-15.9 14.6H107.9A15.91 15.91 0 0 1 92 465.4L59 96h330z"></path></svg>
                </button>
            </div>

            <div class="section__body">
                <div class="inputs chars yellow">
                    <input v-model="yellow1" class="input chars yellow" maxlength="4" type="text" name="yellow1" spellcheck="false" @click="cursorEnd" />
                    <input v-model="yellow2" class="input chars yellow" maxlength="4" type="text" name="yellow2" spellcheck="false" @click="cursorEnd" />
                    <input v-model="yellow3" class="input chars yellow" maxlength="4" type="text" name="yellow3" spellcheck="false" @click="cursorEnd" />
                    <input v-model="yellow4" class="input chars yellow" maxlength="4" type="text" name="yellow4" spellcheck="false" @click="cursorEnd" />
                    <input v-model="yellow5" class="input chars yellow" maxlength="4" type="text" name="yellow5" spellcheck="false" @click="cursorEnd" />
                </div>
            </div>
        </div>

        <div class="section gray">
            <div class="section__header">
                <h1>Invalid Letters</h1>
                <button class="button icon" @click.prevent="resetGray">
                    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 448 512"><path fill="currentColor" d="M440 64H336l-33.6-44.8A48 48 0 0 0 264 0h-80a48 48 0 0 0-38.4 19.2L112 64H8a8 8 0 0 0-8 8v16a8 8 0 0 0 8 8h18.9l33.2 372.3a48 48 0 0 0 47.8 43.7h232.2a48 48 0 0 0 47.8-43.7L421.1 96H440a8 8 0 0 0 8-8V72a8 8 0 0 0-8-8zM171.2 38.4A16.1 16.1 0 0 1 184 32h80a16.1 16.1 0 0 1 12.8 6.4L296 64H152zm184.8 427a15.91 15.91 0 0 1-15.9 14.6H107.9A15.91 15.91 0 0 1 92 465.4L59 96h330z"></path></svg>
                </button>
            </div>

            <div class="section__body">
                <div class="inputs line gray">
                    <input v-model="gray" class="input line gray" maxlength="26" type="text" name="grays" spellcheck="false" @click="cursorEnd" />
                </div>
            </div>
        </div>
    </form>

    <div v-if="debug" class="debug">
        Greens: {{ greens }}<br/>
        Yellows: {{ yellows }}<br/>
        Grays: {{ grays }}<br/>
    </div>

</template>

<style scoped>
.inputs {
    display: flex;
    flex-flow: row nowrap;
    justify-content: space-around;
}

.input {
    background: var(--input-bg);
    width: 100%;
    height: 1.5em;
    font-size: 1.1em;
    color: var(--input-text-color);
    text-transform: uppercase;
    font-weight: bold;
    text-align: center;
    border: none;
    transition: all 100ms;
}

.input:focus {
    /* font-size: 1.4em; */
    transform: scale(1.2);
}

.input.line:focus {
    width: 80%;
}

@media screen and (min-width: 25em) {
    .input {
        font-size: 1.4em;
    }
}

@media screen and (min-width: 30em) {
    .input {
        font-size: 1.7em;
    }
}

.input + .input {
    margin-left: .5em;
}

.input.char {
    width: 1.5em;
}

.input.chars {
    resize: none;
    /* height: 3em; */
    vertical-align: middle;
    /* width: 3em; */
}

.input.line {
    letter-spacing: .25em;
}

.input.green {
    background: var(--color-correct);
}

.input.yellow {
    background: var(--color-present);
}

.input.gray {
    background: var(--color-absent);
}

</style>