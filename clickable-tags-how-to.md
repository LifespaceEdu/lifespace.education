# How to Add Clickable Tag Filtering

Right now the session tags (Individual, Custom Classes, Kids, etc.) are just styled text - decorative labels. This doc explains how to make them clickable so users can filter providers by clicking tags.

## What You Need

Three things make tags "work":

1. **Memory** - A place to store which tags are selected
2. **Listening** - Code that responds when someone clicks a tag
3. **Filtering** - Logic that shows/hides providers based on selections

---

## Step 1: Add Memory

At the top of the script (after `const providers = [...]`), add:

```javascript
let activeSessionFilters = [];
```

**Why:** The page needs somewhere to remember which tags are currently selected. An empty array that gets filled when users click.

---

## Step 2: Make Tags Clickable

In the `renderProviders` function, change the session tag line from:

```javascript
${provider.sessionTypes.map(t => `<span class="session-tag">${t}</span>`).join('')}
```

To:

```javascript
${provider.sessionTypes.map(t => `<span class="session-tag" onclick="toggleSessionFilter('${t}')">${t}</span>`).join('')}
```

**Why:** Each tag now calls a function when clicked, passing its name as an argument.

---

## Step 3: Write the Toggle Function

Add this function (anywhere in the script section):

```javascript
function toggleSessionFilter(tag) {
  // If tag is already active, remove it
  if (activeSessionFilters.includes(tag)) {
    activeSessionFilters = activeSessionFilters.filter(t => t !== tag);
  } else {
    // Otherwise, add it
    activeSessionFilters.push(tag);
  }
  // Re-render with new filters
  renderProviders();
}
```

**Why:** Click once to select, click again to deselect. After each click, refresh the display.

---

## Step 4: Update renderProviders to Filter

At the start of the `renderProviders` function, after getting the type filter, add:

```javascript
// Apply session filters if any are active
if (activeSessionFilters.length > 0) {
  filtered = filtered.filter(provider =>
    activeSessionFilters.some(tag => provider.sessionTypes && provider.sessionTypes.includes(tag))
  );
}
```

**Why:** Before displaying cards, check if any session filters are active. If so, only keep providers who have at least one of the selected tags.

---

## Step 5: Add Visual Feedback (CSS)

Add to the style section:

```css
.session-tag {
  cursor: pointer;
  transition: background 0.2s, color 0.2s;
}

.session-tag:hover {
  background: #c8e0e0;
}

.session-tag.active {
  background: #1a5555;
  color: white;
}
```

**Why:** Users need to see which tags are selected and that tags are clickable.

---

## Step 6: Apply Active Class

Update the tag rendering to check if active:

```javascript
${provider.sessionTypes.map(t =>
  `<span class="session-tag ${activeSessionFilters.includes(t) ? 'active' : ''}" onclick="toggleSessionFilter('${t}')">${t}</span>`
).join('')}
```

**Why:** Adds the `active` class to tags that are currently selected.

---

## Optional: Add a "Clear Filters" Button

If you want users to clear all filters at once:

```javascript
function clearSessionFilters() {
  activeSessionFilters = [];
  renderProviders();
}
```

And add a button somewhere:

```html
<button onclick="clearSessionFilters()">Clear Filters</button>
```

---

## How It All Works Together

1. User clicks "Kids" tag
2. `toggleSessionFilter('Kids')` runs
3. 'Kids' gets added to `activeSessionFilters` array
4. `renderProviders()` runs
5. Filter logic checks each provider: "Does this provider have 'Kids' in their sessionTypes?"
6. Only matching providers display
7. The "Kids" tag shows as highlighted (active class)

Click "Kids" again → removes from array → all providers show again.
