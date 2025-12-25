---
# ===== Required =====
title: "Winter Long Driving Todo Check List"
date: 2025-12-24 18:00:00 -0600

# Max 2 levels only
categories: [Check List, Winter Driving]

# Flat list, keep consistent spelling
tags: [Traveling, Survival]

# ===== Chirpy Features =====
toc: true           # Table of contents
math: true          # LaTeX equations
mermaid: true       # Flow diagrams
pin: false          # Pin to homepage (true/false)

---

![winter_driving](assets/img/winter.png)

## Foreword
This is my safety to-do check list to make long winter driving in Canada less intimidating, so that I don't die, and therefore do more traveling. 

This interactive list is arranged in a time line manner, i.e when to what. The checked boxes will reset if the page is refresh.

## A Day before travel
- [ ] Vehicle Works:
  - [ ] Check engine oil level (5W-20)
  - [ ] Check coolant/antifreeze fluid level
  - [ ] Check break fluid level (dot 4)
  - [ ] Check windshield fluid level
  - [ ] Check tires pressure (32 psi)
  - [ ] Check spare tire pressure (60 psi)
  - [ ] Check wheel lug nuts tightness (80 ft-lbs)
  - [ ] Fill up gas (87)
  - [ ] Plug in car oil pan heater and battery charger
  - [ ] Remove build up snow blocks in the fencers

- [ ] Emergency Items:
  - [ ] Headlamps
  - [ ] Tires patch kit
  - [ ] Portable tire pumps (full charge)
  - [ ] Car battery jump cables
  - [ ] Extra winter gloves
  - [ ] Extra dry socks
  - [ ] Blanket
  - [ ] Scented candle and matches

- [ ] Navigation:
  - [ ] Update Google and Apple offline maps
  - [ ] Pick route (has most gas stations/towns) and break stops (max 2 hours apart)
  - [ ] Check weather forecast

- [ ] Consumables:
  - [ ] 2x Monster Zero Ultra
  - [ ] Hot water

## Right before travel
- [ ] Re-Evaluate Traveling:
  - [ ] Check weather updates
  - [ ] Check route condition (Sask Highway Hotline App)

- [ ] Final Vehicle Inspection:
  - [ ] Clear windshield and wipers
  - [ ] Clear headlights
  - [ ] Clear mirrors and front windows
  - [ ] Clear back window, rear wiper and backup cam
  - [ ] Clear rear lights

- [ ] Mental Support
  - [ ] Ask the Lord for protection
  - [ ] Share location to a trusty fam/friend

## During travel
- (Optional) Abide speed limits
- (Optional) Don't die
- Only stop to help a stranger after checking the surroundings. Avoid stopping if in a middle of nowhere; and if a woman is waving for help, just keep on going. It is too sus and has high chance of getting jumped.
- Before attempt something reckless, think of cơm tấm sườn bì chả trứng with de extra cơm.

<script>
document.addEventListener("DOMContentLoaded", () => {
  function enableTaskListIcons() {
    function toggleIcon(icon, check) {
      if (check) {
        icon.classList.remove("fa-circle", "far");
        icon.classList.add("fa-check-circle", "fas", "checked");
      } else {
        icon.classList.remove("fa-check-circle", "fas", "checked");
        icon.classList.add("fa-circle", "far");
      }
    }
    function getImmediateChildIcons(parentLi) {
      const childLis = Array.from(parentLi.children).filter(
        (li) => li.tagName === "UL" || li.tagName === "LI"
      );
      let icons = [];
      childLis.forEach((el) => {
        if (el.tagName === "UL") {
          icons.push(
            ...Array.from(el.children)
              .filter((li) => li.classList.contains("task-list-item"))
              .map((li) => li.querySelector("i"))
              .filter(Boolean)
          );
        }
      });
      return icons;
    }
    function allChildrenChecked(parentLi) {
      const children = getImmediateChildIcons(parentLi);
      if (!children.length) return false;
      return children.every((icon) => icon.classList.contains("fa-check-circle"));
    }
    function updateParent(parentItem) {
      const parentIcon = parentItem.querySelector("i");
      if (!parentIcon) return;
      toggleIcon(parentIcon, allChildrenChecked(parentItem));
      const grandParent = parentItem.parentElement.closest(".task-list-item");
      if (grandParent) updateParent(grandParent);
    }
    document.querySelectorAll(".task-list-item").forEach((item) => {
      const icon = item.querySelector("i");
      if (!icon) return;
      icon.style.cursor = "pointer";
      icon.onclick = (e) => {
        e.stopPropagation();
        const childIcons = getImmediateChildIcons(item);
        const isParent = childIcons.length > 0;
        const newState = !icon.classList.contains("fa-check-circle");
        toggleIcon(icon, newState);
        if (isParent) {
          childIcons.forEach((childIcon) => toggleIcon(childIcon, newState));
        }
        const parentItem = item.parentElement.closest(".task-list-item");
        if (parentItem) updateParent(parentItem);
      };
    });
  }
  enableTaskListIcons();
});
</script>