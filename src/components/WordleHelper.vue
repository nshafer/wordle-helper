<script lang="ts">
import { defineComponent } from 'vue';

import type { GreensArray, YellowsArray, GraysArray } from '@/solver';
import Solver from '../solver';
import PuzzleInput from './PuzzleInput.vue';
import ResultsView from './ResultsView.vue';

interface Benchmark {
    [name: string]: number
}

const benchmarks: Benchmark = {};

export default defineComponent({
    name: "WordleHelper",
    components: {
        PuzzleInput,
        ResultsView,
    },
    data() {
        return {
            currentPage: "input",
            hasInput: false,
            solver: null as Solver | null,
            answers: [] as string[],
            benchmark: true,
            benchmarkResults: {} as Benchmark,
        }
    },
    computed: {
        isInputPage(): boolean {
            return this.currentPage === "input";
        },
        isResultPage(): boolean {
            return this.currentPage === "result";
        },
    },
    methods: {
        showInputPage() {
            this.currentPage = "input";
        },
        showResultPage() {
            this.currentPage = "result";
        },
        updateAnswers(hasInput: boolean, greens: GreensArray, yellows: YellowsArray, grays: GraysArray) {
            console.log("updateAnswers", greens, yellows, grays);
            this.hasInput = hasInput;
            if (hasInput) {
                this.benchmarkStart("solve");
                // console.time("solve");
                this.solver = new Solver(greens, yellows, grays);
                this.answers = this.solver.solve();
                this.benchmarkEnd("solve");
                // console.timeEnd("solve");
            } else {
                this.answers = [];
            }
        },
        benchmarkStart(name: string) {
            benchmarks[name] = window.performance.now();
        },
        benchmarkEnd(name: string) {
            const end = window.performance.now();
            if (benchmarks[name]) {
                const res = end - benchmarks[name];
                // console.log(`benchmark results for ${name}: ${res} ms`);
                this.benchmarkResults[name] = res;
            }
        },
        benchmarkResult(name: string): string {
            if (this.benchmarkResults[name]) {
                return this.benchmarkResults[name].toFixed(1);
            } else {
                return "-"
            }
        }
    },
});
</script>

<template>
    <main class="helper" @keydown.esc="showInputPage">
        <div class="page input-page" :class="{ show: isInputPage }">
            <div v-if="hasInput" class="page-header double">
                <p>
                    <b>{{ answers.length }}</b> possible answers.
                </p>
                <button class="button with-icon-right" @click.prevent="showResultPage">
                    Results
                    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 256 512"><path fill="currentColor" d="M219.9 266.7L75.89 426.7c-5.906 6.562-16.03 7.094-22.59 1.188c-6.918-6.271-6.783-16.39-1.188-22.62L186.5 256L52.11 106.7C46.23 100.1 46.75 90.04 53.29 84.1C59.86 78.2 69.98 78.73 75.89 85.29l144 159.1C225.4 251.4 225.4 260.6 219.9 266.7z"/></svg>
                </button>
            </div>
            <div v-else class="page-header single">
                <p>
                    Enter the letters from your puzzle
                </p>
            </div>

            <PuzzleInput @inputChanged="updateAnswers" @inputDone="showResultPage"/>

            <div v-if="benchmark" class="benchmark">
                Solve: {{ benchmarkResult("solve") }} ms
            </div>
        </div>

        <div class="page result-page" :class="{ show: isResultPage }">
            <div v-if="hasInput" class="page-header double">
                <button class="button with-icon-left" @click.prevent="showInputPage">
                    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 256 512"><path fill="currentColor" d="M203.9 405.3c5.877 6.594 5.361 16.69-1.188 22.62c-6.562 5.906-16.69 5.375-22.59-1.188L36.1 266.7c-5.469-6.125-5.469-15.31 0-21.44l144-159.1c5.906-6.562 16.03-7.094 22.59-1.188c6.918 6.271 6.783 16.39 1.188 22.62L69.53 256L203.9 405.3z"/></svg>
                    Back
                </button>
                <p>
                    <b>{{ answers.length }}</b> possible answers.
                </p>
            </div>
            <div v-else class="page-header double">
                <button class="button" @click.prevent="showInputPage">&lt;-</button>
                <p>
                    Enter the letters from your puzzle
                </p>
            </div>

            <ResultsView :hasInput="hasInput" :solver="solver" :answers="answers" />

        </div>
    </main>
</template>

<style scoped>
/* .helper {
} */

.input-page {
    padding: .5em;
    display: flex;
    flex-flow: column nowrap;
}

.result-page {
    padding: .5em;
}

.page-header {
    font-size: 1.2em;
    min-height: 2.5em;
    display: flex;
    flex-flow: row nowrap;
    justify-content: center;
    align-items: center;
    margin-bottom: .5em;
}

.page-header p {
    margin: 0;
}

.benchmark {
    margin-top: auto;
    color: var(--gray-3);
}

/* Small screens only */
@media screen and (max-width: 59.9375em) {
    .helper {
        position: relative;
        width: 100%;
        overflow-x: hidden;
        /* overflow-y: visible; */
        /* height: calc(90%); */
    }

    .page {
        position: fixed;
        overflow: auto;
        width: 100%;
        top: var(--header-height);
        bottom: 0;
    }

    .input-page {
        /* position: absolute; */
        transform: translateX(-100%);
        transition: all 100ms;
        width: 100%;
    }

    .input-page.show {
        transform: translateX(0);
    }

    .result-page {
        /* position: absolute; */
        transform: translateX(100%);
        transition: all 100ms;
        width: 100%;
    }

    .result-page.show {
        transform: translateX(0);
    }

    .page-header.double {
        justify-content: space-between;
    }
}

/* Large screens only */
@media screen and (min-width: 60em) {
    .helper {
        display: grid;
        grid-template-columns: 1fr 1fr;
    }

    .input-page {
        padding: 1em .5em 1em 1em;
    }

    .page-header {
        display: none;
    }

    .result-page {
        padding: 1em 1em 1em .5em;
    }

    .result-page .page-header .button {
        display: none;
    }
}

@media screen and (min-width: 81em) {
    .helper {
        gap: 1em
    }

    .input-page {
        padding: 1em 0;
    }

    .page-header {
        display: none;
    }

    .result-page {
        padding: 1em 0;
    }
}

</style>