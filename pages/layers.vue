<script setup>
import { onMounted, onUnmounted, ref } from 'vue';
import gsap from 'gsap';
import { ScrollTrigger } from 'gsap/ScrollTrigger';

const main = ref();
let scrollTween;
let ctx;

const goToSection = (i) => {
  // Remove the GSAP instance with the specific ID
  // to prevent memory leak
  ctx.data.forEach((e) => {
    if (e.vars && e.vars.id === 'scrollTween') {
      e.kill();
    }
  });
  ctx.add(() => {
    scrollTween = gsap.to(window, {
      scrollTo: { y: i * window.innerHeight, autoKill: false },
      duration: 1,
      id: 'scrollTween',
      onComplete: () => (scrollTween = null),
      overwrite: true,
    });
  });
};

onMounted(() => {
  ctx = gsap.context((self) => {
    const panels = self.selector('.panel');
    panels.forEach((panel, i) => {
      ScrollTrigger.create({
        trigger: panel,
        start: 'top bottom',
        end: '+=200%',
        onToggle: (self) => self.isActive && !scrollTween && goToSection(i),
      });
    });
    ScrollTrigger.create({
      start: 0,
      end: 'max',
      snap: 1 / (panels.length - 1),
    });
  }, main.value); // <- Scope!
});

onUnmounted(() => {
  ctx.revert(); // <- Easy Cleanup!
});
</script>

<template>
  <main ref="main">
    <section class="description panel blue">
      <div>
        <h1>Layered pinning</h1>
        <p>Use pinning to layer panels on top of each other as you scroll.</p>
        <div class="scroll-down">
          Scroll down
          <div class="arrow"></div>
        </div>
      </div>
    </section>
    <section class="panel red">ONE</section>
    <section class="panel orange">TWO</section>
    <section class="panel purple">THREE</section>
    <section class="panel green">FOUR</section>
  </main>
</template>
