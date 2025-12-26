---
# ===== Required =====
title: "Winter Long Driving Todo List"
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
This is my safety to-do list to make long winter driving less intimidating, so that I don't die, and therefore do more driving. 

This interactive check list is arranged in a time line manner, i.e when to what. The checked boxes/circles reset as the page refresh. The parent boxes will be automatically checked if all the sub boxes are checked. If the parent box is checked, all the sub boxes automatically get checked.

## A Day before travel
- [ ] Vehicle Works:
  - [ ] Check engine oil level (5W-20)
  - [ ] Check coolant/antifreeze fluid level
  - [ ] Check brake fluid level (dot 4)
  - [ ] Check windshield fluid level
  - [ ] Check tires pressure (32 psi)
  - [ ] Check spare tire pressure (60 psi)
  - [ ] Check wheel lug nuts tightness (80 ft-lbs)
  - [ ] Fill up gas (87)
  - [ ] Plug in car oil pan heater and battery charger
  - [ ] Remove ice in driver floor mat
  - [ ] Remove build up snow blocks in the fencers

- [ ] Emergency Items:
  - [ ] Headlamps (fully charged)
  - [ ] Tires patch kit
  - [ ] Portable tire pumps (fully charged)
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
- Brake early, brake gently and accelerate smoothly, giving time for tires to gain traction.
- Turn on fog lights when passing incoming big vehicles, helping with visibility.
- Shift to low gear when go down a long slope, preventing brake overheat/failures.
- Release both gas and brake, steer then put on awd when car start to slip.
- Switch off high beam proactively for incoming traffic at night. It doesn't have to be two blind idiots.
- Keep good distance with big vehicles ahead, they tend to shoot rocks up.
- Fight the urge to speed, think of merit points and insurance discount.
- Wave and thank to people that slow down for my lane change/merge. It costs nothing to do.
- Don't chug the Monster can.
- (Optional) don't die.

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