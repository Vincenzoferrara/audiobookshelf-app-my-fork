<template>
  <div class="w-full my-4">
    <div class="w-full bg-primary px-4 py-2 flex items-center" :class="showTracks ? 'rounded-t-md' : 'rounded-md'" @click.stop="clickBar">
      <p class="pr-2">{{ $strings.HeaderAudioTracks }}</p>
      <div class="h-6 w-6 rounded-full bg-fg/10 flex items-center justify-center">
        <span class="text-xs font-mono">{{ tracks.length }}</span>
      </div>
      <div class="flex-grow" />
      <div class="h-10 w-10 rounded-full flex justify-center items-center duration-500" :class="showTracks ? 'transform rotate-180' : ''">
        <span class="material-symbols text-3xl">arrow_drop_down</span>
      </div>
    </div>
    <transition name="slide">
      <div class="w-full" v-show="showTracks">
        <table class="text-xs tracksTable">
          <tr>
            <th class="text-left">{{ $strings.LabelFilename }}</th>
            <th class="text-center w-16">{{ $strings.LabelDuration }}</th>
          </tr>
          <template v-for="track in tracks">
            <tr :key="track.index">
              <td>{{ (track.metadata && track.metadata.filename) || track.title || 'Unknown' }}</td>
              <td class="font-mono text-center w-16">
                {{ $secondsToTimestamp(track.duration) }}
              </td>
            </tr>
          </template>
        </table>
      </div>
    </transition>
  </div>
</template>

<script>
export default {
  props: {
    tracks: {
      type: Array,
      default: () => []
    },
    libraryItemId: String
  },
  data() {
    return {
      showTracks: false
    }
  },
  computed: {},
  methods: {
    clickBar() {
      this.showTracks = !this.showTracks
    }
  },
  mounted() {}
}
</script>
