# My Keyboard

My Keyboard is a personal fork of
[Unexpected Keyboard](https://github.com/Julow/Unexpected-Keyboard), adapted for
terminal use on Android 9 and later.

The keyboard keeps the swipe-to-type behavior of the original project and adds:

- A fixed modifier row: Win, Shift, Ctrl, Alt, AltGr, Delete, Esc, Tab, and a
  mode key.
- A dedicated QWERTY text layout with Backspace on the third row.
- AltGr as Ctrl+Alt, with Ctrl and Alt available as separate modifiers.
- Utility pages for a numeric keypad, navigation/editing, and F1-F12.
- Double-tap and long-press modifier locking with underlined latch/lock state.
- No suggestions, autocorrection, or automatic capitalisation.
- A minimal settings screen for theme, keyboard height, and vibration.

The terminal layout and utility pages are optional at this stage. The project
cannot be built in this environment because the Android SDK and NDK are not
installed.

## Build

Follow the upstream build instructions in
[CONTRIBUTING.md](CONTRIBUTING.md). The application ID is `dev.keyboard.my`.

## Attribution

This project is based on Unexpected Keyboard by Julow and contributors:

https://github.com/Julow/Unexpected-Keyboard

Unexpected Keyboard and this fork are distributed under the GNU General Public
License, version 3. See [LICENSE](LICENSE) for details.
