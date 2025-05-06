<script lang="ts">
  import { onMount } from 'svelte';
  import { gsap } from 'gsap';
  import { ScrollTrigger } from 'gsap/all';
  import type { AnimatedImgProps } from '../../types/ui';

  export let imgSrc: AnimatedImgProps['imgSrc'] = '';
  export const barH: AnimatedImgProps['barHeight'] = 40;

  export let barColor = 'bg-white';
  export const once = false;

  gsap.registerPlugin(ScrollTrigger);

  let container: HTMLElement;

  onMount(() => {
    const img = container.querySelector('img');
    const imgWrapper = container.querySelector('.img-wrapper');

    console.log(img, imgWrapper);

    if (!img || !imgWrapper) return;

    const setupAnimation = () => {
      const imgHeight = img.clientHeight;
      const barHeight = barH;

      const barCount = Math.ceil(imgHeight / barHeight);

      let scrollTrigger: ScrollTrigger;

      for (let i = 0; i < barCount; i++) {
        const bar = document.createElement('div');
        bar.className = ' absolute left-0 w-full';
        bar.style.height = `${barHeight}px`;
        bar.style.top = `${i * barHeight}px`;
        bar.classList.add(barColor);
        imgWrapper.appendChild(bar);

        if (once) {
          scrollTrigger = ScrollTrigger.create({
            trigger: img,
            start: 'top 90%',
            end: 'top +=100px',
            markers: false,
            onEnter: () => animateBar(bar),
          });
        } else {
          scrollTrigger = ScrollTrigger.create({
            trigger: img,
            start: 'top 90%',
            end: 'bottom 50%+=100px',
            markers: false,
            onEnter: () => animateBar(bar),
            onEnterBack: () => animateBar(bar),
          });
        }

        function animateBar(bar: HTMLDivElement) {
          gsap.to(bar, {
            display: 'block',
            clipPath: 'inset(0 -2% 100% 0)',
            duration: 0.6,
            delay: i * 0.1,
            ease: 'power1.in',
            onComplete: () => {
              if (imgWrapper) imgWrapper.removeChild(bar);
            },
          });
        }
      }
    };
    img.addEventListener('load', setupAnimation);
  });
</script>

<div class="relative flex w-full" bind:this={container}>
  <img src={imgSrc} alt="" class="h-auto w-full outline-0" />
  <div class=" img-wrapper absolute top-0 left-0 flex h-full w-full flex-col"></div>
</div>
