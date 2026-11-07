# Báo cáo thay đổi — My Keyboard

## Mục tiêu

Fork Unexpected Keyboard tại `C:\Users\Administrator\Desktop\kb` thành **My Keyboard**,
tối ưu cho Termux trên Android 9+ với hàng modifier cố định, layout terminal,
các trang tiện ích và nhận diện ứng dụng riêng.

## Các nhóm thay đổi

### 1. Nền tảng

- `build.gradle.kts`: `minSdk = 28`, `applicationId = dev.keyboard.my`
  (debug dùng hậu tố `.debug`), app label là My Keyboard.
- `settings.gradle.kts`: `rootProject.name = "My Keyboard"`.
- Không đổi namespace/package Java (`juloo.keyboard2`) để giữ diff hẹp.

### 2. Bàn phím và modifier

- Tạo `res/xml/modifier_row.xml`: 9 phím cố định Win, Shift, Ctrl, Alt, AltGr,
  Del, Esc, Tab, Mode.
- `LayoutModifier.add_modifier_row()` chèn hàng modifier vào mọi layout hiển thị.
- `KeyValue`: thêm modifier `ALTGR`, key `win`, event `SWITCH_UTILS*`.
- `KeyModifier`/`KeyEventHandler`: AltGr gửi Ctrl+Alt theo đúng thứ tự xuống/lên.
- `Pointers`: chuẩn hóa Ctrl/Alt khi AltGr bật; bật double-tap lock cho modifier.
- `Keyboard2View`: gạch dưới trạng thái latch/lock dùng màu theme.

### 3. Layout và trang tiện ích

- `res/xml/my_keyboard_text.xml`: layout QWERTY 3 hàng, Backspace hàng 3,
  `bottom_row="false"` và `locale_extra_keys="false"`.
- `Keyboard2` bỏ qua layout picker, luôn dùng layout terminal ở trên.
- Các trang utils:
  - `my_keyboard_utils_numpad.xml` — numpad + phím điều khiển.
  - `my_keyboard_utils_special.xml` — navigation/editing (arrows, Home/End,
    Page Up/Down, clipboard).
  - `my_keyboard_utils_fkeys.xml` — F1-F12 + hàng chuyển trang.
  - `my_keyboard_utils_numpad_landscape.xml` — numpad và F1-F12 cạnh nhau.
- Phím Mode (`switch_utils`) chuyển text → numpad → special → F-keys → text.

### 4. Hành vi gõ

- `Config` ép tắt: split layout, numpad tự động, number row, extra keys,
  suggestions, space autocomplete, autocapitalisation.
- `Suggestions.started()` chỉ bật khi `suggestions_enabled` và editor cho phép.
- `EditorConfig` giữ guard API 30/31 cho API 9+.

### 5. Giao diện và nhận diện

- `res/xml/settings.xml` chỉ còn Theme, Keyboard height, Vibration.
- `SettingsActivity` bỏ các `findPreference` cho setting đã xóa.
- Bỏ nút dictionaries khỏi launcher.
- `res/values/strings.xml`, README và fastlane metadata đổi thành My Keyboard;
  giữ liên kết attribution tới Unexpected Keyboard.

## Kiểm tra đã chạy

Không có Android SDK/NDK và không dùng GitHub Actions, nên chỉ kiểm tra tĩnh:

- `git diff --check` — sạch.
- Parse XML mới/đã sửa bằng XML parser — hợp lệ.
- Rà soát resource ID (`R.xml.*`), tên key và event/modifier — khớp.
- Rà soát `SDK_INT`/API > 28 — đã có guard hoặc fallback (xem `NOTES.md`).
- Xác nhận không còn tham chiếu `double_tap_lock_shift` và không còn
  `findPreference` cho setting đã xóa.

## Giới hạn

- **Chưa build APK**: thiếu SDK/NDK local; CI bị tắt theo yêu cầu local-only.
- Chưa chạy unit test / instrumented test / Android 9 trên máy thật.
- Chưa push; nhánh `my-keyboard` chỉ tồn tại local, `origin` vẫn trỏ upstream.

## Bước tiếp theo

1. Build và cài APK trên Android 9.
2. Chạy checklist test tay trong `NOTES.md`.
3. Sửa lỗi phát hiện trong runtime nếu có.
