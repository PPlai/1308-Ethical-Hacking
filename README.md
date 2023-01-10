# 1308 (Ethical Hacking and Penetration Testing)

บทความนี้เป็นการทดสอบการทำ Penetration testing เพื่อใช้ทดสอบการเจาะระบบสำหรับคนที่สนใจทางด้านของการเจาะระบบ และ เพื่อใช้ประกอบการเรียนการสอนวิชา Ethical Hacking ภาควิชา Network Engineering and Cybersecurity โดยการทดลองนี้จะใช้ VirtualBox
## CROSSROADS 1 Vulnhub Writeup
## เป้าหมาย
> ใช้วิธีอะไรก็ได้เพื่อให้สามารถเจาะเข้าเครื่อง Server และได้รับสิทธิ์เป็น Root และได้ flag ครบทั้ง 2 ตัว
## การติดตั้ง
> https://www.vulnhub.com/entry/crossroads-1,659/

> https://download.vulnhub.com/crossroads/crossroads_vh.ova
## การสร้าง Folder 
> สร้าง Folder ที่ไว้จัดเก็บ file สามารถเลือกสร้างไว้ที่ใดก็ได้ แต่ในตัวอย่างจะเป็นการจัดเก็บไว้ที่ส่วนของ Desktop 

```
  $ sudo su
  # cd Desktop
  # mkdir crossroads
```
![image](https://user-images.githubusercontent.com/87358973/211530778-b7979e1d-da40-41d0-9aae-d0d95c6ee699.png)

# 1.	Identify the target : ระบุเป้าหมาย
> ใช้คำสั่ง netdiscover เพื่อตรวจสอบ Mac Address ทุกเครื่องในระบบเครือข่าย เพื่อค้นหาเป้าหมายที่เราต้องการ
```
# netdiscover
# netdiscover -i eth0 
```
![image](https://user-images.githubusercontent.com/87358973/211531820-ea77535d-2c0c-4a4b-bc1b-be6d56e98544.png)
