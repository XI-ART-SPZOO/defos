# DefOS
Extra native OS functions for games written using the Defold game engine

Currently uses Native Extension for macOS, Windows and HTML5. Contribute!

## Installation
You can use DefOS in your own project by adding this project as a [Defold library dependency](http://www.defold.com/manuals/libraries/). Open your game.project file and in the dependencies field under project add:

https://github.com/subsoap/defos/archive/master.zip

## Methods

**Customize title bar** accessories and title.

```lua
defos.disable_maximize_button()
defos.disable_minimize_button()
defos.disable_window_resize()
defos.set_window_title("I set this title using Defos")
```

---

**Toggle window maximize** status.

```lua
defos.set_maximized(bool_value)
defos.toggle_maximized()
bool_value = defos.is_maximized()
```

---

**Toggle full screen**. On HTML5, this only works from `defos.on_click()`.

```lua
defos.set_fullscreen(bool_value)
defos.toggle_fullscreen()
bool_value = defos.is_fullscreen()
```

---

**Keep window on top**.

```lua
defos.set_always_on_top(bool_value)
defos.toggle_always_on_top()
bool_value = defos.is_always_on_top()
```

---

**Minimize window**.

```lua
defos.minimize()
```

---

**Get/set the window's size and position** in screen coordinates. The window area
includes the title bar, so the actual contained game view area might be smaller
than the given metrics.

Passing `nil` for `x` and `y` will center the window in the middle of the display.

Screen coordinates start at (0, 0) in the top-left corner of the main display.
X axis goes right. Y axis goes down.

```lua
x, y, w, h = defos.get_window_size()
defos.set_window_size(x, y, w, h)
```

---

**Get/set the game view size and position** in screen coordinates. This adjusts
the window so that its containing game view is at the desired size and position.
The window will be larger than the given metrics because it includes the title
bar.

Passing `nil` for `x` and `y` will center the window in the middle of the display.

```lua
x, y, w, h = defos.get_view_size()
defos.set_view_size(x, y, w, h)
```

---

**Query displays**.

`defos.get_displays()` returns a table which can be indexed with either number
indices (like an array), either with display `id`s.

```lua
displays = defos.get_displays()
pprint(displays[1]) -- Print info about the main display
current_display_id = defos.get_current_display_id() -- Get the ID of the game's current display
pprint(displays[current_display_id]) -- Print info about the game's current display
```

A display info table has the following format:
```lua
{
  id = <userdata>,
  bounds = { -- This is the position and size in screen coordinates of the
    x = 0,   -- display (relative to the top-left corner of the main display)
    y = 0,
    width = 1440,
    height = 900,
  }
  mode = {   -- The current resolution mode of the display
    width = 2880,
    height = 1800,
    scaling_factor = 2,
    refresh_rate = 60,
    bits_per_pixel = 32,
    orientation = 0,
    reflect_x = false,
    reflect_y = false,
  },
  name = "Built-in Retina Display",
}
```

---

**Query display modes** for a display.

Returns a table with all the resolution modes a display supports.

```lua
display_id = defos.get_current_display_id()
modes = defos.get_display_modes(display_id)
pprint(modes[1]) -- Print information about the first available resolution mode
```

A resolution mode has the following format:
```lua
{
  width = 2880, -- Full width/height in pixels (not points)
  height = 1800,
  scaling_factor = 2, -- Hi-DPI scaling factor
  refresh_rate = 60,
  bits_per_pixel = 32,
  orientation = 0, -- One of 0, 90, 180, 270
  reflect_x = false, -- Linux supports reflecting either of the axes,
  reflect_y = false, -- effectively flipping the image like a mirror
}
```

---

**Show/hide the mouse cursor.**

```lua
defos.set_cursor_visible(bool_value)
bool_value = defos.is_cursor_visible()
```

---

**Respond to the mouse entering and leaving** the game view area.

```lua
bool_value = defos.is_mouse_in_view()
defos.on_mouse_enter(function ()
  print("Mouse entered view")
end)
defos.on_mouse_leave(function ()
  print("Mouse left view")
end)
```

---

**Get the cursor position**.

```lua
x, y = defos.get_cursor_pos() -- In screen coordinates
x, y = defos.get_cursor_pos_view() -- In game view coordinates
```

---

**Move the cursor** programatically.

```lua
defos.set_cursor_pos(x, y) -- In screen coordinates
defos.set_cursor_pos_view(x, y) -- In game view coordinates
```
---

**Clip cursor** to current game view area.

```lua
defos.set_cursor_clipped(bool_value)
bool_value = defos.is_cursor_clipped()
```

---

**Lock cursor movement**. On HTML5 this only works from `defos.on_click()`.

```lua
defos.set_cursor_locked(bool_value)
bool_value = defos.is_cursor_locked()
defos.on_cursor_lock_disabled(function ()
  print("Called on HTML5 when the user presses ESC and the browser disables locking");
end)
```

---

**Set custom hardware cursors**. `cursor` can be one of the following:
  * `nil`: Resets the cursor to default. Equivalent to `defos.reset_cursor()`.
  * `defos.CURSOR_ARROW`
  * `defos.CURSOR_HAND`
  * `defos.CURSOR_CROSSHAIR`
  * `defos.CURSOR_IBEAM`
  * On HTML5, an URL to an image (data URLs work as well)
  * On Windows, a path to an `.ani` or `.cur` file on the file system.
  * On macOS, a table of the form:  
  ```lua
  {
    image = resource.load("cursor.tiff"),
    hot_spot_x = 18,
    hot_spot_y = 2,
  }
  ```

On macOS, custom cursors can be any image file supported by `NSImage`, but it's highly recommended to
[create a TIFF](https://developer.apple.com/library/content/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Optimizing/Optimizing.html#//apple_ref/doc/uid/TP40012302-CH7-SW27)
with two images, one at 72DPI (for low density displays) and another at 144DPI (for Retina displays).

The hotspot is an anchor point within the image that will overlap with the functional position of the mouse pointer (eg. the tip of the arrow).

```lua
defos.set_cursor(cursor)
defos.reset_cursor()
```

---

**Show/hide the console window** on Windows. Only works when not running
from the Editor.

```lua
defos.set_console_visible(bool_value)
bool_value = defos.is_console_visible()
```

---

On HTML5 only, **get a synchronous event when the user clicks** in the canvas.
This is necessary because some HTML5 functions only work when called
synchronously from an event handler.

This is currently needed for:
* `defos.toggle_fullscreen()`
* `defos.set_cursor_locked(true)`

```lua
defos.on_click(function ()
  print("The user has clicked. I have the chance to respond synchronously")
end)
```

---

**Get the absolute path to the game's containing directory.** On macOS this will be the path to the .app bundle

```lua
path = defos.get_bundle_root()
```

---

**The system path separator.** `"\\"` on Windows, `"/"` everywhere else.

```lua
defos.PATH_SEP
```

---

**Change the game's icon** at runtime.

```lua
defos.set_window_icon(path_to_icon)
```

---

**Returns a table of command line arguments** used to run the app. On HTML5, returns a table with a single string: the query string part of the URL (eg. `{ "?param1=foo&param2=bar" }`).

```lua
arguments = defos.get_arguments()
```

---

If you'd like to see any other feature, open an issue.

## Example
An example is made using [DirtyLarry](https://github.com/andsve/dirtylarry)
![Defos example screenshot](https://user-images.githubusercontent.com/2209596/37050119-158e6b34-2184-11e8-95fd-b2e293fba456.jpg)
