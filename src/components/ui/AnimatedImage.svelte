<script lang="ts">
  import { onMount } from 'svelte';
  import { gsap } from 'gsap';
  import { ScrollTrigger } from 'gsap/all';
  import type { AnimatedImgProps } from '../../types/ui';

  export let imgSrc: AnimatedImgProps['imgSrc'] = '';
  export const barH: AnimatedImgProps['barHeight'] = 40;

  export let scrub = true;
  export let barColor = 'bg-white';

  gsap.registerPlugin(ScrollTrigger);

  let container: HTMLElement;

  onMount(() => {
    const img = container.querySelector('img');
    const imgWrapper = container.querySelector('.img-wrapper');

    if (!img || !imgWrapper) return;

    const setupAnimation = () => {
      const imgHeight = img.clientHeight;
      const barHeight = barH;

      const barCount = Math.floor(imgHeight / barHeight);

      for (let i = 0; i < barCount; i++) {
        const bar = document.createElement('div');
        bar.className = ' absolute left-0 w-full';
        bar.style.height = `${barHeight}px`;
        bar.style.top = `${i * barHeight}px`;
        bar.classList.add(barColor);
        imgWrapper.appendChild(bar);

        if (scrub) {
          gsap.to(bar, {
            display: 'block',
            clipPath: 'inset(0 -2% 100% 0)',
            duration: 0.8,
            delay: i * 0.1,
            ease: 'power1.in',

            scrollTrigger: {
              trigger: img,
              start: 'top 90%',
              end: 'center 60%',
              scrub: scrub,
              toggleActions: 'play none none reverse',
            },
          });
        } else {
          gsap.to(bar, {
            display: 'block',
            clipPath: 'inset(0 -2% 100% 0)',
            duration: 0.8,
            delay: i * 0.1,
            ease: 'power1.in',
            onComplete: () => {
              imgWrapper.removeChild(bar);
            },
          });
        }
      }

      console.log(imgHeight, barHeight, barCount);
    };
    img.addEventListener('load', setupAnimation);
  });
</script>

<div class="relative flex w-full" bind:this={container}>
  <img src={imgSrc} alt="" class="h-auto w-full outline-0" />
  <div class=" img-wrapper absolute top-0 left-0 flex h-full w-full flex-col"></div>
</div>
