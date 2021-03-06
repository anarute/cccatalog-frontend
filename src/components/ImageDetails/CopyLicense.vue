<template>
  <div class="copy-license margin-vertical-normal">
    <h5 class="b-header margin-bottom-small">Credit the Creator</h5>
    <section class="tabs" >
        <ul>
          <li :class="tabClass(0, 'tab')">
            <a class="is-size-6"
               href="#panel0"
               :aria-selected="activeTab == 0"
               @click.prevent="setActiveTab(0)">
              Text
            </a>
          </li>
          <li :class="tabClass(1, 'tab')">
            <a class="is-size-6"
               href="#panel1"
               :aria-selected="activeTab == 1"
               @click.prevent="setActiveTab(1)">
              HTML
            </a>
          </li>
          <li :class="tabClass(2, 'tab')">
            <a class="is-size-6"
               href="#panel2"
               :aria-selected="activeTab == 2"
               @click.prevent="setActiveTab(2)">
              Plain text
            </a>
          </li>
        </ul>
      </section>
      <section class="tabs-content has-background-white padding-normal">
        <div :class="tabClass(0, 'tabs-panel')">
          <span id="attribution" class="photo_usage-attribution is-block" ref="photoAttribution">
            <a :href="image.foreign_landing_url"
                target="_blank"
                rel="noopener">{{ imageTitle }}</a>
            <span v-if="image.creator">
              by
              <a v-if="image.creator_url"
                  :href="image.creator_url"
                  target="_blank"
                  rel="noopener">{{ image.creator }}</a>
              <span v-else>{{ image.creator }}</span>
            </span>
            is licensed under
            <a class="photo_license" :href="licenseURL" target="_blank" rel="noopener">
            {{ fullLicenseName.toUpperCase() }}
            </a>
          </span>

          <copy-button id="copy-attribution-btn"
                       el="#attribution"
                       @copied="onCopyAttribution" />
        </div>
        <div :class="tabClass(1, 'tabs-panel')">
          <textarea id="attribution-html"
                    class="textarea is-family-monospace is-paddingless"
                    :value="attributionHtml"
                    cols="30" rows="4"
                    readonly="readonly">
          </textarea>
          <copy-button id="copy-attribution-btn"
                       el="#attribution-html"
                       @copied="onEmbedAttribution" />
        </div>
        <div :class="tabClass(2, 'tabs-panel')">
          <p id="attribution-text" class="photo_usage-attribution is-block" ref="photoAttribution">
            {{ imageTitle }}
            <span v-if="image.creator">
              by {{ image.creator }}
            </span>
            is licensed under
            {{ fullLicenseName.toUpperCase() }}
          </p>

          <copy-button id="copy-attribution-btn"
                       el="#attribution-text"
                       @copied="onCopyAttribution" />
        </div>
      </section>
  </div>
</template>

<script>
import CopyButton from '@/components/CopyButton';
import { COPY_ATTRIBUTION, EMBED_ATTRIBUTION } from '@/store/action-types';
import { SEND_DETAIL_PAGE_EVENT, DETAIL_PAGE_EVENTS } from '@/store/usage-data-analytics-types';

export default {
  name: 'copy-license',
  props: ['image', 'fullLicenseName', 'attributionHtml', 'licenseURL'],
  components: {
    CopyButton,
  },
  data() {
    return {
      activeTab: 0,
    };
  },
  computed: {
    imageTitle() {
      const title = this.$props.image.title;
      return title !== 'Image' ? `"${title}"` : 'Image';
    },
  },
  methods: {
    tabClass(tabIdx, tabClass) {
      return {
        [tabClass]: true,
        'is-active': tabIdx === this.activeTab,
      };
    },
    setActiveTab(tabIdx) {
      this.activeTab = tabIdx;
    },
    sendDetailPageEvent(eventType) {
      this.$store.dispatch(SEND_DETAIL_PAGE_EVENT, {
        eventType,
        resultUuid: this.$props.image.id,
      });
    },
    onCopyAttribution(e) {
      this.$store.dispatch(COPY_ATTRIBUTION, {
        content: e.content,
      });

      this.sendDetailPageEvent(DETAIL_PAGE_EVENTS.ATTRIBUTION_CLICKED);
    },
    onEmbedAttribution() {
      this.$store.dispatch(EMBED_ATTRIBUTION);

      this.sendDetailPageEvent(DETAIL_PAGE_EVENTS.ATTRIBUTION_CLICKED);
    },
  },
};
</script>

<style lang="scss" scoped>
$width: 35rem;

.copy-license {
  width: $width;
}

textarea {
  width: $width;
  border: none;
  resize: none;
}

.copy-attribution {
  margin-left: auto;
}
.tabs {
  margin-bottom: 0;
}


</style>
