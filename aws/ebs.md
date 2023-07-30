# **EBS**
Là block storage sử dụng với EC2, có thể mount volumes như thiết bị trên ec2 instances. EBS được đính kèm với 1 instance thì độc lập với vòng đời của instance đó.

## **I.EBS Features**
Có thể tạo và đính kèm vào 1 ec2 instance trên cùng AZ. Để sử dụng ở một AZ khác cần tạo snapshot và restore snapshot ở 1 AZ khác, cũng có thể áp dụng cách tương tự với AZ khác. 

Có thể tạo EBS volume như encrypted volumes, khi tạo encrypted volume đính kèm đến ec2, dữ liệu được lưu trữ ở phần còn lại trên ổ đĩa. I/O của đĩa và snapshot được tạo đều được mã hóa.

## **II.Types**
Các loại chính:
1. General Purpose SSD
2. Provisioned IOPS SSD
3. Throughput Optimized HDD & Cold HDD

### **1.General Purpose SSD**
Loại này phân thành 2 loại volumes gp3, gp2 hỗ trợ bởi ổ đĩa trạng thái rắn (SSD). 

Cân bằng giá và hiệu suất, phục vụ cho đa dạng công việc.
VD:


#### **GP3**
- GP3 volumes là đời mới nhất
- SSD volume có chi phí thấp nhất được cung cấp bởi AWS.
- Mang những đặc điểm của general purpose ssd cân bằng giá và hiệu suất cho hầu hết ứng dụng.
- Scale volume performance độc lập với volume size vì vậy có thể mở rộng volume performance mà không cần tăng volume size.
- Rẻ hơn 20% so với gp2.
- Độ trễ miliseconds 1 chữ số, độ bền 99.8% đến 99.9%. Tỉ lệ thất bại hằng năm không cao hơn 0.2%. 2 lỗi tập đĩa trên 1000 tập chạy trong 1 năm. Đem lại hiệu suất cung cấp 99% thời gian.
- Kích thước volume phải nằm trong khoảng: 1GB - 16TiB
- IOPS performance: về cơ bản sẽ luôn luôn chạy với hiệu suất 3000 IOPS, đã được bao gồm trong giá. Có thể cung cấp thêm tối đa đến 16000 IOPS với tỉ lệ 500 IOPS/ 1GB. Có nghĩa khi giá trị cao hơn 3000 thì cứ 1GB sẽ được thêm tối đa 500 IOPS, vì vậy muốn tăng lên tối đa 16000 IOPS thì dung lượng phải là: 16000/500 = 32GB hoặc lớn hơn.
- Throughput performance: Giá trị cơ bản bất biến luôn luôn là 125MiB/s đã bao gồm với giá của storage. Có thể cung cấp thêm lên tới 1000MiB/s với tỉ lệ 0.25 MiB/s mỗi IOPS. Throughput đạt được giá trị tối đa với 4000 IOPS hoặc cao hơn và 8 GiB hoặc lớn hơn. (4000 * 0.25MiB/s).
<div>
<table style="margin: 0 auto;">
    <tr>
        <th>Type</th>
        <th>Values</th>
    </tr>
    <tr>
        <td>IOPS cơ bản</td>
        <td>3000</td>
    </tr>
    <tr>
        <td>IOPS tối đa</td>
        <td>16000</td>
    </tr>
    <tr>
        <td>Throughput cơ bản</td>
        <td>125MiB/s</td>
    </tr>
    <tr>
        <td>Throughput tối đa</td>
        <td>1000MiB/s</td>
    </tr>
</table>
</div>

#### **GP2**
- Volume mặc định của AWS EBS cho EC2.
- IOPS từ 100 IOPS - 16000 IOPS.
- Tăng 3 IOPS mỗi GB.
- Cung cấp dung lượng lưu trữ hiệu quả về chi phí.
- Volume có size từ 1000 trở lên có thể sử dụng base line 3000 IOPS.
- Những volume gp2 có size dưới 1000 vẫn có thể đạt được hiệu suât 3000 IOPS bằng cách sử dụng I/O Credits. I/O Credits được tích lũy trước đó, khi được tiêu thụ thì không tích lũy. Tích lũy càng nhiều thời gian burst càng lâu, công thức là:
```
                                                                    (I/O credit balance)
                                            Burst duration  =----------------------------
                                                            (Burst IOPS) - (Baseline IOPS)
```
- Tích lũy khi nhu cầu sử dụng I/O xuống baseline performance hoặc thấp hơn. Tốc độ tích lũy là 3 I/O credits mỗi GiB của volume size mỗi giây. Giới hạn tích lũy là 5.4 triệu I/O credits. Đủ đê duy trì hiệu suất 3000 IOPS trong 30 phút.

- Through performance: 128 MiB/s và 250 MiB/s phụ thuộc volume size.
- 170GB hoặc nhỏ hơn max sẽ là 128 MiB/s
- lớn hơn 170GB nhưng nhỏ hơn 334GB thì có thể burst tối đa 250 MB/s
- 334 và lớn hơn thì 250 MB/S
Throughput in MiB/s = IOPS performance × I/O size in KiB

### **Provisioned IOPS SSD volumes**
- SSD.
- Hiệu suất cao nhất.
- Sử dụng cho những công việc cần độ trể thấp, IOPS chuyên sâu

#### **io1.**
- cung cấp 99.8% đến 99.9% durability
- Tỉ lệ thất bại hằng năm không cao hơn 0.2%.
- Tối đa 2 volumes thất bại trên 1000 volumes đang chạy mỗi năm.
- Tỉ lệ tối đa IOPS được cung cấp là 50:1
- Đê đạt được IOPS tối đa 64,000 thì phải cần ít nhất 1280 GB.

#### **io2**
- Cung cấp 99.999% durability
- Tỉ lệ thất bại hằng năm không cao hơn 0.001%, có nghĩa cứ 100,000 volumes running có 1 volume lỗi trong 1 năm.
- Khi chạy io2 volumes với ec2 lưu ý:
    - Chạy 1 instance với 1 io2 volume sử dụng 1 instance type hỗ trợ Block Express, volume tự động chạy trên Block Express không quan tâm đến kích thước và IOPS.
    - Không thể launch 1 instance type không hỗ trợ Block Express với 1 io2 mà có kích thước lớn hơn 16 TB hoặc 64,000 IOPS
    - Không thể launch instance với io2 volume encrypted có kích thước lớn hơn 16 TB hoặc IOPS lớn hơn 64,000 từ 1 unencypted AMI hoặc một shared encrypted AMI với Block Express. ***
    - Trường hợp trên phải tạo 1 enctypted AMI trên account đó và sử dụng nó để chạy 1 instance.

- Khi tạo io2 volumes:
    - Kích thước lớn hơn 16TB hoặc IOPS lớn hơn 64,000 trong Region hỗ trợ Block Express thì volume tự động chạy Block Express.
    - Không thể tạo io2 volume với kích thước lớn hơn 16TB và 64,000 IOPS ở region không hỗ trợ Block Express.
- Đính kèm io2 volume:
    - Đính kèm io2 volume đến instance hỗ trợ Block Express. 
    - Có thể mất 48h để optimize volume, trong khoảng thời gian này io2 có độ trễ. Sau khi optimize cung cấp độ trễ dưới phần nghìn s được hỗ trợ bởi BE.
- Performance:
<div>
<table style="margin: 0 auto;">
    <tr>
        <th>IOPS</th>
        <th>Size</th>
        <th>Tỉ lệ tối đa</th>
    </tr>
    <tr>
        <td>100 IOPS - 64,000 IOPS</td>
        <td>4GB -16TB</td>
        <td>500:1</td>
    </tr>
</table>
</div>

- Chỉ trên Instances xây dựng trên Nitro System mới đạt được 64,000 IOPS, những instance family khác tối đa 32,000 IOPS.
- 1,280 GB volume để đạt được tối IOPS tối đa.
- 32,000 IOPS hỗ trợ tối đa I/O 256KB và 500MB/s throughput.
- I/O size ở mức tối đa thì có thể đạt được throughput tối đa ở mức 2000IOPS.
- Volume với nhiều hơn 32,000 IOPS gia tăng mỗi 16KB mỗi 1 IOPS.
https://docs.aws.amazon.com/images/AWSEC2/latest/UserGuide/images/io1_throughput.png

#### **io2 Block Express**
- Loại này chỉ hỗ trợ một số loại instance: C6a, C6in, C7g, C7gn, Inf2, M6a, M6in, M6idn, M7g, P5, R5b, R6a, R6in, R6idn, R7g, Trn1, Trn1n, X2idn, and X2iedn instances.

- Perfomance:
    - Độ trễ trung bình sub-millisecond.
    - Khả năng lưu trữ 64TB
    - IOPS lên đến 256,000 với IOPS:GB tỉ lệ 1000:1. 256GB có thể đạt được IOPS tối đa.
    - Throughput lên đến 4000 MB/s. Scale lên đến 0.256 MB/s mỗi IOPS. Throughput tối đa đạt được tại 16,000 IOPS.
- Billing:
    - io2 và io2 Block Express được bill cùng một rate.
    - Báo cáo không phân biệt giữa io2 Block Express và io2 volume.
    - Sử dụng tags để giúp bạn xác định chi phí liên quan với io2 Block Express.

### **Throughput Optimized HDD và Cold HDD Volumes**
- HDD Backed volumes được cung cấp bởi EBS rơi vào những loại:
    - Throughput Optimized HDD: low cost
    - Cold HDD: lowest cost

- st1 và sc1 volumes luôn luôn nhỏ hơn:
    - Giới hạn throughput của volume
    - Giới hạn throughput của instance

- Lựa chọn đúng EC2 instance tối ưu hóa EBS để tránh network bottlenecks.

#### Throughput Optimized HDD
- Throughput Optimized HDD được sử dụng cho những trường hợp như: EMR, ETL, data warehouse, log processing.
- Các công việc cần đọc ghi liên tục.
- Đính kèm đến EBS optimized instance cung cấp hiệu suất nhất quán, ít nhất 90% của 99% thời gian trong 1 năm. 

#### Throughput credit và burst performance
- Giống gp2 sử dụng thông lượng tích lũy credit, nhưng sử dụng cho I/O.
- Baseline là 40MB/s mỗi TB, burst lên 250 MB/s mỗi TB
- Công thức:
    ``` 
        (Volume size) × (Credit accumulation rate per TiB) = Throughput
    ```

### Cold HDD volumes (sc1)
- Hiệu suất về throughput không phải IOPS nhưng thấp hơn st1.
- Sử dụng cho dữ liệu ít truy cập.
- Throughput rate: 12 MB/s baseline, 80 MB/s mỗi TB burst.
- Công thức: 
    ``` 
        (Volume size) × (Credit accumulation rate per TiB) = Throughput
    ```


# cả 2 volume này:
- Volumes được đính kèm đến 1 smaller instance được giới hạn cho thông lượng của instance hơn là của st1 hoặc sc1 throghput.
- Thời gian scan:
    Volume size
------------ = Scan time
    Throughput
- Tôi ưu đọc ghi tuần tự không nên dùng cho đọc khi nhỏ, hoặc I/O ngẫu nhiên.
- Amazon EBS BurstBalance metric để giám sát burst bucket balance của volumes này.