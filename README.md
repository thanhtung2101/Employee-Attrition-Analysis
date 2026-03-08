# Employee-Attrition-Analysis
# Báo cáo Phân tích Dữ liệu Nhân sự (HR Analytics) với Power BI

## I. Giới thiệu 

### 1. Giới thiệu về Bộ dữ liệu 
Dự án này sử dụng bộ dữ liệu Nhân sự (HR Dataset) mô phỏng thông tin chi tiết của lực lượng lao động trong một doanh nghiệp. Bộ dữ liệu chứa đựng các thông tin quan trọng từ khi nhân viên được tuyển dụng cho đến khi họ rời đi, bao gồm: thông tin nhân khẩu học (tuổi, giới tính), thông tin công việc (vị trí, phòng ban, mức lương), kênh tuyển dụng, điểm đánh giá hiệu suất, mức độ gắn kết và lý do nghỉ việc.

Mục tiêu của dự án là xây dựng một hệ thống Dashboard trực quan trên Power BI giúp bộ phận Nhân sự (HR) và Ban Giám đốc dễ dàng theo dõi tình hình nhân sự của tổ chức, từ đó đưa ra các quyết định chiến lược về giữ chân nhân tài và tối ưu hóa chi phí.

### 2. Giải thích về bộ dữ liệu (Data Dictionary)
Dữ liệu được tổ chức thành mô hình quan hệ (Relational Model) với bảng trung tâm là `Employee` và các bảng danh mục (Dimension tables) bổ trợ. Các trường dữ liệu chính bao gồm:

* **Bảng Fact - Employee (Dữ liệu Nhân viên):**
    * `Employee`: Tên nhân viên.
    * `DateofHire` / `DateofTermination`: Ngày bắt đầu làm việc và ngày nghỉ việc.
    * `EmploymentStatus`: Trạng thái làm việc (Active, Voluntarily Terminated,...).
    * `TerminationReason`: Lý do nghỉ việc.
    * `Salary`: Mức lương hàng năm.
    * `EngagementScore` / `SatisfactionScore`: Điểm gắn kết và mức độ hài lòng của nhân viên.
    * `AbsenceDays`: Số ngày vắng mặt.
    * `RecruitmentSource`: Kênh tuyển dụng (Indeed, LinkedIn, Employee Referral, Google Search,...).
* **Các bảng Dimension (Danh mục):**
    * **Department:** Quản lý thông tin 6 phòng ban chính (`Admin Offices`, `Executive Office`, `IT/IS`, `Software Engineering`, `Production`, `Sales`).
    * **Position:** Danh sách các chức danh công việc và ID phòng ban tương ứng.
    * **Manager:** Thông tin người quản lý trực tiếp.
    * **Performance:** Phân loại điểm đánh giá hiệu suất (`Not To Keep`, `Needs Improvement`, `Fully Meets`, `Exceeds`).
    * **MaritalStatus:** Tình trạng hôn nhân.

### 3. Câu hỏi Kinh doanh (Business Questions)
Dự án được thiết kế để giải quyết các bài toán trọng tâm sau:
1.  **Tổng quan Nhân sự:** Quy mô nhân sự hiện tại là bao nhiêu? Chi phí lương trung bình và sự phân bổ nhân sự theo từng phòng ban diễn ra như thế nào?
2.  **Phân tích Biến động (Turnover/Attrition):** Tỷ lệ nhân viên nghỉ việc là bao nhiêu? Nguyên nhân chính dẫn đến việc rời đi là gì và đối tượng nào có rủi ro nghỉ việc cao nhất?
3.  **Hiệu quả Tuyển dụng:** Kênh tuyển dụng nào mang lại số lượng ứng viên lớn nhất và kênh nào mang lại nhân sự chất lượng, gắn bó lâu dài nhất?
4.  **Hiệu suất & Gắn kết:** Sự hài lòng và mức độ gắn kết có ảnh hưởng thế nào đến số ngày vắng mặt và quyết định nghỉ việc của nhân viên?
5.  **Tính công bằng:** Có sự chênh lệch đáng kể nào về mức lương giữa các nhóm giới tính tại cùng một vị trí không?

---

## II. Phương pháp Tư duy Thiết kế (Design Thinking Method)
Để đảm bảo Dashboard mang lại giá trị thực tiễn cao nhất, dự án được triển khai theo 5 bước của phương pháp Design Thinking:

1.  **Empathize (Thấu cảm):** Đặt mình vào góc nhìn của Giám đốc Nhân sự (CHRO). Họ cần theo dõi nhanh tỷ lệ nghỉ việc (đang gây tốn kém chi phí) và đánh giá được chất lượng của các kênh tuyển dụng để phân bổ ngân sách năm tới.
2.  **Define (Xác định vấn đề):** Lựa chọn các chỉ số cốt lõi (KPIs) cần hiển thị: *Total Employees, Total Salary, Turnover Rate, Average Tenure, Absence Rate, Average Satisfaction Score, Average Age*.
3.  **Ideate (Lên ý tưởng):** Cấu trúc báo cáo thành các góc nhìn chuyên sâu để người dùng không bị ngợp dữ liệu: 
    * *Overview:* Nhìn nhận bức tranh toàn cảnh.
    * *Demographics:* Hiểu rõ sự đa dạng của lực lượng lao động.
    * *Performance & Engagement:* Đánh giá chất lượng và thái độ nhân sự.
    * *Attrition Details:* Phân tích chuyên sâu về rủi ro nghỉ việc.
4.  **Prototype (Xây dựng mô hình):**
    * *Data Preparation:* Sử dụng Power Query để chuẩn hóa dữ liệu (xử lý lỗi định dạng ngày tháng, tách/gộp cột tên nhân viên, xử lý giá trị null).
    * *Data Modeling:* Xây dựng mô hình dữ liệu (Data Model) liên kết bảng `Employee` với các bảng `Manager`, `Position`, `Department`, `Performance` qua các khóa chính (ID).
    * *DAX:* Viết các hàm DAX để tạo Measures linh hoạt tính toán tỷ lệ nghỉ việc, tổng lương.
5.  **Test (Kiểm thử):** Đảm bảo các bộ lọc (Slicers) và tính năng Cross-filtering hoạt động trơn tru giữa các biểu đồ, đồng thời tối ưu hóa UI/UX cho dễ nhìn.

---

## III. Trực quan hóa Dữ liệu (Visualization)

* **Trang 1: HR Overview & Demographics (Tổng quan Nhân sự & Nhân khẩu học)**
<img width="1240" height="690" alt="image" src="https://github.com/user-attachments/assets/b1fe63a0-d147-4e41-bf86-d02e9fe5f271" />

   Cung cấp bức tranh toàn cảnh về lực lượng lao động thông qua các chỉ số KPI cốt lõi (Tổng số nhân viên, Mức lương trung bình, Tỷ lệ nhân viên đang làm việc). Trang này cũng kết hợp phân tích sự phân bổ nhân sự theo phòng ban, cũng như các yếu tố đa dạng/nhân khẩu học (Giới tính, Độ tuổi, Tình trạng hôn nhân).

* **Trang 2: Attrition & Performance Analysis (Phân tích Nghỉ việc & Hiệu suất)**
<img width="1242" height="695" alt="image" src="https://github.com/user-attachments/assets/f5e2e7c4-a990-4bf1-8cd5-966bb3e46f56" />
  
   Đi sâu vào phân tích rủi ro nhân sự thông qua tỷ lệ biến động (Turnover/Attrition Rate), chi tiết các lý do nghỉ việc và đánh giá hiệu quả của các nguồn tuyển dụng. Đồng thời, trang này đối chiếu mức độ hài lòng (Satisfaction), điểm gắn kết (Engagement) với số ngày vắng mặt và đánh giá hiệu suất của nhân viên để tìm ra các xu hướng tiềm ẩn.

## IV. Thông tin Chi tiết (Insights)

1.  **Về Biến động nhân sự (Attrition):**
    * Khối **Production (Sản xuất)** ghi nhận số lượng nhân viên nghỉ việc cao nhất, đặc biệt tập trung ở vị trí *Production Technician I và II* với tỷ lệ nghỉ việc (Turnover Rate) lần lượt là 39.10% và 47.27%.
    * Các nguyên nhân nghỉ việc (Termination Reason) phổ biến nhất liên quan đến việc không hài lòng, tìm được cơ hội mới (Another position) hoặc do mức lương chưa phù hợp.
2.  **Về Hiệu quả Tuyển dụng (Recruitment Source):**
    * Các nền tảng như *Indeed* và *LinkedIn* là nguồn cung cấp số lượng ứng viên đầu vào lớn nhất chiếm lần lượt 81% và 73%.
    * Tuy nhiên, kênh *Employee Referral* (Nhân viên nội bộ giới thiệu) lại mang đến những nhân sự có tỷ lệ giữ chân (Retention rate) cao và điểm đánh giá hiệu suất tốt hơn mức trung bình.
3.  **Về Phân bổ Ngân sách Lương:**
    * Các bộ phận mang tính chuyên môn kỹ thuật cao như *Software Engineering* và *IT/IS* có mức lương trung bình dẫn đầu công ty. Không có dấu hiệu chênh lệch lương rõ rệt giữa nam và nữ ở cùng một vị trí công việc.

---

## V. Đề xuất & Khuyến nghị (Recommendations)

1.  **Chính sách can thiệp cho khối Production:** Do đây là bộ phận có tỷ lệ nghỉ việc cao nhất, HR cần tiến hành các cuộc khảo sát ngắn định kỳ hoặc "Stay Interviews" (phỏng vấn giữ chân) để nắm bắt tâm tư nguyện vọng của các Kỹ thuật viên. Cần xem xét lại tính cạnh tranh của cấu trúc lương thưởng tại phòng ban này so với thị trường.
2.  **Tối ưu hóa chiến lược Tuyển dụng:** Cân nhắc dịch chuyển một phần ngân sách từ các kênh quảng cáo chung sang việc phát triển mạnh mẽ **Chương trình Giới thiệu Nhân sự nội bộ (Employee Referral Program)** bằng các khoản thưởng hấp dẫn hơn, nhằm thu hút nhân sự chất lượng cao và phù hợp với văn hóa công ty.
3.  **Hệ thống cảnh báo sớm (Early Warning System):** Xây dựng quy trình theo dõi sát sao đối với những nhân sự bắt đầu có dấu hiệu gia tăng đột biến về số ngày vắng mặt (Absence Days) hoặc có điểm Engagement Score giảm sút. Các Manager trực tiếp cần được thông báo để có các buổi trao đổi (1-on-1) hỗ trợ kịp thời trước khi nhân viên đưa ra quyết định nghỉ việc.

---
