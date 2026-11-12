# Thiết kế lại giao diện ShiftBook

Ngày: 2026-11-12
Trạng thái: đã duyệt
Mockup tham chiếu: https://claude.ai/artifact/SeSLuZy94TXVAuJXd7zU9q

## 1. Mục đích và lý do

ShiftBook là app một file (`cham-cong.html`) để chủ sở hữu tự chấm công ca, theo dõi giờ làm và tính lương, dữ liệu lưu trên Firestore.

Chủ app thấy giao diện hiện tại "màu sắc nhìn chán" và "nhìn giống template". Không phải lỗi chức năng — mọi tính năng vẫn dùng tốt. Vấn đề thuần về hình thức và thứ tự ưu tiên thị giác.

Ba nguyên nhân cụ thể, xác định bằng cách đọc khối CSS dòng 5–356:

1. **Nền `--bg:#EEF1F6`** là xám pha xanh lạnh — đúng màu mặc định của hầu hết dashboard template.
2. **Sáu màu ca `--s1`…`--s6`** bão hòa cao (vàng, xanh dương, cam, tím, lục, hồng) đứng cạnh accent cam `#F2611D`. Bảy màu mạnh cạnh nhau, không màu nào thắng.
3. **Mọi khối đều giống nhau**: `.card`, `.mini-pay`, `.table-wrap`, `.stat` đều là nền trắng, bo `18px`, cùng một `--shadow` hai lớp. Không có gì nổi lên trước, mắt không biết nhìn đâu.

Mục tiêu là làm app trông có chủ đích, giữ nguyên toàn bộ chức năng.

## 2. Mục tiêu

- Một bảng màu có bản sắc: nền ấm, **một** màu nhấn duy nhất.
- Có điểm nhấn thị giác rõ ràng, không rơi vào trắng–đen đơn thuần.
- Hệ thống phân cấp rõ: số liệu là thứ to nhất trên màn hình, tiêu đề mục lùi xuống.
- Dùng tốt trên cả điện thoại và laptop (người dùng dùng cả hai ngang nhau).
- Dark mode vẫn đẹp, vẫn tự theo hệ thống như hiện nay.

## 3. Không làm (non-goals)

- Không đổi cấu trúc 4 tab (Hôm nay · Lịch · Lương · Cài đặt).
- Không thêm, bớt hay đổi tính năng nào. Mọi nút, mọi phép tính, mọi đường ghi Firestore giữ nguyên.
- Không đổi cấu trúc dữ liệu Firestore.
- Không tách file. App vẫn là một file HTML duy nhất.
- Không thêm thư viện hay framework CSS.
- Không đổi font. `Be Vietnam Pro` + `JetBrains Mono` giữ nguyên.

## 4. Điều tuyệt đối tránh (anti-goals)

Những thứ này khiến bản redesign coi như thất bại dù chạy được:

- **Lịch tháng loạn màu trở lại.** Commit `5ef8fc5` đã cố ý gom ngày đi làm về một màu. Bản redesign không được đảo ngược.
- **Vùng bấm nhỏ đi.** Mọi nút bấm chính giữ `min-height:44px`.
- **Mất khả năng truy cập.** Giữ `:focus-visible`, `aria-*`, `prefers-reduced-motion`, tương phản chữ đạt WCAG AA.
- **Số liệu khó đọc.** Giữ `font-variant-numeric:tabular-nums` ở mọi chỗ hiện số.
- **Chữ tràn / vỡ layout** khi tên ca dài hoặc số tiền nhiều chữ số.

## 5. Ràng buộc

- Một file `cham-cong.html`, không build step, mở trực tiếp bằng trình duyệt cũng chạy.
- CSS thuần, không preprocessor. Dùng CSS custom properties như hiện tại.
- Deploy qua Firebase Hosting (`firebase.json`), không có bước nào xử lý CSS.
- Hỗ trợ iOS Safari: giữ `env(safe-area-inset-*)`, `-webkit-backdrop-filter`.
- `color-mix(in srgb, …)` đã dùng sẵn trong code hiện tại nên được phép dùng tiếp.

## 6. Các hướng đã cân nhắc

**A. Nền ấm + một màu nhấn duy nhất.** Đổi nền xám lạnh sang off-white ấm, một accent duy nhất, gom 6 màu ca về một họ trầm. Điểm nhấn đến từ khối hero tối và số lớn. Đánh đổi: ít màu hơn nên phải dựa vào kích thước và khoảng trắng để phân cấp.

**B. Kiểu editorial, chữ số làm chủ đạo.** Bỏ gần hết thẻ và bóng đổ, dùng đường kẻ mảnh chia khối, số mono rất lớn làm bản sắc. Đánh đổi: sạch nhất nhưng trên điện thoại khó nhận ra ranh giới giữa các mục.

**C. Mỗi tab một tông màu.** Giữ nhiều màu nhưng có hệ thống, mỗi tab sở hữu một tông. Đánh đổi: vẫn là nhiều màu — gần như chính vấn đề đang muốn sửa. **Loại.**

**Đã chọn: A pha B.** Nền ấm và một accent (từ A), cộng với việc bỏ bóng đổ, dùng viền mảnh và phóng to số liệu (từ B). Lý do: yêu cầu là "đơn giản nhưng vẫn có điểm nhấn" — điểm nhấn đến từ **một** màu và **một** khối lớn, không đến từ sáu màu. B thuần bị loại vì trên điện thoại cần ranh giới khối rõ hơn.

Chủ app đã xem mockup tương tác và duyệt hướng này.

## 7. Bảng màu mới

Màu nhấn: **teal `#0F766E`** (chủ app chọn).

### Light (khối `:root`)

```
--bg:#FAF8F5;  --surface:#FFFFFF;  --surface-2:#F4F1EB;
--ink:#17150F; --muted:#6E675D;    --line:#E6E0D5;
--accent:#0F766E; --accent-ink:#FFFFFF; --accent-soft:#DCEDEA;
--hero:#15211F; --hero-ink:#FBFAF8; --hero-muted:#93A5A1; --hero-line:#2A3936;
--s1:#0F766E; --s1-soft:#DCEDEA;
--s2:#2A5F8F; --s2-soft:#DFE9F2;
--s3:#8A5A2B; --s3-soft:#F2E7D8;
--s4:#6B4E8F; --s4-soft:#EAE2F2;
--s5:#4A7340; --s5-soft:#E2EDDE;
--s6:#8F4A62; --s6-soft:#F2E0E6;
--work:#0F766E; --work-ink:#FFFFFF; --work-soft:#DCEDEA;
--phep:#6B4E8F; --phep-soft:#EAE2F2;
--om:#8A5A2B;   --om-soft:#F2E7D8;
--le:#A33A2C;   --le-soft:#F6E1DD;
--lich:#8C857A; --lich-soft:#EFEBE3;
--warn:#8A5A2B; --warn-soft:#F2E7D8;
--danger:#A33A2C;
--on-shift:#FFFFFF;
--r:14px;
```

### Dark

```
--bg:#12110E;  --surface:#1A1915;  --surface-2:#232119;
--ink:#F2EFE8; --muted:#9C958A;    --line:#2E2B23;
--accent:#4FD1C5; --accent-ink:#0C1A18; --accent-soft:#17322F;
--hero:#0B1413; --hero-ink:#F2EFE8; --hero-muted:#8EA3A0; --hero-line:#223330;
--s1:#4FD1C5; --s1-soft:#17322F;
--s2:#7FB1E0; --s2-soft:#1A2733;
--s3:#D9A066; --s3-soft:#332718;
--s4:#B295DC; --s4-soft:#271F33;
--s5:#93C286; --s5-soft:#1D2A1A;
--s6:#DC94AC; --s6-soft:#331E25;
--work:#4FD1C5; --work-ink:#0C1A18; --work-soft:#17322F;
--phep:#B295DC; --phep-soft:#271F33;
--om:#D9A066;   --om-soft:#332718;
--le:#E08573;   --le-soft:#33201B;
--lich:#8C857A; --lich-soft:#232119;
--warn:#D9A066; --warn-soft:#332718;
--danger:#E08573;
--on-shift:#0B0F17;
```

Giữ nguyên cơ chế ba khối như hiện tại: `:root`, `@media (prefers-color-scheme: dark){:root:not([data-theme="light"])}`, và `:root[data-theme="dark"]`.

### Ghi chú về các token màu

- **`--work` bằng `--accent`.** Ngày đi làm dùng chính màu nhấn. Đây là lý do app trông "một màu": thứ xuất hiện nhiều nhất trên lịch chính là accent.
- **`--s1` cũng bằng `--accent`.** Ca đầu tiên trong Cài đặt lấy màu nhấn, các ca sau lấy màu trầm khác.
- **`--s1`…`--s6` chỉ dùng cho nút chọn ca** ở tab Hôm nay, đúng như hành vi hiện tại. Không dùng trên lịch tháng.
- **Bỏ token `--shadow` và `--shadow-lg`** khỏi hệ thống. Thay bằng `border:1px solid var(--line)`. Chỉ còn hai chỗ được giữ bóng: nền mờ của cửa sổ sửa ngày, và thanh tab dưới đáy trên điện thoại (dùng `backdrop-filter`, không dùng shadow).
- **`--r` giảm từ `18px` xuống `14px`.** Bo góc nhẹ hơn trông ít "template" hơn. Riêng `.hero` dùng `18px`, `.sheet` dùng `18px`.

## 8. Đặc tả từng phần

Mỗi mục dưới đây ghi: hiện trạng → thành gì. Dấu ⚠ đánh dấu chỗ phải sửa JavaScript chứ không chỉ CSS.

### 8.1 Khung chung

- `.card` → nền `var(--surface)`, `border:1px solid var(--line)`, `border-radius:var(--r)`, **không** `box-shadow`.
- `.sec-title h2` → từ `17px/700` đen thành nhãn `12px/700`, `text-transform:uppercase`, `letter-spacing:.09em`, màu `var(--muted)`. Chữ to được dành riêng cho số liệu.
- `.app` giữ `max-width:1040px`.
- Giữ nguyên `.panel-grid` hai cột từ `900px` trở lên.

### 8.2 Thanh trên

- `.logo` bo `9px` (từ `11px`), bỏ `box-shadow` màu cam.
- `.sync` và `.refresh-btn` gộp về một kiểu `ghost` chung: cao `32px`, bo tròn hết, viền `1px`, nền trong suốt, chữ `12px` màu muted. Giữ nguyên mọi `data-state` và màu chấm theo trạng thái.

### 8.3 Thanh tab

- **Điện thoại (< 900px):** giữ nguyên vị trí cố định dưới đáy, nền `color-mix(in srgb, var(--bg) 88%, transparent)` + `backdrop-filter:blur(16px)`, viền trên `1px`. Tab đang chọn chỉ đổi màu chữ thành `var(--accent)`, **bỏ** nền viên thuốc `var(--cs)`.
- **Laptop (≥ 900px):** từ hàng viên thuốc nổi có bóng đổ thành tab gạch chân: `display:inline-flex`, chiếm hết chiều ngang, `border-bottom:1px solid var(--line)`, tab đang chọn có `border-bottom:2px solid var(--accent)` và `margin-bottom:-1px`.
- **Bỏ** bốn dòng gán màu riêng cho từng tab (`.tab[data-tab="today"]{--c:var(--s3)}`…). Mọi tab dùng chung `var(--accent)`.

### 8.4 Tab Hôm nay — khối hero

Đây là điểm nhấn duy nhất của toàn app.

- Nền `var(--hero)`, bo `18px`, **bỏ** `::after` (đốm gradient tròn ở góc). Khối phẳng.
- `.big` (số giờ hôm nay): thêm `font-family:var(--f-mono)`, cỡ `68px` trên điện thoại, `84px` từ `640px` trở lên, `line-height:.92`, `letter-spacing:-.05em`.
- `.hero-label` thành nhãn uppercase `11.5px` màu `var(--hero-muted)`.
- ⚠ **Thêm thanh tiến độ ca** ngay dưới `.big-label`: `<div class="bar"><i></i></div>`, cao `3px`, nền `var(--hero-line)`, phần đã chạy nền `var(--accent)`.
  - **Không** dùng `isOpen()`. `toggleShift()` luôn ghi sẵn cả `in` lẫn `out` khi bấm nút chấm ca, nên ca chấm bằng nút không bao giờ "mở"; điều kiện `isOpen()` sẽ làm thanh này không bao giờ hiện.
  - Thay vào đó so **giờ hiện tại** với khung giờ của từng ca trong ngày, qua hàm mới `shiftProgress(d, now)`: lấy `in`–`out` của ca (ca bỏ trống `out` thì mượn `out` của ca cùng `in` trong Cài đặt), xử lý ca qua đêm bằng cộng 1440 phút, trả về `(now − in) / (out − in)`.
  - Chỉ hiện khi giờ hiện tại nằm trong một ca của hôm nay. Mọi trường hợp khác ẩn hẳn bằng `hidden`.
  - Sửa trong `renderToday()`, hàm `shiftProgress()` đặt ngay trên nó.
- `.stamps` hiện đang `display:none` — để nguyên, không hồi sinh.

### 8.5 Tab Hôm nay — chọn ca

- `.shift-btn`: nền trong suốt, `border:1px solid var(--line)`, bo `12px`. Khi là ca có màu thì **chỉ** chữ giờ `.st` mang màu `var(--c)`, nền vẫn trong suốt. Hover đổi màu viền thành `var(--c)`.
- Trạng thái `.on` giữ nguyên cách làm hiện tại: nền đặc `var(--c)`, chữ `var(--on-shift)`. Bỏ `box-shadow` màu.
- ⚠ **Bỏ dòng `.sc`** ("Bấm để chấm ca này" / "✓ Đã chấm · bấm để bỏ") khỏi HTML sinh trong `renderToday()`. Trạng thái đã thể hiện qua nền đặc và `aria-pressed`, dòng chữ này làm nút cao gấp rưỡi mà không thêm thông tin. Giữ `aria-pressed` để trình đọc màn hình vẫn biết.

### 8.6 Tab Hôm nay — lương rút gọn

- `.mini-pay`: viền `1px` thay bóng đổ, hover đổi màu viền thành accent.
- `.mini-pay .v` tăng từ `28px` lên `34px`, bỏ màu accent, dùng `var(--ink)` — vì giờ nhãn đã là uppercase muted nên số tự nổi.
- Bỏ dấu `›` (`.chev`). Cả khối là nút bấm, mũi tên thừa.

### 8.7 Tab Hôm nay — tuần này

⚠ Đổi hẳn hình thức, phải viết lại `renderWeek()` (dòng ~954).

Từ dải 7 ô vuông chen chúc (`.week-strip` + `.wd`) thành 7 **dòng ngang**, mỗi dòng là `grid-template-columns:34px 1fr 52px`:

```
T2  ████████████████████  8h00
T3  ████████████████████  8h00
T4  ████████████████▏     6h30
T5                        nghỉ
T6  ███████████████▏      6h12
T7                        —
CN                        —
```

- Cột 1: tên thứ, `12.5px/700` màu muted. Hôm nay thì màu accent.
- Cột 2: `.track` cao `6px` bo `3px` nền `var(--surface-2)`; thanh trong `<i>` nền `var(--c)`, chiều rộng theo tỉ lệ giờ ngày đó trên **ngày nhiều giờ nhất trong tuần** (nếu tuần chưa có giờ nào thì mọi thanh rộng 0).
- Cột 3: số giờ mono `12.5px/700`, căn phải. Ngày nghỉ ghi chữ nghỉ ngắn, màu muted, không đậm.
- Mỗi dòng vẫn là `<button data-key="…">` để bấm mở cửa sổ sửa ngày, giữ `aria-label` đầy đủ như hiện tại.
- Màu `var(--c)` lấy từ `cellInfo(k).cls` y như hiện nay — tức ngày đi làm là một màu, ngày nghỉ theo loại nghỉ.
- `#weekMeta` giữ nguyên nội dung, chỉ đổi kiểu chữ.

### 8.8 Tab Lịch — thống kê tháng

⚠ Sửa chuỗi HTML trong `renderMonth()` (dòng ~972).

Từ 4 thẻ rời có bóng đổ thành **một khối** chia ô bằng đường kẻ: `.stats` có viền ngoài `1px` và `border-radius:var(--r)`, `overflow:hidden`; mỗi `.stat` ngăn nhau bằng `border-right`/`border-bottom`. 2 cột trên điện thoại, 4 cột từ `640px`.

Đổi lớp màu: `tone-blue`/`tone-green`/`tone-pink`/`tone-orange` → "Tổng giờ trong tháng" dùng `t-work`, "Làm thêm" dùng `t-om`, hai ô còn lại không có lớp màu (số màu `var(--ink)`).

`.stat .k` thành nhãn uppercase `11px` muted, `.stat .v` cỡ `24px` mono.

### 8.9 Tab Lịch — lưới tháng

- `.cal` viền `1px` thay bóng đổ.
- `.day` bo `9px` (từ `11px`), viền `1px` (từ `2px`).
- Ngày tương lai (`.future`): nền trong suốt + `border-color:var(--line)` thay vì nền `var(--surface-2)`. Tháng nhìn ra ngay đâu là phần đã qua.
- `.day.today` giữ viền accent.
- **Không đổi logic màu.** `cellInfo()` giữ nguyên: ngày đi làm trả `t-work` (một màu), ngày nghỉ trả `t-<loại>`. Chú giải giữ nguyên "Đi làm" + 4 loại nghỉ.
- `.legend i` từ ô có viền thành ô đặc `10px` bo `3px` màu `var(--c)`.

### 8.10 Tab Lịch — chi tiết tháng

⚠ Đổi markup, bọc bảng vào khối gấp mở.

Bảng chi tiết tháng hiện luôn hiện. Đổi thành `<details class="detail">` **mặc định đóng**:

```
┌──────────────────────────────────────────────────────┐
│ Xem chi tiết tháng   18 ngày công · 146h20 · bấm… ⌄ │
└──────────────────────────────────────────────────────┘
```

- `<summary>` cao tối thiểu `50px`, viền `1px`, bo `var(--r)`, có mũi tên `chev` xoay `180°` khi mở.
- Dòng tóm tắt bên trong summary lấy từ `summarize(keys)` của tháng đang xem: `${sum.workDays} ngày công · ${fmtDur(sum.work)} · bấm ngày để sửa`. ⚠ Phải cập nhật mỗi lần `renderMonth()` chạy.
- Dưới `560px` thì ẩn dòng tóm tắt, chỉ còn chữ "Xem chi tiết tháng".
- Khi mở: `summary` bỏ bo góc dưới và `border-bottom-color:transparent`, `.table-wrap` bỏ bo góc trên và `border-top:0`, để hai phần dính thành một khối.
- Dùng `<details>`/`<summary>` thuần, **không viết JS đóng mở**. Bàn phím và trình đọc màn hình tự chạy đúng.
- Nội dung bảng, 6 cột, mọi nút `row-btn`/`pay-btn` bên trong giữ nguyên hoàn toàn.
- Chip trong cột "Ca làm": bỏ `.ses-chips` nhiều chip màu, mỗi ca một chip `t-work` chữ mono.

### 8.11 Tab Lịch — xuất file

- `.export` giữ nguyên vị trí (ngay dưới khối chi tiết tháng) và giữ nguyên 3 lựa chọn phạm vi. Chỉ đổi `select` sang viền `1px` bo `11px`.
- Lý do giữ ở tab Lịch: phạm vi xuất phụ thuộc tháng đang xem.

### 8.12 Tab Lương

- `.pay` bỏ `border-top:5px solid var(--accent)`, dùng viền `1px` đều.
- `.pay-amount .v` tăng từ `44px` lên `52px` (điện thoại) / `64px` (từ `640px`), thêm `font-family:var(--f-mono)`, màu `var(--ink)` thay vì accent.
- `.pay-facts` từ 4 thẻ nền `var(--cs)` thành 4 ô chia bằng `border-right`, có `border-top` chung ở trên. Bỏ nền màu.

### 8.13 Tab Cài đặt

- `.set-card` bỏ `border-top:5px solid var(--c)` và bỏ ô vuông màu trước tiêu đề (`h2::before`). Năm thẻ dùng chung một kiểu viền `1px`.
- Bỏ các lớp `tone-grey`/`tone-violet`/`tone-green`/`tone-blue`/`tone-amber` trên 5 thẻ này.
- Ô nhập (`.field input`, `.sh-row input`) nền `var(--surface)` thay `var(--surface-2)`, viền `1px` bo `10px`, focus đổi viền sang accent.
- Nhãn `.field > span` thành uppercase `11px` muted.
- Thứ tự 5 thẻ giữ nguyên: Tài khoản · Ca làm cố định · Lương · Kỳ lương · Sao lưu dữ liệu.
- Khối Sao lưu giữ nguyên hai nút "Tải file sao lưu" / "Nhập từ file sao lưu" và khối xác nhận `#importConfirm`.

### 8.14 Cửa sổ sửa ngày

- `.backdrop` nền `color-mix(in srgb, var(--ink) 42%, transparent)` thay `--backdrop`.
- `.sheet` viền `1px`, bo `18px`, bỏ `box-shadow`.
- `.seg`: nút đang chọn nền `var(--surface)` + viền `1px` thay vì bóng đổ.
- `.qchip`: nền trong suốt + viền `1px`; khi `aria-pressed="true"` thì nền đặc `var(--c)` — ⚠ hiện đang hardcode `var(--work)`, đổi sang `var(--c)` để chip ca mang màu ca của nó.
- `.tf span` và `.field > span` thành uppercase `11px` muted.
- `.ed-pay` viền `1px` + nền `var(--surface-2)`.
- Nút "Xoá ngày này" giữ nguyên kiểu chữ đỏ không nền.

### 8.15 Toast

`.toast` giữ nguyên vị trí và cách hoạt động, đổi sang nền `var(--ink)` chữ `var(--bg)` (đã đúng sẵn), bỏ `box-shadow`, bo `12px`.

## 9. Những gì để người triển khai tự quyết

- Thứ tự sắp xếp lại các quy tắc CSS trong khối `<style>`, miễn giữ được chú thích tiếng Việt đang có.
- Cách tính chiều rộng thanh trong dải tuần (chuẩn hóa theo ngày cao nhất là gợi ý, không bắt buộc) miễn là tuần rỗng không gây chia cho 0.
- Tên lớp CSS mới. Ưu tiên tiếp tục lối đặt tên ngắn đang dùng (`.day`, `.stat`, `.tf`).
- Có tách `.ghost` thành lớp chung cho `.sync` và `.refresh-btn` hay giữ hai lớp riêng.

## 10. Kiểm tra trước khi coi là xong

- Bốn tab mở được, light và dark đều đúng, không còn màu xám-xanh lạnh ở đâu.
- Chấm một ca, bỏ chấm, mở cửa sổ sửa ngày, lưu, xoá — tất cả vẫn chạy.
- Lịch tháng: ngày đi làm cùng một màu; ngày nghỉ đúng màu theo loại; ngày tương lai nền trong suốt.
- Dải tuần hiển thị đúng tỉ lệ, tuần chưa có dữ liệu không vỡ.
- Khối "Xem chi tiết tháng" đóng mở được bằng chuột và bằng phím `Enter`/`Space`.
- Xuất Excel, xuất CSV, tải file sao lưu, nhập file sao lưu vẫn chạy.
- Màn hình rộng 360px không có thanh cuộn ngang.
- Số tiền 8 chữ số và tên ca dài không làm vỡ khối nào.

## 11. Quyết định phát sinh lúc triển khai

Một lượt soát tài liệu đã chỉ ra 10 chỗ thiếu thông tin. Dưới đây là cách xử lý từng chỗ. Tất cả là quyết định của người triển khai, chủ app chưa duyệt từng cái một.

1. **Thanh tiến độ ca** — xem §8.4 đã sửa. Điều kiện cũ là sai, sẽ khiến thanh không bao giờ hiện.
2. **Tab bar trên laptop** — giữ `position:sticky` nhưng đổi `top:10px` thành `top:0` và thêm `background:var(--bg)` cùng `padding-top:10px`, để nội dung không trôi lộ qua chữ.
3. **Popover chọn tháng** — giữ bóng đổ. Thay `--shadow-lg` bằng token mới `--float`, dùng cho đúng ba thứ thật sự nổi lên trên: `.mp-pop`, `.sheet`, `.toast`.
4. **`pay-facts` ở tab Lương** — bỏ `tone-blue/green/pink/orange` trong `renderPay()`, chỉ "Tổng giờ làm" giữ `t-work` và "Làm thêm" giữ `t-om`, giống tab Lịch. Đây là sửa JS, không chỉ CSS.
5. **Dòng tuần** — `min-height:44px` cho đủ vùng bấm; cột đầu xếp hai dòng, thứ ở trên và ngày trong tháng ở dưới dạng chữ nhỏ; chấm nhấp nháy "đang làm" chuyển sang `.week-row.is-open .h::before`.
6. **`.week-card`** — bỏ vạch xanh `border-top:5px` và `.week-total` đổi sang `var(--ink)`.
7. **`.table-head`** — xoá. Nhãn và dòng gợi ý chuyển hết vào `<summary>`.
8. **Ô lịch** — giữ nền `var(--cs)`. Lịch là chỗ duy nhất màu nhấn trải rộng, đúng ý "một màu lặp lại".
9. **Chữ ngày nghỉ ở dải tuần** — giữ `OFF_SHORT` theo từng loại nghỉ (Phép / Ốm / Lễ / Off), không gộp thành một chữ.
10. **Mockup và tài liệu lệch nhau** — tài liệu là bản có thẩm quyền.

Ngoài ra, sửa một lỗi có sẵn không nằm trong phạm vi ban đầu: file thiếu `<!doctype html>`, `<meta charset>` và `<meta name="viewport">`. Hệ quả là trang chạy ở quirks mode, hỏng dấu tiếng Việt khi máy chủ không khai báo UTF-8, và trên điện thoại trình duyệt dựng ở bề ngang mặc định rồi thu nhỏ — nên toàn bộ breakpoint mobile và `env(safe-area-inset-*)` chưa từng chạy đúng. Đã thêm bốn thẻ vào đầu file, kèm `lang="vi"` và `color-scheme`.

