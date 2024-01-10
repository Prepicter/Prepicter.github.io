---
title : python으로 바로가기 파일(.lnk) 실제 주소 확인법

date : 2024-01-09 13:00:00 +09:00

categories : [Python]

tags : [tips]

---

python으로 바로가기 파일(.lnk) 실제 주소 확인법

    import  win32com.client
    
    shell  =  win32com.client.Dispatch("WScript.Shell")
    shell.CreateShortCut(lnk_path).Targetpath]

C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Accessories\wordpad.lnk
->
C:\\Program Files\\Windows NT\\Accessories\\wordpad.exe

참고
> https://stackoverflow.com/questions/65572953/how-to-get-the-real-path-of-shortcut


