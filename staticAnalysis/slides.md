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

**Todd Gureckis**
_New York University_ <a href="https://todd.gureckislab.org" class="ns-c-iconlink"><mdi-open-in-new /></a>


---
hideInToc: true
---

# What's in this talk

<br>

<Toc />

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
