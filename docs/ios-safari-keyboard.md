# iOS Safari keyboard fix for ttyd

## Symptom

On iOS Safari, tapping the terminal shows the cursor but the software keyboard does not open. The page is otherwise usable, so it looks like a focus problem rather than a network problem.

## Cause

`xterm.js` uses a hidden helper `<textarea>` to receive keyboard input. Its default CSS places the textarea at `0x0` / off-screen with `z-index: -5`. iOS Safari refuses to open the keyboard for a non-visible or zero-size textarea.

## Fix

Override the helper textarea and explicitly focus it on tap:

```css
.xterm .xterm-helper-textarea {
  position: fixed !important;
  left: 0 !important;
  top: 0 !important;
  width: 1px !important;
  height: 1px !important;
  opacity: 0 !important;
  font-size: 16px !important; /* prevents iOS auto-zoom */
  z-index: 2147483647 !important;
}
```

```js
function focusTerminal(event) {
  const term = document.querySelector('.xterm');
  const textarea = document.querySelector('.xterm-helper-textarea');
  if (!term || !textarea) return;
  if (term === event.target || term.contains(event.target)) {
    textarea.focus();
  }
}

document.addEventListener('touchstart', focusTerminal, { passive: true });
document.addEventListener('mousedown', focusTerminal, { passive: true });
```

Also add a visible fallback button that calls `textarea.focus()` for cases where the first tap only dismisses the URL bar.

## Applying it to ttyd

`ttyd` supports a custom index page via `-I`:

```bash
ttyd -I ~/.cc-sessions/ttyd-index.html ...
```

Do not replace the whole ttyd bundle unless you are tracking upstream changes. Prefer appending the CSS/JS above to the generated index, or inject it through your reverse proxy. If you redistribute a modified ttyd index, keep the upstream ttyd MIT license notice.
