# Pico-8 to PixelFalls API Mapping

This document provides a comprehensive mapping of all Pico-8 engine-supported Lua methods to their corresponding PixelFalls equivalents (where available).

**Legend:**
- ✅ = Implemented in PixelFalls
- ❌ = Not yet implemented
- 🔄 = Partial implementation or different behavior
- 📝 = Notes provided

---

## Graphics: Drawing Primitives

| Pico-8 Function | PixelFalls Equivalent | Status | Notes |
|----------------|----------------------|--------|-------|
| `cls([c])` | `cls()` / `cls(colorIndex)` | ✅ | Clear screen to color (default 0) |
| `color([c])` | `setColor(colorIndex)` | ✅ | Set current draw color |
| `pset(x, y, [c])` | `drawPixel(x, y, [colorIndex])` | ✅ | World coords (camera applied); optional color parameter |
| - | `drawPixelScreen(x, y, [colorIndex])` | ✅ | PixelFalls-specific: screen coords (no camera offset), useful for UI |
| `pget(x, y) -> c` | `getPixel(x, y) -> colorIndex` | ✅ | Get pixel color at `(x,y)`; returns nearest PICO-8 palette index |
| `line(x0, y0, x1, y1, [c])` | `drawLine(x1, y1, x2, y2, [colorIndex])` | ✅ | World coords (camera applied); optional color parameter |
| `rect(x0, y0, x1, y1, [c])` | `drawRect(x0, y0, x1, y1, [colorIndex])` | ✅ | Uses x0,y0,x1,y1 coordinates (PICO-8 style); optional color; coordinates are normalized automatically |
| `rectfill(x0, y0, x1, y1, [c])` | `drawFillRect(x0, y0, x1, y1, [colorIndex])` | ✅ | Uses x0,y0,x1,y1 coordinates (PICO-8 style); optional color; supports fill patterns; coordinates are normalized automatically |
| `circ(x, y, r, [c])` | `drawCircle(x, y, r, [colorIndex])` | ✅ | World coords (camera applied); optional color parameter |
| `circfill(x, y, r, [c])` | `drawFillCircle(x, y, r, [colorIndex])` | ✅ | World coords (camera applied); optional color parameter; supports fill patterns |
| `ovalfill(x0, y0, x1, y1, [c])` | `drawFillOval(x0, y0, x1, y1, [colorIndex])` | ✅ | Filled ellipse; optional color parameter; supports fill patterns |
| `oval(x0, y0, x1, y1, [c])` | `drawOval(x0, y0, x1, y1, [colorIndex])` | ✅ | Ellipse outline; optional color parameter |
| `fillp([pattern])` | `fillPattern([pattern])` | ✅ | Set fill pattern (0..65535) or -1 for solid; affects filled shapes |

---

## Graphics: Draw State and Transforms

| Pico-8 Function | PixelFalls Equivalent | Status | Notes |
|----------------|----------------------|--------|-------|
| `camera([x, y])` | `camera(x, y)` / `camera()` | ✅ | Set camera scroll offset; PixelFalls also has `cameraX()` and `cameraY()` getters |
| `clip([x, y, w, h])` | `clip()` / `clip(x, y, w, h)` | ✅ | Set clipping rectangle; no args resets to full screen |
| `pal([c0, c1, t])` | `setPaletteRemap(c0, c1, [t])` | ✅ | Accepts Pico-8 arguments (optionally table form); `t` flag is accepted but always affects draw pipeline |
| `palt([c, t])` | `setColorTransparency(c, [t])` | ✅ | Accepts Pico-8 arguments (optionally table form); omitted `t` defaults to transparent |

---

## Sprites and Tiles

| Pico-8 Function | PixelFalls Equivalent | Status | Notes |
|----------------|----------------------|--------|-------|
| `spr(n, x, y, [w, h], [flip_x], [flip_y])` | `drawSprite(spriteIndex, x, y, [w, h], [flipX, flipY])` | ✅ | Full parameter support: w/h scaling and flip parameters |
| `sspr(sx, sy, sw, sh, dx, dy, [dw, dh], [flip_x], [flip_y])` | `drawSubSprite(sx, sy, sw, sh, dx, dy, [dw, dh], [flipX, flipY])` | ✅ | Draw sub-rectangle of spritesheet with optional scaling and flipping |
| - | `drawSpriteRotated(spriteIndex, x, y, [w, h], [flipX, flipY], [angleDeg])` | ✅ | PixelFalls-specific: draw sprite with rotation |
| `sget(x, y) -> c` | `ssGetPixel(x, y)` | ✅ | Returns spritesheet color at `(x,y)` |
| `sset(x, y, c)` | `ssSetPixel(x, y, c)` | ✅ | Sets spritesheet pixel at `(x,y)` |
| `fget(n, [f]) -> bool\|bits` | `spriteGetFlags(n)`, `spriteGetFlag(n, bit)` | ✅ | Byte/bit accessors; `spriteGetFlag` returns a boolean |
| `fset(n, [f], [v])` | `spriteSetFlags(n, value)`, `spriteSetFlag(n, bit, [bool])` | ✅ | Write full flag byte or individual bit; bool parameter defaults to `true` |

---

## Map

| Pico-8 Function | PixelFalls Equivalent | Status | Notes |
|----------------|----------------------|--------|-------|
| `map([cell_x], [cell_y], [sx], [sy], [cw], [ch], [layer_mask])` | `drawMap(mapIndex, x, y)` | 🔄 | PixelFalls uses simplified API (mapIndex, x, y); lacks cell selection and layer mask |
| `mget(cell_x, cell_y) -> sprite_n` | `mapGetTile(x, y)` | ✅ | Returns sprite index stored at tile `(x, y)` of the active map |
| `mset(cell_x, cell_y, sprite_n)` | ❌ | ❌ | Set tile sprite index |
| - | `getNumberOfMaps() -> int` | ✅ | PixelFalls-specific: returns number of maps |

---

## Text

| Pico-8 Function | PixelFalls Equivalent | Status | Notes |
|----------------|----------------------|--------|-------|
| `print(str, [x], [y], [c]) -> next_y` | `print(...)` / `drawText(text, x, y, size, colorIndex)` | 🔄 | PixelFalls `print()` goes to console; `drawText()` renders to screen but requires size parameter |
| `cursor([x], [y])` | ❌ | ❌ | Set text cursor position |

---

## Input

| Pico-8 Function | PixelFalls Equivalent | Status | Notes |
|----------------|----------------------|--------|-------|
| `btn(i, [p]) -> bool` | `buttonDown(key: int) -> bool` | 🔄 | PixelFalls uses key constants (KEY_UP, KEY_DOWN, etc.) instead of button indices |
| `btnp([i], [p]) -> bool\|i` | `buttonPressed(key: int) -> bool` | 🔄 | PixelFalls uses key constants; lacks multi-button query |
| - | Key constants: `KEY_UP`, `KEY_DOWN`, `KEY_LEFT`, `KEY_RIGHT`, `KEY_Z`, `KEY_X`, `KEY_M`, `KEY_ESCAPE` | ✅ | PixelFalls-specific constants |

**Pico-8 Button Mapping:**
- Button 0 = Left → `KEY_LEFT`
- Button 1 = Right → `KEY_RIGHT`
- Button 2 = Up → `KEY_UP`
- Button 3 = Down → `KEY_DOWN`
- Button 4 = O → `KEY_Z` (typically)
- Button 5 = X → `KEY_X`

---

## Audio

| Pico-8 Function | PixelFalls Equivalent | Status | Notes |
|----------------|----------------------|--------|-------|
| `sfx(n, [channel], [offset], [length])` | `playSFX(sfx_index)` / `playSFX(sfx_index, channel)` / `playSFX(sfx_index, channel, offset)` / `playSFX(sfx_index, channel, offset, length)` | ✅ | Full parameter support |
| `music(n, [fade_ms], [channel_mask])` | `playMusic(music_index)` / `playMusic(music_index, fade_len)` / `playMusic(music_index, fade_len, channel_mask)` | ✅ | Full parameter support |
| - | `stopAudio()` / `stopAudio(channel)` | ✅ | PixelFalls-specific: stop audio |

---

## Time / Random

| Pico-8 Function | PixelFalls Equivalent | Status | Notes |
|----------------|----------------------|--------|-------|
| `time() -> seconds` | ❌ | ❌ | Elapsed time since boot in seconds |
| `t() -> seconds` | ❌ | ❌ | Alias of `time()` |
| `stat(i) -> number\|string` | `fps() -> int` | 🔄 | PixelFalls only provides FPS; Pico-8 has many stat indices |
| `rnd([x]) -> r` | ❌ | ❌ | Random number generator |
| `srand(seed)` | ❌ | ❌ | Seed RNG |

**Pico-8 `stat(i)` Indices (not implemented in PixelFalls):**
- `stat(0)`: CPU % used last frame
- `stat(1)`: Memory used (KB)
- `stat(2)`: Peak memory (KB)
- `stat(7)`: Clip rectangle x
- `stat(8)`: Clip rectangle y
- `stat(9)`: Clip width
- `stat(10)`: Clip height
- `stat(90)`: FPS (commonly used)

---

## Memory / Data

| Pico-8 Function | PixelFalls Equivalent | Status | Notes |
|----------------|----------------------|--------|-------|
| `peek(addr) -> byte` | ❌ | ❌ | Read 8-bit at `addr` |
| `poke(addr, val)` | ❌ | ❌ | Write 8-bit `val` at `addr` |
| `peek2(addr) -> word` | ❌ | ❌ | Read 16-bit little-endian |
| `poke2(addr, word)` | ❌ | ❌ | Write 16-bit little-endian |
| `peek4(addr) -> dword` | ❌ | ❌ | Read 32-bit little-endian |
| `poke4(addr, dword)` | ❌ | ❌ | Write 32-bit little-endian |
| `memcpy(dest, src, len)` | ❌ | ❌ | Copy memory region |
| `memset(dest, val, len)` | ❌ | ❌ | Fill memory region |
| `reload([dest_addr], [src_addr], [len], [src_cart_id])` | ❌ | ❌ | Reload data from cart |
| `cstore([dest_addr], [src_addr], [len], [dest_cart_id])` | ❌ | ❌ | Save data to cart |
| `cartdata(id) -> ok` | ❌ | ❌ | Bind persistent storage namespace |
| `dget(i) -> number` | ❌ | ❌ | Get persisted value at slot `i` |
| `dset(i, val)` | ❌ | ❌ | Set persisted value at slot `i` |
| `pmem(i, [val]) -> number\|nil` | ❌ | ❌ | Alternate persistent memory accessor |

---

## Math

| Pico-8 Function | PixelFalls Equivalent | Status | Notes |
|----------------|----------------------|--------|-------|
| `abs(x)` | Standard Lua `math.abs(x)` | ✅ | Standard Lua math library |
| `flr(x)` | Standard Lua `math.floor(x)` | ✅ | Standard Lua math library |
| `ceil(x)` | Standard Lua `math.ceil(x)` | ✅ | Standard Lua math library |
| `mid(x,y,z)` | ❌ | ❌ | Clamp `x` between `y` and `z` |
| `min(a,b,...)` | Standard Lua `math.min(a,b,...)` | ✅ | Standard Lua math library |
| `max(a,b,...)` | Standard Lua `math.max(a,b,...)` | ✅ | Standard Lua math library |
| `sgn(x)` | ❌ | ❌ | Sign function (-1, 0, or 1) |
| `sqrt(x)` | Standard Lua `math.sqrt(x)` | ✅ | Standard Lua math library |
| `sin(x)` | Standard Lua `math.sin(x)` | ✅ | Standard Lua math library |
| `cos(x)` | Standard Lua `math.cos(x)` | ✅ | Standard Lua math library |
| `atan2(dy, dx)` | Standard Lua `math.atan2(dy, dx)` | ✅ | Standard Lua math library |
| `band(a,b,...)` | ❌ | ❌ | Bitwise AND |
| `bor(a,b,...)` | ❌ | ❌ | Bitwise OR |
| `bxor(a,b,...)` | ❌ | ❌ | Bitwise XOR |
| `bnot(x)` | ❌ | ❌ | Bitwise NOT |
| `shl(x, n)` | ❌ | ❌ | Shift left |
| `shr(x, n)` | ❌ | ❌ | Shift right |

---

## Strings / Types / Tables

| Pico-8 Function | PixelFalls Equivalent | Status | Notes |
|----------------|----------------------|--------|-------|
| `tostr(x, [hex]) -> string` | Standard Lua `tostring(x)` | 🔄 | Standard Lua lacks hex option |
| `tonum(s, [hex]) -> number` | Standard Lua `tonumber(s)` | 🔄 | Standard Lua lacks hex option |
| `sub(s, i, [j]) -> string` | Standard Lua `string.sub(s, i, [j])` | ✅ | Note: Pico-8 uses 1-based indices |
| `split(s, [sep], [convert_numbers]) -> table` | ❌ | ❌ | Split string with optional conversion |
| `chr(code) -> string` | Standard Lua `string.char(code)` | ✅ | Standard Lua |
| `ord(char) -> code\|code1,code2` | Standard Lua `string.byte(char)` | ✅ | Standard Lua |
| `type(x) -> string` | Standard Lua `type(x)` | ✅ | Standard Lua |
| `count(t) -> n` | Standard Lua `#t` or `table.getn(t)` | 🔄 | Pico-8 `count()` counts array part only |

---

## Coroutines

| Pico-8 Function | PixelFalls Equivalent | Status | Notes |
|----------------|----------------------|--------|-------|
| `cocreate(fn) -> co` | Standard Lua `coroutine.create(fn)` | ✅ | Standard Lua coroutines |
| `coresume(co, ...) -> ok, ...` | Standard Lua `coroutine.resume(co, ...)` | ✅ | Standard Lua coroutines |
| `costatus(co) -> 'suspended'\|'running'\|'dead'` | Standard Lua `coroutine.status(co)` | ✅ | Standard Lua coroutines |
| `yield(...)` | Standard Lua `coroutine.yield(...)` | ✅ | Standard Lua coroutines |

---

## System / Control Flow

| Pico-8 Function | PixelFalls Equivalent | Status | Notes |
|----------------|----------------------|--------|-------|
| `run()` | ❌ | ❌ | Soft-reboot cart |
| `reload_cart()` / `reload()` | ❌ | ❌ | Reload cart (see memory section) |
| `shutdown()` | ❌ | ❌ | Not standard Pico-8 |
| `extcmd(cmd_str)` | ❌ | ❌ | Editor commands (optional) |
| `printh(str, [filename], [overwrite], [save_to_desktop])` | `print(...)` | 🔄 | PixelFalls `print()` goes to console only |

---

## Lifecycle Hooks

| Pico-8 Function | PixelFalls Equivalent | Status | Notes |
|----------------|----------------------|--------|-------|
| `_init()` | `_init()` | ✅ | Called once on load |
| `_update()` / `_update60()` | `_update()` | ✅ | Logic step |
| `_draw()` | `_draw()` | ✅ | Render step |

---

## Summary Statistics

**Total Pico-8 Functions Listed:** ~100+

**Implementation Status:**
- ✅ **Fully Implemented:** ~40 functions
- 🔄 **Partially Implemented / Different API:** ~10 functions
- ❌ **Not Implemented:** ~50+ functions

**Key Missing Categories:**
1. **Memory Operations:** All `peek`/`poke` variants, `memcpy`, `memset`, `reload`, `cstore`
2. **Persistent Storage:** `cartdata`, `dget`, `dset`, `pmem`
3. **Sprite/Map Editing:** `mapSetTile` (`mset` equivalent)
4. **Time/Random:** `time()`, `