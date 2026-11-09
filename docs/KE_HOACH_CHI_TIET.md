# KẾ HOẠCH CHI TIẾT – APP LUYỆN THUYẾT TRÌNH TIẾNG ANH CHO SINH VIÊN

> **Cách dùng chính:** sinh viên cầm **điện thoại làm micro** (quét mã QR, mở trang web, không cần cài app)
> và thuyết trình bằng tiếng Anh. Âm thanh truyền qua Wi‑Fi về **laptop của giảng viên**.
> Laptop hiện **phụ đề trực tiếp**. Khi sinh viên nói xong, app lập **bản transcript đầy đủ**,
> **tô màu những chỗ sai** về **phát âm**, **từ vựng** và **ngữ pháp/cấu trúc**, kèm **đề xuất sửa** cho từng lỗi.
>
> Nền tảng: **app desktop trên laptop (Windows + macOS)**. Điện thoại chỉ mở trang web nên chạy được
> trên cả iPhone lẫn Android mà không phải đưa app lên App Store hay CH Play.

---

## 1. Tóm tắt quyết định chính

| Vấn đề | Quyết định | Lý do |
|---|---|---|
| App chính | **Electron + React + TypeScript** trên laptop | Một mã nguồn cho Windows và macOS, cài bằng file `.exe` / `.dmg` |
| Micro | **Điện thoại sinh viên, qua trang web nội bộ (LAN) + mã QR** | Không cài app; iPhone dùng được qua Safari |
| Truyền âm thanh | WebSocket qua Wi‑Fi (trực tiếp) **+ ghi âm dự phòng ngay trên điện thoại** | Wi‑Fi chập chờn vẫn không mất bài; cuối bài gửi lại file đầy đủ |
| Phụ đề trực tiếp | Dịch vụ **STT streaming** (nhận dạng giọng nói → chữ) | Hiện chữ trong vòng dưới 1 giây, chiếu lên màn hình cho cả lớp |
| Transcript cuối | Chạy lại STT trên **toàn bộ file ghi âm**, có mốc thời gian từng từ | Chính xác hơn bản trực tiếp |
| **Lỗi phát âm** | **Dịch vụ chấm phát âm** (ví dụ Azure Pronunciation Assessment, SpeechSuper, Speechace) | Phát âm phải chấm **từ âm thanh**; mô hình ngôn ngữ chỉ đọc chữ nên không chấm được |
| **Lỗi từ vựng + ngữ pháp/cấu trúc** | **Claude API** (`claude-sonnet-5-5`) đọc toàn bộ transcript | Hiểu ngữ cảnh cả bài, trả về lỗi theo vị trí từng từ kèm giải thích tiếng Việt |
| Lưu trữ | **SQLite** trên laptop | Lưu theo lớp, theo sinh viên; không cần server |
| Khóa API | Nhập trong Cài đặt, lưu bằng kho khóa của hệ điều hành | Không ghi khóa vào mã nguồn |

---

## 2. Quy trình một buổi thuyết trình

```
 GIẢNG VIÊN (laptop)                     SINH VIÊN (điện thoại)
 ───────────────────                     ──────────────────────
 1. Mở app → "Buổi mới"
    chọn lớp, chủ đề → hiện QR + mã 6 số
                                         2. Quét QR → mở trang web
                                            nhập tên/MSSV (hoặc chọn từ danh sách)
                                            (tùy chọn) dán kịch bản bài nói
                                         3. Bấm "BẮT ĐẦU THUYẾT TRÌNH"
                                            → cho phép dùng micro
 4. Màn hình chiếu: phụ đề chạy trực tiếp ◀── âm thanh qua Wi‑Fi ───
    đồng hồ, tốc độ nói, mức âm lượng      (điện thoại vẫn ghi âm dự phòng)
                                         5. Bấm "KẾT THÚC"
                                            → gửi file ghi âm đầy đủ về laptop
 6. App phân tích (≈ 30–90 giây):
    a. transcript chính xác + mốc thời gian
    b. chấm phát âm từng từ
    c. phát hiện lỗi từ vựng, ngữ pháp/cấu trúc
    d. ghép tất cả lại thành báo cáo
 7. BÁO CÁO: transcript tô màu lỗi,       8. Sinh viên xem báo cáo trên điện thoại
    bấm vào lỗi để nghe lại đoạn âm thanh     (cùng trang web) hoặc nhận file PDF
    và xem đề xuất sửa
 9. Lưu vào hồ sơ sinh viên → sinh viên kế tiếp quét QR
```

---

## 3. Báo cáo sau thuyết trình (sản phẩm chính)

### 3.1. Tô màu lỗi trong transcript
| Màu | Loại lỗi | Ví dụ | Đề xuất hiện ra |
|---|---|---|---|
| 🟣 **Tím** | **Phát âm** | "**think**" đọc thành /sɪŋk/ | Âm đúng /θɪŋk/, âm bị sai (θ → s), nút nghe giọng mẫu và nút nghe lại giọng mình |
| 🟠 **Cam** | **Từ vựng** (dùng sai từ, sai kết hợp từ, lặp từ, từ quá đơn giản) | "make a research" | "**do/conduct** research"; nghĩa và ví dụ |
| 🔴 **Đỏ** | **Ngữ pháp / cấu trúc** (thì, mạo từ, số ít/nhiều, trật tự từ, câu thiếu động từ…) | "Yesterday I **go**…" | "Yesterday I **went**…"; giải thích ngắn bằng tiếng Việt |
| 🔵 **Xanh dương** | **Gợi ý nâng cấp** (câu đúng nhưng có cách nói hay hơn) | "It is very important" | "It is **crucial**…" / "What matters most is…" |

- Rê chuột hoặc bấm vào chỗ tô màu sẽ mở thẻ góp ý: lỗi, cách sửa, giải thích tiếng Việt, nút ▶ nghe lại đoạn đó.
- Có bộ lọc bật/tắt từng loại lỗi và chọn mức độ (chỉ lỗi nặng / tất cả).
- Có chế độ **"Bản đã sửa"**: hiện toàn bộ bài nói sau khi sửa để sinh viên đọc luyện lại.

### 3.2. Phần tổng quan đầu báo cáo
- **Điểm** (0–100): Phát âm, Độ trôi chảy, Từ vựng, Ngữ pháp, cùng điểm tổng (trọng số giảng viên chỉnh được).
- Thời lượng, số từ, **tốc độ nói (từ/phút)**, số lần ngập ngừng ("um", "uh"), các khoảng ngừng dài.
- **Top 5 lỗi lặp lại** (ví dụ: bỏ âm cuối /s/, quên chia thì quá khứ).
- **Nhận xét nội dung bài thuyết trình**: có mở bài, thân bài, kết luận không; có dùng từ nối/chuyển ý không; gợi ý 3–5 cụm câu chuyển ý.
- **Từ vựng nên học**: danh sách từ hoặc cụm từ gợi ý, có thể lưu vào sổ từ vựng.

### 3.3. Xuất và chia sẻ
- Xuất **PDF** (transcript có tô màu, bảng lỗi, điểm) để gửi sinh viên hoặc lưu hồ sơ.
- Xuất **Excel/CSV** điểm cả lớp.
- Sinh viên xem báo cáo ngay trên điện thoại qua trang web nội bộ khi còn trong lớp.

---

## 4. Kiến trúc tổng thể

```
┌──────── ĐIỆN THOẠI (trình duyệt) ────────┐
│ Trang web "phone-web"                     │
│ • getUserMedia → AudioWorklet (16 kHz PCM)│──── WebSocket (wss://) qua Wi‑Fi ───┐
│ • MediaRecorder: ghi âm dự phòng          │──── cuối bài: tải lên file đầy đủ ──┤
│ • Xem báo cáo                             │◀─── báo cáo (HTML) ─────────────────┤
└───────────────────────────────────────────┘                                     │
┌──────────────────────── LAPTOP (App Electron) ──────────────────────────────────▼─┐
│ Phone Bridge: máy chủ HTTPS + WebSocket, QR, mã ghép đôi 6 số                     │
│        │                                                                          │
│        ├──▶ STT streaming ──▶ Phụ đề trực tiếp (màn hình chiếu)                    │
│        │                                                                          │
│        └──▶ Lưu file WAV ──▶ PIPELINE PHÂN TÍCH (sau khi kết thúc)                 │
│                               1. STT toàn bài (mốc thời gian từng từ)             │
│                               2. Chấm phát âm  ──────┐                            │
│                               3. Claude: từ vựng,    ├──▶ 4. Ghép lỗi theo từ ──▶ BÁO CÁO
│                                  ngữ pháp, cấu trúc, │                            │
│                                  nội dung ───────────┘                            │
│ SQLite: lớp, sinh viên, buổi thuyết trình, lỗi, điểm, từ vựng                     │
└───────────────────────────────────────────────────────────────────────────────────┘
                 │ Internet
                 ▼
     Dịch vụ STT  ·  Dịch vụ chấm phát âm  ·  Claude API
```

---

## 5. Điện thoại làm micro (Phone Bridge) – phần lõi, làm ngay từ đầu

### 5.1. Cách hoạt động
1. App laptop chạy máy chủ HTTPS + WebSocket trong mạng nội bộ, ví dụ `https://192.168.1.15:8443`.
2. Màn hình hiện **mã QR** (chứa địa chỉ + mã phiên) và **mã 6 số** để nhập tay nếu không quét được.
3. Điện thoại mở trang web → nhập tên → bấm **Bắt đầu** → âm thanh được gửi liên tục từng gói 100 ms.
4. Mỗi lúc chỉ **1 điện thoại "đang nói"**; các máy khác vào hàng chờ, giảng viên bấm để gọi người kế tiếp.

### 5.2. Không để mất bài khi Wi‑Fi yếu
- Điện thoại **ghi âm song song** (MediaRecorder). Khi bấm Kết thúc thì tải file đầy đủ lên laptop.
- Phân tích cuối luôn dùng **file đầy đủ**. Luồng trực tiếp chỉ để hiện phụ đề.
- Rớt mạng giữa chừng: điện thoại tự kết nối lại, phụ đề có thể ngắt quãng nhưng báo cáo vẫn đủ.
- Trang web giữ màn hình sáng (Wake Lock) để điện thoại không tự khóa khi đang nói.

### 5.3. Vấn đề của iPhone/Safari và cách xử lý
| Vấn đề | Xử lý |
|---|---|
| Safari chỉ cho dùng micro trên trang **HTTPS** | Laptop tự tạo chứng chỉ. Lần đầu bấm "Vẫn truy cập" theo hướng dẫn có ảnh. Bản sau: cài chứng chỉ gốc (mkcert) một lần |
| Phải có thao tác chạm thì mới bật được âm thanh | Dùng nút "Bắt đầu" to, rõ |
| Tắt màn hình hoặc chuyển app thì micro dừng | Wake Lock, cảnh báo "đừng thoát trang khi đang nói" |
| Wi‑Fi trường chặn các máy nói chuyện với nhau | Laptop phát Wi‑Fi (hotspot) hoặc dùng bộ phát Wi‑Fi riêng cho lớp |

### 5.4. Chất lượng âm thanh
- Tắt khử ồn quá mạnh hoặc tự chỉnh âm lượng (`echoCancellation`/`autoGainControl`) nếu thấy làm méo giọng, vì có thể làm sai điểm phát âm. Sẽ thử cả hai chế độ ở tuần 1.
- Hướng dẫn sinh viên cầm điện thoại cách miệng khoảng 15–25 cm, đứng xa loa máy chiếu.
- Đo mức âm lượng; nếu quá nhỏ hoặc bị vỡ tiếng thì cảnh báo ngay trên điện thoại.

---

## 6. Nhận dạng giọng nói (transcript)

- **Trực tiếp**: STT streaming, hiện chữ xám (đang nghe) và chữ đen (đã chốt).
- **Cuối bài**: STT trên cả file, lấy **mốc thời gian và độ tin cậy từng từ**, giữ cả từ ngập ngừng.
- **Bắt buộc tắt** chế độ tự sửa/làm đẹp câu của dịch vụ STT nếu có, để giữ nguyên lỗi của sinh viên.
- Giao diện `SttProvider` dùng chung để đổi nhà cung cấp dễ dàng; tuần 1 so sánh 2–3 dịch vụ trên ghi âm thật của sinh viên.
- Tùy chọn sau này: Whisper chạy offline cho bản transcript cuối (miễn phí, riêng tư).

---

## 7. Chấm phát âm

**Vì sao cần dịch vụ riêng:** lỗi phát âm nằm trong âm thanh. Nếu chỉ đưa chữ cho AI thì không biết sinh viên đọc
"think" thành "sink" hay đọc đúng. Cần dịch vụ so sánh âm thanh với cách đọc chuẩn đến **từng âm vị (phoneme)**.

### 7.1. Hai chế độ
| Chế độ | Khi nào | Độ chính xác |
|---|---|---|
| **Có kịch bản** (sinh viên dán bài viết sẵn) | Bài chuẩn bị trước | **Cao nhất**: so với văn bản chuẩn, phát hiện cả từ bị bỏ hoặc đọc sai thành từ khác |
| **Nói tự do** (không có kịch bản) | Nói tự nhiên, hỏi đáp | Dùng chính transcript làm văn bản tham chiếu, chấm từng từ |

### 7.2. Kết quả lấy về và cách hiển thị
- Điểm từng từ (0–100), điểm từng âm vị, âm bị đọc sai, bỏ hoặc thừa âm.
- Điểm tổng: độ chính xác, độ trôi chảy, độ đầy đủ, ngữ điệu (nếu dịch vụ có).
- Từ có điểm dưới ngưỡng (ví dụ < 60, chỉnh được) → **tô tím**. Thẻ góp ý gồm: phiên âm IPA đúng, âm bị sai, mẹo sửa bằng tiếng Việt (ví dụ "/θ/: đặt đầu lưỡi giữa hai hàm răng rồi thổi hơi"), nút ▶ nghe giọng mẫu (TTS) và ▶ nghe lại giọng mình.
- **Lỗi hay gặp của người Việt** được gom nhóm để nhận xét: bỏ âm cuối (/s/, /t/, /d/, /k/), /θ/ /ð/, /ʃ/ ↔ /s/, nguyên âm dài/ngắn, trọng âm từ.

### 7.3. Phân biệt lỗi phát âm và lỗi từ vựng
Khi phát âm sai, STT có thể ghi thành **một từ khác** (đọc "ship" thành "sheep"). Nếu không xử lý, lỗi này
sẽ bị báo nhầm là lỗi từ vựng. Cách xử lý:
- Nếu có kịch bản: so khớp transcript với kịch bản, từ khác nhưng **gần âm** thì xếp vào lỗi phát âm.
- Nếu không có kịch bản: gửi kèm độ tin cậy STT cho Claude. Prompt yêu cầu: từ sai nhưng gần âm với một từ hợp lý
  trong ngữ cảnh thì xếp **"có thể là lỗi phát âm"**, không báo là lỗi từ vựng.

---

## 8. Phát hiện lỗi từ vựng, ngữ pháp, cấu trúc (Claude)

### 8.1. Đầu vào
- Transcript đầy đủ, **đánh số từng từ** (w0, w1, …) để Claude trả lỗi đúng vị trí.
- Từ có độ tin cậy STT thấp và từ bị chấm phát âm kém.
- Chủ đề, trình độ mục tiêu (B1/B2/C1), kịch bản (nếu có), mức độ sửa.
- Bài dài (trên 10 phút) thì chia đoạn khoảng 300 từ, mỗi đoạn kèm đoạn trước làm ngữ cảnh, xử lý song song.

### 8.2. Đầu ra (JSON cố định – dùng structured output / tool use)
```json
{
  "issues": [
    { "type": "grammar", "subtype": "verb_tense",
      "word_range": [12, 12], "original": "go", "suggestion": "went",
      "explain_vi": "'Last week' là quá khứ → dùng quá khứ đơn.", "severity": "major" },
    { "type": "vocabulary", "subtype": "collocation",
      "word_range": [20, 22], "original": "make a research", "suggestion": "conduct research",
      "explain_vi": "'research' đi với 'do/conduct', không đi với 'make'; và là danh từ không đếm được.", "severity": "major" },
    { "type": "structure", "subtype": "sentence_structure",
      "word_range": [30, 41], "original": "Because the price is high. So many people don't buy.",
      "suggestion": "Because the price is high, many people don't buy it.",
      "explain_vi": "Không dùng 'because' và 'so' cùng lúc; mệnh đề 'because' không đứng một mình.", "severity": "major" },
    { "type": "upgrade", "word_range": [50, 53], "original": "very very important",
      "suggestion": "absolutely crucial", "explain_vi": "Tránh lặp 'very'; dùng tính từ mạnh hơn.", "severity": "minor" }
  ],
  "corrected_text": "…toàn bộ bài sau khi sửa…",
  "content_feedback_vi": {
    "structure": "Có mở bài rõ, thiếu kết luận tóm tắt ý chính.",
    "transitions": ["Moving on to…", "Another key point is…", "To sum up,…"],
    "strengths": ["Ví dụ minh họa cụ thể"]
  },
  "scores": { "vocabulary": 72, "grammar": 65 }
}
```

### 8.3. Nguyên tắc của prompt
- Đây là **văn nói thuyết trình**: không bắt lỗi dấu câu hoặc viết hoa (do STT tự thêm), chấp nhận cách nói tự nhiên.
- Chỉ báo lỗi chắc chắn; trường hợp nghi do STT nghe sai thì đánh dấu "có thể".
- Giải thích tiếng Việt ngắn (≤ 30 từ); gợi ý chỉ cao hơn trình độ hiện tại **một bậc**.
- Kiểm tra JSON trả về (đúng schema, `word_range` hợp lệ, `original` khớp transcript). Nếu sai thì yêu cầu trả lại một lần.

### 8.4. Model và chi phí
- Phân tích cuối bài: `claude-sonnet-5-5` (chất lượng quan trọng hơn tốc độ, chỉ gọi 1 lần mỗi bài).
- Gợi ý nhanh trên phụ đề trực tiếp (tùy chọn, có thể tắt): `claude-haiku-5-5`.
- Dùng prompt caching cho prompt hệ thống. Chi phí thực tế mỗi bài (STT + phát âm + Claude) sẽ đo ở tuần 1–2, sau đó đặt **giới hạn chi tiêu** trong Cài đặt.

---

## 9. Ghép kết quả thành báo cáo
1. Lấy transcript cuối làm **trục chính** (mỗi từ có chỉ số và mốc thời gian).
2. Gắn điểm phát âm vào từng từ theo mốc thời gian.
3. Gắn lỗi của Claude theo `word_range`.
4. Một từ có nhiều lỗi thì hiện **màu theo lỗi nặng nhất** và gộp nhiều mục trong cùng thẻ góp ý.
5. Tính điểm tổng, top lỗi lặp lại, thống kê, rồi lưu vào SQLite và tạo báo cáo HTML/PDF.

---

## 10. Giao diện (phác thảo)

**Màn hình chiếu khi sinh viên đang nói**
```
┌──────────────────────────────────────────────────────────────────────┐
│ 🎤 Nguyễn Văn A – 2051xxxx   Chủ đề: Climate change   ⏱ 03:12   ▮▮▮▮▯ │
├──────────────────────────────────────────────────────────────────────┤
│   Good morning everyone. Today I want to talk about climate change,  │
│   which is one of the biggest problem in the world. Last week I      │
│   ░░ read an article about… (đang nghe)                              │
├──────────────────────────────────────────────────────────────────────┤
│ Tốc độ: 112 từ/phút      Hàng chờ: B (đã quét), C (đã quét)  [Kết thúc]│
└──────────────────────────────────────────────────────────────────────┘
```

**Báo cáo**
```
┌──────────────────────────────────────────────┬───────────────────────┐
│ ĐIỂM  Phát âm 68 · Trôi chảy 74 · Từ vựng 72 │ GÓP Ý                 │
│       Ngữ pháp 65 · TỔNG 70                  │ 🔴 problem → problems │
│ [🟣 Phát âm] [🟠 Từ vựng] [🔴 Ngữ pháp] [🔵] │ "one of the + danh từ │
├──────────────────────────────────────────────┤  số nhiều"            │
│ Good morning everyone. Today I want to talk  │ ▶ nghe lại 00:14      │
│ about climate change, which is one of the    │                       │
│ biggest 🔴problem🔴 in the world. Last week I │ 🟣 think /θɪŋk/       │
│ 🔴go🔴 to a seminar and 🟠make a research🟠   │ bạn đọc /sɪŋk/        │
│ … I 🟣think🟣 we must act now …               │ ▶ mẫu  ▶ giọng bạn    │
├──────────────────────────────────────────────┴───────────────────────┤
│ Top lỗi: bỏ âm cuối /s/ (7) · thì quá khứ (4) · /θ/ (3)               │
│ [Bản đã sửa] [Xuất PDF] [Gửi sinh viên] [Sinh viên kế tiếp ▶]         │
└──────────────────────────────────────────────────────────────────────┘
```

Các màn hình khác: **Lớp học / danh sách sinh viên**, **Hồ sơ sinh viên** (tiến bộ qua các bài), **Cài đặt**, **Trang điện thoại** (nút to: Bắt đầu / Kết thúc, mức âm lượng, xem báo cáo).

---

## 11. Lưu trữ dữ liệu (SQLite)

```sql
classes        (id, name, term)
students       (id, class_id, student_code, full_name)
presentations  (id, student_id, topic, script_text, started_at, duration_ms,
                audio_path, transcript_json, scores_json, content_feedback_json)
words          (presentation_id, idx, text, start_ms, end_ms, stt_confidence, pron_score, phonemes_json)
issues         (id, presentation_id, type, subtype, word_start, word_end,
                original, suggestion, explain_vi, severity)
vocab          (id, student_id, word, meaning_vi, example, added_at)
settings       (key, value)
```
- File âm thanh lưu trên laptop. Có tùy chọn tự xóa sau N ngày (mặc định 30) để bảo vệ quyền riêng tư.
- Thống kê tiến bộ = so sánh điểm và số lỗi theo loại qua các bài của cùng sinh viên.

---

## 12. Cấu trúc thư mục mã nguồn dự kiến

```
appluyennoi/
├─ docs/
├─ apps/
│  ├─ desktop/                    # Electron (laptop giảng viên)
│  │  ├─ src/main/
│  │  │  ├─ phone-bridge/         # HTTPS + WebSocket, chứng chỉ, QR, ghép đôi, hàng chờ
│  │  │  ├─ stt/                  # SttProvider (streaming + cả file)
│  │  │  ├─ pronunciation/        # PronunciationProvider
│  │  │  ├─ analysis/             # gọi Claude, prompt, schema, ghép kết quả, chấm điểm
│  │  │  ├─ report/               # tạo HTML/PDF
│  │  │  └─ db/
│  │  ├─ src/preload/
│  │  └─ src/renderer/            # React: Phụ đề, Báo cáo, Lớp học, Cài đặt
│  └─ phone-web/                  # trang web cho điện thoại sinh viên
├─ packages/shared/               # kiểu dữ liệu chung (TypeScript)
└─ tests/                         # ghi âm mẫu + transcript có đáp án lỗi
```
Công cụ: Vite, electron-builder, better-sqlite3, ffmpeg (đóng gói kèm), Vitest, Playwright.

---

## 13. Lộ trình (≈ 10 tuần, 1–2 lập trình viên)

| Tuần | Công việc | Kết quả bàn giao |
|---|---|---|
| **1** | **Thử nghiệm rủi ro lớn nhất:** (a) iPhone Safari gửi âm thanh về laptop qua HTTPS/WebSocket; (b) so sánh 2–3 dịch vụ STT và 2 dịch vụ chấm phát âm trên 20 bài ghi âm thật của sinh viên; (c) prompt Claude trên 30 transcript mẫu | Báo cáo chọn công nghệ + prompt v1 |
| **2** | Khung Electron + React; Phone Bridge: QR, mã ghép đôi, trang phone-web, Bắt đầu/Kết thúc | Điện thoại nói thì laptop nhận được âm thanh |
| **3** | Ghi âm dự phòng trên điện thoại, tải file lên, tự kết nối lại, hàng chờ nhiều sinh viên; phụ đề trực tiếp | **Phụ đề trực tiếp từ điện thoại** |
| **4** | Pipeline cuối bài: STT cả file + mốc thời gian từng từ; lưu SQLite | Transcript đầy đủ chính xác |
| **5** | Tích hợp chấm phát âm (có/không kịch bản), tô tím, nghe lại đoạn âm thanh | **Lỗi phát âm** |
| **6** | Claude: từ vựng, ngữ pháp, cấu trúc, nội dung; kiểm tra JSON; ghép lỗi; điểm | **Lỗi từ vựng + ngữ pháp + đề xuất sửa** |
| **7** | Màn hình báo cáo, bộ lọc, bản đã sửa, xuất PDF; sinh viên xem báo cáo trên điện thoại | **Bản MVP hoàn chỉnh** |
| **8** | **Dùng thử trong 1–2 lớp thật**, thu phản hồi giảng viên và sinh viên, đo độ chính xác | Danh sách cần sửa |
| **9** | Sửa lỗi, tinh chỉnh prompt/ngưỡng chấm; lớp học, hồ sơ sinh viên, xuất điểm Excel | Bản 1.0 |
| **10** | Đóng gói bản cài Windows/macOS, hướng dẫn sử dụng (có ảnh cho bước HTTPS trên iPhone) | **Phát hành 1.0** |

Sau đó: sổ từ vựng/flashcard, so sánh tiến bộ qua các bài, chế độ offline (Whisper), hỏi đáp với AI sau thuyết trình.

---

## 14. Kiểm thử & tiêu chí chất lượng

- **Bộ dữ liệu**: 30 bài thuyết trình ghi âm thật (có sự đồng ý), nhiều trình độ, có tiếng ồn lớp học. Giảng viên đánh dấu lỗi thủ công để làm đáp án.
- **Transcript**: tỉ lệ lỗi nhận dạng từ (WER) ≤ 12% khi điện thoại cách miệng ≤ 30 cm.
- **Ngữ pháp/từ vựng**: phát hiện ≥ 80% lỗi giảng viên đánh dấu; báo lỗi oan ≤ 10%.
- **Phát âm**: các từ app tô tím trùng ≥ 70% với từ giảng viên cho là sai (sẽ điều chỉnh ngưỡng theo dữ liệu thật).
- **Thời gian**: báo cáo xong ≤ 90 giây cho bài 5 phút; phụ đề trực tiếp trễ < 1 giây.
- **Độ bền**: rút Wi‑Fi 10 giây giữa bài thì báo cáo vẫn đầy đủ; chạy liền 3 giờ (cả buổi học) không lỗi.
- **Thiết bị**: thử trên iPhone (Safari) và Android (Chrome), mỗi loại ít nhất 2 đời máy.

---

## 15. Rủi ro & cách xử lý

| Rủi ro | Cách xử lý |
|---|---|
| iPhone không cho dùng micro vì chứng chỉ tự ký | Làm thử ngay tuần 1; có hướng dẫn kèm ảnh; dự phòng: sinh viên ghi âm bằng app Ghi âm rồi gửi file (AirDrop/Zalo), app mở file để phân tích |
| Wi‑Fi trường chặn kết nối giữa các máy | Laptop phát hotspot hoặc dùng bộ phát Wi‑Fi riêng |
| STT tự sửa ngữ pháp nên che lỗi | Tắt smart format; chọn dịch vụ ở tuần 1 dựa trên dữ liệu thật |
| Phát âm sai bị nhận thành từ khác, báo nhầm thành lỗi từ vựng | Mục 7.3: dùng kịch bản, độ tin cậy STT, nhãn "có thể là lỗi phát âm" |
| Chấm phát âm chưa chuẩn với giọng Việt | Giảng viên chỉnh ngưỡng; nút "Báo chấm sai" để thu dữ liệu cải tiến |
| Ồn lớp học, tiếng vọng loa máy chiếu | Hướng dẫn cầm điện thoại gần miệng; cảnh báo khi âm thanh kém |
| Chi phí API khi cả lớp dùng | Đo chi phí thực tế mỗi bài ở tuần 1; giới hạn chi tiêu; tắt gợi ý trực tiếp nếu không cần |
| Quyền riêng tư giọng nói sinh viên | Xin đồng ý; nói rõ dữ liệu gửi tới dịch vụ nào; tự xóa âm thanh sau N ngày; nút xóa dữ liệu một sinh viên |

---

## 16. Cần chuẩn bị / quyết định trước khi code

1. Tài khoản và khóa API: 1 dịch vụ STT, 1 dịch vụ chấm phát âm, Claude API.
2. 20–30 bài thuyết trình ghi âm thật của sinh viên (có đồng ý), kèm đánh dấu lỗi của giảng viên.
3. Laptop giảng viên chủ yếu dùng Windows hay macOS? Phòng học có Wi‑Fi ổn định, có chặn kết nối giữa các máy không?
4. Thang điểm và trọng số mong muốn (phát âm / trôi chảy / từ vựng / ngữ pháp / nội dung).
5. Sinh viên thường thuyết trình theo kịch bản soạn sẵn hay nói tự do? (ảnh hưởng độ chính xác chấm phát âm)
6. Ngân sách chi phí API mỗi học kỳ.
