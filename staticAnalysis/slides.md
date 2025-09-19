---
# You can also start simply with 'default'
theme: neversink
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://cover.sli.dev
# some information about your slides (markdown enabled)
title: Open static firmware analysis
# apply unocss classes to the current slide
class: text-center
color: dark
# https://sli.dev/features/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
# transition: slide-left
# enable MDC Syntax: https://sli.dev/features/mdc
mdc: true
hideInToc: true
# open graph
# seoMeta:
#  ogImage: https://cover.sli.dev
---

# Open static firmware analysis

... or what is a stack, why should I care and how to do it effectifly :)

<div @click="$slidev.nav.next" class="mt-12 py-1" hover:bg="white op-10">
  Press Space for next page <carbon:arrow-right />
</div>


:: note ::

<div class="fw-200" >

Slidev template neversink by <a href="https://todd.gureckislab.org" class="ns-c-iconlink">Todd Gureckis</a>

</div>


---
hideInToc: true
---

### What are we talking about

<img src="/featureidea.png" alt="drawing" width="80%"/>

<!-- https://image.slidesharecdn.com/stevejobsvsbillgates-130528160028-phpapp02/95/slide-7-1024.jpg -->
<!-- https://productmanagermeme.com/storage/assets/images/i-have-a-feature-idea-1738836703.webp -->

<br>

<Toc columns="1" maxDepth=1 />

---
---

# How to tackle limited device memory on maturing firmware with increasing features

**Tools to help you start thinking**

<br>

<Toc columns="1" minDepth=2 maxDepth=3 mode='onlySiblings'/>


---
layout: center
---

## Understand RAM and ROM usage of your application (at one point)

---
layout: top-title-two-cols
color: dark
title: 'Determine dynamic stack usage - To paint a stack'
level: 3
---

:: title ::

### Determine dynamic stack usage - To paint a stack

:: left ::

In zephyr part of the Thread analyzer or in FreeRTOS as Highwarter marks...

<img style="height:250px;" src="/stack-painting.png" />

...running and trigger events to enter suspected largest call chain.

<small>1. [interrupt.memfault.com/blog/measuring-stack-usage](https://interrupt.memfault.com/blog/measuring-stack-usage)</small>

:: right ::

<img style="height:50px; float: right;" src="/paintbrush.png" />
<v-switch>
  <template #0><img src="/stackPaint01.png" /> </template>
  <template #1><img src="/stackPaint02.png" /> </template>
  <template #2><img src="/stackPaint03.png" /> </template>
  <template #3><img src="/stackPaint04.png" /> </template>
  <template #4><img src="/stackPaint05.png" /> </template>
  <template #5><img src="/stackPaint06.png" /> </template>
  <template #6><img src="/stackPaint07.png" /> </template>
  <template #7><img src="/stackPaint08.png" /> </template>
  <template #8><img src="/stackPaint09.png" /> </template>
  <template #9><img src="/stackPaint10.png" /> </template>
  <template #10><img src="/stackPaint11.png" /> </template>
  <template #11><img src="/stackPaint12.png" /> </template>
  <template #12><img src="/stackPaint13.png" /> </template>
  <template #13><img src="/stackPaint14.png" /> </template>
</v-switch>

<style>
    img {
        height: 50%;
    }
</style>

---

In zephyr we can tweak our user or also the default and driver stack threads then...

<img src="/defaultStackSizes.png" alt="drawing" width="60%"/>

...think of networking, USB, BLE, ...

...but how far can we go and are we still in save margins as development continues?

---
title: 'Looking into MAP files'
level: 3
---

# Looking into MAP files

<img src="/MapViewer.png" alt="drawing" width="90%"/>

1. [github.com/govind-mukundan/MapViewer](https://github.com/govind-mukundan/MapViewer)
1. [interrupt.memfault.com/blog/get-the-most-out-of-the-linker-map-file](https://interrupt.memfault.com/blog/get-the-most-out-of-the-linker-map-file)
1. [docs.zephyrproject.org/latest/develop/optimizations/tools.html#build-targets-ram-plot-rom-plot](https://docs.zephyrproject.org/latest/develop/optimizations/tools.html#build-targets-ram-plot-rom-plot)
1. [github.com/ARMmbed/mbed-os-linker-report](https://github.com/ARMmbed/mbed-os-linker-report?tab=readme-ov-file)
1. [github.com/eleciawhite/MapFiles/blob/main/BuriedTreasureMapFiles_slides_ewhite.pdf](https://github.com/eleciawhite/MapFiles/blob/main/BuriedTreasureMapFiles_slides_ewhite.pdf)
1. [github.com/bmwcarit/Emma/tree/master](https://github.com/bmwcarit/Emma/tree/master)

<!-- https://memoryexplorer.dev/ -->

---
layout: iframe
# mbed-os-linker-report
url: https://armmbed.github.io/mbed-os-linker-report/
scale: 0.65
---

---

### Looking into ELF files - static stack usages with some help of GCC

ELF files are a one stop shop to get

<small>

* symbol names, addresses and sizes for variables and functions
* static calls between functions

</small>

<v-click>

... although parsing can be slow, gcc offers some info directly like (1)

<small>

* `-fstack-usage` -> the size of a function on the stack
* `-fcallgraph-info` -> all static calls of a function

</small>

</v-click>

<v-click>

...and some information we can not consider without extra info like

<small>

* dynamic calls
* which functions in an RTOS are thread entrypoints on their own stack

</small>

</v-click>

<hr>

<small>

1. https://www.adacore.com/uploads/technical-papers/Stack_Analysis.pdf
1. [gcc -fstack-usage](https://gcc.gnu.org/onlinedocs/gnat_ugn/Static-Stack-Usage-Analysis.html) -> generates .su files - in Kconfig enabled by CONFIG_STACK_USAGE=y
1. west build -t puncover

</small>

---
layout: iframe
# puncover_html
url: https://paulwuertz.github.io/puncover_html/cannectivity_lpcxpresso55s16/index.html
scale: 0.65
---

---
layout: center
---

## Understand RAM and ROM usage of your application (over time)

---
layout: image-right
image: /overtimestats.png
---

### Tracking Firmware Size Metrics

One nice example to set up firmware size tracking in CI pipeline (1).

<v-click>

Includes most high-level info, but we...

* need to setup a database
* miss more precice blames
* do not have data of thread stack data
* ...

<hr>

-> So there is room to improve :)

<hr>

</v-click>


<small>1. https://interrupt.memfault.com/blog/code-size-deltas</small>

---

# Motivation - Proposals where we can improve existing tools for application development


<br>

<Toc columns="1" minDepth=2 maxDepth=3 mode='onlySiblings'/>

<v-clicks>

</v-clicks>

---
layout: two-cols-title
title: 'Extensions to puncover'
level: 2
---

:: title ::

### Motivation - Ideas where we can improve for RTOS development

:: left ::


:: right ::

<v-clicks>

</v-clicks>

---
title: 'Define thread entries for reports and warnings+errors in CI'
level: 3
---

daddad

---
title: 'Define dynamic calls manually'
level: 3
---

daddad



---
layout: two-cols-title
title: 'Symbol and analysis exports to file'
level: 3
---

:: title ::

### Motivation - Ideas where we can improve for RTOS development

:: left ::


:: right ::

<v-clicks>

</v-clicks>

---
layout: two-cols-title
title: 'Independent tool to visualize and compare puncover exports - pexplorer'
level: 2
---

:: title ::

## Motivation - Ideas where we can improve for RTOS development

:: left ::


:: right ::

<v-clicks>

</v-clicks>

---
title: 'Compare two builds - code reviews, learning and beyond'
level: 3
---

### Compare two builds

daddad

---
title: 'More graphics, more intuitivity'
level: 3
---

### More graphics, more intuitivity

daddad
