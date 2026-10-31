# My Keyboard notes

## Mục tiêu

Fork này biến Unexpected Keyboard thành bàn phím tối ưu cho Termux trên Android 9
(`minSdk = 28`), với hàng modifier cố định, layout QWERTY gọn và các trang tiện
ích cho numpad, điều hướng và F1-F12.

## Quyết định thiết kế

- Tên ứng dụng: `My Keyboard`.
- `applicationId`: `dev.keyboard.my`; debug dùng hậu tố `.debug`.
- Giữ namespace/package Java `juloo.keyboard2` để giảm phạm vi thay đổi.
- `AltGr` gửi đồng thời Ctrl+Alt.
- `Del` là forward delete (`KEYCODE_FORWARD_DEL`); Backspace vẫn ở hàng chữ thứ 3.
- Suggestions, autocorrect và autocapitalisation bị tắt ở mức hành vi; code,
  dictionaries, cdict và native không bị xóa.
- Settings chỉ còn Theme, Keyboard height và Vibration.
- Modifier row cố định 9 phím: Win, Shift, Ctrl, Alt, AltGr, Del, Esc, Tab, Mode.
- Layout text luôn là layout terminal, không phụ thuộc layout picker hoặc locale.
- Các trang utils gồm numpad, navigation/editing và F1-F12.
- Không thêm thư viện, không hard-code màu; dùng theme hiện có.
- Chỉ commit local, không push và không dùng GitHub Actions.

## Kiến trúc dự kiến

- `LayoutModifier` nạp `modifier_row.xml` và chèn hàng này vào mọi layout đang
  hiển thị.
- `Keyboard2` bỏ qua layout picker, dùng `my_keyboard_text.xml`; các mode utils
  được mở qua event `SWITCH_UTILS_*`.
- `KeyValue` thêm modifier `ALTGR`, key `win` và event utils.
- `Pointers` chuẩn hóa Ctrl/Alt khi AltGr đang bật, tránh gửi trùng meta state.
- `KeyEventHandler` gửi Ctrl và Alt cho AltGr; `Keyboard2View` vẽ gạch dưới cho
  modifier đang latch/lock.
- Layout đặt trực tiếp trong `res/xml`; không sửa `srcs/layouts` để tránh ảnh
  hưởng `gen_layouts.py` và `check_layout.py`.

## API 28 audit

Các API mới hơn Android 9 đã có guard hoặc fallback:

- `EditorInfo.getInitialTextBeforeCursor()`: guard API 30 trong `EditorConfig`.
- `InputConnection.getSurroundingText()`: guard API 31 trong `CurrentlyTypedWord`.
- `ClipboardManager.clearPrimaryClip()`: dùng từ API 28, đúng minSdk.
- `Window.setDecorFitsSystemWindows()`, `WindowInsets.Type`,
  `LayoutParams.setFitInsetsTypes()`: chỉ chạy trong nhánh API 35.
- `View.setSystemGestureExclusionRects()`: chỉ chạy khi API 29.
- `FEATURE_SENSOR_HINGE_ANGLE`: guard API 30 trong `FoldStateTracker`.
- `VibrationEffect`: guard API 29 ở nhánh sliding, có fallback rung cũ.
- `Locale.toLanguageTag()` và Direct Boot preferences: guard API 24; Android 9
  luôn đi nhánh mới.
- `View.addOnUnhandledKeyEventListener()`: guard API 28 trong `LauncherActivity`.
- `WindowInsets`/navigation-bar APIs: guard API 21/26/35 trong `Keyboard2View`.
- Monet colors: có fallback `res/values/values.xml` cho API < 31.
- Các nhánh `SDK_INT < 28` còn lại không gây lỗi; chúng trở thành dead branch
  nhưng giữ nguyên để giới hạn diff.

## Kiểm tra

Không có SDK/NDK local và không chạy CI trong phiên này. Kiểm tra tĩnh gồm:

- `git diff --check`
- parse toàn bộ XML mới bằng XML parser
- rà `rg "SDK_INT|VERSION"` và các API > 28
- rà resource ID, tên key và enum event/modifier
- kiểm tra layout không nằm trong `srcs/layouts`

## Checklist test tay trên Android 9

- [ ] Cài APK, bật My Keyboard trong Input method settings.
- [ ] Gõ chữ, số, dấu câu, Backspace và Del trên Termux.
- [ ] Giữ hoặc double-tap Ctrl/Alt/Shift/AltGr để latch/lock; kiểm tra gạch dưới.
- [ ] AltGr kết hợp phím chữ tạo Ctrl+Alt đúng.
- [ ] Win mở ứng dụng/khay hệ thống nếu app nhận meta.
- [ ] Esc, Tab hoạt động trong Termux.
- [ ] Mode mở numpad, navigation/editing, F1-F12 và quay lại text.
- [ ] Không còn candidates/suggestions hay autocapitalisation.
- [ ] Settings chỉ hiện Theme, Keyboard height, Vibration.
- [ ] Theme System đúng sáng/tối trên Android 9.
- [ ] Không crash khi xoay màn hình, mở app khác hoặc gõ trong PIN field.
