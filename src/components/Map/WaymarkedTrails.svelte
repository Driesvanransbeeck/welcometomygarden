<script>
  export let showHiking = false;
  export let showCycling = true;

  import { getContext, onMount } from 'svelte';
  import key from './mapbox-context.js';

  const { getMap } = getContext(key);
  const map = getMap();

  const getVisibilityProperty = visibility => (visibility ? 'visible' : 'none');

  const toggleHikingVisibility = visible => {
    map.setLayoutProperty('hiking-trails', 'visibility', getVisibilityProperty(visible));
  };

  const toggleCyclingVisibility = visible => {
    map.setLayoutProperty('cycling-trails', 'visibility', getVisibilityProperty(visible));
  };

  let toggleable = false;
  onMount(() => {
    map.on('load', () => {
      map.addSource('waymarked-hiking', {
        type: 'raster',
        tiles: ['https://tile.waymarkedtrails.org/hiking/{z}/{x}/{y}.png'],
        tileSize: 256
      });

      map.addSource('waymarked-cycling', {
        type: 'raster',
        tiles: ['https://tile.waymarkedtrails.org/cycling/{z}/{x}/{y}.png'],
        tileSize: 256
      });

      map.addLayer({
        id: 'hiking-trails',
        type: 'raster',
        source: 'waymarked-hiking',
        layout: {
          visibility: getVisibilityProperty(showHiking)
        }
      });

      map.addLayer({
        id: 'cycling-trails',
        type: 'raster',
        source: 'waymarked-cycling',
        layout: {
          visibility: getVisibilityProperty(showCycling)
        }
      });
      toggleable = true;
    });
  });

  $: if (toggleable) toggleHikingVisibility(showHiking);
  $: if (toggleable) toggleCyclingVisibility(showCycling);
</script>
