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

<v-click>

ELF files are a one stop shop to get

<small>

* symbol names, addresses and sizes for variables and functions
* static calls between functions


</small>
</v-click>

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
</v-click>

<hr>

<v-click>
-> So there is room to improve :)

</v-click>

<hr>



<small>1. https://interrupt.memfault.com/blog/code-size-deltas</small>

---

# Motivation - Proposals where we can improve existing tools for application development


<br>

<Toc columns="1" minDepth=2 maxDepth=3 mode='onlySiblings'/>

---
layout: two-cols-title
title: 'Extensions to puncover'
level: 2
---

:: title ::

### Extensions to puncover

:: left ::

<v-clicks at="0">

* elf_file, build_dir, src_root, port are existing puncover arguments...

</v-clicks>

:: right ::

CLI Settings as a yaml file with configargparse

```yaml {|1-4}
elf_file: /home/p4w5/git/cannecti/build/zephyr/zephyr.elf
build_dir: /home/p4w5/git/cannecti/build/
src_root: /home/p4w5/git/Prusa-Firmware-Buddy/
port: 5001
feature_version: "cannectivity-1.0"
generate-report: true
report-type: json
report-filename: report
report-max-static-stack-usage: [
  bg_thread_main:::1024,
  led_thread:::1024,
  gs_usb_tx_thread:::1024,
  gs_usb_rx_thread:::1024,
  log_process_thread_func:::768,
]
add-dynamic-calls: [
    z_impl_can_send->can_mcan_send,
    can_mcan_send->gs_usb_can_tx_callback,
]
error_on_exceeded_stack_usage: true
warn_threshold_size_for_max_static_stack_usage: 100
```


---
title: 'Define thread entries for reports and warnings+errors in CI'
layout: two-cols-title
level: 3
---

:: title ::

### Extensions to puncover - Settings to define threads and stack sizes

:: left ::

* elf_file, build_dir, src_root, port are existing puncover arguments...
* list of all function symbols that define a thread entry
    * seperated by ::: to allow C++ :: operator :)
    * define threshold to raise a warning and enable an error on the pipeline


:: right ::

CLI Settings as a yaml file with configargparse

```yaml {9-15,20,21}
elf_file: /home/p4w5/git/cannecti/build/zephyr/zephyr.elf
build_dir: /home/p4w5/git/cannecti/build/
src_root: /home/p4w5/git/Prusa-Firmware-Buddy/
port: 5001
feature_version: "cannectivity-1.0"
generate-report: true
report-type: json
report-filename: report
report-max-static-stack-usage: [
  bg_thread_main:::1024,
  led_thread:::1024,
  gs_usb_tx_thread:::1024,
  gs_usb_rx_thread:::1024,
  log_process_thread_func:::768,
]
add-dynamic-calls: [
    z_impl_can_send->can_mcan_send,
    can_mcan_send->gs_usb_can_tx_callback,
]
error_on_exceeded_stack_usage: true
warn_threshold_size_for_max_static_stack_usage: 100
```

---
title: 'Define dynamic calls manually'
layout: two-cols-title
level: 3
---

:: title ::

### Extensions to puncover - Define dynamic calls manually

:: left ::

* elf_file, build_dir, src_root, port are existing puncover arguments...
* list of all function symbols that define a thread entry
    * seperated by ::: to allow C++ :: operator :)
    * define threshold to raise a warning and enable an error on the pipeline
* define dynamic calls as arguments we know are used as callback

:: right ::

CLI Settings as a yaml file with configargparse

```yaml {16-19}
elf_file: /home/p4w5/git/cannecti/build/zephyr/zephyr.elf
build_dir: /home/p4w5/git/cannecti/build/
src_root: /home/p4w5/git/Prusa-Firmware-Buddy/
port: 5001
feature_version: "cannectivity-1.0"
generate-report: true
report-type: json
report-filename: report
report-max-static-stack-usage: [
  bg_thread_main:::1024,
  led_thread:::1024,
  gs_usb_tx_thread:::1024,
  gs_usb_rx_thread:::1024,
  log_process_thread_func:::768,
]
add-dynamic-calls: [
    z_impl_can_send->can_mcan_send,
    can_mcan_send->gs_usb_can_tx_callback,
]
error_on_exceeded_stack_usage: true
warn_threshold_size_for_max_static_stack_usage: 100
```

---
layout: two-cols-title
title: 'Symbol and analysis exports to file'
level: 3
---

:: title ::

### Extensions to puncover - Symbol and analysis exports to file

:: left ::

<v-clicks at="0">

* function export
    * asm as text array
    * hack: deepest_callex_tree's as nested JSON (in python it is circular reference)
    * -> could be a better defined scheme - help creating and reviewing appreciated :)
* variable export
    * maybe more typeinfo would be nice ^^

</v-clicks>

:: right ::

````md magic-move
```json{*}{class:'!children:text-xs'}
[{
    "name": "led_tick", "base_file": "led.c", "line": 371,
    "asm": [
        "led_tick():",
        "     610:\tb507      \tpush\t{r0, r1, r2, lr}",
        "     612:\t2000      \tmovs\tr0, #0",
        "     614:\tf7ff ffd4 \tbl\t5c0 <led_event_enqueue.constprop.0>",
        "     618:\tb130      \tcbz\tr0, 628 <led_tick+0x18>",
        "z_log_msg_simple_create_2():",
        "     628:\tb003      \tadd\tsp, #12",
        "...",
    ],
    "type": "function", "address": "00000610", "size": 40,
    "file": "home/p4w5/git/cannecti/cannectivity/app/src/led.c",
    "callers": "[]",
    "callees": "[\"home/p4w5/git/cannecti/cannectivity/app/src/led.c/led_event_enqueue\", \"home/p4w5/git/cannecti/zephyr/subsys/logging/log_msg.c/z_impl_z_log_msg_simple_create_2\"]",
    "calls_float_function": false,
    "display_name": "led_tick", "stack_size": 16, "stack_qualifiers": "static",
    "deepest_caller_tree_size": 16,
    "deepest_caller_tree": "[{\"full_symbol_path\": \"home/p4w5/git/cannecti/cannectivity/app/src/led.c/led_tick\", \"code_size\": 40, \"stack_size\": 16}]",
    "deepest_callee_tree_size": 496,
    "deepest_callee_tree": "[{\"full_symbol_path\": \"home/p4w5/git/cannecti/cannectivity/app/src/led.c/led_tick\", \"code_size\": 40, \"stack_size\": 16}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/cannectivity/app/src/led.c/led_event_enqueue\", \"code_size\": 80, \"stack_size\": 64}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/subsys/logging/log_msg.c/z_impl_z_log_msg_static_create\", \"code_size\": 264, \"stack_size\": 136}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/subsys/logging/log_msg.c/z_log_msg_finalize\", \"code_size\": 50, \"stack_size\": 16}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/subsys/logging/log_core.c/z_log_msg_commit\", \"code_size\": 36, \"stack_size\": 8}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/subsys/logging/log_core.c/z_log_msg_post_finalize\", \"code_size\": 120, \"stack_size\": 16}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/subsys/logging/log_core.c/z_impl_log_process\", \"code_size\": 168, \"stack_size\": 16}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/subsys/logging/log_core.c/z_log_msg_free\", \"code_size\": 16, \"stack_size\": 0}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/lib/os/mpsc_pbuf.c/mpsc_pbuf_free\", \"code_size\": 118, \"stack_size\": 24}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/kernel/sem.c/z_impl_k_sem_give\", \"code_size\": 116, \"stack_size\": 16}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/kernel/poll.c/z_handle_obj_poll_events\", \"code_size\": 62, \"stack_size\": 16}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/kernel/poll.c/signal_poll_event\", \"code_size\": 134, \"stack_size\": 24}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/kernel/work.c/z_work_submit_to_queue\", \"code_size\": 38, \"stack_size\": 16}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/kernel/work.c/submit_to_queue_locked\", \"code_size\": 180, \"stack_size\": 24}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/kernel/work.c/notify_queue_locked\", \"code_size\": 14, \"stack_size\": \"?\"}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/kernel/sched.c/z_sched_wake\", \"code_size\": 68, \"stack_size\": 16}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/kernel/sched.c/ready_thread\", \"code_size\": 132, \"stack_size\": 16}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/kernel/timeslicing.c/z_reset_time_slice\", \"code_size\": 80, \"stack_size\": 16}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/kernel/timeout.c/z_add_timeout\", \"code_size\": 236, \"stack_size\": 24}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/kernel/timeout.c/next_timeout\", \"code_size\": 50, \"stack_size\": 8}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/kernel/timeout.c/elapsed\", \"code_size\": 20, \"stack_size\": 0}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/drivers/timer/cortex_m_systick.c/sys_clock_elapsed\", \"code_size\": 60, \"stack_size\": 16}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/drivers/timer/cortex_m_systick.c/elapsed\", \"code_size\": 76, \"stack_size\": 8}]"
}, // {...}, ...,
]
```
```json{*}{class:'!children:text-xs'}
[{
    "name": "led_tick", "base_file": "led.c", "line": 371,
    "asm": [
        "led_tick():",
        "     610:\tb507      \tpush\t{r0, r1, r2, lr}",
        "     612:\t2000      \tmovs\tr0, #0",
        "     614:\tf7ff ffd4 \tbl\t5c0 <led_event_enqueue.constprop.0>",
        "     618:\tb130      \tcbz\tr0, 628 <led_tick+0x18>",
        "z_log_msg_simple_create_2():",
        "     628:\tb003      \tadd\tsp, #12",
        "...",
    ],
    "type": "function", "address": "00000610", "size": 40,
    "file": "home/p4w5/git/cannecti/cannectivity/app/src/led.c",
    "callers": "[]",
    "callees": "[\"home/p4w5/git/cannecti/cannectivity/app/src/led.c/led_event_enqueue\", \"home/p4w5/git/cannecti/zephyr/subsys/logging/log_msg.c/z_impl_z_log_msg_simple_create_2\"]",
    "calls_float_function": false,
    "display_name": "led_tick", "stack_size": 16, "stack_qualifiers": "static",
    "deepest_caller_tree_size": 16,
    "deepest_caller_tree": "[{\"full_symbol_path\": \"home/p4w5/git/cannecti/cannectivity/app/src/led.c/led_tick\", \"code_size\": 40, \"stack_size\": 16}]",
    "deepest_callee_tree_size": 496,
    "deepest_callee_tree": "[{\"full_symbol_path\": \"home/p4w5/git/cannecti/cannectivity/app/src/led.c/led_tick\", \"code_size\": 40, \"stack_size\": 16}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/cannectivity/app/src/led.c/led_event_enqueue\", \"code_size\": 80, \"stack_size\": 64}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/subsys/logging/log_msg.c/z_impl_z_log_msg_static_create\", \"code_size\": 264, \"stack_size\": 136}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/subsys/logging/log_msg.c/z_log_msg_finalize\", \"code_size\": 50, \"stack_size\": 16}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/subsys/logging/log_core.c/z_log_msg_commit\", \"code_size\": 36, \"stack_size\": 8}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/subsys/logging/log_core.c/z_log_msg_post_finalize\", \"code_size\": 120, \"stack_size\": 16}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/subsys/logging/log_core.c/z_impl_log_process\", \"code_size\": 168, \"stack_size\": 16}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/subsys/logging/log_core.c/z_log_msg_free\", \"code_size\": 16, \"stack_size\": 0}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/lib/os/mpsc_pbuf.c/mpsc_pbuf_free\", \"code_size\": 118, \"stack_size\": 24}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/kernel/sem.c/z_impl_k_sem_give\", \"code_size\": 116, \"stack_size\": 16}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/kernel/poll.c/z_handle_obj_poll_events\", \"code_size\": 62, \"stack_size\": 16}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/kernel/poll.c/signal_poll_event\", \"code_size\": 134, \"stack_size\": 24}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/kernel/work.c/z_work_submit_to_queue\", \"code_size\": 38, \"stack_size\": 16}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/kernel/work.c/submit_to_queue_locked\", \"code_size\": 180, \"stack_size\": 24}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/kernel/work.c/notify_queue_locked\", \"code_size\": 14, \"stack_size\": \"?\"}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/kernel/sched.c/z_sched_wake\", \"code_size\": 68, \"stack_size\": 16}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/kernel/sched.c/ready_thread\", \"code_size\": 132, \"stack_size\": 16}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/kernel/timeslicing.c/z_reset_time_slice\", \"code_size\": 80, \"stack_size\": 16}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/kernel/timeout.c/z_add_timeout\", \"code_size\": 236, \"stack_size\": 24}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/kernel/timeout.c/next_timeout\", \"code_size\": 50, \"stack_size\": 8}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/kernel/timeout.c/elapsed\", \"code_size\": 20, \"stack_size\": 0}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/drivers/timer/cortex_m_systick.c/sys_clock_elapsed\", \"code_size\": 60, \"stack_size\": 16}, {\"full_symbol_path\": \"home/p4w5/git/cannecti/zephyr/drivers/timer/cortex_m_systick.c/elapsed\", \"code_size\": 76, \"stack_size\": 8}]"
}, // {...}, ...,
]
```
```json{*}{class:'!children:text-xs'}
[
// [{...}, ...,
{
    "name": "buf32",
    "size": 1024,
    "base_file": "log_core.c",
    "line": 125,
    "type": "variable",
    "address": "20000ee0",
    "file": "~/cannecti/zephyr/subsys/logging/log_core.c",
    "display_name": "buf32"
}]
```
````

---
title: 'Independent tool to visualize and compare puncover exports - pexplorer'
level: 2
---


## New tool to visualize and compare puncover exports - pexplorer

![](/welcomexplorer.png)

---
title: 'Compare two builds - code reviews, learning and beyond'
level: 3
---

### Compare two builds

![](/diffview.png)

---
title: 'More graphics, more intuitivity to identify where logic, data and include costs are'
level: 3
---

### More graphics, more intuiton to identify where logic, data and libs costs memory

![](/stacKSizeTrend.png)

---
layout: iframe
# mbed-os-linker-report
url: https://paulwuertz.github.io/pexplorer/
scale: 0.65
---

---
layout: two-cols-title
title: 'Motivation - Ideas where we can improve (for RTOS development)'
level: 1
---

# Motivation - Ideas where we can improve (for RTOS development)


![](/gliwa-book.png)

https://www.gliwa.com/book/
