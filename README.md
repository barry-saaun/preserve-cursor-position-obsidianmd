# Implementations Roadmap

> [!NOTE]
> The `data.json` is readily available in the plugin folder from the `'obsidian'` package

## Performance Consideration

- Update the local `data.json` every 500ms -> 1000ms using the data from the in-memory object

## Loading data

- On plugin load => calls `this.onLoad()` to hydrate the in-memory data
- On cursor change => update the in-memory hash-maps

## Plugin Lifecycle

```typescript

import interface EditorPosition

interface CursorData extends {
	[filePath: string]: {line: number, ch: number}
}
```

1. `RememberCursorPlugin` class is instantiated once per plugin-enabling

    - loads `cursorData` from `data.json`
    - Registers `file-open` listener

2. User opens `note.md` -> `file-open` event fires

    - Plugin looks up cursorData\[file.path\]
    - IF found -> restores cursor

3. User moves cursor -> `cursorActivity` event fires

    - Plugin updates cursorData\[file.path\] in memory
    - Debounced `data` writes to `data.json` in every 500 -> 1000ms

4. User closes obsidian -> `this.unload()` runs:
    - Any pending debounced saves flush
    - Event listeners are cleaned up

## Events

### `file-open`
