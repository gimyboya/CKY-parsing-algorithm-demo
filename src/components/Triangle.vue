<template>
  <v-card height="100%">
    <v-card-title>Result</v-card-title>
    <v-card-text>
      <v-layout row wrap>
        <v-flex px-1 mb-2 sx12 md6>
          <v-text-field label="Sentence" placeholder="Enter a sentence" outline v-model="sentence"></v-text-field>
          <v-btn :disabled="disabled" type="success" @click="parse">Parse</v-btn>
          <v-btn :disabled="!disabled" type="error" @click="reset">Reset</v-btn>
        </v-flex>
        <v-flex px-1 mb-2 sx12 md6>
          <v-alert v-model="success" type="success" dismissible>The sentence can be derived</v-alert>
          <v-alert v-model="error" type="error" dismissible>The sentence can't be derived</v-alert>
        </v-flex>
      </v-layout>
    </v-card-text>
    <v-container
      v-if="tokenize[0] != ''"
      align-center
      align-content-center
      justify-center
      grid-list-md
      text-xs-center
    >
      <template v-for="i in (tokenize.length + 1)">
        <v-layout row :key="`${i}`">
          <v-flex v-for="(words, j) in tokenize" :key="`${j}`" xs1>
            <v-card v-if="i === tokenize.length + 1" :id="`card${i-1}${j}`" color="#2290EB">
              <v-card-text class="px-0">{{words}}</v-card-text>
            </v-card>
            <v-card v-else-if="j > i - 1" :id="`card${i-1}${j}`" color="#0E435C">
              <v-card-text class="px-0"></v-card-text>
            </v-card>
            <v-card v-else :id="`card${i-1}${j}`" color="#81CEF5">
              <v-card-text class="px-0"></v-card-text>
            </v-card>
          </v-flex>
        </v-layout>
      </template>
    </v-container>
  </v-card>
</template>

<script>
import { EventBus } from '../evenBus.js'
import _ from 'lodash'

export default {
  data() {
    return {
      sentence: '',
      disabled: false,
      success: false,
      error: false,
    }
  },
  mounted() {
    const that = this
    EventBus.$on('solution', (b) => {
      if (b) {
        that.success = true
      } else {
        that.error = true
      }
    })
  },
  methods: {
    parse() {
      EventBus.$emit('parse', this.sentence)
      this.disabled = true
    },
    reset() {
      EventBus.$emit('reset')
      this.sentence = ''
      this.disabled = false
      this.success = false
      this.error = false
    },
  },
  computed: {
    tokenize() {
      return _.trim(this.sentence).split(' ')
    },
  },
}
</script>
