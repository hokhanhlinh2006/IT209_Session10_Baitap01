
# BÁO CÁO KỸ THUẬT: PHÂN TÍCH KIẾN TRÚC CONTAINER VS VIRTUAL MACHINE

- **Họ và tên:** Hồ Khánh Linh
- **Mã sinh viên:** B24DTCN359
- **Lớp:** CNTT3
- **Bài tập:** Homework Session 10 - Exercise 1

## 1. So sánh kiến trúc Virtual Machine và Container

Virtual Machine (VM) và Container đều là công nghệ ảo hóa được sử dụng để triển khai ứng dụng. Tuy nhiên, VM ảo hóa phần cứng thông qua Hypervisor, trong khi Container sử dụng chung kernel của hệ điều hành máy chủ và được quản lý bởi Container Engine như Docker.

| Tiêu chí | Virtual Machine (VM) | Container |
|---|---|---|
| Dung lượng Image | Thường lớn, có thể từ vài GB do chứa hệ điều hành khách | Thường nhỏ hơn, từ MB đến GB tùy ứng dụng và các lớp image |
| Thời gian khởi động | Thường mất từ vài giây đến vài phút vì phải khởi động hệ điều hành | Thường nhanh, có thể chỉ mất vài giây hoặc ít hơn |
| Hao phí OS | Mỗi VM thường chạy một hệ điều hành khách riêng, tiêu tốn thêm CPU, RAM và dung lượng lưu trữ | Dùng chung kernel của máy chủ nên giảm phần tài nguyên dành cho hệ điều hành |
| Mức độ cô lập | Thường có ranh giới cô lập mạnh hơn nhờ lớp Hypervisor | Cô lập ở mức tiến trình và tài nguyên hệ điều hành; cần cấu hình bảo mật phù hợp |
| Khả năng mở rộng | Tạo VM mới thường tốn nhiều tài nguyên và thời gian hơn | Có thể tạo, nhân bản và triển khai nhiều container nhanh chóng |

Do những đặc điểm trên, Container phù hợp với các ứng dụng cần triển khai nhanh, mở rộng linh hoạt và sử dụng tài nguyên hiệu quả. Tuy nhiên, VM vẫn có ưu thế trong những trường hợp cần hệ điều hành riêng hoặc ranh giới cô lập mạnh hơn.

## 2. Vai trò của Namespaces và Control Groups (cgroups)

### 2.1. Namespaces - Cơ chế cô lập

Namespaces là một tính năng của Linux giúp tạo ra các không gian riêng biệt cho những nhóm tiến trình. Nhờ đó, các container có thể hoạt động như những môi trường độc lập dù cùng sử dụng kernel của máy chủ.

Các loại namespaces tiêu biểu gồm:

- **PID Namespace:** Cô lập không gian định danh tiến trình (Process ID). Tiến trình trong container có thể nhìn thấy cây tiến trình riêng thay vì toàn bộ tiến trình trên máy chủ.
- **Network Namespace:** Cung cấp không gian mạng riêng, bao gồm giao diện mạng, địa chỉ IP, bảng định tuyến và cổng mạng riêng.
- **Mount Namespace:** Cô lập các điểm gắn kết hệ thống tệp (Mount points), giúp mỗi container có góc nhìn riêng về hệ thống tệp.

Nhờ Namespaces, Docker có thể tổ chức các container thành những môi trường chạy riêng biệt. Tuy nhiên, Namespaces không có nghĩa là mỗi container có một kernel riêng và không thay thế hoàn toàn các cơ chế bảo mật khác.

### 2.2. Control Groups (cgroups) - Quản lý tài nguyên

Control Groups là cơ chế của Linux dùng để theo dõi và kiểm soát mức sử dụng tài nguyên của các nhóm tiến trình. Docker sử dụng cgroups để quản lý tài nguyên của container.

- **CPU:** Có thể đặt giới hạn mức sử dụng CPU hoặc phân chia tỷ lệ CPU giữa các container.
- **RAM:** Có thể giới hạn bộ nhớ mà container được phép sử dụng, giúp hạn chế việc một ứng dụng tiêu thụ quá nhiều RAM.
- **Theo dõi tài nguyên:** Hỗ trợ thống kê mức sử dụng tài nguyên để phục vụ giám sát và quản trị.

Namespaces tập trung vào việc cô lập môi trường, còn cgroups tập trung vào quản lý và giới hạn tài nguyên. Hai cơ chế này kết hợp giúp container hoạt động độc lập hơn và sử dụng tài nguyên máy chủ hiệu quả hơn.

## 3. Thông số Docker trên hệ thống thực tế

Các lệnh kiểm tra được thực hiện trên macOS thông qua Docker Desktop:

- **Server Version:** 29.8.2
- **Cgroup Driver:** cgroupfs
- **Containers:** 3 (Running: 1, Paused: 0, Stopped: 2)
- **Images:** 5

Kết quả cho thấy Docker Engine đang hoạt động bình thường.
Hệ thống sử dụng Cgroup Driver là cgroupfs và Cgroup Version 2
để hỗ trợ quản lý tài nguyên container.

![img.png](img.png)

## 4. Kết luận

VM và Container đều có vai trò quan trọng trong triển khai phần mềm. VM cung cấp môi trường hệ điều hành riêng và mức cô lập mạnh hơn trong nhiều trường hợp, nhưng thường tiêu tốn nhiều tài nguyên hơn. Container tận dụng kernel của hệ điều hành máy chủ, giúp giảm chi phí tài nguyên, rút ngắn thời gian khởi động và hỗ trợ triển khai ứng dụng linh hoạt.

Vì vậy, doanh nghiệp có thể chuyển từ mô hình triển khai ứng dụng trên các VPS hoặc VM riêng lẻ sang Docker Container để chuẩn hóa môi trường, đóng gói ứng dụng cùng các thành phần phụ thuộc và thuận tiện mở rộng dịch vụ. Việc lựa chọn vẫn cần căn cứ vào yêu cầu bảo mật, hiệu năng và kiến trúc của từng hệ thống.
