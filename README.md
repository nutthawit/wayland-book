```sh
cc -o ./build/client client.c xdg-shell-protocol.c -lwayland-client -lrt
```

## Glossary

[Notification](./markdowns/glossary_notification.md)

## XKB

`xkb_new_context`: Create a new xkb context (the `xkb_context` object).

### Why +8

Example code:

```c
static void
wl_keyboard_key(void *data, struct wl_keyboard *wl_keyboard,
		uint32_t serial, uint32_t time, uint32_t key, uint32_t state)
{
	struct client_state *client_state = data;
	char buf[128];
	uint32_t keycode = key + 8;
	xkb_keysym_t sym = xkb_state_key_get_one_sym(
			client_state->xkb_state, keycode);
	xkb_keysym_get_name(sym, buf, sizeof(buf));
	const char *action = 
		state == WL_KEYBOARD_KEY_STATE_PRESSED ? "press" : "release";
	fprintf(stderr, "key %s: sym: %-12s (%d), ", action, buf, sym);
	xkb_state_key_get_utf8(client_state->xkb_state, keycode,
			buf, sizeof(buf));
	fprintf(stderr, "utf8: '%s'\n", buf);
}
```

The reason you see `keycode = key + 8;` (or `*key + 8` in the `wl_keyboard_enter` function) is to account for the **difference in key code standards** used by the Linux kernel and the X Keyboard Extension (**xkbcommon**).

***

## The Key Code Offset

### 1. The Wayland/Linux Kernel Standard

The Wayland protocol (specifically the `wl_keyboard_key` event) uses **Linux kernel key codes**. These codes are historically based on the AT keyboard scan codes, where the very first key is often assigned the value of **8** (since codes 0 through 7 were reserved or unused in the old AT standard).

Therefore, when the Wayland compositor sends a key event, the `key` argument is the **Linux kernel key code**.

### 2. The XKB Common Standard

The `libxkbcommon` library, which is used to translate key codes into symbols (like 'A', 'Enter', or 'Control'), uses its own internal key code system. This system requires key codes to start at **8**.

* **XKB's minimum key code is 8.**
* A key code of 0, 1, ..., 7 is invalid in the XKB context.

### 3. The Need for `+ 8`

Historically, the X Window System (and by extension, XKB) introduced an offset:

$$\text{XKB Key Code} = \text{Linux Kernel Key Code} + 8$$

Since Wayland clients often need to interact with the well-established XKB library for keymap handling, the code applies this **fixed offset of $+8$** to convert the raw key code received from the compositor into the expected key code for the `libxkbcommon` functions:

* `xkb_state_key_get_one_sym(client_state->xkb_state, **keycode**)`
* `xkb_state_key_get_utf8(client_state->xkb_state, **keycode**, ...)`

This ensures that the `xkbcommon` functions receive a valid key code they can correctly process.

## Seat


