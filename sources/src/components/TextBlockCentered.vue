<template>
  <div class="container">
    <div v-if="data.twitter === false" class="col-lg-12 col-md-12 mx-auto">
      <div class="row-md-5 row-lg-4 order-1 textblock-left no-border dashed-header link-fix">
        <h1 :class="{'mb-0' : data.talks}" v-html="data.header"/>
        <!--<p style="font-size: 1rem" v-if="data.talks">(Click person's name for more info)</p>-->
      </div>
      <button
        v-if="data.previousTalks || data.textHiddenByDefault"
        type="button"
        @click="hiddenContentShown = !hiddenContentShown"
        class="button-primary mt-4">
        {{ hiddenContentShown ? 'Hide' : 'Show' }}
      </button>
      <div
        v-if="data.text && (!data.textHiddenByDefault || hiddenContentShown)"
        v-html="data.text"
        class="row order-2 block-text textblock-right p-3 no-border" />
      <div
        v-if="data.previousTalks">
        <transition appear name="fade">
          <div v-if="hiddenContentShown" class="mt-4">
            <talker-item
              v-for="(talk, key) in data.previousTalks"
              :key="key"
              :header="talk.header"
              :margin="talk.margin"
              :authors="talk.authors"
              :title="talk.title"
              :type="talk.type || ''"
              :description="talk.description"
              :description-expanded="talk.descriptionExpanded"
              :bio="talk.bio"
              :img-url="talk.imgUrl"
              :url="talk.url"/>
          </div>
        </transition>
      </div>
      <div
        v-else-if="data.talks">
        <div class="mt-4">
          <talker-item
            v-for="(talk, key) in data.talks"
            :key="key"
            :header="talk.header"
            :margin="talk.margin"
            :authors="talk.authors"
            :title="talk.title"
            :keynote="talk.keynote"
            :time="talk.time"
            :sponsored-by="talk.sponsoredBy"
            :type="talk.type || ''"
            :description="talk.description"
            :description-expanded="talk.descriptionExpanded"
            :bio="talk.bio"
            :second-bio="talk.secondBio"
            :img-url="talk.imgUrl"
            :second-img-url="talk.secondImgUrl"
            :url="talk.url"/>
        </div>
      </div>
    </div>
    <div v-else>
      <div class="col-lg-12 mx-auto">
        <div class="row-md-5 row-lg-4 order-1 textblock-left no-border dashed-header link-fix">
          <h1 v-html="data.header"/>
        </div>
        <div class="row">
          <div
            v-html="data.text"
            class="col-lg-7 order-2 block-text textblock-right p-3 no-border"
          />
          <div class="col-md-12 col-lg-5 order-3 textblock-right ml-0 p-3">
            <twitter-timeline
              class="p-xs-5 p-lg-0"
              id="robotframework"
              widget-class="twitterprofile"
              sourceType="profile"
              :style="isMobile ? '' : isTablet ? 'height: 850px; overflow: scroll' : 'height: 580px; overflow: scroll'"
              :options="options"
            />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Timeline from "vue-tweet-embed/timeline"
import TalkerItem from "@/Components/TalkerItem"

export default {
  props: ["data"],
  components: {
    "twitter-timeline": Timeline,
    TalkerItem
  },
  data() {
    return {
      options: {
        theme: "dark",
        tweetLimit: window.innerWidth < 992 ? 3 : 10,
        chrome:
          window.innerWidth < 992 ? ["nofooter", "noscroll"] : ["nofooter"]
      },
      hiddenContentShown: false
    };
  },
  computed: {
    isMobile() {
      return window.innerWidth < 992;
    },
    isTablet() {
      return window.innerWidth < 1200;
    }
  },
  created() {
    const anchor = document.URL.split('#').length > 1 ? document.URL.split('#')[1] : null
    if (this.data.textHiddenByDefault && anchor === this.data.header.replace(/ /g, '-').toLowerCase()) this.hiddenContentShown = true
    if (!this.data.talks) return
    if (this.data.talks.some(({ title }) => title && anchor === title.replace(/ /g, '-').toLowerCase())) this.hiddenContentShown = true
  }
};
</script>
