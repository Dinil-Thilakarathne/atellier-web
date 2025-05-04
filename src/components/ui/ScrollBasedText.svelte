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

  onMount(() => {
    const text = document.querySelector('.scroll-based-text');

    if (!text) return;

    const scrollTrigger = ScrollTrigger.create({
      trigger: text,
      start: scrollStart, // Trigger when the top of the element is 80% down the viewport
      end: scrollEnd, // End when the bottom of the element is 20% up the viewport
      onEnter: () => {
        animateText();
        console.log('Entered viewport');
      },
      onLeave: () => {
        console.log('Left viewport');
        if (once) return;
        resetText();
      },
      onEnterBack: () => {
        animateText();
        console.log('Entered viewport again');
      },
      onLeaveBack: () => {
        console.log('Left viewport again');
        if (once) return;
        resetText();
      },
      markers: markers, // Show markers for debugging
      once: once,
    });

    function animateText() {
      const split = new SplitText(text, {
        type: 'lines',
        linesClass: 'line',
        wordsClass: 'word',
        mask: 'lines',
        smartWrap: true,
        autoSplit: true,
        onSplit: self => {
          gsap.set(text, {
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
      gsap.set(text, {
        autoAlpha: 0, // Set initial opacity to 0
        visibility: 'hidden', // Set initial visibility to hidden
      });
    }

    onDestroy(() => {
      scrollTrigger.kill();
    });
  });
</script>

<h1 class={cn('scroll-based-text text-4xl', className)}>
  {text}
</h1>
