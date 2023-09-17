---
layout:     post
title:      真正的 Portable 軟件製作—TurboStudio
subtitle:   我.... 才不會告訴你做便攜這麼簡單
date:       2023-09-18
author:     TQGX
header-img: img/cat.jpg
catalog:   true
tags: 無聊 2023
---

基於上篇... [關於真正的 Portable 軟件原理](https://tqgx.github.io/2023/09/17/%E9%97%9C%E6%96%BC%E7%9C%9F%E6%AD%A3%E7%9A%84-Portable-%E8%BB%9F%E4%BB%B6%E5%8E%9F%E7%90%86/)
我想大家可以很好分清楚便攜軟件的重要性了。當然，我自己也有偷懶一下，這篇文章將會教你如何真正做TurboStudio的軟件，~~從入門到入土~~

- 本篇文章將會用TurboStudio 19.6.1208 版本作為教學，需要下載請到[OpenTools](https://github.com/tqgx/OpenTools/releases/tag/Database-All-Tools)
- 找到Turbo.Studio.19.6.1208.28v11.7z 即可下載，壓縮密碼已附在文章上。那就讓我們正式開始吧
- 製作TurboStudio的便攜軟件，建議使用虛擬機。

## 本篇文章將會用最快速的方式教學：

快照一，先讓我們把Capture Before的動畫跑完

![img](https://github.com/tqgx/tqgx.github.io/blob/master/img/TQGX/T1.png?raw=true)

## 參數設定：

這個步驟主要是將所有資料夾、註冊表，放在一個資料夾內，若沒做這個步驟，你的Portable軟件就相當於沒做到Settings→ 
![img](https://github.com/tqgx/tqgx.github.io/blob/master/img/TQGX/T4.png?raw=true)

- 看到我輸入@APPDIR@\Data的地方，請跟著輸入
- 將Project Type更改成 Standalone/ISV Application (exe)
- 句選 Compress payload (是為了壓縮）
- 句選 Shutdown process tree on root process exit （可以正常關閉檔案）


## 禁止軟件使用網路：
- 到Network→DNS，
- 看到Hostname or IP Address，輸入：*
- 看到Redlirect 輸入：0.0.0.0
- 
![img](https://github.com/tqgx/tqgx.github.io/blob/master/img/TQGX/T5.png?raw=true)


讓我們轉移到 IP Restrictions

![img](https://github.com/tqgx/tqgx.github.io/blob/master/img/TQGX/T6.png?raw=true)

- 在Route 輸入：*
- 在Type 輸入：Deny 

__提醒：如果軟件要用網路，把上面的參數刪除即可__

## 虛擬機快照 （以上步驟做完，這可以加快製作速度）
在下面圖片使用快照的是Vmware 15 版本，拍了快照之後，我們每次都從這個地方回滾，因為參數都設置好了，就會方便超多！！
![img](https://github.com/tqgx/tqgx.github.io/blob/master/img/TQGX/T7.png?raw=true)


### 安裝軟件： 
我們安裝軟件吧

![img](https://github.com/tqgx/tqgx.github.io/blob/master/img/TQGX/T2.png?raw=true)

- 安裝好之後，在這個步驟將軟件的所有參數設定好，或是激活器，許可證輸一輸，反正就是把所有設定做一做
- 完成好之後，我們再到下個步驟Capture and Diff

![img](https://github.com/tqgx/tqgx.github.io/blob/master/img/TQGX/T3.png?raw=true)

### 隔離資料夾：
這個步驟是為了將資料夾和註冊表，不會跑到外面一定要做的事（如果你確保這些檔案都不會跑出來，可以全設隔離也沒關係）
- 反正簡單說就是看到哪裡有資料夾，哪邊就設Write Copy、在下圖當中我設置都是常見的
- 資料夾隔離：
![img](https://github.com/tqgx/tqgx.github.io/blob/master/img/TQGX/T8.png?raw=true)
![img](https://github.com/tqgx/tqgx.github.io/blob/master/img/TQGX/T9.png?raw=true)

### 隔離註冊表：
- 註冊表全設即可 （原因很簡單，不要自己亂刪。到時候註冊表沒包在一個資料夾，會跑出去）
![img](https://github.com/tqgx/tqgx.github.io/blob/master/img/TQGX/T10.png?raw=true)

### 選擇軟件的開啟路徑（選擇.exe）
- 這個地方是為了讓你選擇軟件的正確開啟，譬如我們選擇.lnk，這就是路徑檔案，選擇.exe才是正確的....
![img](https://github.com/tqgx/tqgx.github.io/blob/master/img/TQGX/T10.png?raw=true)


# Build 製作完成！
![img](https://github.com/tqgx/tqgx.github.io/blob/master/img/TQGX/T12.png?raw=true)
![img](https://github.com/tqgx/tqgx.github.io/blob/master/img/TQGX/T13.png?raw=true)

- 享受勝利的果實吧！
- 你看，如此乾淨全部東西都在一個資料夾，我們可以用MasterSeeker做檢查喔！



BYE :) 這篇文章從2023年9月18號 0:00開始撰寫到 凌晨的1:55分
