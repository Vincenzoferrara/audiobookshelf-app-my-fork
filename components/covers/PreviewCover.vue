<template>
  <div class="relative rounded-sm" :style="{ height: width * bookCoverAspectRatio + 'px', width: width + 'px', maxWidth: width + 'px', minWidth: width + 'px' }" @mouseover="isHovering = true" @mouseleave="isHovering = false">
    <div class="w-full h-full relative overflow-hidden">
      <div v-show="showCoverBg" class="absolute top-0 left-0 w-full h-full overflow-hidden rounded-sm bg-primary">
        <div class="absolute cover-bg" ref="coverBg" />
      </div>
      <img ref="cover" :src="cover" @error="imageError" @load="imageLoaded" class="w-full h-full absolute top-0 left-0" :class="showCoverBg ? 'object-contain' : 'object-fill'" />

      <a v-if="!imageFailed && showOpenNewTab && isHovering" :href="cover" @click.stop target="_blank" class="absolute bg-primary flex items-center justify-center shadow-sm rounded-full hover:scale-110 transform duration-100" :style="{ top: sizeMultiplier * 0.5 + 'rem', right: sizeMultiplier * 0.5 + 'rem', width: 2.5 * sizeMultiplier + 'rem', height: 2.5 * sizeMultiplier + 'rem' }">
        <span class="material-symbols" :style="{ fontSize: sizeMultiplier * 1.75 + 'rem' }">open_in_new</span>
      </a>
    </div>

    <div v-if="imageFailed" class="absolute top-0 left-0 right-0 bottom-0 w-full h-full bg-red-100" :style="{ padding: placeholderCoverPadding + 'rem' }">
      <div class="w-full h-full border-2 border-error flex flex-col items-center justify-center">
        <img src="/Logo.png" class="mb-2" :style="{ height: 64 * sizeMultiplier + 'px' }" />
        <p class="text-centertext-error" :style="{ fontSize: sizeMultiplier + 'rem' }">Invalid Cover</p>
      </div>
    </div>

    <p v-if="!imageFailed && showResolution" class="absolute -bottom-5 left-0 right-0 mx-auto text-xs text-gray-300 text-center">{{ resolution }}</p>
  </div>
</template>

<script>
export default {
  props: {
    src: String,
    width: {
      type: Number,
      default: 120
    },
    showOpenNewTab: Boolean,
    bookCoverAspectRatio: Number,
    showResolution: {
      type: Boolean,
      default: true
    }
  },
  data() {
    return {
      imageFailed: false,
      showCoverBg: false,
      isHovering: false,
      naturalHeight: 0,
      naturalWidth: 0
    }
  },
  watch: {
    cover() {
      this.imageFailed = false
    }
  },
  computed: {
    cover() {
      return this.src
    },
    sizeMultiplier() {
      return this.width / 120
    },
    placeholderCoverPadding() {
      return 0.8 * this.sizeMultiplier
    },
    resolution() {
      return `${this.naturalWidth}x${this.naturalHeight}px`
    },
    placeholderUrl() {
      const config = this.$config || this.$nuxt.$config
      return `${config.routerBasePath}/book_placeholder.jpg`
    }
  },
  methods: {
    setCoverBg() {
      if (this.$refs.coverBg) {
        this.$refs.coverBg.style.backgroundImage = `url("${this.src}")`
      }
    },
    imageLoaded() {
      if (this.$refs.cover && this.src !== this.placeholderUrl) {
        var { naturalWidth, naturalHeight } = this.$refs.cover
        this.naturalHeight = naturalHeight
        this.naturalWidth = naturalWidth

        var aspectRatio = naturalHeight / naturalWidth
        var arDiff = Math.abs(aspectRatio - this.bookCoverAspectRatio)

        // If image aspect ratio is <= 1.45 or >= 1.75 then use cover bg, otherwise stretch to fit
        if (arDiff > 0.15) {
          this.showCoverBg = true
          this.$nextTick(this.setCoverBg)
        } else {
          this.showCoverBg = false
        }
      }
    },
    imageError(err) {
      console.error('ImgError', err)
      this.imageFailed = true
    }
  },
  mounted() {}
}
</script>
