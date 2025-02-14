<script lang="ts">
  import { Storage } from "@plasmohq/storage";

  import { isExtensionEnabled } from "~popup/store";
  import ControlEnabled from "~popup/views/ControlEnabled.svelte";
  import ControlQuality from "~popup/views/ControlQuality.svelte";
  import ControlSize from "~popup/views/ControlSize.svelte";
  import Header from "~popup/views/Header.svelte";
  import Promotions from "~popup/views/Promotions.svelte";
  import { initial } from "~shared-scripts/ythd-setup";
  import { getI18n } from "~shared-scripts/ythd-utils";
  import type { QualityFpsPreferences, VideoAutoResize, VideoSize } from "~types";

  export let isOptionsPage = false;
  let qualitiesStored: QualityFpsPreferences;
  let isResizeVideo: VideoAutoResize;
  let sizeVideo: VideoSize;

  const storageLocal = new Storage({ area: "local" });
  const storageSync = new Storage({ area: "sync" });

  Promise.all([
    storageLocal.get<QualityFpsPreferences>("qualities"),
    storageLocal.get<boolean>("isExtensionEnabled"),
    storageSync.get<VideoSize>("size"),
    storageSync.get<VideoAutoResize>("autoResize")
  ]).then(
    ([
      qualities = initial.qualities,
      isExtEnabled = initial.isExtensionEnabled,
      size = initial.size,
      autoResize = initial.isResizeVideo
    ]) => {
      qualitiesStored = qualities;
      $isExtensionEnabled = isExtEnabled;
      sizeVideo = size;
      isResizeVideo = autoResize;
    }
  );

  const isDesktop = !navigator.userAgent.includes("Android");
</script>

<main class:rtl={getI18n("@@bidi_dir") === "rtl"} class:options-page={isOptionsPage}>
  {#if !isOptionsPage}
    <Header />
  {/if}

  {#if $isExtensionEnabled !== undefined}
    <ControlEnabled />
  {/if}

  {#if $isExtensionEnabled}
    {#if qualitiesStored}
      <ControlQuality {qualitiesStored} />
    {/if}

    {#if isDesktop}
      <ControlSize {isResizeVideo} {sizeVideo} />
    {/if}
  {/if}

  <Promotions />
</main>

<style global lang="scss">
  @use "./popup/styles/main";
  @use "./popup/styles/variables";
</style>
