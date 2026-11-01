1. Đọc log lỗi của Grafana
Để kiểm tra nguyên nhân gây lỗi kết nối, hãy chạy lệnh xem log của container Grafana:

docker-compose logs grafana
Dấu hiệu nhận biết: Bạn sẽ thấy các dòng log báo lỗi timeout hoặc "connection refused" khi Grafana cố gắng gọi API lấy dữ liệu từ địa chỉ cũ 192.168.1.15:9090.

2. Sửa file cấu hình datasource.yml
Trong mạng nội bộ của Docker (Docker network), các container có thể phân giải IP và giao tiếp với nhau trực tiếp thông qua tên service. Bạn cần thay thế IP tĩnh bằng tên service của Prometheus (giả định là prometheus).

Mở file grafana/provisioning/datasources/datasource.yml và sửa lại thành:

YAML
apiVersion: 1
datasources:
  - name: Prometheus
    type: prometheus
    url: http://prometheus:9090
    access: proxy
    isDefault: true
3. Khởi động lại Grafana và kiểm tra
Áp dụng cấu hình mới bằng cách khởi động lại service Grafana:

docker-compose restart grafana
Kiểm tra kết nối: Truy cập vào giao diện web của Grafana, điều hướng tới mục Connections > Data sources (hoặc Configuration > Data Sources tùy phiên bản), chọn Prometheus và bấm Save & Test. Khi hệ thống báo "Data source is working", hãy quay lại Dashboard để xác nhận dữ liệu đã hiển thị bình thường.