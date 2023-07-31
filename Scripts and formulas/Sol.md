### Des : 
![image](https://github.com/KMANVK/imaginary_ctf_2023/assets/94669750/dea78b73-8fd5-4e13-bd07-197731314cc6)

### Sol : 

+ Đề cung cấp cho ta các file : 

![](https://hackmd.io/_uploads/HkmF534i3.png)

Chạy các chuỗi trên tệp lối tắt, chúng ta có thể thấy powershell đang được sử dụng:  
![image](https://github.com/KMANVK/imaginary_ctf_2023/assets/94669750/8fcf35bd-9935-40e9-866c-9116df7176fb)



Tệp hóa đơn.vbs chứa những gì mình mong đợi, mã độc bị xáo trộn.

![image](https://github.com/KMANVK/imaginary_ctf_2023/assets/94669750/8702d100-439c-4904-9eb8-4a8d0281ab0a)


logs.zip chứa các tệp evtx. Sử dụng [evtx_dump.py](https://github.com/williballenthin/python-evtx/blob/master/scripts/evtx_dump.py) để chuyển đổi chúng thành định dạng có thể đọc được cho linux và để có thể dễ dàng duyệt qua chúng.

```bash
for file in ./*.evtx;do evtx_dump.py "$file" > converted/"${file%.evtx}.xml";done
```

Bởi vì powershell đã được sử dụng, sau khi chuyển đổi tất cả nhật ký trình xem sự kiện, có thể chạy các chuỗi trên mọi thứ và grep cho powershell:

```bash
strings *|grep powershell
```
Mình nhanh chóng tìm thấy một số thứ thú vị.Thấy một lệnh powershell đã được chạy với chuỗi base64:  
![image](https://github.com/KMANVK/imaginary_ctf_2023/assets/94669750/f8af7610-90d5-480b-b8f9-96a16d7ae9b0)


Giải mã chuỗi đó, chúng ta có thể thấy URL Google Trang tính:  

![image](https://github.com/KMANVK/imaginary_ctf_2023/assets/94669750/319090a0-6cb3-4e6d-b14d-606400c8fcf9)

`https://sheets.googleapis.com/v4/spreadsheets/1HpB4GqqYwI6X71z4p2EK88FoJjrsW2DKbSkx-ro5lQQ?key=AIzaSyDUpjSf7R1l1dQohA5Qv9EdyWA3KBOMc0U&ranges=Sheet1!O37&includeGridData=true`

Tuy nhiên, dường như chúng ta không thể truy cập URL này vì vậy đây là những gì đã thực hiện hiện tại:  

![image](https://github.com/KMANVK/imaginary_ctf_2023/assets/94669750/56ac3fe2-d1cf-4c66-9fc8-aa6ac4043447)


Mình đã thu thập khá nhiều thông tin cho đến nay. Hãy chạy docker và kết nối với IP và cổng được cung cấp bằng netcat. Mình được thông báo rằng để có được lá cờ và phải trả lời một số câu hỏi.

![image](https://github.com/KMANVK/imaginary_ctf_2023/assets/94669750/3c91f5b7-9497-4861-bc95-034cd475dec0)


Câu hỏi 1:  
![image](https://github.com/KMANVK/imaginary_ctf_2023/assets/94669750/4cef747b-a59f-4fa4-89fd-9c775722783b)

Khi mình tìm kiếm powershell, có thể thấy dòng này cung cấp cho mình câu trả lời đang tìm kiếm:  
![image](https://github.com/KMANVK/imaginary_ctf_2023/assets/94669750/8e7f7a46-b3e6-4aa8-ac69-62d2c1896d97)


![image](https://github.com/KMANVK/imaginary_ctf_2023/assets/94669750/16f8c69e-9586-420a-b6d6-42ce643fca80)


Câu hỏi 2:  
![image](https://github.com/KMANVK/imaginary_ctf_2023/assets/94669750/1afcf201-f32b-43f8-a23a-3484a2405975)


Chỉ có hai chức năng trong tập lệnh. Câu trả lời dễ dàng ngay cả khi dùng thử/lỗi. Trong khi cái đầu tiên xây dựng tải trọng, thì cái thứ hai thực hiện giải mã nguồn.  
![image](https://github.com/KMANVK/imaginary_ctf_2023/assets/94669750/0fdaf53f-5403-4908-b070-87977a446a1f)


Câu 3:  
![image](https://github.com/KMANVK/imaginary_ctf_2023/assets/94669750/371791e5-02a0-4c1c-aaec-e68227b6959b)


Từ việc tìm hiểu của mình trước đó và đã biết điều này đó là powershell.

Câu 4:  
![image](https://github.com/KMANVK/imaginary_ctf_2023/assets/94669750/a16989d9-4b26-4b48-a8db-387ebd6e92b3)


Phát hiện ra điều này thông qua việc tìm kiếm . Mặc dù  không thể truy cập trang Google Trang tính nhưng mình có URL chứa ID bảng tính.

Câu 5:  
![image](https://github.com/KMANVK/imaginary_ctf_2023/assets/94669750/9fc0b162-3d44-4563-bf2e-914fb1ea8066)


Có thông tin này trong cùng một URL Google Trang tính mà chúng tôi đã phát hiện:  


Câu 6:  
![image](https://github.com/KMANVK/imaginary_ctf_2023/assets/94669750/234b93e6-2c2c-4fbc-88d5-5d6130ea5a6c)


Để tìm thấy điều này, cần mở file `Microsoft-Windows-PowerShell%4Operational.evtx` bằng `Event viewer` nơi tìm thấy lệnh powershell đang thực thi base64, có thể tìm thấy tệp đó là gì bằng cách thêm các cờ `-rn` vào grep.  


 Mở tệp có thể thấy EventID:  

![image](https://github.com/KMANVK/imaginary_ctf_2023/assets/94669750/0932f1db-81e0-40b9-90f9-9e7389d6256b)


![image](https://github.com/KMANVK/imaginary_ctf_2023/assets/94669750/6b66e65d-2395-479f-b400-75163168a506)


Câu 7:  
![image](https://github.com/KMANVK/imaginary_ctf_2023/assets/94669750/b761a5af-6bc9-44d6-b4dd-f14c3d3aa1ad)


Nếu chúng ta tiếp tục đọc tệp mà chúng ta vừa mở cho Câu hỏi 6, ở phía dưới một chút, chúng ta có thể thấy phép toán XOR:  

![image](https://github.com/KMANVK/imaginary_ctf_2023/assets/94669750/02daea1d-3d3c-4c61-8bf7-ec2967bb49d4)

Và cuối cùng mình cũng nhận được lá cờ của mình:  

![image](https://github.com/KMANVK/imaginary_ctf_2023/assets/94669750/c5bff268-3a40-4e09-b29c-c282156d6317)


#flag : `HTB{GSH33ts_4nd_str4ng3_f0rmula3_byp4ss1ng_f1r3w4lls!!}`