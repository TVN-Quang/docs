Tính chất cô lập trong Docker sử dụng 5 namespace để thực hiện:
- Network
- Mount
- User
- PID
- UTS: cô lập hostname và tên miền

Để tạo 1 namespace mới và chạy ứng dụng bên trong nó Docker sử dụng namespace system call. File system namespace như là chroot() trên steroids. Giả sử 1 directory nơi cài đặt toàn bộ hệ thống tập tin gốc. Nếu chạy 1 ứng dụng có chroot() như / thì có quyền truy cập những file và sub directory trong directory này. 

Khi sử dụng một docker image sử dụng Dockerfile có debian image như base image, nó pull Debian filesystem từ docker hub và store trong storage của docker tùy thuộc vào volume driver sử dụng.
    