<template>
  <div>
    <component :is="uiTemplate"/>
  </div>
</template>

<script>
import dottie from 'dottie'
import { openURL, date } from 'quasar'
import 'video.js/dist/video-js.css'
import { videoPlayer } from 'vue-video-player'
import VueSignature from 'vue-signature-pad'
import * as validators from 'vuelidate/lib/validators'
import QMap from '@wmfs/quasar-map-mapbox'
import template from 'lodash.template'

import cardscriptToQuasar from '@wmfs/cardscript-to-quasar'
import extractDefaults from '@wmfs/cardscript-extract-defaults'
import extractToc from '@wmfs/cardscript-table-of-contents'
import extractLists from '@wmfs/cardscript-extract-lists'
import extractVuelidate from '@wmfs/cardscript-to-vuelidate'
import sdk from '@wmfs/cardscript-vue-sdk'

const { formatDate } = date

export default {
  name: 'Cardscript',
  props: ['cardscript'],
  data () {
    return {
      uiTemplate: {}
    }
  },
  mounted () {
    setTimeout(() => {
      this.$nextTick(() => {
        const { cardscript } = this
        const options = {}

        const defaultValues = extractDefaults(cardscript)
        const defaultInternals = sdk.getDefaultInternals(cardscript)
        defaultInternals.cardListDefaults = defaultValues.cardLists

        const content = {
          quasarTemplate: cardscriptToQuasar(cardscript, options).template,
          data: defaultValues.rootView,
          internals: defaultInternals,
          lists: extractLists(cardscript),
          toc: extractToc(cardscript),
          validations: extractVuelidate(cardscript, validators)
        }

        const that = this

        this.uiTemplate = {
          template: content.quasarTemplate,
          components: {
            videoPlayer,
            VueSignaturePad: VueSignature,
            QMap,
            ...QMap.components
          },
          validations: {
            data: content.validations
          },
          data () {
            return {
              data: content.data,
              lists: content.lists,
              internals: content.internals
            }
          },
          methods: {
            formatDate,
            openURL,
            parseTemplate (temp, data) {
              const d = data || this.data
              const compiled = template(
                temp,
                {
                  interpolate: /{{([\s\S]+?)}}/g
                }
              )
              return compiled(d)
            },
            resizeSignatureModal ({ id, dataPath }) {
              this.$refs[`${id}SignaturePad`].resizeCanvas()
              const data = dottie.get(this, `${dataPath}.${id}`)
              this.$refs[`${id}SignaturePad`].fromDataURL(data)
            },
            showSignatureModal ({ id, dataPath }) {
              dottie.set(this, `${dataPath}.${id}OpenModal`, true)
            },
            clearSignature ({ id }) {
              this.$refs[`${id}SignaturePad`].clearSignature()
            },
            undoSignature ({ id }) {
              this.$refs[`${id}SignaturePad`].undoSignature()
            },
            saveSignature ({ id, dataPath }) {
              const { data } = this.$refs[`${id}SignaturePad`].saveSignature()
              dottie.set(this, `${dataPath}.${id}`, data)
              dottie.set(this, `${dataPath}.${id}OpenModal`, false)
            },
            createNewCardList (cardListId) {
              this.internals.currentCardListData[cardListId] = this.internals.cardListDefaults[cardListId] || {}
              this.internals.dialogControl[cardListId] = true
            },
            pushCardListContent (cardListId) {
              const parentCardListId = this.internals.cardListParents[cardListId]
              const clone = JSON.parse(JSON.stringify(this.internals.currentCardListData[cardListId]))

              if (parentCardListId === null) {
                this.data[cardListId].push(clone)
              } else {
                this.internals.currentCardListData[parentCardListId][cardListId].push(clone)
              }
              this.internals.dialogControl[cardListId] = false
            },
            removeCardListContent (cardListId, index) {
              const parentCardListId = this.internals.cardListParents[cardListId]

              if (parentCardListId === null) {
                this.data[cardListId].splice(index, 1)
              } else {
                this.internals.currentCardListData[parentCardListId][cardListId].splice(index, 1)
              }
            },
            action (type, config) {
              that.$emit(type, config, this)
            }
          }
        }
      })
    }, 20)
  }
}
</script>

<style>
.video-js {
  width: 100%;
}
</style>
