<script lang="ts">
  import { onMount, onDestroy } from 'svelte';
  import { gsap } from 'gsap';
  import { ScrollTrigger } from 'gsap/ScrollTrigger';
  import { animate } from 'motion/react';

  export let duration = 0.5;
  export let delay = 0;
  export let markers = false;
  export let scrollStart = 'top 90%';
  export let scrollEnd = 'bottom 0%';
  export let once = true;

  gsap.registerPlugin(ScrollTrigger);

  let container: HTMLElement;

  onMount(() => {
    if (!container) return;

    let scrollTrigger: ScrollTrigger;

    if (once == false) {
      scrollTrigger = ScrollTrigger.create({
        trigger: container,
        start: scrollStart,
        end: scrollEnd,
        onEnter: () => animateIn(),
        onLeave: () => reset(),
        onEnterBack: () => animateIn(),
        onLeaveBack: () => reset(),
        markers: markers,
      });
    } else {
      animateIn();
    }

    function animateIn() {
      gsap.fromTo(
        container,
        { autoAlpha: 0, y: 50 },
        { autoAlpha: 1, y: 0, duration: duration, delay: delay, ease: 'power1.out' }
      );
    }

    function reset() {
      gsap.set(container, { autoAlpha: 0, y: 50 });
    }

    onDestroy(() => {
      scrollTrigger.kill();
    });
  });
</script>

<div class=" overflow-clip">
  <div bind:this={container} class=" invisible">
    <slot></slot>
  </div>
</div>
