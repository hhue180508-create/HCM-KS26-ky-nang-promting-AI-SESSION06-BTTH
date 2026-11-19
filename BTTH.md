# **BÁO CÁO: THIẾT KẾ GIẢI PHÁP PROMPT CHAINING CHO ỨNG DỤNG AI TRONG HỌC TẬP & NGHIÊN CỨU**

## **1\. Mục tiêu và thiết kế giải pháp**

### **1.1. Mục tiêu**

Xây dựng hệ thống AI kết hợp hai chức năng:

* **Feynman Tutor:** Giúp sinh viên hiểu kiến thức bằng cách giải thích đơn giản, dễ hiểu.  
* **Rubric Grading:** Đánh giá câu trả lời của sinh viên theo tiêu chí và thang điểm có sẵn.

Sử dụng kỹ thuật Prompt Chaining để chia công việc thành nhiều bước, trong đó kết quả của bước trước được chuyển làm dữ liệu đầu vào cho bước tiếp theo.

### **1.2. Quy trình Prompt Chaining**

Hệ thống gồm 4 bước:

**Bước 1: Kiểm tra dữ liệu đầu vào**

* Kiểm tra chủ đề, tài liệu, câu trả lời và Rubric.  
* Phát hiện dữ liệu trống, sai định dạng hoặc không hợp lệ.  
* Nếu thiếu dữ liệu quan trọng, yêu cầu bổ sung và không chuyển sang bước chấm điểm.

**Bước 2: Giải thích theo phương pháp Feynman**

* Giải thích kiến thức bằng ngôn ngữ đơn giản.  
* Sử dụng ví dụ thực tế.  
* Chỉ ra những nội dung quan trọng sinh viên cần hiểu.

**Bước 3: Chấm điểm theo Rubric**

* Đối chiếu câu trả lời với tài liệu và từng tiêu chí.  
* Chấm điểm riêng từng tiêu chí.  
* Giải thích lý do cho điểm, không tự thêm tiêu chí.

**Bước 4: Tổng hợp kết quả**

* Kết hợp phần giải thích và đánh giá.  
* Đưa ra nhận xét, hướng cải thiện và câu hỏi kiểm tra.  
* Xuất kết quả theo cấu trúc thống nhất.

### **Sơ đồ hoạt động**

```
INPUT
  |
  v
PROMPT 1: KIỂM TRA DỮ LIỆU
  |
  v
PROMPT 2: FEYNMAN TUTOR
  |
  v
PROMPT 3: RUBRIC GRADING
  |
  v
PROMPT 4: TỔNG HỢP KẾT QUẢ
  |
  v
OUTPUT HOÀN CHỈNH
```

---

## **2\. Prompt hệ thống và các Prompt thực thi**

### **2.1. Prompt hệ thống**

**Vai trò:** Bạn là hệ thống AI hỗ trợ học tập và đánh giá kiến thức sinh viên.

**Nguyên tắc hoạt động:**

1. Thực hiện đúng thứ tự các bước trong quy trình.  
2. Chỉ sử dụng dữ liệu được cung cấp.  
3. Không tự tạo thông tin hoặc tiêu chí chấm điểm.  
4. Kiểm tra dữ liệu trước khi giải thích và đánh giá.  
5. Giữ nguyên dữ liệu gốc khi chuyển giữa các bước.  
6. Sử dụng định dạng JSON thống nhất để bàn giao dữ liệu.  
7. Nếu phát hiện lỗi, trả về thông báo rõ ràng thay vì tiếp tục xử lý dữ liệu không hợp lệ.

### **2.2. Prompt 1: Kiểm tra dữ liệu đầu vào**

**Prompt:**

Bạn là bộ phận kiểm tra dữ liệu đầu vào.

Hãy kiểm tra các thông tin sau:

* Chủ đề: {{CHU\_DE}}  
* Tài liệu: {{TAI\_LIEU}}  
* Câu trả lời: {{CAU\_TRA\_LOI}}  
* Rubric: {{RUBRIC}}  
* Tổng điểm: {{TONG\_DIEM}}

**Yêu cầu:**

* Kiểm tra trường dữ liệu bị trống hoặc sai định dạng.  
* Kiểm tra tổng điểm có khớp với tổng điểm tối đa của các tiêu chí không.  
* Nếu thiếu dữ liệu quan trọng, trả về trạng thái `NEED_INPUT`.  
* Nếu dữ liệu không hợp lệ, trả về `INVALID`.  
* Nếu dữ liệu hợp lệ, trả về `VALID` và chuyển dữ liệu sang bước tiếp theo.  
* Không tự suy đoán hoặc điền thông tin còn thiếu.

**Output:**

```
{
  "status": "VALID",
  "message": "Dữ liệu hợp lệ",
  "next_step": "FEYNMAN_TUTOR"
}
```

### **2.3. Prompt 2: Gia sư Feynman**

**Prompt:**

Bạn là AI Tutor sử dụng phương pháp Feynman.

Dựa trên dữ liệu đã được kiểm tra ở bước 1:

* Chủ đề: {{CHU\_DE}}  
* Tài liệu: {{TAI\_LIEU}}  
* Câu trả lời: {{CAU\_TRA\_LOI}}

**Yêu cầu:**

1. Giải thích chủ đề bằng ngôn ngữ đơn giản.  
2. Sử dụng ví dụ thực tế để minh họa.  
3. Chỉ ra nội dung sinh viên đã hiểu đúng.  
4. Xác định nội dung còn thiếu hoặc chưa chính xác.  
5. Không tự tạo thông tin ngoài tài liệu.

**Output:**

```
{
  "concept": "Giải thích khái niệm",
  "example": "Ví dụ minh họa",
  "correct_points": ["Nội dung đã hiểu đúng"],
  "missing_points": ["Nội dung cần bổ sung"]
}
```

### **2.4. Prompt 3: Chấm điểm Rubric**

**Prompt:**

Bạn là AI chuyên đánh giá bài học theo Rubric.

Sử dụng dữ liệu đã được kiểm tra và kết quả từ Prompt 2\.

* Câu trả lời: {{CAU\_TRA\_LOI}}  
* Rubric: {{RUBRIC}}  
* Tổng điểm tối đa: {{TONG\_DIEM}}

**Yêu cầu:**

1. Đánh giá từng tiêu chí độc lập.  
2. Chỉ cho điểm dựa trên nội dung thực tế của câu trả lời.  
3. Giải thích lý do cho từng mức điểm.  
4. Không tự thêm tiêu chí hoặc thay đổi thang điểm.  
5. Kiểm tra tổng điểm không vượt quá điểm tối đa.  
6. Nếu Rubric không hợp lệ, dừng chấm và báo lỗi.

**Output:**

```
{
  "status": "SUCCESS",
  "scores": [
    {
      "criterion": "Tên tiêu chí",
      "max_score": 4,
      "score": 3,
      "reason": "Lý do cho điểm"
    }
  ],
  "total_score": 3,
  "max_total_score": 4
}
```

### **2.5. Prompt 4: Tổng hợp kết quả**

**Prompt:**

Bạn là bộ phận tổng hợp kết quả học tập.

Hãy kết hợp kết quả Feynman Tutor từ Prompt 2 và Rubric Grading từ Prompt 3\.

**Yêu cầu:**

* Trình bày phần giải thích kiến thức.  
* Hiển thị bảng điểm theo từng tiêu chí.  
* Tổng hợp ưu điểm và hạn chế của câu trả lời.  
* Đưa ra hướng cải thiện.  
* Đặt một câu hỏi để sinh viên tự kiểm tra kiến thức.  
* Kiểm tra tổng điểm trước khi xuất kết quả.  
* Nếu một bước trước đó báo lỗi, không tự tạo kết quả thay thế.

**Output:** Trình bày bằng Markdown với các phần:

1. Giải thích kiến thức.  
2. Bảng đánh giá Rubric.  
3. Tổng điểm.  
4. Nhận xét.  
5. Gợi ý cải thiện.  
6. Câu hỏi kiểm tra kiến thức.

---

## **3\. Cơ chế xử lý lỗi dữ liệu**

| Trường hợp | Cách xử lý |
| ----- | ----- |
| Thiếu chủ đề | Yêu cầu nhập chủ đề |
| Thiếu câu trả lời | Yêu cầu sinh viên bổ sung |
| Thiếu tài liệu | Thông báo giới hạn khả năng đối chiếu |
| Thiếu Rubric | Không tự đặt tiêu chí chấm điểm |
| Sai định dạng dữ liệu | Yêu cầu gửi lại dữ liệu |
| Tổng điểm không khớp | Báo lỗi và yêu cầu kiểm tra |
| AI không tạo được kết quả JSON hợp lệ | Yêu cầu tạo lại theo cấu trúc quy định |
| Một bước trước bị lỗi | Dừng chuỗi và thông báo lỗi, không chấm điểm |

**Nguyên tắc:** Chỉ chuyển sang bước tiếp theo khi bước hiện tại trả về dữ liệu hợp lệ. Điều này giúp hệ thống không bị lỗi dây chuyền.

---

## **4\. Kết quả chạy thử**

### **4.1. Dữ liệu đầu vào**

* Chủ đề: Protocol trong mạng máy tính.  
* Tài liệu: Protocol là tập hợp các quy tắc giúp các thiết bị giao tiếp và trao đổi dữ liệu.  
* Câu trả lời sinh viên: "Protocol là quy tắc giúp máy tính trao đổi thông tin với nhau. Ví dụ HTTP giúp trình duyệt giao tiếp với máy chủ."  
* Rubric:  
  * Độ chính xác: 4 điểm.  
  * Giải thích dễ hiểu: 3 điểm.  
  * Ví dụ thực tế: 3 điểm.  
* Tổng điểm: 10 điểm.

### **4.2. Kết quả từng bước**

**Bước 1: Kiểm tra dữ liệu**

```
{
  "status": "VALID",
  "message": "Dữ liệu đầu vào đầy đủ và hợp lệ",
  "next_step": "FEYNMAN_TUTOR"
}
```

**Bước 2: Feynman Tutor**

```
{
  "concept": "Protocol là bộ quy tắc giúp các thiết bị máy tính giao tiếp và trao đổi dữ liệu với nhau.",
  "example": "HTTP giúp trình duyệt gửi yêu cầu và nhận dữ liệu từ máy chủ khi truy cập website.",
  "correct_points": [
    "Hiểu Protocol là quy tắc giao tiếp",
    "Biết HTTP là một ví dụ về giao thức"
  ],
  "missing_points": [
    "Chưa giải thích rõ vai trò của HTTP trong quá trình trao đổi dữ liệu"
  ]
}
```

**Bước 3: Rubric Grading**

```
{
  "status": "SUCCESS",
  "scores": [
    {
      "criterion": "Độ chính xác",
      "max_score": 4,
      "score": 4,
      "reason": "Giải thích đúng khái niệm Protocol."
    },
    {
      "criterion": "Giải thích dễ hiểu",
      "max_score": 3,
      "score": 3,
      "reason": "Câu trả lời ngắn gọn và dễ hiểu."
    },
    {
      "criterion": "Ví dụ thực tế",
      "max_score": 3,
      "score": 2,
      "reason": "Có ví dụ HTTP nhưng chưa giải thích đầy đủ vai trò."
    }
  ],
  "total_score": 9,
  "max_total_score": 10
}
```

**Bước 4: Kết quả tổng hợp**

# **KẾT QUẢ ĐÁNH GIÁ HỌC TẬP**

**1\. Giải thích kiến thức**

Protocol là bộ quy tắc giúp các thiết bị máy tính hiểu và trao đổi thông tin với nhau. Ví dụ, HTTP hỗ trợ trình duyệt gửi yêu cầu và nhận dữ liệu từ máy chủ khi truy cập website.

**2\. Bảng đánh giá**

| Tiêu chí | Điểm tối đa | Điểm đạt |
| ----- | ----- | ----- |
| Độ chính xác | 4 | 4 |
| Giải thích dễ hiểu | 3 | 3 |
| Ví dụ thực tế | 3 | 2 |
| **Tổng điểm** | **10** | **9** |

**3\. Nhận xét**

* Ưu điểm: Hiểu đúng khái niệm, diễn đạt dễ hiểu.  
* Hạn chế: Cần giải thích rõ hơn vai trò của HTTP.

**4\. Gợi ý cải thiện**

Bổ sung cách HTTP giúp trình duyệt gửi yêu cầu và nhận dữ liệu từ máy chủ.

**5\. Câu hỏi kiểm tra**

HTTP có vai trò gì khi bạn truy cập một trang web?

---

## **5\. Kết luận**

Giải pháp Prompt Chaining chia quy trình học tập thành bốn bước liên kết: kiểm tra dữ liệu, giải thích theo Feynman, chấm điểm theo Rubric và tổng hợp kết quả.

Cách làm này giúp dễ kiểm soát từng công đoạn, hạn chế lỗi dữ liệu, tạo đầu ra thống nhất và thuận tiện khi nâng cấp hệ thống. Đồng thời, AI chỉ hỗ trợ giải thích và đề xuất điểm, giảng viên vẫn cần kiểm tra kết quả trước khi xác nhận điểm chính thức.

