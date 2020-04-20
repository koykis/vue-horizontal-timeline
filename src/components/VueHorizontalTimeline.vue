<template>
  <div class="vue-horizontal-timeline" :style="setTimelineStyles">
    <section class="timeline">
      <ol>
        <li v-for="(item, i) in items" :key="i" :style="setLineColor">
          <div class="time" :class="getTimeClass(item)" :style="getTimeStyles" @click="cardClicked(item)">
            <slot v-if="$slots.default" v-bind:item="item"/>
            <span
              class="title"
              v-if="!$slots.default && item[titleAttr]"
              :class="getTitleClasses">
              {{ item[titleAttr] | textSubstr(titleSubstr) }}
            </span>
            <div
              class="content"
              v-if="!$slots.default && item[contentAttr]"
              :class="getContentClasses">
              <link-prevue :url="item[contentAttr]">
                <template slot-scope="props">
                  <v-card>
                    <a :href="props.url" target="_blank" style="text-decoration: none; color: white">
                      <v-img class="white&#45;&#45;text align-end" width="300" height="180" :alt="props.title" :src="props.img">
                        <v-card-title style="background: rgba(0, 0, 0, 0.5)">{{props.title}}</v-card-title>
                      </v-img>
                    </a>
                  </v-card>
                </template>
              </link-prevue>
            </div>
          </div>
        </li>
        <li :style="setLineColor"></li>
      </ol>
    </section>
  </div>
</template>

<script>
  import LinkPrevue from 'link-prevue';
  export default {
    name: 'VueHorizontalTimeline',
    components: {
      'link-prevue': LinkPrevue
    },
    props: {
      items: {
        type: Array,
        required: true
      },
      itemSelected: {
        type: Object,
        default: () => ({})
      },
      itemUniqueKey: {
        type: String,
        default: ''
      },
      titleAttr: {
        type: String,
        default: 'title'
      },
      titleCentered: {
        type: Boolean,
        default: false
      },
      titleClass: {
        type: String,
        default: ''
      },
      titleSubstr: {
        type: Number,
        default: 18
      },
      contentAttr: {
        type: String,
        default: 'content'
      },
      contentCentered: {
        type: Boolean,
        default: false
      },
      contentClass: {
        type: String,
        default: ''
      },
      contentSubstr: {
        type: Number,
        default: 250
      },
      minWidth: {
        type: String,
        default: '200px'
      },
      minHeight: {
        type: String,
        default: ''
      },
      timelinePadding: {
        type: String,
        default: ''
      },
      timelineBackground: {
        type: String,
        default: ''
      },
      lineColor: {
        type: String,
        default: '#03A9F4'
      },
      clickable: {
        type: [String, Boolean],
        default: true
      }
    },
    filters: {
      textSubstr (value, qtd = 250, mask = '...') {
        return value && value.length > qtd ? `${value.substring(0, qtd)}${mask}` : value
      }
    },
    computed: {
      setTimelineStyles () {
        const { timelineBackground, timelinePadding } = this
        const styleObj = {}

        if (timelinePadding) {
          styleObj.padding = timelinePadding
        }

        if (timelineBackground) {
          styleObj.background = timelineBackground
        }

        return styleObj
      },
      setLineColor () {
        const { lineColor } = this

        return lineColor ? `background: ${lineColor}` : ''
      },
      getTimeStyles () {
        const { minWidth, minHeight, clickable } = this
        const styleObj = {
          minWidth,
          minHeight
        }

        if (!clickable) {
          styleObj.cursor = 'default'
        }

        return styleObj
      },
      getTitleClasses () {
        const { titleClass, titleCentered } = this

        return titleClass || { 'text-center': titleCentered }
      },
      getContentClasses () {
        const { contentClass, contentCentered } = this

        return contentClass || { 'text-center': contentCentered }
      }
    },
    methods: {
      cardClicked (item) {
        if (!this.clickable) {
          return
        }

        this.$emit('update:itemSelected', { ...item })
        this.$emit('click', item)
      },
      getTimeClass (item) {
        const { itemUniqueKey, itemSelected } = this

        if (itemUniqueKey && itemSelected) {
          return {
            'border-blue': item[itemUniqueKey] === itemSelected[itemUniqueKey]
          }
        }

        return {}
      }
    }
  }
</script>

<style scoped>
  @keyframes triangle-odd {
    100% {
      filter: drop-shadow(4px 2px 1px rgba(0, 0, 0, 0.2)) drop-shadow(2px 4px 0px rgba(0, 0, 0, 0.14));
    }
  }
  @keyframes triangle-even {
    100% {
      filter: drop-shadow(4px -1px 1px rgba(0, 0, 0, 0.2));
    }
  }
  .vue-horizontal-timeline {
    position: relative;
    background: #e9e9e9;
    width: 100%;
    height: 70vh;
    box-shadow: 0 4px 10px 0 rgba(0, 0, 0, 0.3);
    border-radius: 5px;
    overflow: hidden;
  }
  .vue-horizontal-timeline .text-center {
    text-align: center;
  }
  .vue-horizontal-timeline .timeline {
    white-space: nowrap;
    overflow-x: hidden;
    padding-top: 23vh;
    height: inherit;
  }
  .vue-horizontal-timeline .timeline .title {
    display: block;
    font-size: 1.4rem;
    font-weight: bold;
    margin-bottom: 8px;
  }
  .vue-horizontal-timeline .timeline .content {
    display: block;
    font-size: 1rem;
    margin-bottom: 4px;
    overflow: hidden;
  }
  .vue-horizontal-timeline .timeline ol {
    font-size: 0;
    width: 100%;
    padding: 100px 0 120px 0px;
    transition: all 0.5s;
  }
  .vue-horizontal-timeline .timeline ol li {
    position: relative;
    display: inline-block;
    list-style-type: none;
    width: 180px;
    height: 3px;
    background: #03a9f4;
  }
  .vue-horizontal-timeline .timeline ol li:last-child {
    width: 330px;
  }
  .vue-horizontal-timeline .timeline ol li:not(:last-child) {
    padding-right: 0px;
    margin-right: 0px;
  }
  .vue-horizontal-timeline .timeline ol li:not(:last-child)::after {
    content: '';
    position: absolute;
    top: 50%;
    left: calc(100% - 140px);
    bottom: 0;
    width: 14px;
    height: 14px;
    transform: translateY(-50%);
    border-radius: 50%;
    background: #e91e63;
    border: 1px solid #fff;
  }
  .vue-horizontal-timeline .timeline ol li .time {
    position: absolute;
    left: 3em;
    padding: 10px;
    font-size: 1rem;
    white-space: normal;
    color: white;
    background: #333f50;
    box-shadow: 3px 3px 1px -2px rgba(0, 0, 0, 0.2), 0px 2px 2px 0 rgba(0, 0, 0, 0.14), 0px 1px 5px 0px rgba(0, 0, 0, 0.12);
    transition: 0.2s all ease-out;
    cursor: pointer;
  }
  .vue-horizontal-timeline .timeline ol li .time:hover {
    box-shadow: 6.5px 8px 3px -2px rgba(0, 0, 0, 0.2), 0px 2px 2px 0px rgba(0, 0, 0, 0.14), 0px 1px 5px 0px rgba(0, 0, 0, 0.12);
  }
  .vue-horizontal-timeline .timeline ol li .time::before {
    content: '';
    position: absolute;
    top: 100%;
    left: 0;
    width: 0;
    height: 0;
    border-style: solid;
  }
  .vue-horizontal-timeline .timeline ol li:nth-child(even) .time {
    top: calc(100% + 16px);
    border-top-right-radius: 3px;
    border-bottom-right-radius: 3px;
    border-bottom-left-radius: 3px;
  }
  .vue-horizontal-timeline .timeline ol li:nth-child(even) .time::before {
    top: -8px;
    border-width: 8px 0 0 8px;
    border-color: transparent transparent transparent #333f50;
    filter: drop-shadow(1px -1px 1px rgba(0, 0, 0, 0.2));
  }
  .vue-horizontal-timeline .timeline ol li:nth-child(even) .time:hover::before {
    animation: triangle-even 0.2s both;
  }
  .vue-horizontal-timeline .timeline ol li:nth-child(even) .time.border-blue {
    filter: drop-shadow(-1px 0px 0 #03a9f4) drop-shadow(1px 1px 0 #03a9f4) drop-shadow(0px -1px 0 #03a9f4);
  }
  .vue-horizontal-timeline .timeline ol li:nth-child(odd) .time {
    top: -16px;
    transform: translateY(-100%);
    border-top-right-radius: 3px;
    border-bottom-right-radius: 3px;
    border-top-left-radius: 3px;
  }
  .vue-horizontal-timeline .timeline ol li:nth-child(odd) .time::before {
    top: 100%;
    border-width: 8px 8px 0 0;
    border-color: #333f50 transparent transparent transparent;
    filter: drop-shadow(1px 1px 1px rgba(0, 0, 0, 0.2)) drop-shadow(1px 1px 0px rgba(0, 0, 0, 0.14));
  }
  .vue-horizontal-timeline .timeline ol li:nth-child(odd) .time:hover::before {
    animation: triangle-odd 0.2s both;
  }
  .vue-horizontal-timeline .timeline ol li:nth-child(odd) .time.border-blue {
    filter: drop-shadow(-1px 0px 0 #03a9f4) drop-shadow(1px 1px 0 #03a9f4) drop-shadow(0px -1px 0 #03a9f4);
  }
</style>
