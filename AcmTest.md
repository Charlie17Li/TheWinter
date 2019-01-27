### 2018年
题目
#### 1 - 分数
> 1/1 + 1/2 + 1/4 + 1/8 + 1/16 + ....
> 每项是前一项的一半，如果一共有20项,求这个和是多少，结果用分数表示出来。

#### 2 - 星期一

> 整个20世纪（1901年1月1日至2000年12月31日之间），一共有多少个星期一？


题解

#### 分数
等比数列求和即可：1048575/524288

#### 星期一
**闰年：**每年有385.2422天，也即每过4年，就会少一天，所以四年一闰，
但还是有误差，所以又有400年再闰。这样每年365.2425天，一万年只差3天.  
(集合问题) 1~10000有多少个闰年？

本题的第一想法，算出2000年12月31日星期几，然后算出整个区间有多少天.
```cpp
int getLeapYear(int RangeL, int RangeR){
    int cnt = 0;
    for(int i = RangeL; i <= RangeR; i++){
        if(i % 400 == 0){
            cnt++;
            continue;
        }
        if(i % 4 == 0 && i % 100 != 0){
            cnt++;
            continue;
        }
    }
    return cnt;
}
int getDays(int RangeL, int RangeR){
    int sumDays = (RangeR - RangeL + 1) * 365;
    sumDays += getLeapYear(RangeL, RangeR);
    return sumDays;
}//自此问题解决
```



