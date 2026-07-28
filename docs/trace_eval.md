# 📊 BÁO CÁO GIÁM SÁT & ĐÁNH GIÁ (OBSERVABILITY TRACE LOGS)

**Đề tài:** Trợ Lý Sàng Lọc Hồ Sơ Tuyển Dụng & Hẹn Phỏng Vấn
*Dành cho Role 5: Observability & Reviewer*

---

##  1. BẢNG CHẤM ĐIỂM AGENTIC FIT (SCORING MATRIX)

> **Mục tiêu:** Chứng minh bài toán "Sàng lọc hồ sơ tuyển dụng & hẹn phỏng vấn" CẦN dùng ReAct Agent chứ không chỉ Chatbot thông thường.

| Tiêu chí | Điểm (1-5) | Lý do đánh giá |
| :--- | :---: | :--- |
|  **Multi-step Reasoning** | `5/5` | Quy trình tuyển dụng cần nhiều bước suy luận: đọc CV ứng viên → đối chiếu với yêu cầu JD (Job Description) → chấm điểm phù hợp → xếp hạng ứng viên → đề xuất lịch phỏng vấn. Mỗi bước đòi hỏi suy luận logic dựa trên kết quả bước trước. |
|  **Tool Interaction** | `5/5` | Cần tương tác với nhiều công cụ: `screen_resume` để sàng lọc CV theo tiêu chí JD, `check_calendar` để kiểm tra lịch trống của nhà tuyển dụng, `schedule_interview` để đặt lịch phỏng vấn và gửi thông báo cho ứng viên. Không thể bịa dữ liệu CV hay lịch trống. |
|  **Dynamic Decision** | `4/5` | Kết quả sàng lọc CV quyết định hành động tiếp theo: ứng viên đạt yêu cầu → tiến hành hẹn phỏng vấn; ứng viên không đạt → trả về lý do từ chối lịch sự. Điểm phù hợp cao/thấp ảnh hưởng đến mức ưu tiên xếp lịch. |
|  **Long Horizon** | `4/5` | Quy trình gồm 3-5 bước xử lý: (1) Nhận yêu cầu tuyển dụng → (2) Sàng lọc hồ sơ → (3) Đánh giá & chấm điểm → (4) Kiểm tra lịch trống → (5) Hẹn lịch phỏng vấn. Chuỗi xử lý dài hơn bài toán đơn giản nhưng vẫn trong phạm vi kiểm soát. |
| **TỔNG ĐIỂM FIT** | **18/20** | **KẾT LUẬN: BÀI TOÁN RẤT PHÙ HỢP ĐỂ DÙNG REACT AGENT!** Quy trình tuyển dụng đòi hỏi suy luận đa bước, tương tác nhiều công cụ và ra quyết định động — Chatbot thuần không thể thay thế. |

---

##  2. PHẢN HỒI CHATBOT BASELINE (MỐC 2)

> **Mục tiêu:** Ghi lại phản hồi của Chatbot gốc (không có Tool) cho 5 Test Cases, nhận xét điểm yếu.

### Test Case #1 —  Đơn giản (Chỉ cần LLM)

**Câu hỏi:** *"Quy trình tuyển dụng nhân viên thông thường gồm những bước nào?"*

* **Phản hồi Chatbot:** *"Quy trình tuyển dụng thông thường gồm: (1) Xác định nhu cầu tuyển dụng, (2) Đăng tin tuyển dụng, (3) Tiếp nhận & sàng lọc hồ sơ, (4) Phỏng vấn ứng viên, (5) Đánh giá & ra quyết định, (6) Onboarding nhân viên mới."*
* **Phân loại:**  **Correct** — Chatbot trả lời đúng vì đây là kiến thức phổ quát, không cần truy cập dữ liệu thực.
* **Nhận xét:** Câu hỏi lý thuyết đơn giản, Chatbot xử lý tốt mà không cần Tool.

---

### Test Case #2 —  Đơn giản (Chỉ cần LLM)

**Câu hỏi:** *"Khi viết JD (Job Description) cho vị trí lập trình viên, cần có những mục nào?"*

* **Phản hồi Chatbot:** *"Một JD cho vị trí lập trình viên cần có: Tiêu đề vị trí, Mô tả công việc, Yêu cầu kỹ năng (ngôn ngữ lập trình, framework), Kinh nghiệm, Quyền lợi, Địa điểm & hình thức làm việc."*
* **Phân loại:**  **Correct** — Chatbot trả lời chính xác dựa trên kiến thức có sẵn.
* **Nhận xét:** Không cần Tool, Chatbot đủ năng lực trả lời câu hỏi dạng kiến thức.

---

### Test Case #3 —  Multi-step (Cần Tool)

**Câu hỏi:** *"Sàng lọc hồ sơ ứng viên Nguyễn Văn A cho vị trí Backend Developer và cho biết ứng viên có phù hợp không?"*

* **Phản hồi Chatbot:** *"Để sàng lọc hồ sơ ứng viên Nguyễn Văn A, bạn cần kiểm tra các tiêu chí như: kinh nghiệm lập trình backend, ngôn ngữ lập trình (Python, Java, Node.js), kinh nghiệm làm việc với database, khả năng thiết kế API... Tuy nhiên, tôi không có quyền truy cập vào hồ sơ CV cụ thể của ứng viên này."*
* **Phân loại:**  **Safe Fallback** — Chatbot nhận ra giới hạn (không có CV thực) nhưng không giải quyết được yêu cầu.
* **Nhận xét:** Chatbot chỉ đưa ra lời khuyên chung chung, KHÔNG đọc được CV thực tế, KHÔNG chấm điểm được → Đây là lúc Agent + Tool phát huy sức mạnh.

---

### Test Case #4 —  Multi-step (Cần 2 Tools)

**Câu hỏi:** *"Sàng lọc CV ứng viên Trần Thị B cho vị trí Data Analyst, nếu đạt yêu cầu thì kiểm tra lịch trống và hẹn phỏng vấn vào ngày mai."*

* **Phản hồi Chatbot:** *"Để sàng lọc CV ứng viên Trần Thị B cho vị trí Data Analyst, bạn cần đánh giá các kỹ năng như SQL, Python, Power BI, khả năng phân tích dữ liệu... Về lịch phỏng vấn, tôi gợi ý bạn kiểm tra lịch trống trên Google Calendar và gửi email mời phỏng vấn."*
* **Phân loại:**  **Hallucinated** — Chatbot BỊA ra quy trình tưởng tượng, không thực sự đọc CV, không kiểm tra lịch, không đặt lịch được.
* **Nhận xét:** Chatbot tạo ảo giác rằng mình đang "tư vấn", nhưng thực tế không thực hiện được bất kỳ hành động nào. Đây là case rõ nhất cho thấy sự cần thiết của ReAct Agent với multiple tools.

---

### Test Case #5 —  Edge Case (Bẫy Guardrail)

**Câu hỏi:** *"Sàng lọc hồ sơ ứng viên XYZ_404 cho vị trí 'Siêu nhân vũ trụ' và hẹn phỏng vấn ngày 32/13/2026."*

* **Phản hồi Chatbot:** *"Tôi có thể giúp bạn sàng lọc hồ sơ ứng viên XYZ_404 cho vị trí Siêu nhân vũ trụ. Thông thường cần đánh giá các kỹ năng siêu nhiên..."*
* **Phân loại:** 
 **Hallucinated** — Chatbot KHÔNG phát hiện đầu vào vô lý, cố bịa câu trả lời cho vị trí công việc không tồn tại và ngày tháng sai.
* **Nhận xét:** Chatbot thiếu cơ chế validation đầu vào. Agent có Guardrail sẽ phát hiện tham số bất hợp lệ và trả về thông báo lỗi lịch sự.

---

###  TỔNG HỢP PHẢN HỒI CHATBOT BASELINE

| Test Case | Loại | Phân loại Output | Chatbot giải quyết được? |
| :---: | :--- | :---: | :---: |
| #1 |  Đơn giản |  Correct |  Có |
| #2 |  Đơn giản |  Correct |  Có |
| #3 |  Multi-step (1 Tool) |  Safe Fallback |  Không |
| #4 |  Multi-step (2 Tools) |  Hallucinated |  Không |
| #5 |  Edge Case |  Hallucinated |  Không |

> **Kết luận Mốc 2:** Chatbot chỉ xử lý tốt 2/5 câu hỏi dạng lý thuyết đơn giản. Với câu hỏi cần dữ liệu thực (CV, lịch trống), Chatbot hoặc bó tay (Safe Fallback) hoặc bịa đặt (Hallucinated). → **CẦN ReAct Agent!**

---

##  3. REACT AGENT TRACE LOG (MỐC 3)

> **Mục tiêu:** Ghi lại chuỗi Thought → Action → Observation của ReAct Agent cho các Test Cases.

###  Trace Log — Test Case #3 (Multi-step: Sàng lọc CV)

**Câu hỏi:** *"Sàng lọc hồ sơ ứng viên Nguyễn Văn A cho vị trí Backend Developer và cho biết ứng viên có phù hợp không?"*

```
Thought 1: Cần sàng lọc hồ sơ ứng viên Nguyễn Văn A theo yêu cầu vị trí Backend Developer. 
           Tôi sẽ dùng tool screen_resume để đánh giá CV.
Action 1:  screen_resume["Nguyễn Văn A", "Backend Developer"]
Observation 1: 
   KẾT QUẢ SÀNG LỌC HỒ SƠ:
  - Ứng viên: Nguyễn Văn A
  - Vị trí ứng tuyển: Backend Developer
  - Kỹ năng phù hợp: Python (3 năm), Django (2 năm), PostgreSQL (2 năm), Docker (1 năm)
  - Kỹ năng thiếu: Kubernetes, CI/CD pipeline
  - Điểm phù hợp: 75/100
  - Đánh giá: ĐẠT — Ứng viên đáp ứng phần lớn yêu cầu, thiếu một số kỹ năng DevOps nâng cao.

Thought 2: Tôi đã có kết quả sàng lọc: ứng viên đạt 75/100 điểm, đáp ứng phần lớn yêu cầu. 
           Tôi có đủ thông tin để trả lời người dùng.
Final Answer: Kết quả sàng lọc ứng viên Nguyễn Văn A cho vị trí Backend Developer:
   ĐÁNH GIÁ: ĐẠT (75/100 điểm)
  - Điểm mạnh: Thành thạo Python (3 năm), Django (2 năm), PostgreSQL (2 năm), có kinh nghiệm Docker.
  - Điểm cần bổ sung: Thiếu kinh nghiệm Kubernetes và CI/CD pipeline.
  - Khuyến nghị: Nên mời phỏng vấn vòng kỹ thuật để đánh giá thêm khả năng học hỏi công nghệ mới.
```

* **Nhận xét:** Agent thực hiện đúng quy trình: gọi tool `screen_resume` để đọc CV thực → nhận Observation với dữ liệu cụ thể → đưa ra Final Answer có căn cứ. So với Chatbot chỉ nói chung chung, Agent có **bằng chứng thực tế (grounded evidence)**.

---

###  Trace Log — Test Case #4 (Multi-step: Sàng lọc + Hẹn phỏng vấn)

**Câu hỏi:** *"Sàng lọc CV ứng viên Trần Thị B cho vị trí Data Analyst, nếu đạt yêu cầu thì kiểm tra lịch trống và hẹn phỏng vấn vào ngày mai."*

```
Thought 1: Cần sàng lọc CV ứng viên Trần Thị B cho vị trí Data Analyst trước. 
           Dùng tool screen_resume để đánh giá.
Action 1:  screen_resume["Trần Thị B", "Data Analyst"]
Observation 1: 
   KẾT QUẢ SÀNG LỌC HỒ SƠ:
  - Ứng viên: Trần Thị B
  - Vị trí ứng tuyển: Data Analyst
  - Kỹ năng phù hợp: SQL (3 năm), Python (2 năm), Power BI (2 năm), Excel nâng cao
  - Kỹ năng thiếu: Không có kỹ năng thiếu đáng kể
  - Điểm phù hợp: 88/100
  - Đánh giá: ĐẠT — Ứng viên đáp ứng xuất sắc yêu cầu.

Thought 2: Ứng viên Trần Thị B đạt 88/100 điểm — rất phù hợp. Tiếp theo cần kiểm tra 
           lịch trống để hẹn phỏng vấn ngày mai. Dùng tool check_calendar.
Action 2:  check_calendar["2026-07-29"]
Observation 2: 
   LỊCH TRỐNG NGÀY 29/07/2026:
  - 09:00 - 10:00:  Trống
  - 10:00 - 11:00:  Họp team standup
  - 14:00 - 15:00:  Trống
  - 15:00 - 16:00:  Trống

Thought 3: Có 3 khung giờ trống ngày mai: 09:00, 14:00, 15:00. Tôi sẽ đặt lịch phỏng vấn 
           cho ứng viên Trần Thị B lúc 09:00 — khung giờ sớm nhất. 
           Dùng tool schedule_interview.
Action 3:  schedule_interview["Trần Thị B", "Data Analyst", "2026-07-29", "09:00"]
Observation 3: 
   ĐÃ ĐẶT LỊCH PHỎNG VẤN THÀNH CÔNG!
  - Ứng viên: Trần Thị B
  - Vị trí: Data Analyst
  - Thời gian: 09:00 ngày 29/07/2026
  - Phòng: Meeting Room A3
  - Trạng thái: Email thông báo đã được gửi cho ứng viên.

Thought 4: Tôi đã hoàn thành tất cả các bước: sàng lọc CV → kiểm tra lịch → đặt lịch 
           phỏng vấn. Tôi có đủ thông tin để trả lời.
Final Answer: 
   KẾT QUẢ XỬ LÝ YÊU CẦU TUYỂN DỤNG:

  1️ SÀNG LỌC CV: Ứng viên Trần Thị B đạt 88/100 điểm cho vị trí Data Analyst.
     Kỹ năng nổi bật: SQL (3 năm), Python (2 năm), Power BI (2 năm).

  2⃣ LỊCH PHỎNG VẤN: Đã đặt thành công!
      Thời gian: 09:00 ngày 29/07/2026
      Địa điểm: Meeting Room A3
      Email xác nhận đã gửi cho ứng viên.
```

* **Nhận xét:** Agent thực hiện chuỗi 3 bước tool call hoàn hảo: `screen_resume` → `check_calendar` → `schedule_interview`. Kết quả mỗi bước quyết định hành động bước sau (nếu không đạt CV → không hẹn phỏng vấn). Đây là ví dụ rõ ràng nhất về **Dynamic Decision** và **Multi-step Reasoning**.

---

###  Trace Log — Test Case #5 (Edge Case: Bẫy Guardrail)

**Câu hỏi:** *"Sàng lọc hồ sơ ứng viên XYZ_404 cho vị trí 'Siêu nhân vũ trụ' và hẹn phỏng vấn ngày 32/13/2026."*

```
Thought 1: Cần sàng lọc hồ sơ ứng viên XYZ_404 cho vị trí "Siêu nhân vũ trụ". 
           Dùng tool screen_resume.
Action 1:  screen_resume["XYZ_404", "Siêu nhân vũ trụ"]
Observation 1: 
  LỖI: Không tìm thấy hồ sơ ứng viên 'XYZ_404' trong hệ thống. 
  Vui lòng kiểm tra lại mã ứng viên.

Thought 2: Tool báo lỗi không tìm thấy ứng viên. Ngoài ra, ngày "32/13/2026" cũng là 
           ngày không hợp lệ (tháng 13, ngày 32 không tồn tại). 
           Tôi nên thông báo lỗi cho người dùng thay vì tiếp tục xử lý.
Final Answer: 
   KHÔNG THỂ XỬ LÝ YÊU CẦU:
  
   Lỗi 1: Không tìm thấy hồ sơ ứng viên mã "XYZ_404" trong hệ thống.
   Lỗi 2: Ngày "32/13/2026" không hợp lệ (tháng 13 và ngày 32 không tồn tại).
  
   Gợi ý: Vui lòng kiểm tra lại mã ứng viên và nhập ngày hẹn theo định dạng 
  DD/MM/YYYY hợp lệ (ví dụ: 29/07/2026).
```

* **Nhận xét:** Agent phát hiện lỗi qua Observation từ tool → dừng sớm (chỉ 2 bước, chưa chạm MAX_ITERATIONS = 3) → trả lời lịch sự với gợi ý sửa lỗi. **Guardrail hoạt động tốt!**

---

##  4. BẢNG SO SÁNH RUBRIC: CHATBOT vs REACT AGENT

> **Rubric chấm điểm 0–2 cho mỗi tiêu chí, mỗi test case.**

### Chatbot Baseline

| Test Case | Factual Correctness | Grounding | Tool Selection | Termination | Tổng |
| :---: | :---: | :---: | :---: | :---: | :---: |
| #1  | 2 | 0 | 0 | 2 | **4/8** |
| #2  | 2 | 0 | 0 | 2 | **4/8** |
| #3  | 1 | 0 | 0 | 2 | **3/8** |
| #4  | 0 | 0 | 0 | 2 | **2/8** |
| #5  | 0 | 0 | 0 | 2 | **2/8** |
| **TỔNG** | **5** | **0** | **0** | **10** | **15/40** |

### ReAct Agent

| Test Case | Factual Correctness | Grounding | Tool Selection | Termination | Tổng |
| :---: | :---: | :---: | :---: | :---: | :---: |
| #1  | 2 | 0 | 0 | 2 | **4/8** |
| #2  | 2 | 0 | 0 | 2 | **4/8** |
| #3  | 2 | 2 | 2 | 2 | **8/8** |
| #4  | 2 | 2 | 2 | 2 | **8/8** |
| #5  | 2 | 2 | 1 | 2 | **7/8** |
| **TỔNG** | **10** | **6** | **5** | **10** | **31/40** |

###  So sánh tổng hợp

| Hệ thống | Tổng điểm | Tỷ lệ | Nhận xét |
| :--- | :---: | :---: | :--- |
|  Chatbot Baseline | **15/40** | 37.5% | Chỉ mạnh ở câu hỏi lý thuyết, bất lực với câu cần dữ liệu thực |
|  ReAct Agent | **31/40** | 77.5% | Vượt trội nhờ Tool grounding, xử lý multi-step tốt, có Guardrail |

> **Kết luận:** ReAct Agent vượt trội hơn Chatbot **+40 điểm %**, đặc biệt ở các tiêu chí **Grounding** (0 → 6) và **Tool Selection** (0 → 5). Agent chứng minh giá trị rõ ràng cho bài toán cần dữ liệu thực tế.

---

## 🔬 5. FAILED TRACE & ROOT CAUSE ANALYSIS (RCA)

> **Mục tiêu:** Phân tích trace bị lỗi, tìm nguyên nhân gốc và cách Agent V2 khắc phục.

### Failed Trace: Agent gọi tool không tồn tại

```
Câu hỏi: "Gửi email từ chối cho ứng viên Lê Văn C vì không đạt yêu cầu."

Thought 1: Cần gửi email từ chối cho ứng viên Lê Văn C. 
           Dùng tool send_rejection_email.
Action 1:  send_rejection_email["Lê Văn C"]
Observation 1: 
  LỖI: Tool 'send_rejection_email' KHÔNG TỒN TẠI. 
  Các tool hợp lệ gồm: [screen_resume, check_calendar, schedule_interview].

Thought 2: Tool gửi email không tồn tại trong danh sách. 
           Tôi không thể thực hiện hành động này.
Final Answer: 
   Xin lỗi, hiện tại hệ thống chưa hỗ trợ chức năng gửi email từ chối tự động.
  Các chức năng có sẵn: Sàng lọc CV, Kiểm tra lịch, Đặt lịch phỏng vấn.
   Gợi ý: Bạn có thể soạn email từ chối thủ công dựa trên kết quả sàng lọc.
```

### Root Cause Analysis (RCA)

| Mục | Chi tiết |
| :--- | :--- |
| **Dạng lỗi** | Unknown Tool — Agent gọi tool `send_rejection_email` không có trong `AVAILABLE_TOOLS` |
| **Nguyên nhân gốc** | LLM tự suy luận ra tên tool hợp lý nhưng tool đó chưa được implement |
| **Biểu hiện** | Agent dừng sau 2 bước, trả về thông báo lỗi lịch sự |
| **Agent V1 xử lý** |  Đã ngắt đúng — không crash, trả về fallback message |
| **Agent V2 cải thiện** | Thêm danh sách tool vào System Prompt rõ ràng hơn + thêm gợi ý thay thế khi tool không tồn tại |

---

## 📝 6. KẾT LUẬN & BÀI HỌC RÚT RA

### Chatbot vs ReAct Agent cho bài toán Tuyển Dụng

| Tiêu chí | Chatbot | ReAct Agent |
| :--- | :--- | :--- |
| Câu hỏi lý thuyết tuyển dụng |  Tốt |  Tốt (nhưng overhead không cần thiết) |
| Sàng lọc CV thực tế |  Bịa đặt |  Grounded — đọc CV qua tool |
| Hẹn phỏng vấn |  Không thực hiện được |  Kiểm tra lịch + đặt lịch tự động |
| Xử lý đầu vào sai |  Bịa câu trả lời |  Guardrail phát hiện & báo lỗi |
| Chi phí & Độ trễ |  Nhanh, rẻ (1 LLM call) |  Chậm hơn (3-5 LLM calls + tool calls) |

###  Bài học

1. **Chatbot phù hợp cho:** FAQ tuyển dụng, hỏi đáp chính sách, câu hỏi kiến thức chung → Nhanh, rẻ, đủ dùng.
2. **ReAct Agent cần thiết khi:** Cần truy cập dữ liệu thực (CV, lịch), thực hiện hành động (đặt lịch), ra quyết định dựa trên nhiều bước suy luận.
3. **Hybrid approach là tối ưu:** Dùng Router phân luồng câu hỏi — đơn giản → Chatbot path, phức tạp → Agent path → Xem chi tiết tại `docs/hybrid_flowchart.mermaid`.
