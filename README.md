# Số hóa hóa đơn và biểu mẫu với Amazon Textract

Dự án môn Điện toán đám mây nhằm xây dựng pipeline serverless để tiếp nhận tài liệu, trích xuất thông tin và lưu kết quả trên AWS. README này ghi nhận phạm vi đã thực hiện trong **Tuần 1**.

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

- Sử dụng AWS Region us-east-1 và IAM role LabRole do Learner Lab cung cấp.
- Tạo VPC riêng với hai subnet ở us-east-1a và us-east-1b, kiểm tra route table chính.
- Tạo S3 bucket với hai prefix input/ và output/; cấu hình sự kiện tạo object chỉ áp dụng cho input/.
- Tạo DynamoDB table 	extract-pipeline-results với partition key PK, sort key SK và chế độ On-demand.
- Tạo SNS topic 	extract-pipeline-notifications và xác nhận email subscription.
- Tạo Lambda Processor 	extract-pipeline-processor và Lambda Exporter 	extract-pipeline-exporter dạng skeleton.
- Kiểm thử Lambda Processor nhận sự kiện S3, ghi log lên CloudWatch và trả về mã trạng thái 200.
- Xác nhận tải smoke-test.txt vào input/ kích hoạt Processor; tải file thử vào output/ không kích hoạt Processor.

## Trạng thái chức năng

Đến hết tuần 1, Lambda Processor mới là mã smoke test để xác nhận luồng sự kiện. Nhóm **chưa tích hợp Textract**, **chưa ghi kết quả trích xuất vào DynamoDB**, **chưa tạo CSV từ dữ liệu trích xuất** và **chưa gửi thông báo từ Lambda qua SNS**. Nhóm cũng chưa đánh giá độ chính xác trên bộ 30 tài liệu mẫu. Đây là các nội dung dự kiến cho giai đoạn triển khai tiếp theo.

## Tài nguyên AWS trong môi trường lab

| Thành phần | Tên hoặc cấu hình |
| --- | --- |
| Region | us-east-1 (US East, N. Virginia) |
| IAM role | LabRole |
| S3 bucket | 	extract-pipeline-535709085695 |
| S3 input prefix | input/ |
| S3 output prefix | output/ |
| DynamoDB table | 	extract-pipeline-results (PK, SK; On-demand) |
| SNS topic | 	extract-pipeline-notifications |
| Lambda Processor | 	extract-pipeline-processor |
| Lambda Exporter | 	extract-pipeline-exporter |

Các tên và cấu hình trên mô tả môi trường AWS Academy Learner Lab được ghi nhận trong báo cáo tuần 1; chúng không phải thông tin xác thực. Không đưa access key, secret key, token hoặc mật khẩu lên repository.

## Sơ đồ kiến trúc & Luồng dữ liệu thử nghiệm

`mermaid
flowchart LR
    subgraph TestData [sample-data - Du lieu kiem thu]
        txt[smoke-test.txt]
        csv[output-smoke-test.csv]
    end
    subgraph S3 [Amazon S3 Bucket]
        input[input/]
        output[output/]
    end
    subgraph Serverless [Pipeline xu ly]
        proc[Lambda Processor]
        textract[Amazon Textract]
        dynamo[(DynamoDB)]
        exp[Lambda Exporter]
    end
    subgraph Ops [Giam sat & Thong bao]
        sns[Amazon SNS]
        cw[CloudWatch]
    end
    txt -. Upload .-> input
    input -->|S3 Event Trigger| proc
    proc -. Goi trich xuat .-> textract
    proc -. Ghi ket qua .-> dynamo
    proc -. Ghi log .-> cw
    proc -. Gui thong bao .-> sns
    dynamo -. Doc du lieu .-> exp
    exp -. Tao file ket qua .-> output
    output -. Doi chieu mau .-> csv
`

## Cấu trúc repository

`	ext
.
|-- README.md                     # Tài liệu tổng quan dự án và kiến trúc hệ thống
|-- BaoCaoTuan1.docx              # Báo cáo chi tiết kết quả thực hiện Tuần 1 (Microsoft Word)
|-- docs/
|   |-- BaoCaoTuan1.md            # Báo cáo Tuần 1 dạng Markdown (đã nhúc toàn bộ ảnh, xem được trên GitHub)
|   -- images/                   # Thư mục lưu trữ hình ảnh minh chứng thực nghiệm
|       |-- hinh01.png ... hinh39.png             # 39 ảnh minh chứng Hình 1-39 của báo cáo
|       -- audit_sheet_1.png ... audit_sheet_3.png # 3 bảng kiểm kê đối chiếu tài nguyên (QA)
-- sample-data/                  # Dữ liệu phục vụ kiểm thử pipeline
    |-- smoke-test.txt            # Dữ liệu mẫu đầu vào (kiểm thử S3 trigger với prefix input/)
    -- output-smoke-test.csv     # Dữ liệu mẫu đầu ra trích xuất (format CSV cho prefix output/)
`

## Báo cáo tiến độ và Danh mục hình ảnh minh chứng

> Xem báo cáo đầy đủ kèm hình ảnh minh chứng trực quan tại: [docs/BaoCaoTuan1.md](docs/BaoCaoTuan1.md)

Bảng tra cứu nhanh các ảnh minh chứng trong [docs/images/](docs/images/):

| Nhóm tài nguyên / Kiểm thử | Hình trong báo cáo | Liên kết ảnh | Vị trí |
| :--- | :--- | :--- | :--- |
| **Môi trường Lab & IAM** | Hình 1-3 | [hinh01.png](docs/images/hinh01.png) - [hinh02.png](docs/images/hinh02.png) - [hinh03.png](docs/images/hinh03.png) | Mục 3 & 3.1 |
| **Mạng VPC & Subnet** | Hình 4-10 | [hinh04.png](docs/images/hinh04.png) - [hinh05.png](docs/images/hinh05.png) - [hinh08.png](docs/images/hinh08.png) | Mục 3.2 |
| **Sơ đồ kiến trúc** | Hình 11 | [hinh11.png](docs/images/hinh11.png) | Mục 3.3 |
| **Amazon S3** | Hình 12-14 | [hinh12.png](docs/images/hinh12.png) - [hinh13.png](docs/images/hinh13.png) - [hinh14.png](docs/images/hinh14.png) | Mục 4.1 & 4.2 |
| **Gắn Tag tài nguyên** | Hình 15-22 | [hinh15.png](docs/images/hinh15.png) - [hinh16.png](docs/images/hinh16.png) | Mục 4.3 |
| **Amazon DynamoDB** | Hình 23-25 | [hinh23.png](docs/images/hinh23.png) - [hinh24.png](docs/images/hinh24.png) - [hinh25.png](docs/images/hinh25.png) | Mục 4.4 |
| **Amazon SNS** | Hình 26-27 | [hinh26.png](docs/images/hinh26.png) - [hinh27.png](docs/images/hinh27.png) | Mục 4.5 |
| **Lambda Processor** | Hình 28-29 | [hinh28.png](docs/images/hinh28.png) - [hinh29.png](docs/images/hinh29.png) | Mục 4.6 |
| **Cấu hình S3 Trigger** | Hình 30-32 | [hinh30.png](docs/images/hinh30.png) - [hinh31.png](docs/images/hinh31.png) - [hinh32.png](docs/images/hinh32.png) | Mục 4.7 |
| **Smoke Test input/** | Hình 33-34 | [hinh33.png](docs/images/hinh33.png) - [hinh34.png](docs/images/hinh34.png) | Mục 4.8 |
| **Smoke Test output/** | Hình 35-36 | [hinh35.png](docs/images/hinh35.png) - [hinh36.png](docs/images/hinh36.png) | Mục 4.9 |
| **Lambda Exporter** | Hình 37-39 | [hinh37.png](docs/images/hinh37.png) - [hinh38.png](docs/images/hinh38.png) - [hinh39.png](docs/images/hinh39.png) | Mục 4.10 |
| **Bảng kiểm kê QA** | Sheet 1-3 | [audit_sheet_1.png](docs/images/audit_sheet_1.png) - [audit_sheet_2.png](docs/images/audit_sheet_2.png) - [audit_sheet_3.png](docs/images/audit_sheet_3.png) | Mục 7 |

## Quy định lưu trữ ảnh minh chứng

- Toàn bộ ảnh chụp màn hình minh chứng kết quả lưu thống nhất tại docs/images/.
- Tên file theo quy ước hinh01.png, hinh02.png, ... tương ứng với số thứ tự Hình trong báo cáo.
- Khi thêm ảnh mới, sử dụng cú pháp liên kết tương đối trong Markdown:
  `markdown
  ![Mô tả ảnh](docs/images/hinhNN.png)
  `
