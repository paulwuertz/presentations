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

# What's in this talk

<br>

<Toc columns="2" maxDepth="3"/>


---
layout: image-right
image: /puncover_two_col.png
backgroundSize: contain
color: dark
---

# Intro into the state of elf analysis in zephyr

<small>

**west build -t puncover**

</small>

Optimization tools in [zephyr doc](https://docs.zephyrproject.org/latest/develop/optimizations/tools.html)

<v-clicks>

* ram and rom report as cli tools
* puncover as a GUI verision that also has...
    * assembly view of functions
    * extracted call-graph of your firmware
    * call-stack sizes when build with a flag*
* puncover has some 🔋  included already
* demo time - TODO link to static export :)

</v-clicks>

<small>* [gcc -fstack-usage](https://gcc.gnu.org/onlinedocs/gnat_ugn/Static-Stack-Usage-Analysis.html) -> generates .su files <br> * in Kconfig CONFIG_STACK_USAGE=y</small>

---
layout: image-right
image: /gittruck-readme2.png
color: dark
---

# Motivation - How to get into a new firmware codebase?

<v-clicks>

* documentation if we are lucky :)
* -> explore the files and folders
    * with tool like [git-truck](https://github.com/git-truck/git-truck)
    * or with firmware stats like [puncover](https://github.com/HBehrens/puncover/) or [linker-reports](https://github.com/ARMmbed/mbed-os-linker-report/tree/master)
* when a RTOS is used, look at all threads and data channels like message queues in between
* know the history - commits, release notes, merge requests...

</v-clicks>

---
layout: two-cols-title
color: dark
---

:: title ::

# Find some bytes somewhere...

:: left ::

### The problem

<v-clicks>

* product with STM32F1 - 10+ years of features
* firmware supports several product configurations
* and 0-100s of bytes of memory left (Flash and RAM)
* running FreeRTOS with ~12 threads
* ~32/96kB RAM used by RTOS stacks
* new feature and no memory to implement -> optimize or triage

</v-clicks>

:: right ::

### Newly learned optimization helper:

* gcc .. -fstack-usage ...


```cpp {2,3|5|all}
void CoreControlModule::receivedMessage(Msg msg)
{
    switch(msg.type)
    {
        /* ... */
        case MSG_TYPES::SETTINGS:
        {
            const GRID_SETTINGS::Settings_t config
                = *reinterpret_cast<GRID_SETTINGS::Settings_t*>(Packet->Data);

        peripheralController.setGridSettings<GRID_SETTINGS::Settings_t>(config);
                HC_IC_postEventDataProcessed(eventDataProcessed, (uint8_t)PF_ACK, 0, NULL);
            }
            break;
        }
        /* ... */
        case MSG_TYPES::PERIPHERAL_UPDATE:
        {
            const GRID_SETTINGS::Settings_t config
                = *reinterpret_cast<GRID_SETTINGS::Settings_t*>(Packet->Data);

        peripheralController.setGridSettings<GRID_SETTINGS::Settings_t>(config);
                HC_IC_postEventDataProcessed(eventDataProcessed, (uint8_t)PF_ACK, 0, NULL);
            }
            break;
        }
        /* ... */
```

<v-clicks>


</v-clicks>


---
layout: top-title-two-cols
color: dark
---

:: title ::

# Determine dynamic stack usage - To paint a stack


:: left ::



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
