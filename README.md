# Số hóa hóa đơn và biểu mẫu với Amazon Textract

Dự án môn Điện toán đám mây nhằm xây dựng pipeline serverless để tiếp nhận tài liệu, trích xuất thông tin và lưu kết quả trên AWS. README này ghi nhận phạm vi đã thực hiện trong **Tuần 1**; các chức năng chưa triển khai được nêu rõ ở phần trạng thái.

## Mục tiêu dự án

Pipeline dự kiến sử dụng các dịch vụ AWS sau:

- **Amazon S3** lưu tài liệu đầu vào và dữ liệu đầu ra.
- **AWS Lambda** tiếp nhận sự kiện và điều phối xử lý.
- **Amazon Textract** trích xuất thông tin từ hóa đơn, biểu mẫu.
- **Amazon DynamoDB** lưu kết quả trích xuất.
- **Amazon SNS** gửi thông báo trạng thái.
- **Amazon CloudWatch** ghi nhận log và metrics.

## Phạm vi Tuần 1

Trong tuần 1, nhóm tập trung thiết kế kiến trúc, khởi tạo tài nguyên nền tảng và kiểm tra luồng sự kiện S3 đến Lambda trong AWS Academy Learner Lab.

Đã thực hiện và kiểm tra:

- Sử dụng AWS Region `us-east-1` và IAM role `LabRole` do Learner Lab cung cấp.
- Tạo S3 bucket với hai prefix `input/` và `output/`; cấu hình sự kiện tạo object chỉ áp dụng cho `input/`.
- Tạo DynamoDB table `textract-pipeline-results` với partition key `PK`, sort key `SK` và chế độ On-demand.
- Tạo SNS topic `textract-pipeline-notifications` và xác nhận email subscription.
- Tạo Lambda Processor `textract-pipeline-processor` và Lambda Exporter `textract-pipeline-exporter` dạng skeleton.
- Kiểm thử Lambda Processor nhận sự kiện S3, ghi log lên CloudWatch và trả về mã trạng thái 200.
- Xác nhận tải `smoke-test.txt` vào `input/` kích hoạt Processor; tải file thử vào `output/` không kích hoạt Processor.

## Trạng thái chức năng

Đến hết tuần 1, Lambda Processor mới là mã smoke test để xác nhận luồng sự kiện. Nhóm **chưa tích hợp Textract**, **chưa ghi kết quả trích xuất vào DynamoDB**, **chưa tạo CSV từ dữ liệu trích xuất** và **chưa gửi thông báo từ Lambda qua SNS**. Nhóm cũng chưa đánh giá độ chính xác trên bộ 30 tài liệu mẫu. Đây là các nội dung dự kiến cho giai đoạn triển khai tiếp theo.

## Tài nguyên AWS trong môi trường lab

| Thành phần | Tên hoặc cấu hình |
| --- | --- |
| Region | `us-east-1` (US East, N. Virginia) |
| IAM role | `LabRole` |
| S3 bucket | `textract-pipeline-535709085695` |
| S3 input prefix | `input/` |
| S3 output prefix | `output/` |
| DynamoDB table | `textract-pipeline-results` (`PK`, `SK`; On-demand) |
| SNS topic | `textract-pipeline-notifications` |
| Lambda Processor | `textract-pipeline-processor` |
| Lambda Exporter | `textract-pipeline-exporter` |

Các tên và cấu hình trên mô tả môi trường AWS Academy Learner Lab được ghi nhận trong báo cáo tuần 1; chúng không phải thông tin xác thực. Không đưa access key, secret key, token hoặc mật khẩu lên repository.

## Cấu trúc repository

```text
.
├── README.md
└── BaoCaoTuan1_da_chinh_sua.docx   # Thêm báo cáo vào repository nếu cần
```

Khi thêm ảnh minh chứng, nên đặt chúng trong `docs/images/` và liên kết tới từng ảnh từ báo cáo hoặc README.

## Báo cáo và minh chứng

- Repository: <https://github.com/quocviet171006-gif/textract-document-digitization>
- Báo cáo tuần 1: thêm file báo cáo đã nộp vào repository và cập nhật liên kết trực tiếp tại đây.
- Ảnh minh chứng: thêm ảnh vào `docs/images/`, sau đó liên kết tới các ảnh cần thiết tại đây.

## Nhật ký thay đổi

Các thay đổi của dự án cần được ghi bằng commit message cụ thể để có thể đối chiếu file đã thêm hoặc chỉnh sửa, ví dụ:

```text
Add week 1 report and project README
Document S3 to Lambda smoke test
Add architecture and AWS resource screenshots
```
