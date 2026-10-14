# Báo cáo thẩm định mã nguồn và Xây dựng kịch bản Dọn dẹp An toàn

## Phần 1: Phân tích (Auditing) mã AI gợi ý

### 1. Điểm cực kỳ nguy hiểm
Đoạn mã do AI gợi ý là một thảm họa hệ thống nghiêm trọng, chứa lệnh **xóa toàn bộ ổ đĩa hệ điều hành**.

### 2. Ý nghĩa các tham số
- **`-Path "C:\*"`**: Chỉ định mục tiêu là toàn bộ các tệp và thư mục nằm ngay tại gốc ổ C.
- **`-Recurse`**: Cho phép quét sâu vào tất cả các thư mục con, thư mục hệ thống bên trong ổ C.
- **`-Force`**: Ép buộc xóa bất chấp các tệp đang bị khóa, tệp hệ thống (System files), tệp ẩn hoặc tệp chỉ đọc (Read-only) mà không thèm hiển thị cảnh báo xác nhận.

### 3. Hậu quả thực tế
Nếu thực thi đoạn script này trên máy chủ, hệ điều hành Windows sẽ bị **xóa sạch ngay lập tức**, làm tê liệt toàn bộ hệ thống điều khiển của "Trang trại Lan thông minh" và gây mất dữ liệu vĩnh viễn.

### 4. Áp dụng 3 Nguyên tắc "Vàng" thẩm định mã AI
1. **Không bao giờ tin tưởng tuyệt đối vào mã AI có quyền ghi/xóa:** Luôn dừng lại đọc hiểu từng tham số trước khi bấm Enter.
2. **Tuyệt đối tuân thủ Nguyên tắc "Khoanh vùng Đường dẫn" (Scoped Path):** Không bao giờ dùng các ký tự đại diện chung chung dạng gốc ổ đĩa (`C:\*` hay `*`) cho các lệnh tác động mạnh như xóa dữ liệu.
3. **Kiểm tra phạm vi tác động:** Xác định rõ ràng vị trí thư mục làm việc hiện tại trước khi thực hiện bất kỳ lệnh hủy diệt nào.

---

## Phần 2: Triển khai kịch bản an toàn (`safe_cleanup.ps1`)

Dưới đây là mã nguồn tệp kịch bản được thiết kế chuẩn xác, khoanh vùng an toàn tuyệt đối cho dự án:

```powershell
<#
.SYNOPSIS
    Script don dep file .tmp va trich xuat log canh bao an toan cho he thong.
.AUTHOR
    Nguyen Trong Nhan
#>

# 1. Khoanh vung duong dan: Chuyen den dung thu muc logs cua du an
$targetDir = "C:\SmartOrchidProject\logs"
Set-Location -Path $targetDir

Write-Host "[INFO] Dang xu ly tai thu muc: $targetDir" -ForegroundColor Cyan

# 2. Loc cac dong chua "WARNING" tu system.log va luu ra issues.txt
Get-Content "system.log" | Select-String "WARNING" | Out-File -FilePath "issues.txt" -Encoding utf8

# 3. Chi xoa cac tep .tmp co gioi hạn pham vi trong dung thu muc nay
Remove-Item -Path "*.tmp" -Force

Write-Host "[SUCCESS] Da hoan tat loc canh bao va xoa tep tam .tmp an toan!" -ForegroundColor Green
