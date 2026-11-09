# KẾ HOẠCH CHI TIẾT – APP LUYỆN NÓI TIẾNG ANH (bản cho laptop)

> Mục tiêu: học viên nói tiếng Anh vào micro (micro laptop, tai nghe, hoặc **điện thoại dùng làm micro**),
> app hiện **phụ đề tiếng Anh theo thời gian thực**, đồng thời **sửa lỗi**, **gợi ý từ vựng** và
> **gợi ý cấu trúc câu** hay hơn.
>
> Nền tảng: **ứng dụng desktop trên laptop (Windows + macOS)**. Không làm app iPhone vì phải đưa lên
> App Store (duyệt, tài khoản developer, phí hằng năm). Điện thoại vẫn dùng được – chỉ làm micro.

---

## 1. Tóm tắt quyết định chính

| Vấn đề | Quyết định | Lý do |
|---|---|---|
| Nền tảng | App desktop **Electron + React + TypeScript** | Một mã nguồn chạy cả Windows và macOS, đóng gói thành file `.exe` / `.dmg`, không qua kho ứng dụng |
| Thu âm | Web Audio API trong Electron (`getUserMedia`) | Đọc được mọi micro hệ điều hành nhận diện: micro laptop, tai nghe, USB, Bluetooth |
| Điện thoại làm micro | **Trang web nội bộ (LAN) + mã QR** – không cần cài app | Điện thoại mở trình duyệt, quét QR, bấm "Bắt đầu" → âm thanh truyền về laptop qua Wi‑Fi |
| Nhận dạng giọng nói (phụ đề) | Dịch vụ **streaming STT trên mây** (mặc định) + **Whisper chạy offline** (tùy chọn) | Mây: độ trễ thấp (~300–800 ms), chính xác. Offline: miễn phí, riêng tư, cần máy khỏe |
| Sửa lỗi / gợi ý | **Claude API** (model nhanh `claude-haiku-5-5` cho từng câu, `claude-sonnet-5-5` cho tổng kết buổi) | Trả về JSON có cấu trúc: lỗi, câu sửa, giải thích tiếng Việt, từ vựng, cấu trúc |
| Lưu trữ | **SQLite** cục bộ trên máy | Lưu buổi học, lỗi hay gặp, sổ từ vựng; không cần server riêng |
| Khóa API | Người dùng/giáo viên nhập trong Cài đặt, lưu bằng kho khóa của hệ điều hành (`keytar` / safeStorage) | Không ghi khóa vào mã nguồn |

---

## 2. Người dùng & tình huống sử dụng

1. **Học viên tự luyện**: mở app → chọn chủ đề (giới thiệu bản thân, phỏng vấn, IELTS Part 2…) → nói → xem phụ đề và lời sửa ngay.
2. **Giáo viên trong lớp**: laptop nối máy chiếu, học viên cầm điện thoại làm micro (quét QR), cả lớp cùng xem phụ đề và lỗi trên màn hình lớn.
3. **Luyện từ file ghi âm**: học viên ghi âm sẵn (bằng app Ghi âm của điện thoại) → kéo thả file `.m4a/.mp3/.wav` vào app → app chạy phụ đề và sửa lỗi y như lúc nói trực tiếp.

---

## 3. Danh sách tính năng

### 3.1. MVP (bản đầu tiên – bắt buộc có)
- [ ] Chọn thiết bị micro (danh sách micro hệ điều hành nhận), đo mức âm lượng (thanh VU).
- [ ] Nút **Bắt đầu / Tạm dừng / Kết thúc** buổi luyện.
- [ ] **Phụ đề thời gian thực**: chữ xám = đang nghe (tạm thời), chữ đen = câu đã chốt.
- [ ] Khi một câu được chốt → gửi đi phân tích → hiện **thẻ góp ý** bên phải:
  - Câu gốc (lỗi gạch chân đỏ) → câu đã sửa (xanh).
  - Giải thích ngắn bằng **tiếng Việt** (loại lỗi: thì, mạo từ, giới từ, hòa hợp chủ‑vị, từ sai…).
  - 1–3 **từ vựng nâng cao** thay thế (kèm nghĩa, ví dụ).
  - 1 **cấu trúc câu** hay hơn (ví dụ: "Not only… but also…").
- [ ] Mở file âm thanh có sẵn để phân tích.
- [ ] Lưu lịch sử buổi học, xuất bản ghi (TXT/PDF).
- [ ] Trang Cài đặt: khóa API, ngôn ngữ giải thích, trình độ (A2/B1/B2/C1), mức độ sửa (chỉ lỗi nặng / tất cả).

### 3.2. Giai đoạn 2
- [ ] **Điện thoại làm micro qua Wi‑Fi** (QR code) – chi tiết ở mục 5.
- [ ] **Tổng kết buổi học**: số lỗi theo loại, 5 lỗi lặp lại nhiều nhất, từ vựng mới, điểm ước lượng (trôi chảy, ngữ pháp, từ vựng).
- [ ] **Sổ từ vựng** + ôn tập kiểu flashcard (lặp lại ngắt quãng).
- [ ] Chủ đề luyện nói + câu hỏi gợi mở do AI tạo.
- [ ] Đo tốc độ nói (từ/phút), số lần ngập ngừng ("um", "uh").

### 3.3. Giai đoạn 3 (mở rộng)
- [ ] Chấm phát âm (pronunciation assessment) theo từng từ.
- [ ] Chế độ hội thoại: AI đóng vai người phỏng vấn, đọc câu hỏi bằng giọng nói (TTS).
- [ ] Chế độ offline hoàn toàn (Whisper + mô hình nhỏ chạy cục bộ).
- [ ] Tài khoản giáo viên xem tiến bộ của nhiều học viên (cần server – chỉ làm khi có nhu cầu).

---

## 4. Kiến trúc tổng thể

```
┌──────────────────── LAPTOP (App Electron) ─────────────────────────────┐
│                                                                        │
│  NGUỒN ÂM THANH                 XỬ LÝ                     GIAO DIỆN     │
│  ┌──────────────┐                                                      │
│  │ Micro laptop │──┐                                                   │
│  │ / tai nghe   │  │   ┌───────────────┐   ┌──────────────┐  ┌───────┐ │
│  └──────────────┘  ├──▶│ Audio Engine  │──▶│  STT Client  │─▶│ Phụ đề│ │
│  ┌──────────────┐  │   │ 16kHz mono PCM│   │ (streaming)  │  │ (live)│ │
│  │ File ghi âm  │──┤   │ + VAD         │   └──────┬───────┘  └───────┘ │
│  └──────────────┘  │   └───────────────┘          │ câu đã chốt         │
│  ┌──────────────┐  │                       ┌──────▼───────┐  ┌───────┐ │
│  │ Điện thoại   │──┘                       │ Feedback     │─▶│ Thẻ   │ │
│  │ (WebSocket   │                          │ Engine       │  │ góp ý │ │
│  │  qua Wi‑Fi)  │                          │ (Claude API) │  └───────┘ │
│  └──────────────┘                          └──────┬───────┘            │
│                                             ┌─────▼─────┐              │
│                                             │  SQLite   │ lịch sử, từ  │
│                                             └───────────┘ vựng, lỗi    │
└────────────────────────────────────────────────────────────────────────┘
          │ Internet (HTTPS / WebSocket)
          ▼
   Dịch vụ STT streaming          Claude API
```

### 4.1. Luồng dữ liệu một câu nói
1. Micro → `AudioWorklet` lấy mẫu, chuyển về **PCM 16‑bit, 16 kHz, mono**, gói 100 ms/khung.
2. Khung âm thanh gửi liên tục qua WebSocket tới dịch vụ STT.
3. STT trả về 2 loại kết quả:
   - **interim** (tạm thời) → cập nhật dòng phụ đề màu xám, ~200–500 ms/lần.
   - **final** (đã chốt, khi người nói ngắt nghỉ) → dòng chữ đen.
4. Câu **final** đưa vào hàng đợi phân tích. Gom thêm 1–2 câu trước làm ngữ cảnh.
5. Feedback Engine gọi Claude (streaming), nhận JSON → hiện thẻ góp ý (mục tiêu < 2 giây sau khi chốt câu).
6. Lưu câu + góp ý vào SQLite.

### 4.2. Ngân sách độ trễ (mục tiêu)
| Bước | Mục tiêu |
|---|---|
| Thu âm + đóng gói | < 100 ms |
| Phụ đề tạm thời hiện lên | < 700 ms sau khi nói |
| Câu được chốt (sau khi ngừng nói) | 300–800 ms |
| Thẻ góp ý hiện lên | < 2,5 s sau khi câu chốt |

---

## 5. Cách lấy âm thanh – trả lời câu hỏi "mở âm thanh bằng gì?"

App đọc âm thanh **giống như mọi ứng dụng gọi điện/họp trực tuyến** (Zoom, Meet): lấy từ thiết bị
micro mà hệ điều hành đang nhận. Có 4 cách, xếp theo độ đơn giản:

### Cách A – Micro laptop hoặc tai nghe (mặc định, dễ nhất)
- Tai nghe có dây/Bluetooth, hoặc micro USB. Chọn trong menu "Thiết bị micro" của app.
- Khuyên dùng tai nghe có micro để giảm tiếng ồn → phụ đề chính xác hơn.

### Cách B – Điện thoại làm micro qua Wi‑Fi (**không cần cài app trên điện thoại**) ⭐
1. App laptop bật một máy chủ nhỏ trong mạng nội bộ (ví dụ `https://192.168.1.15:8443`) và hiện **mã QR**.
2. Điện thoại (iPhone hoặc Android) **cùng Wi‑Fi** quét QR → mở trang web trong Safari/Chrome.
3. Bấm **"Bắt đầu nói"** → trình duyệt xin quyền micro → âm thanh gửi về laptop qua WebSocket.
4. Laptop nhận âm thanh và xử lý như micro thường; phụ đề hiện trên laptop (và có thể hiện lại trên điện thoại).

Lưu ý kỹ thuật:
- Safari trên iPhone **chỉ cho dùng micro trên trang HTTPS** → app tự tạo chứng chỉ (self‑signed).
  Lần đầu điện thoại sẽ cảnh báo "không an toàn" → hướng dẫn bấm "Vẫn truy cập". Có thể cải tiến sau bằng `mkcert`.
- Mỗi phiên kết nối có **mã ghép đôi 6 số** để người khác trong cùng Wi‑Fi không chen vào.
- Wi‑Fi trường học đôi khi chặn các máy nói chuyện với nhau (client isolation) → dùng **điểm phát Wi‑Fi từ laptop/điện thoại**.

### Cách C – Điện thoại nối với máy tính bằng hệ điều hành / phần mềm có sẵn
- **Mac + iPhone**: tính năng *Continuity Camera* của macOS cho phép chọn iPhone làm micro → app thấy ngay trong danh sách micro.
- **Windows**: dùng phần mềm điện thoại‑thành‑micro có sẵn (ví dụ WO Mic) hoặc tai nghe Bluetooth.
- App không cần làm gì thêm: chỉ cần chọn đúng thiết bị trong danh sách.

### Cách D – Mở file ghi âm có sẵn
- Kéo thả file `.mp3 .m4a .wav .ogg .webm` vào app.
- App giải mã bằng `ffmpeg` (đóng gói kèm app) → phát lại cùng phụ đề chạy theo thời gian, lỗi đánh dấu theo mốc thời gian.

---

## 6. Nhận dạng giọng nói (Speech‑to‑Text)

Thiết kế một **giao diện chung** để đổi nhà cung cấp mà không sửa phần còn lại:

```ts
interface SttProvider {
  start(opts: { language: 'en-US'; sampleRate: 16000 }): Promise<void>;
  pushAudio(pcm: Int16Array): void;
  stop(): Promise<void>;
  on(event: 'interim' | 'final', cb: (r: SttResult) => void): void;
}
interface SttResult {
  text: string;
  words: { word: string; start: number; end: number; confidence: number }[];
  isFinal: boolean;
}
```

| Lựa chọn | Ưu | Nhược | Dùng khi |
|---|---|---|---|
| STT streaming trên mây (Deepgram, AssemblyAI, Azure Speech, Google Speech…) | Độ trễ thấp, chính xác với giọng người Việt nói tiếng Anh, có điểm tin cậy từng từ | Tốn phí theo phút, cần Internet | **Mặc định MVP** |
| Whisper offline (`whisper.cpp` / `faster-whisper`) | Miễn phí, riêng tư, chạy không cần mạng | Không phải streaming thật (phải cắt đoạn 2–5 s), cần máy mạnh/GPU | Chế độ tiết kiệm/offline |

**Quan trọng cho việc sửa lỗi:** nhiều dịch vụ STT tự "làm đẹp" câu (tự sửa ngữ pháp nhẹ, thêm dấu câu).
Cần **tắt các tùy chọn tự sửa/smart format** nếu có, và giữ lại cả từ ngập ngừng ("um", "uh")
để phản ánh đúng câu học viên nói. Trước khi chốt nhà cung cấp, làm **thử nghiệm so sánh** (mục 11, tuần 1).

---

## 7. Bộ sửa lỗi & gợi ý (Feedback Engine)

### 7.1. Khi nào gọi
- Mỗi khi có câu **final** dài ≥ 3 từ. Bỏ qua câu chỉ có "yes", "okay"…
- Gộp các câu ngắn liền nhau trong 1,5 s thành một lượt gọi để tiết kiệm.
- Tối đa 1 yêu cầu đang chạy + 1 trong hàng đợi; nếu người nói quá nhanh thì gộp tiếp.

### 7.2. Đầu vào gửi cho Claude
- Câu cần sửa + 2 câu trước (ngữ cảnh) + chủ đề đang luyện.
- Trình độ học viên (B1…), mức độ sửa, ngôn ngữ giải thích (tiếng Việt).
- Các từ có độ tin cậy STT thấp → ghi chú "có thể do nhận dạng sai, đừng bắt lỗi chắc chắn".

### 7.3. Đầu ra (JSON cố định – dùng tính năng structured output / tool use)
```json
{
  "original": "Yesterday I go to school by bus and meet my friend.",
  "corrected": "Yesterday I went to school by bus and met my friend.",
  "is_correct": false,
  "errors": [
    { "span": "go", "fix": "went", "type": "verb_tense",
      "explain_vi": "'Yesterday' là quá khứ → dùng quá khứ đơn 'went'." },
    { "span": "meet", "fix": "met", "type": "verb_tense",
      "explain_vi": "Động từ thứ hai cũng phải ở quá khứ: 'met'." }
  ],
  "vocabulary": [
    { "word": "commute", "meaning_vi": "đi lại (đi làm/đi học) hằng ngày",
      "example": "I commute to school by bus." }
  ],
  "structure": {
    "pattern": "On my way to …, I ran into …",
    "example": "On my way to school, I ran into an old friend.",
    "explain_vi": "'Run into' = tình cờ gặp, tự nhiên hơn 'meet'."
  },
  "naturalness_tip_vi": "Người bản xứ hay nói 'took the bus to school'."
}
```

### 7.4. Nguyên tắc của prompt hệ thống (tóm tắt)
- Chỉ sửa lỗi **thật**; không bắt lỗi văn nói chấp nhận được ("gonna", câu ngắn).
- Không bắt lỗi dấu câu/viết hoa (vì là văn nói, do STT tự thêm).
- Giải thích ngắn (≤ 25 từ), bằng tiếng Việt, đúng trình độ.
- Gợi ý từ vựng/cấu trúc **cao hơn trình độ hiện tại một bậc**, không quá khó.
- Nếu câu đúng: `is_correct: true`, vẫn có thể đưa 1 gợi ý nói tự nhiên hơn.

### 7.5. Chọn model & chi phí
- Góp ý từng câu: `claude-haiku-5-5` (nhanh, rẻ). Nếu thấy chất lượng chưa đủ thì chuyển `claude-sonnet-5-5`.
- Tổng kết cuối buổi: `claude-sonnet-5-5` (phân tích toàn bộ buổi, tìm lỗi lặp lại).
- Dùng **prompt caching** cho phần prompt hệ thống dài (giống nhau mọi lần gọi) để giảm chi phí và độ trễ.
- Chi phí thực tế sẽ đo ở tuần 1–2 (số token/câu × số câu/buổi) rồi ghi vào tài liệu; đặt **giới hạn chi tiêu** trong Cài đặt.

---

## 8. Giao diện (phác thảo)

```
┌────────────────────────────────────────────────────────────────────────┐
│ 🎤 Micro: [Tai nghe AirPods ▾]  ▮▮▮▮▯▯▯  │ Chủ đề: [Phỏng vấn xin việc ▾]│
│ [● Bắt đầu]  [⏸ Tạm dừng]  [■ Kết thúc]   │ 📱 Dùng điện thoại (QR)      │
├───────────────────────────────────────────┬────────────────────────────┤
│ PHỤ ĐỀ                                    │ GÓP Ý                      │
│                                           │ ┌────────────────────────┐ │
│ I have been working as a teacher          │ │ ✗ I go to school…      │ │
│ for three years.                     ✓    │ │ ✓ I went to school…    │ │
│                                           │ │ 📘 Thì quá khứ đơn vì… │ │
│ Yesterday I ~~go~~ to school by bus  ⚠    │ │ 💡 Từ vựng: commute    │ │
│ and ~~meet~~ my friend.                   │ │ 🧩 Cấu trúc: ran into  │ │
│                                           │ └────────────────────────┘ │
│ and I think that the job is very ░░░░     │ ┌────────────────────────┐ │
│ (đang nghe…)                              │ │ ✓ Câu đúng! Gợi ý: …   │ │
│                                           │ └────────────────────────┘ │
├───────────────────────────────────────────┴────────────────────────────┤
│ ⏱ 04:32   Số câu: 18   Lỗi: 6   Từ/phút: 96   [Lưu từ vựng] [Xuất PDF] │
└────────────────────────────────────────────────────────────────────────┘
```

Màn hình khác: **Lịch sử buổi học**, **Tổng kết buổi**, **Sổ từ vựng**, **Cài đặt**, **Trang điện thoại** (nút micro to, đơn giản).
Hỗ trợ **chế độ trình chiếu** (chữ lớn) cho lớp học.

---

## 9. Lưu trữ dữ liệu (SQLite)

```sql
sessions   (id, started_at, ended_at, topic, level, audio_path, summary_json)
utterances (id, session_id, start_ms, end_ms, text, corrected, is_correct, feedback_json)
errors     (id, utterance_id, type, span, fix, explain_vi)
vocab      (id, word, meaning_vi, example, source_utterance_id, added_at,
            next_review_at, ease, interval_days)
settings   (key, value)
```
- File ghi âm buổi học: lưu tùy chọn (mặc định **không lưu** để bảo vệ riêng tư).
- Thống kê "lỗi hay gặp" = `GROUP BY type` trên bảng `errors`.

---

## 10. Cấu trúc thư mục mã nguồn dự kiến

```
appluyennoi/
├─ docs/                      # tài liệu (file này)
├─ apps/
│  ├─ desktop/                # Electron
│  │  ├─ src/main/            # tiến trình chính: cửa sổ, SQLite, khóa API, server LAN
│  │  │  ├─ stt/              # SttProvider + các nhà cung cấp
│  │  │  ├─ feedback/         # gọi Claude, prompt, schema JSON
│  │  │  ├─ phone-bridge/     # HTTPS + WebSocket cho điện thoại, QR, ghép đôi
│  │  │  └─ db/               # migrations, truy vấn
│  │  ├─ src/preload/         # cầu nối an toàn main ↔ renderer
│  │  └─ src/renderer/        # React UI: Phụ đề, Thẻ góp ý, Lịch sử, Cài đặt
│  │     └─ audio/            # AudioWorklet, VAD, đo âm lượng
│  └─ phone-web/              # trang web nhỏ cho điện thoại (micro qua trình duyệt)
├─ packages/shared/           # kiểu dữ liệu dùng chung (TypeScript)
└─ tests/                     # bộ câu mẫu có lỗi + file âm thanh mẫu
```

Công cụ: Vite, electron-builder (đóng gói `.exe`/`.dmg`), better-sqlite3, Vitest, Playwright (test giao diện), ESLint/Prettier.

---

## 11. Lộ trình thực hiện (≈ 10 tuần, 1–2 lập trình viên)

| Tuần | Công việc | Kết quả bàn giao |
|---|---|---|
| **1** | Thử nghiệm kỹ thuật: thu âm trong Electron, thử 2–3 dịch vụ STT với 20 đoạn ghi âm học viên thật (đo độ chính xác, độ trễ, có tự sửa ngữ pháp không). Thử prompt sửa lỗi trên 100 câu mẫu | Báo cáo chọn nhà cung cấp STT; prompt v1 |
| **2** | Khung dự án Electron + React, chọn micro, thanh âm lượng, Cài đặt + lưu khóa API an toàn | App chạy được, nghe được micro |
| **3** | Tích hợp STT streaming, hiển thị phụ đề interim/final, tự kết nối lại khi rớt mạng | **Phụ đề thời gian thực** hoạt động |
| **4** | Feedback Engine: hàng đợi, gọi Claude, kiểm tra JSON, thẻ góp ý, gạch chân lỗi trong phụ đề | **Sửa lỗi + gợi ý** hoạt động |
| **5** | SQLite: lưu buổi học, lịch sử, xuất TXT/PDF; mở file âm thanh có sẵn (ffmpeg) | **MVP hoàn chỉnh** |
| **6** | Thử nghiệm với 5–10 học viên thật, sửa lỗi, tinh chỉnh prompt, đóng gói bản cài Windows/macOS | **Bản MVP 1.0** |
| **7–8** | Điện thoại làm micro qua Wi‑Fi (HTTPS tự ký, QR, mã ghép đôi, trang phone-web) | Dùng iPhone/Android làm micro không cần cài app |
| **9** | Tổng kết buổi học, sổ từ vựng + flashcard, thống kê từ/phút | Bản 1.1 |
| **10** | Chế độ trình chiếu cho lớp học, chủ đề luyện nói, hoàn thiện & tài liệu hướng dẫn | **Bản 1.2** |

Sau đó (giai đoạn 3): chấm phát âm, hội thoại với AI, chế độ offline.

---

## 12. Kiểm thử & tiêu chí chất lượng

- **Bộ dữ liệu chuẩn**: 200 câu tiếng Anh có lỗi điển hình của người Việt (thì, mạo từ, số ít/nhiều, giới từ, "I very like…", thiếu động từ to be…) kèm đáp án.
  - Mục tiêu: phát hiện đúng ≥ 85% lỗi; báo lỗi sai (câu đúng mà bị sửa) ≤ 10%.
- **Âm thanh mẫu**: 30 file ghi âm (giọng nam/nữ, nhiều trình độ, có tiếng ồn lớp học).
  - Mục tiêu: tỉ lệ lỗi nhận dạng từ (WER) ≤ 15% trong môi trường yên tĩnh.
- **Độ trễ**: đo tự động theo mục 4.2, ghi log mỗi buổi.
- **Ổn định**: chạy liên tục 60 phút không rò rỉ bộ nhớ, tự nối lại khi mất mạng.
- **Unit test**: chuẩn hóa âm thanh, ghép câu, kiểm tra JSON, truy vấn SQLite.

---

## 13. Rủi ro & cách xử lý

| Rủi ro | Cách xử lý |
|---|---|
| STT tự sửa ngữ pháp → che mất lỗi của học viên | Tắt smart format/auto‑correct; chọn nhà cung cấp "nghe đúng nguyên văn" ở tuần 1 |
| STT nghe sai do phát âm → app bắt lỗi oan | Gửi kèm độ tin cậy từng từ; prompt yêu cầu không bắt lỗi chắc chắn với từ tin cậy thấp; có nút "App nghe sai" |
| Độ trễ góp ý cao | Model nhanh, streaming, prompt caching, giới hạn độ dài đầu ra |
| Mất Internet | Hiện cảnh báo, chuyển sang Whisper offline (giai đoạn 3) hoặc chỉ ghi âm để phân tích sau |
| Chi phí API | Giới hạn chi tiêu, gộp câu, bỏ qua câu quá ngắn, thống kê chi phí trong Cài đặt |
| Wi‑Fi chặn kết nối điện thoại–laptop | Hướng dẫn dùng điểm phát Wi‑Fi; dự phòng tai nghe/micro USB |
| iPhone cảnh báo chứng chỉ HTTPS | Hướng dẫn từng bước có ảnh; sau này dùng mkcert |
| Riêng tư giọng nói học sinh | Mặc định không lưu âm thanh; nói rõ dữ liệu gửi tới dịch vụ nào; nút xóa toàn bộ dữ liệu |
| macOS chặn quyền micro / app chưa ký số | Khai báo quyền micro trong `Info.plist`; ký & notarize app khi phát hành rộng |

---

## 14. Việc cần chuẩn bị / quyết định trước khi code

1. Tài khoản & khóa API: 1 dịch vụ STT streaming + Claude API.
2. Thu 20–30 đoạn ghi âm thật của học viên (có sự đồng ý) để thử nghiệm tuần 1.
3. Xác nhận hệ điều hành chính của người dùng (Windows hay macOS chiếm đa số?) để ưu tiên kiểm thử.
4. Xác nhận ngân sách chi phí API hằng tháng → đặt giới hạn mặc định.
5. Danh sách chủ đề luyện nói và trình độ học viên mục tiêu.
