# Cronjob


## Lợi ích
CronJob hữu dụng bởi vì thực hiện những hành động cần thiết trong container riêng biệt. Xác định phiên bản của 1 container, cập nhật riêng biệt từng cron, chỉnh sửa nó với những phụ thuộc cụ thể mà nó cần.

## Monitor và Considerations
### **Concurrency Policy**
Cronjobs nhúng concurrency contorls để loại bỏ concurrent excution mặc dù K8S mặc định cho phép conccurency. Nếu bật conccurrency scheduled sẽ bắt đầu thập chí khi lần chạy cuối chưa hoàn thành. Nó là không mong đợi cho những job yêu cầu thực hiện tuần tự.

Để điều khiển conccurency thì cấu hình concurrency policy trên CronJob object. Có thể thiết lập 3 giá trị:
1. Allow: giá trị mặc định
2. Forbid: loại bỏ chạy đồng thời. Bỏ qua các scheduled starts nếu lần chạy cuối chưa hoàn thành. (nghĩa là đã đến thời điểm để start job nhưng job cũ chưa chạy xong thì sẽ bỏ qua cái lịch start job mới đó.)
3. Replace: terminates các lần chạy chưa hoàn thành khi jobs tiếp theo được scheduled, cho phép lần chạy mới xử lý.


Có thể áp dụng conccurency policy cho cluster để tạo CronJobs mà chỉ có quyền chạy 1 job tại bất cứ thời gian nào.
### **Starting Deadline**:
Xác định Scheduled CronJob có thể start hay không. Xác định thời gian mà 1 Job bị deplay có thể start (nguyên nhân là do job trước đó chưa hoàn thành).
VD: Job được lên lịch (scheduled) tại 10 giờ và starting deadline là 15s thì có thể được start vào lúc 10:00:14, còn không thể start nữa nếu 10:00:15.

### **Retaining Job History**
2 giá trị khác là successful jobs history limit và failed jobs history limit.
successful jobs history limit: giới hạn thời gian của duy trì lịch sử của job. Mặc đinh 3 success và 1 failed job. Các giá trị này có thể thay đổi, giá trị cao hơn giữ lịch sữ lâu hơn, phục vụ cho debug.

### **CronJob Monitoring**
Tên của job bao gồm tên cronjob và timestamp start.
>`$ kubectl logs job/example-cron-1648239040`

## Cronjobs Limitations

