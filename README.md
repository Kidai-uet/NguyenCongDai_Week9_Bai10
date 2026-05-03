Báo cáo Bài 10: Phân tích và khắc phục Broken Pipeline
1. Lỗi số 1: Thiếu bước Checkout Code
   Vị trí lỗi: File .github/workflows/maven.yml (hoặc ci.yml).

Log bằng chứng:
Error: The goal you specified requires a project to execute but there is no POM in this directory

Nguyên nhân kỹ thuật: Pipeline chưa thực hiện hành động actions/checkout. Điều này khiến máy ảo của GitHub Actions không có mã nguồn và tệp pom.xml để thực hiện lệnh mvn package.

Cách khắc phục: Thêm bước - uses: actions/checkout@v3 vào trước bước thiết lập JDK.

2. Lỗi số 2: Sai phiên bản Dependency
   Vị trí lỗi: File pom.xml, dòng khai báo version cho logback-classic.

Log bằng chứng:
[ERROR] Failed to execute goal on project shipping-app: Could not resolve dependencies for project com.lab:shipping-app:jar:1.0-SNAPSHOT: Failure to find ch.qos.logback:logback-classic:jar:9.9.9

Nguyên nhân kỹ thuật: Tệp cấu hình Maven yêu cầu phiên bản 9.9.9 cho thư viện Logback. Tuy nhiên, phiên bản này không tồn tại trên Maven Central Repository, dẫn đến lỗi Resolve Dependency.

Cách khắc phục: Hạ cấp hoặc sửa lại phiên bản về bản ổn định (ví dụ: 1.4.14).

3. Lỗi số 3: Plugin Surefire không tương thích (Lỗi Xanh giả - False Positive)
   Vị trí lỗi: File pom.xml, dòng khai báo version cho maven-surefire-plugin.

Log bằng chứng:
[INFO] Tests run: 0, Failures: 0, Errors: 0, Skipped: 0

Nguyên nhân kỹ thuật: Sử dụng maven-surefire-plugin phiên bản quá cũ (2.12.4) không hỗ trợ JUnit 5. Kết quả là plugin không nhận diện được các bài test, dẫn đến việc build thành công nhưng thực chất không có bài kiểm tra nào được chạy.

Cách khắc phục: Cập nhật phiên bản plugin lên 3.2.5 để hỗ trợ đầy đủ JUnit 5.

4. Lỗi số 4 (Lỗi tự tạo): Sai lệch logic Unit Test
   Vị trí lỗi: File src/test/java/com/lab/ShippingCalculatorTest.java, dòng testStandard.

Log bằng chứng:
[ERROR] Failures: [ERROR] ShippingCalculatorTest.testStandard:12 expected: <99999.0> but was: <15000.0>

Nguyên nhân kỹ thuật: Cố tình thay đổi giá trị kỳ vọng (Expected) trong Unit Test khác với logic xử lý thực tế của code. Khi giá trị thực tế (15000.0) không khớp với kỳ vọng sai (99999.0), Assertion sẽ thất bại và làm đỏ Pipeline.

Cách khắc phục: Điều chỉnh lại giá trị kỳ vọng trong Test Case cho khớp với logic nghiệp vụ của ShippingCalculator.