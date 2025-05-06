<script lang="ts">
  import { onMount, onDestroy } from 'svelte';
  import { gsap } from 'gsap';
  import { SplitText } from 'gsap/SplitText';
  import { ScrollTrigger } from 'gsap/ScrollTrigger';
  import { cn } from '../../lib/utils';

  const {
    text = 'Hello World',
    className = '',
    markers = false,
    scrollStart = 'top 90%',
    scrollEnd = 'bottom 0%',
    duration = 0.5,
    once = true,
  } = $props();

  gsap.registerPlugin(SplitText);
  gsap.registerPlugin(ScrollTrigger);

  let ele: HTMLElement | null;

  onMount(() => {
    if (!ele) return;

    let scrollTrigger: ScrollTrigger;

    if (once == true) {
      scrollTrigger = ScrollTrigger.create({
        trigger: ele,
        start: scrollStart, // Trigger when the top of the element is 80% down the viewport
        end: scrollEnd, // End when the bottom of the element is 20% up the viewport
        onEnter: () => {
          animateText();
        },
        markers: markers, // Show markers for debugging
      });
    } else {
      scrollTrigger = ScrollTrigger.create({
        trigger: ele,
        start: scrollStart, // Trigger when the top of the element is 80% down the viewport
        end: scrollEnd, // End when the bottom of the element is 20% up the viewport
        onEnter: () => {
          animateText();
        },
        onLeave: () => {
          resetText();
        },
        onEnterBack: () => {
          animateText();
        },
        onLeaveBack: () => {
          resetText();
        },
        markers: markers, // Show markers for debugging
      });
    }

    function animateText() {
      const split = new SplitText(ele, {
        type: 'lines',
        linesClass: 'line',
        wordsClass: 'word',
        mask: 'lines',
        smartWrap: true,
        autoSplit: true,
        onSplit: self => {
          gsap.set(ele, {
            autoAlpha: 1, // Set initial opacity to 0
            visibility: 'visible', // Set initial visibility to hidden
          });
          gsap.from(self.lines, {
            duration: duration,
            y: 100, // animate from 100px below
            autoAlpha: 0, // fade in from opacity: 0 and visibility: hidden
            stagger: 0.1, // 0.1 seconds between each
            onComplete: () => {
              self.revert();
            },
          });
        },
      });
    }

    function resetText() {
      gsap.set(ele, {
        autoAlpha: 0, // Set initial opacity to 0
        visibility: 'hidden', // Set initial visibility to hidden
      });
    }

    // animateText();
  });
</script>

<h1 class={cn('scroll-based-text text-4xl', className)} bind:this={ele}>
  {text}
</h1>
