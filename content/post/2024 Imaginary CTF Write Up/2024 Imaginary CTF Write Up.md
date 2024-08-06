---
title: "2024 Imaginary CTF Write Up"
date: 2024-07-26T09:10:42Z
image: ImaginaryCTF_2024.png
hidden: false
comments: true
draft: false
categories:
    - CTF
tags: 
    - 資安
    - CTF
    - Write-up
---

🚩Team: [ICEDTEA](https://ctftime.org/team/303514)  
🚩CTF challenge link: https://2024.imaginaryctf.org/Challenges.html

![image](https://hackmd.io/_uploads/SJWs3cJKA.png)
## 🌐WEB

### readme
> https://cybersharing.net/s/67af3fd941707117

按下Load Preview  
![image](https://hackmd.io/_uploads/rJaUcxpu0.png)

接著打開開發者工具，找到對應的請求  
![image](https://hackmd.io/_uploads/ry1N5ea_0.png)

把要求網址的後面的readme.tar.gz改成flag.txt即可拿到檔案  
![image](https://hackmd.io/_uploads/HywQqg6u0.png)  
https://cybersharing.net/api/download/file/7a829a2b-4674-471a-9875-02691f5ce6c5/703dc4c6-7b06-4813-874a-857fc1ea4098/7aeb7c98-9bf5-44b3-9eb3-972f225a3576/flag.txt

但打開會發現這實際上不是.txt檔，所以先丟進Linux裡面查看檔案類型  
```bash=
wget https://cybersharing.net/api/download/file/7a829a2b-4674-471a-9875-02691f5ce6c5/703dc4c6-7b06-4813-874a-857fc1ea4098/7aeb7c98-9bf5-44b3-9eb3-972f225a3576/flag.txt
file flag.txt
```

發現是gzip檔，解壓縮它並繼續查看檔案類型  
```bash=
mv flag.txt flag.gz
gzip -d flag.gz
file flag
```

發現是tar壓縮檔，解壓縮它  
```bash
tar -xvf flag
```

在解壓縮出來的檔案中有一個名為flag的檔案，查看它的內容  
```bash
cat flag | grep ictf
```

報錯了，終端回應```grep: (standard input): binary file matches```，於是加上```-a```選項，這會強制 grep 處理二進制文件為文本  
```bash
cat flag | grep -a ictf
```

flag：```ictf{path_normalization_to_the_rescue}```



## 🔍FORENSICS


### cartesian-1
上Instagram搜尋Terrence Descartes
> https://www.instagram.com/descartes.terry2001/
 
在限時動態中發現flag  
![螢幕擷取畫面 2024-07-20 171424](https://hackmd.io/_uploads/BJCXeZFO0.png)


### cartesian-2
上LinkedIn搜尋Terrence Descartes，會看到一個跟前面Instagram帳號同樣頭像的[帳戶](https://www.linkedin.com/feed/update/urn:li:activity:7220221551969759232/)，點下去後查看第一則貼文的第二張照片，裡面有寫flag(2/1)  
![1721434962084](https://hackmd.io/_uploads/ryuHquo_C.jpg)

利用[epieos](https://epieos.com/)搜尋terrencedescartes@gmail.com(用姓名猜出來的)

Google Calendar右邊的[連結](https://calendar.google.com/calendar/u/0/embed?src=terrencedescartes@gmail.com)點進去

題目說要觀察他去年夏天旅行，所以前往2023年6月，會看到SUMMER TRIP!!!，點下去就能看到flag(2/2)了  
![screencapture-calendar-google-calendar-u-0-embed-2024-07-22-14_32_59](https://hackmd.io/_uploads/rkf8TdjdA.png)


### cartesian-3
> http://cartesian.chal.imaginaryctf.org/

1. What is your email?  
A: terrencedescartes@gmail.com  
用姓名去猜即可

2. On what day were you born? (YYYY-MM-DD)  
A: 2001-01-18  
此篇[Instagram貼文](https://www.instagram.com/p/C9lkBOjxQCW/)是他的半歲生日慶祝文，用發文日期減去6個月後即是他的生日，年份的部分則在名稱中就透露了

3. What is the name of your favorite pet?  
A: bonnie  
這篇[Instagram貼文](https://www.instagram.com/p/C9llEYxxl5x/)寫的

4. What city have you primarily lived in for the past three months?  
A: San Diego  
這篇[Instagram貼文](https://www.instagram.com/p/C9lmMxHRRrU/?img_index=1)寫說剛從學校回到家，代表過去3個月應該都在學校附近生活，而Instagram自我介紹透露出學校是UCSD，搜尋後發現在San Diego  
![image](https://hackmd.io/_uploads/HyI5VcsOR.png)

5. In what city did you grow up?  
A: Phoenix  
根據他的公開資訊得知：

Home**↔️**NYC:2139英里(資訊來自google日曆)  
![image (5)](https://hackmd.io/_uploads/H16JcYodC.png)

Home**↔️**西雅圖:1114英里(資訊來自github)  
![image](https://hackmd.io/_uploads/SJULe00_R.png)

Home**↔️**學校UCSD:300英里(資訊來自Instagram)  
![image](https://hackmd.io/_uploads/B1c37coO0.png)

- 以下三角定位的部分是由隊友完成的：

找不到適合的工具來三點定位，於是打開google地球，玩了一下量測發現是單位是**公尺**，透過ChatGPT先把隊友提供的線索轉換成公尺。  
![meter](https://hackmd.io/_uploads/HkEUoKiOC.png)

首先點紐約市拉一條約3436000公尺的線(大概就好不用太準只是用來找方向的)  
![image (6)](https://hackmd.io/_uploads/B14TCtjdC.png)

然後搜尋UCSD，從UCSD拉一條大概接近1787000公尺多的線，區域應該是在亞利桑那州，而且鳳凰城很靠近量測點，所以猜測是鳳凰城。  
![USD](https://hackmd.io/_uploads/ryMRAKi_0.png)

最後從西雅圖那個點拉一條482000m的線，確實在亞利桑那州範圍內，接著找出距離最接近1,787,065m位置的城市，看了一下鳳凰城，把量測點拉過去驗證，符合猜測。  
![USD1](https://hackmd.io/_uploads/rJAA0Ko_0.png)

6. What is the name of your favorite poet?  
A: Robert Frost  
從他Instagram帳戶追蹤的對象看出來的，每個都跟Robert Frost有關  
![image](https://hackmd.io/_uploads/SJo9-5juA.png)

7. What was the make and model of your first car?  
A: Honda Civic  
這篇[Instagram貼文](https://www.instagram.com/p/C9lmMxHRRrU/?img_index=1)最後有寫 #firstcar，對圖片使用google智慧鏡頭即可搜尋出車款  
![image](https://hackmd.io/_uploads/rkwMx9jd0.png)

8. In what year was your father born?  
A: 1981  
上github搜尋terrence descartes只能找找到一個[帳戶](https://github.com/descartes1337)，查看他唯一的專案的commits裡的[redact sensitive info](https://github.com/descartes1337/birthday-card/commit/e6f565a35fd10136647336780731a4d19aabfac7)會發現他爸43歲，2024-43=1981  
![image](https://hackmd.io/_uploads/H118gciOA.png)

9. What is your mother's maiden name?  
A: Jackson  
![image](https://hackmd.io/_uploads/ryJhgcodA.png)  
從此篇[LinkedIn貼文](https://www.linkedin.com/feed/update/urn:li:activity:7219899411361882112/)中得知她媽媽姓名為Amelia Jackson Descartes，根據美國冠夫姓的方式，會把夫姓放後面，引此婚前姓氏為Jackson

10. At what company do you work at?  
A: Cohort Calculations  
他的LinkedIn寫的：  
![螢幕擷取畫面 2024-07-22 153643](https://hackmd.io/_uploads/SyQQ3KjuA.png)

11. In what city did you go on vacation last summer?  
A: Saint Paul  
![image](https://hackmd.io/_uploads/SkU03YoO0.png)  
搜尋[此貼文](https://www.linkedin.com/feed/update/urn:li:activity:7220221551969759232/)第二張圖片左上角的地點的所在地即可得知答案

12. What are you supposed to do on August 21?  
A: Drop off top secret information  
在他的google日曆中察看8/21日的行程即可得知答案  
![image](https://hackmd.io/_uploads/H1CQ0tjOR.png)

13. Who was your boss in your first job?  
A: Farmer Johnson  
在他的LinkedIn中寫的第一份工作的公司名稱的一部分即是答案  
![image](https://hackmd.io/_uploads/Byp30Ksd0.png)

最後填完所有資料按下**submit**就會跳到flag頁面  
![flag](https://hackmd.io/_uploads/HkpDlciOR.png)


### dog-mom
> https://cybersharing.net/s/4f1dbcbb01b5f98d  
![image (3)](https://hackmd.io/_uploads/HyeAR9i_C.jpg)

搜尋圖片其中一個地方後google辨識出地點了  
![螢幕擷取畫面 2024-07-22 162256 (1)](https://hackmd.io/_uploads/SJL6PqoOR.png)

接著在google地球上搜尋此地點後稍微逛一下附近即可找到題目圖片左下方的地點  
![螢幕擷取畫面 2024-07-22 163540](https://hackmd.io/_uploads/HJVw5cj_R.png)

根據拍攝角度來看應該是在有一定高度的地方拍的，大概率是在學習大教堂上  
![dog mon 2](https://hackmd.io/_uploads/SJaeRcoO0.jpg)

盡可能還原拍攝角度了XD  
![dog mon](https://hackmd.io/_uploads/SJfOaqouC.jpg)


### playful-puppy
> https://cybersharing.net/s/1b3a43db9cb73c3d

題目給的是一個minecraft地圖檔，稍微翻一下後在```playful-puppy\world\datapacks\Dog data\data\test\function```裡發現了一個名為```dog.mcfunction```的檔案，打開來發現是一堆生成狗(在minecraft裡是狼)的指令，語法如下：  
```
summon minecraft:wolf ~ ~ ~ {CustomName:dog name,Owner:cleverbear57,CustomNameVisible:0,variant:black,CollarColor:15}
```  

接著把```playful-puppy\world```丟到你的minecraft資料夾的```saves```底下，在遊戲中打開它，會發現一堆狗  
![2024-07-25_16.23.27](https://hackmd.io/_uploads/SkLVZc1FR.jpg)

所以猜測```dog.mcfunction```生成的都不是他的狗，把裡面的生成指令改成Kill指令把多餘的狗殺掉後就會剩下他的狗了。

指令改成：    
```
kill @e[type=minecraft:wolf,name="dog name"]
```  
 
接著進到遊戲中按下T，執行```/reload```，再執行```/function test:dog```，即可消滅多餘的狗，就可以找到他的狗了，接著把名稱填入ictf{``` ```}內即可  
![2024-07-21_14.14.03](https://hackmd.io/_uploads/SkVHn2yt0.jpg)

事後看[官方writeup](https://github.com/ImaginaryCTF/ImaginaryCTF-2024-Challenges-Public/tree/main/Forensics/playful-puppy)發現根本不是這樣解，是出題者忘了把```dog.mcfunction```刪掉了www

正常來講應該是要從題目給的圖片中看出項圈是藍色的，再透過google搜尋發現決定項圈顏色的標籤是CollarColor，而藍色的ID是11，再透過[NBTExplorer](https://www.minecraftforum.net/forums/mapping-and-modding-java-edition/minecraft-tools/1262665-nbtexplorer-nbt-editor-for-windows-and-mac)打開地圖檔，按下<kbd>Ctrl</kbd>+<kbd>F</kbd>，Name處填入CollarColor，Value處填入11，按下Find後即可在```CollarColor: 11```底下看到```CustomName: "6ed247d7539bb3bf"```