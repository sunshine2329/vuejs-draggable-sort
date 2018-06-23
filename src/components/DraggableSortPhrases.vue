<template>
  <div id="draggable-phrases">
    <div style="text-align: left; top: 16px; left: 16px; right: 16px" class="fixed-top">
      <b-button v-on:click.prevent="onBack" style="width: auto;">Back</b-button>
      <b-button v-on:click.prevent="onDone" style="float: right; width: auto;">Done</b-button>
    </div>
    <div id="scroll-container" class="d-flex">
      <draggable id="draggable-container" v-model="sortedPhrases" :options="options" style="width: 100%">
        <transition-group>
            <div v-for="(phrase, index) in sortedPhrases" v-bind:key="`phrase-${index}`" class="d-flex">
              <div class="phrase-row phrase-row-number d-flex justify-content-center align-content-center">{{ index + 1 }}.</div>
              <div class="phrase-row d-flex justify-content-center align-items-center">
                <div style="flex: 1">{{ phrase }}</div>
                <i class="handle"><v-icon name="move"></v-icon></i>
              </div>
            </div>
        </transition-group>
      </draggable>
    </div>
  </div>
</template>

<script>
import draggable from 'vuedraggable'
export default {
  components: {
    draggable
  },
  props: {
    phrases: {
      type: Array,
      required: true
    }
  },
  data () {
    return {
      sortedPhrases: [],
      options: {
        handle: '.handle',
        scroll: true,
        group: {name: 'Phrases', pull: false, put: true},
        scrollFn: function (offsetX, offsetY, originalEvent, touchEvt, hoverTagetEl) {
          const scrollContainerHeight = document.getElementById('scroll-container').offsetHeight
          const scrollHeight = document.getElementById('draggable-container').scrollHeight
          var scrollTop = document.getElementById('scroll-container').scrollTop
          const maxTop = scrollHeight - scrollContainerHeight
          if (originalEvent.clientY < 120 && scrollTop > 0) {
            scrollTop -= 10
            document.getElementById('scroll-container').scrollTop = scrollTop > 0 ? scrollTop : 0
          } else if (originalEvent.clientY > scrollContainerHeight - 100 && scrollTop < maxTop) {
            scrollTop += 10
            document.getElementById('scroll-container').scrollTop = scrollTop < maxTop ? scrollTop : maxTop
          }
        }
      }
    }
  },
  mounted () {
    this.sortedPhrases = this.phrases.slice()
    // for (var i = 0; i < this.phrases.length; i++) {
    //   this.sortedPhrases.push({index: (i + 1), phrase: this.phrases[i]})
    // }
  },
  methods: {
    onBack: function () {
      this.$emit('initialize')
    },
    onDone: function () {
      // var results = this.sortedPhrases.map(phrase => phrase.phrase)
      this.$emit('onEnd', this.sortedPhrases)
    }
  }
}
</script>

<style scoped>
#draggable-phrases {
  overflow-y: hidden;
  height: 100%;
  padding: 16px;
}
#scroll-container {
  position: absolute;
  top: 70px;
  left: 0px;
  right: 0px;
  bottom: 0px;
  overflow-y: auto;
  overflow-x: hidden;
}
.sortable-chosen {
  opacity: 0.3 !important;
}
.sortable-drag {
  opacity: 1 !important;
}
.sortable-drag .phrase-row {
  background: #86735a;
}
.sortable-drag .phrase-row-number {
  opacity: 0;
}
.phrase-row {
  background: #e7e7e7;
  color: black;
  margin: 0px;
  padding: 8px 16px;
  margin-bottom: 2px;
  width: 100%;
  justify-content: space-between;
  font-size: 16px;
  font-weight: 400;
}
.phrase-row-number {
  width: 40px;
  background-color: #86735a;
  color: white;
  align-items: center;
  flex: 1;
  font-size: 18px;
  font-weight: 500;
}
.handle {
  margin-left: 8px;
  display: inline-block;
  font-family: "Glyphicons Halflings";
  font-style: normal;
  font-weight: 400;
  line-height: 1;
  -webkit-font-smoothing: antialiased;
  width: 20px;
  height: 20px;
}
.handle:hover {
  cursor: pointer;
}
</style>
