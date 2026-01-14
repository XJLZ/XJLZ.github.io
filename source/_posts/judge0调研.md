---
title: Judge0调研
date: 2025-11-10 15:32:00
tags:
- Judge0
---



### 项目： https://github.com/judge0/judge0/releases/tag/v1.13.1



#### Deployment Steps

1. Download and extract the release archive:

```
wget https://github.com/judge0/judge0/releases/download/v1.13.1/judge0-v1.13.1.zip
unzip judge0-v1.13.1.zip
```



1. Visit [this website](https://www.random.org/passwords/?num=1&len=32&format=plain&rnd=new) to generate a random password.
2. Use the generated password to update the variable `REDIS_PASSWORD` in the `judge0.conf` file.
3. Visit again [this website](https://www.random.org/passwords/?num=1&len=32&format=plain&rnd=new) to generate another random password.
4. Use the generated password to update the variable `POSTGRES_PASSWORD` in the `judge0.conf` file.
5. Run all services and wait a few seconds until everything is initialized:

```
cd judge0-v1.13.1
docker-compose up -d db redis
sleep 10s
docker-compose up -d
sleep 5s
```

Your instance of Judge0 CE v1.13.1 is now up and running; visit docs at `http://<IP ADDRESS OF YOUR SERVER>:2358/docs`.

---
base64解码网址：https://base64.us/
---



# C

```json
{
    "id": 48,
    "name": "C (GCC 7.4.0)"
},
{
    "id": 49,
    "name": "C (GCC 8.3.0)"
},
{
    "id": 50,
    "name": "C (GCC 9.2.0)"
}
```



```c
#include <stdio.h>   // 标准输入输出库
#include <stdlib.h>  // 标准库，用于 exit() 函数

// 函数声明
void printWelcomeMessage();
int add(int a, int b);
void printArray(int arr[], int size);

int main() {
    // 欢迎信息
    printWelcomeMessage();
    
    // 基本数据类型
    int num1 = 1;
    int num1 = 3;
    // 条件判断
    if (num1 == num2) {
        printf("两个数相等!\n");
    } else if (num1 > num2) {
        printf("第一个数大于第二个数\n");
    } else {
        printf("第一个数小于第二个数\n");
    }

    // 调用函数：加法运算
    int result = add(num1, num2);
    printf("两个数的和是: %d\n", result);

    // 数组操作
    int arr[] = {1, 2, 3, 4, 5};  // 定义一个数组
    printf("数组的元素: ");
    printArray(arr, 5);  // 打印数组中的元素

    // 循环：打印1到10
    printf("1到10的数字: ");
    for (int i = 1; i <= 10; i++) {
        printf("%d ", i);
    }
    printf("\n");

    // 程序结束
    printf("感谢使用C语言教程!\n");
    return 0;  // 程序正常结束
}

// 打印欢迎信息的函数
void printWelcomeMessage() {
    printf("欢迎来到C语言基础教学!\n");
    printf("这是一个简单的C语言示例程序。\n\n");
}

// 加法函数
int add(int a, int b) {
    return a + b;
}

// 打印数组元素的函数
void printArray(int arr[], int size) {
    for (int i = 0; i < size; i++) {
        printf("%d ", arr[i]);
    }
    printf("\n");
}

```

## 编码

```
I2luY2x1ZGUgPHN0ZGlvLmg+ICAgLy8g5qCH5YeG6L6T5YWl6L6T5Ye65bqTCiNpbmNsdWRlIDxzdGRsaWIuaD4gIC8vIOagh+WHhuW6k++8jOeUqOS6jiBleGl0KCkg5Ye95pWwCgovLyDlh73mlbDlo7DmmI4Kdm9pZCBwcmludFdlbGNvbWVNZXNzYWdlKCk7CmludCBhZGQoaW50IGEsIGludCBiKTsKdm9pZCBwcmludEFycmF5KGludCBhcnJbXSwgaW50IHNpemUpOwoKaW50IG1haW4oKSB7CiAgICAvLyDmrKLov47kv6Hmga8KICAgIHByaW50V2VsY29tZU1lc3NhZ2UoKTsKICAgIAogICAgLy8g5Z+65pys5pWw5o2u57G75Z6LCiAgICBpbnQgbnVtMSA9IDI7CiAgICBpbnQgbnVtMiA9IDQ7CiAgICBwcmludGYoIuivt+i+k+WFpeesrOS4gOS4quaVtOaVsDogIik7CiAgICBwcmludGYoIuivt+i+k+WFpeesrOS6jOS4quaVtOaVsDogIik7CgogICAgLy8g5p2h5Lu25Yik5patCiAgICBpZiAobnVtMSA9PSBudW0yKSB7CiAgICAgICAgcHJpbnRmKCLkuKTkuKrmlbDnm7jnrYkhXG4iKTsKICAgIH0gZWxzZSBpZiAobnVtMSA+IG51bTIpIHsKICAgICAgICBwcmludGYoIuesrOS4gOS4quaVsOWkp+S6juesrOS6jOS4quaVsFxuIik7CiAgICB9IGVsc2UgewogICAgICAgIHByaW50Zigi56ys5LiA5Liq5pWw5bCP5LqO56ys5LqM5Liq5pWwXG4iKTsKICAgIH0KCiAgICAvLyDosIPnlKjlh73mlbDvvJrliqDms5Xov5DnrpcKICAgIGludCByZXN1bHQgPSBhZGQobnVtMSwgbnVtMik7CiAgICBwcmludGYoIuS4pOS4quaVsOeahOWSjOaYrzogJWRcbiIsIHJlc3VsdCk7CgogICAgLy8g5pWw57uE5pON5L2cCiAgICBpbnQgYXJyW10gPSB7MSwgMiwgMywgNCwgNX07ICAvLyDlrprkuYnkuIDkuKrmlbDnu4QKICAgIHByaW50Zigi5pWw57uE55qE5YWD57SgOiAiKTsKICAgIHByaW50QXJyYXkoYXJyLCA1KTsgIC8vIOaJk+WNsOaVsOe7hOS4reeahOWFg+e0oAoKICAgIC8vIOW+queOr++8muaJk+WNsDHliLAxMAogICAgcHJpbnRmKCIx5YiwMTDnmoTmlbDlrZc6ICIpOwogICAgZm9yIChpbnQgaSA9IDE7IGkgPD0gMTA7IGkrKykgewogICAgICAgIHByaW50ZigiJWQgIiwgaSk7CiAgICB9CiAgICBwcmludGYoIlxuIik7CgogICAgLy8g56iL5bqP57uT5p2fCiAgICBwcmludGYoIuaEn+iwouS9v+eUqEPor63oqIDmlZnnqIshXG4iKTsKICAgIHJldHVybiAwOyAgLy8g56iL5bqP5q2j5bi457uT5p2fCn0KCi8vIOaJk+WNsOasoui/juS/oeaBr+eahOWHveaVsAp2b2lkIHByaW50V2VsY29tZU1lc3NhZ2UoKSB7CiAgICBwcmludGYoIuasoui/juadpeWIsEPor63oqIDln7rnoYDmlZnlraYhXG4iKTsKICAgIHByaW50Zigi6L+Z5piv5LiA5Liq566A5Y2V55qEQ+ivreiogOekuuS+i+eoi+W6j+OAglxuXG4iKTsKfQoKLy8g5Yqg5rOV5Ye95pWwCmludCBhZGQoaW50IGEsIGludCBiKSB7CiAgICByZXR1cm4gYSArIGI7Cn0KCi8vIOaJk+WNsOaVsOe7hOWFg+e0oOeahOWHveaVsAp2b2lkIHByaW50QXJyYXkoaW50IGFycltdLCBpbnQgc2l6ZSkgewogICAgZm9yIChpbnQgaSA9IDA7IGkgPCBzaXplOyBpKyspIHsKICAgICAgICBwcmludGYoIiVkICIsIGFycltpXSk7CiAgICB9CiAgICBwcmludGYoIlxuIik7Cn0=
```

## 结果输出

```
5qyi6L+O5p2l5YiwQ+ivreiogOWfuuehgOaVmeWtpiEK6L+Z5piv5LiA5Liq\n566A5Y2V55qEQ+ivreiogOekuuS+i+eoi+W6j+OAggoK6K+36L6T5YWl56ys\n5LiA5Liq5pW05pWwOiDor7fovpPlhaXnrKzkuozkuKrmlbTmlbA6IOesrOS4\ngOS4quaVsOWwj+S6juesrOS6jOS4quaVsArkuKTkuKrmlbDnmoTlkozmmK86\nIDYK5pWw57uE55qE5YWD57SgOiAxIDIgMyA0IDUgCjHliLAxMOeahOaVsOWt\nlzogMSAyIDMgNCA1IDYgNyA4IDkgMTAgCuaEn+iwouS9v+eUqEPor63oqIDm\nlZnnqIshCg==\n
```



# Java

```json
{
    "id": 62,
    "name": "Java (OpenJDK 13.0.1)"
}
```



```
import java.security.SecureRandom;
import java.util.Random;


public final class Main {
    public static final SecureRandom DEFAULT_NUMBER_GENERATOR = new SecureRandom();
    public static final char[] DEFAULT_ALPHABET = "0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ".toCharArray();
    public static final int DEFAULT_SIZE = 21;

    public static void main(String[] args) {
        System.out.println(randomNanoId());
    }
    public static String randomNanoId() {
        return randomNanoId(DEFAULT_NUMBER_GENERATOR, DEFAULT_ALPHABET, DEFAULT_SIZE);
    }

    public static String randomNanoIdWithSize(int size) {
        return randomNanoId(DEFAULT_NUMBER_GENERATOR, DEFAULT_ALPHABET, size);
    }

    public static String randomNanoId(final Random random, final char[] alphabet, final int size) {
        if (random == null) {
            throw new IllegalArgumentException("random cannot be null.");
        }
        if (alphabet == null) {
            throw new IllegalArgumentException("alphabet cannot be null.");
        }
        if (alphabet.length == 0 || alphabet.length >= 256) {
            throw new IllegalArgumentException("alphabet must contain between 1 and 255 symbols.");
        }
        if (size <= 0) {
            throw new IllegalArgumentException("size must be greater than zero.");
        }
        final int mask = (2 << (int) Math.floor(Math.log(alphabet.length - 1) / Math.log(2))) - 1;
        final int step = (int) Math.ceil(1.6 * mask * size / alphabet.length);

        final StringBuilder idBuilder = new StringBuilder(size);
        final byte[] bytes = new byte[step];

        while (true) {
            random.nextBytes(bytes);
            for (int i = 0; i < step; i++) {
                final int alphabetIndex = bytes[i] & mask;
                if (alphabetIndex < alphabet.length) {
                    idBuilder.append(alphabet[alphabetIndex]);
                    if (idBuilder.length() == size) {
                        return idBuilder.toString();
                    }
                }
            }
        }
    }
}

```

## 编码

```
aW1wb3J0IGphdmEuc2VjdXJpdHkuU2VjdXJlUmFuZG9tOwppbXBvcnQgamF2YS51dGlsLlJhbmRvbTsKCgpwdWJsaWMgZmluYWwgY2xhc3MgTWFpbiB7CiAgICBwdWJsaWMgc3RhdGljIGZpbmFsIFNlY3VyZVJhbmRvbSBERUZBVUxUX05VTUJFUl9HRU5FUkFUT1IgPSBuZXcgU2VjdXJlUmFuZG9tKCk7CiAgICBwdWJsaWMgc3RhdGljIGZpbmFsIGNoYXJbXSBERUZBVUxUX0FMUEhBQkVUID0gIjAxMjM0NTY3ODlhYmNkZWZnaGlqa2xtbm9wcXJzdHV2d3h5ekFCQ0RFRkdISUpLTE1OT1BRUlNUVVZXWFlaIi50b0NoYXJBcnJheSgpOwogICAgcHVibGljIHN0YXRpYyBmaW5hbCBpbnQgREVGQVVMVF9TSVpFID0gMjE7CgogICAgcHVibGljIHN0YXRpYyB2b2lkIG1haW4oU3RyaW5nW10gYXJncykgewogICAgICAgIFN5c3RlbS5vdXQucHJpbnRsbihyYW5kb21OYW5vSWQoKSk7CiAgICB9CiAgICBwdWJsaWMgc3RhdGljIFN0cmluZyByYW5kb21OYW5vSWQoKSB7CiAgICAgICAgcmV0dXJuIHJhbmRvbU5hbm9JZChERUZBVUxUX05VTUJFUl9HRU5FUkFUT1IsIERFRkFVTFRfQUxQSEFCRVQsIERFRkFVTFRfU0laRSk7CiAgICB9CgogICAgcHVibGljIHN0YXRpYyBTdHJpbmcgcmFuZG9tTmFub0lkV2l0aFNpemUoaW50IHNpemUpIHsKICAgICAgICByZXR1cm4gcmFuZG9tTmFub0lkKERFRkFVTFRfTlVNQkVSX0dFTkVSQVRPUiwgREVGQVVMVF9BTFBIQUJFVCwgc2l6ZSk7CiAgICB9CgogICAgcHVibGljIHN0YXRpYyBTdHJpbmcgcmFuZG9tTmFub0lkKGZpbmFsIFJhbmRvbSByYW5kb20sIGZpbmFsIGNoYXJbXSBhbHBoYWJldCwgZmluYWwgaW50IHNpemUpIHsKICAgICAgICBpZiAocmFuZG9tID09IG51bGwpIHsKICAgICAgICAgICAgdGhyb3cgbmV3IElsbGVnYWxBcmd1bWVudEV4Y2VwdGlvbigicmFuZG9tIGNhbm5vdCBiZSBudWxsLiIpOwogICAgICAgIH0KICAgICAgICBpZiAoYWxwaGFiZXQgPT0gbnVsbCkgewogICAgICAgICAgICB0aHJvdyBuZXcgSWxsZWdhbEFyZ3VtZW50RXhjZXB0aW9uKCJhbHBoYWJldCBjYW5ub3QgYmUgbnVsbC4iKTsKICAgICAgICB9CiAgICAgICAgaWYgKGFscGhhYmV0Lmxlbmd0aCA9PSAwIHx8IGFscGhhYmV0Lmxlbmd0aCA+PSAyNTYpIHsKICAgICAgICAgICAgdGhyb3cgbmV3IElsbGVnYWxBcmd1bWVudEV4Y2VwdGlvbigiYWxwaGFiZXQgbXVzdCBjb250YWluIGJldHdlZW4gMSBhbmQgMjU1IHN5bWJvbHMuIik7CiAgICAgICAgfQogICAgICAgIGlmIChzaXplIDw9IDApIHsKICAgICAgICAgICAgdGhyb3cgbmV3IElsbGVnYWxBcmd1bWVudEV4Y2VwdGlvbigic2l6ZSBtdXN0IGJlIGdyZWF0ZXIgdGhhbiB6ZXJvLiIpOwogICAgICAgIH0KICAgICAgICBmaW5hbCBpbnQgbWFzayA9ICgyIDw8IChpbnQpIE1hdGguZmxvb3IoTWF0aC5sb2coYWxwaGFiZXQubGVuZ3RoIC0gMSkgLyBNYXRoLmxvZygyKSkpIC0gMTsKICAgICAgICBmaW5hbCBpbnQgc3RlcCA9IChpbnQpIE1hdGguY2VpbCgxLjYgKiBtYXNrICogc2l6ZSAvIGFscGhhYmV0Lmxlbmd0aCk7CgogICAgICAgIGZpbmFsIFN0cmluZ0J1aWxkZXIgaWRCdWlsZGVyID0gbmV3IFN0cmluZ0J1aWxkZXIoc2l6ZSk7CiAgICAgICAgZmluYWwgYnl0ZVtdIGJ5dGVzID0gbmV3IGJ5dGVbc3RlcF07CgogICAgICAgIHdoaWxlICh0cnVlKSB7CiAgICAgICAgICAgIHJhbmRvbS5uZXh0Qnl0ZXMoYnl0ZXMpOwogICAgICAgICAgICBmb3IgKGludCBpID0gMDsgaSA8IHN0ZXA7IGkrKykgewogICAgICAgICAgICAgICAgZmluYWwgaW50IGFscGhhYmV0SW5kZXggPSBieXRlc1tpXSAmIG1hc2s7CiAgICAgICAgICAgICAgICBpZiAoYWxwaGFiZXRJbmRleCA8IGFscGhhYmV0Lmxlbmd0aCkgewogICAgICAgICAgICAgICAgICAgIGlkQnVpbGRlci5hcHBlbmQoYWxwaGFiZXRbYWxwaGFiZXRJbmRleF0pOwogICAgICAgICAgICAgICAgICAgIGlmIChpZEJ1aWxkZXIubGVuZ3RoKCkgPT0gc2l6ZSkgewogICAgICAgICAgICAgICAgICAgICAgICByZXR1cm4gaWRCdWlsZGVyLnRvU3RyaW5nKCk7CiAgICAgICAgICAgICAgICAgICAgfQogICAgICAgICAgICAgICAgfQogICAgICAgICAgICB9CiAgICAgICAgfQogICAgfQp9Cg==
```



## 结果输出

```
TnFCRGpBU1RiUWU4MzRUbk55T0UxCg==\n
```



# JavaScript

```json
{
    "id": 63,
    "name": "JavaScript (Node.js 12.14.0)"
}
```

```
// 教学函数：打印欢迎信息
function printWelcomeMessage() {
    console.log("欢迎来到 Node.js 基础教学!");
    console.log("这是一个简单的 Node.js 示例程序。\n");
}

// 加法函数
function add(a, b) {
    return a + b;
}

// 打印数组函数
function printArray(arr) {
    arr.forEach(item => {
        process.stdout.write(item + " ");  // 输出而不换行
    });
    console.log();  // 换行
}

// 主程序
function main(num1, num2) {
    // 欢迎信息
    printWelcomeMessage();

    // 条件判断
    if (num1 === num2) {
        console.log("两个数相等!");
    } else if (num1 > num2) {
        console.log("第一个数大于第二个数");
    } else {
        console.log("第一个数小于第二个数");
    }

    // 调用加法函数
    const result = add(num1, num2);
    console.log(`两个数的和是: ${result}`);

    // 数组操作
    const arr = [1, 2, 3, 4, 5];
    console.log("数组的元素: ");
    printArray(arr);

    // 循环打印 1 到 10
    console.log("1到10的数字: ");
    for (let i = 1; i <= 10; i++) {
        process.stdout.write(i + " ");  // 输出而不换行
    }
    console.log();  // 换行

    // 程序结束
    console.log("感谢使用 Node.js 教程!");
}

// 调用主程序，传递参数代替用户输入
main(5, 3);

```

## 编码

```
Ly8g5pWZ5a2m5Ye95pWw77ya5omT5Y2w5qyi6L+O5L+h5oGvCmZ1bmN0aW9uIHByaW50V2VsY29tZU1lc3NhZ2UoKSB7CiAgICBjb25zb2xlLmxvZygi5qyi6L+O5p2l5YiwIE5vZGUuanMg5Z+656GA5pWZ5a2mISIpOwogICAgY29uc29sZS5sb2coIui/meaYr+S4gOS4queugOWNleeahCBOb2RlLmpzIOekuuS+i+eoi+W6j+OAglxuIik7Cn0KCi8vIOWKoOazleWHveaVsApmdW5jdGlvbiBhZGQoYSwgYikgewogICAgcmV0dXJuIGEgKyBiOwp9CgovLyDmiZPljbDmlbDnu4Tlh73mlbAKZnVuY3Rpb24gcHJpbnRBcnJheShhcnIpIHsKICAgIGFyci5mb3JFYWNoKGl0ZW0gPT4gewogICAgICAgIHByb2Nlc3Muc3Rkb3V0LndyaXRlKGl0ZW0gKyAiICIpOyAgLy8g6L6T5Ye66ICM5LiN5o2i6KGMCiAgICB9KTsKICAgIGNvbnNvbGUubG9nKCk7ICAvLyDmjaLooYwKfQoKLy8g5Li756iL5bqPCmZ1bmN0aW9uIG1haW4obnVtMSwgbnVtMikgewogICAgLy8g5qyi6L+O5L+h5oGvCiAgICBwcmludFdlbGNvbWVNZXNzYWdlKCk7CgogICAgLy8g5p2h5Lu25Yik5patCiAgICBpZiAobnVtMSA9PT0gbnVtMikgewogICAgICAgIGNvbnNvbGUubG9nKCLkuKTkuKrmlbDnm7jnrYkhIik7CiAgICB9IGVsc2UgaWYgKG51bTEgPiBudW0yKSB7CiAgICAgICAgY29uc29sZS5sb2coIuesrOS4gOS4quaVsOWkp+S6juesrOS6jOS4quaVsCIpOwogICAgfSBlbHNlIHsKICAgICAgICBjb25zb2xlLmxvZygi56ys5LiA5Liq5pWw5bCP5LqO56ys5LqM5Liq5pWwIik7CiAgICB9CgogICAgLy8g6LCD55So5Yqg5rOV5Ye95pWwCiAgICBjb25zdCByZXN1bHQgPSBhZGQobnVtMSwgbnVtMik7CiAgICBjb25zb2xlLmxvZyhg5Lik5Liq5pWw55qE5ZKM5pivOiAke3Jlc3VsdH1gKTsKCiAgICAvLyDmlbDnu4Tmk43kvZwKICAgIGNvbnN0IGFyciA9IFsxLCAyLCAzLCA0LCA1XTsKICAgIGNvbnNvbGUubG9nKCLmlbDnu4TnmoTlhYPntKA6ICIpOwogICAgcHJpbnRBcnJheShhcnIpOwoKICAgIC8vIOW+queOr+aJk+WNsCAxIOWIsCAxMAogICAgY29uc29sZS5sb2coIjHliLAxMOeahOaVsOWtlzogIik7CiAgICBmb3IgKGxldCBpID0gMTsgaSA8PSAxMDsgaSsrKSB7CiAgICAgICAgcHJvY2Vzcy5zdGRvdXQud3JpdGUoaSArICIgIik7ICAvLyDovpPlh7rogIzkuI3mjaLooYwKICAgIH0KICAgIGNvbnNvbGUubG9nKCk7ICAvLyDmjaLooYwKCiAgICAvLyDnqIvluo/nu5PmnZ8KICAgIGNvbnNvbGUubG9nKCLmhJ/osKLkvb/nlKggTm9kZS5qcyDmlZnnqIshIik7Cn0KCi8vIOiwg+eUqOS4u+eoi+W6j++8jOS8oOmAkuWPguaVsOS7o+abv+eUqOaIt+i+k+WFpQptYWluKDUsIDMpOwo=
```

## 输出结果

```
5qyi6L+O5p2l5YiwIE5vZGUuanMg5Z+656GA5pWZ5a2mIQrov5nmmK/kuIDk\nuKrnroDljZXnmoQgTm9kZS5qcyDnpLrkvovnqIvluo/jgIIKCuesrOS4gOS4\nquaVsOWkp+S6juesrOS6jOS4quaVsArkuKTkuKrmlbDnmoTlkozmmK86IDgK\n5pWw57uE55qE5YWD57SgOiAKMSAyIDMgNCA1IAox5YiwMTDnmoTmlbDlrZc6\nIAoxIDIgMyA0IDUgNiA3IDggOSAxMCAK5oSf6LCi5L2/55SoIE5vZGUuanMg\n5pWZ56iLIQo=\n
```



# Python

```
{
    "id": 70,
    "name": "Python (2.7.17)"
},
{
    "id": 71,
    "name": "Python (3.8.1)"
}
```

**Python (3.8.1)**

```
# 欢迎信息函数
def print_welcome_message():
    print("欢迎来到 Python 基础教学!")
    print("这是一个简单的 Python 示例程序。\n")

# 加法函数
def add(a, b):
    return a + b

# 打印列表函数
def print_list(lst):
    for item in lst:
        print(item, end=" ")
    print()  # 换行

# 主程序
def main():
    # 欢迎信息
    print_welcome_message()

    # 基本输入输出
    num1 = 1
    num2 = 2

    # 条件判断
    if num1 == num2:
        print("两个数相等!")
    elif num1 > num2:
        print("第一个数大于第二个数")
    else:
        print("第一个数小于第二个数")

    # 调用加法函数
    result = add(num1, num2)
    print(f"两个数的和是: {result}")

    # 列表操作
    my_list = [1, 2, 3, 4, 5]
    print("列表的元素: ", end="")
    print_list(my_list)  # 打印列表中的元素

    # 循环打印 1 到 10
    print("1到10的数字: ", end="")
    for i in range(1, 11):
        print(i, end=" ")
    print()  # 换行

    # 程序结束
    print("感谢使用 Python 教程!")

# 调用主程序
if __name__ == "__main__":
    main()

```

## 编码

```
IyDmrKLov47kv6Hmga/lh73mlbAKZGVmIHByaW50X3dlbGNvbWVfbWVzc2FnZSgpOgogICAgcHJpbnQoIuasoui/juadpeWIsCBQeXRob24g5Z+656GA5pWZ5a2mISIpCiAgICBwcmludCgi6L+Z5piv5LiA5Liq566A5Y2V55qEIFB5dGhvbiDnpLrkvovnqIvluo/jgIJcbiIpCgojIOWKoOazleWHveaVsApkZWYgYWRkKGEsIGIpOgogICAgcmV0dXJuIGEgKyBiCgojIOaJk+WNsOWIl+ihqOWHveaVsApkZWYgcHJpbnRfbGlzdChsc3QpOgogICAgZm9yIGl0ZW0gaW4gbHN0OgogICAgICAgIHByaW50KGl0ZW0sIGVuZD0iICIpCiAgICBwcmludCgpICAjIOaNouihjAoKIyDkuLvnqIvluo8KZGVmIG1haW4oKToKICAgICMg5qyi6L+O5L+h5oGvCiAgICBwcmludF93ZWxjb21lX21lc3NhZ2UoKQoKICAgICMg5Z+65pys6L6T5YWl6L6T5Ye6CiAgICBudW0xID0gMQogICAgbnVtMiA9IDIKCiAgICAjIOadoeS7tuWIpOaWrQogICAgaWYgbnVtMSA9PSBudW0yOgogICAgICAgIHByaW50KCLkuKTkuKrmlbDnm7jnrYkhIikKICAgIGVsaWYgbnVtMSA+IG51bTI6CiAgICAgICAgcHJpbnQoIuesrOS4gOS4quaVsOWkp+S6juesrOS6jOS4quaVsCIpCiAgICBlbHNlOgogICAgICAgIHByaW50KCLnrKzkuIDkuKrmlbDlsI/kuo7nrKzkuozkuKrmlbAiKQoKICAgICMg6LCD55So5Yqg5rOV5Ye95pWwCiAgICByZXN1bHQgPSBhZGQobnVtMSwgbnVtMikKICAgIHByaW50KGYi5Lik5Liq5pWw55qE5ZKM5pivOiB7cmVzdWx0fSIpCgogICAgIyDliJfooajmk43kvZwKICAgIG15X2xpc3QgPSBbMSwgMiwgMywgNCwgNV0KICAgIHByaW50KCLliJfooajnmoTlhYPntKA6ICIsIGVuZD0iIikKICAgIHByaW50X2xpc3QobXlfbGlzdCkgICMg5omT5Y2w5YiX6KGo5Lit55qE5YWD57SgCgogICAgIyDlvqrnjq/miZPljbAgMSDliLAgMTAKICAgIHByaW50KCIx5YiwMTDnmoTmlbDlrZc6ICIsIGVuZD0iIikKICAgIGZvciBpIGluIHJhbmdlKDEsIDExKToKICAgICAgICBwcmludChpLCBlbmQ9IiAiKQogICAgcHJpbnQoKSAgIyDmjaLooYwKCiAgICAjIOeoi+W6j+e7k+adnwogICAgcHJpbnQoIuaEn+iwouS9v+eUqCBQeXRob24g5pWZ56iLISIpCgojIOiwg+eUqOS4u+eoi+W6jwppZiBfX25hbWVfXyA9PSAiX19tYWluX18iOgogICAgbWFpbigpCg==
```

## 结果输出

```
5qyi6L+O5p2l5YiwIFB5dGhvbiDln7rnoYDmlZnlraYhCui/meaYr+S4gOS4\nqueugOWNleeahCBQeXRob24g56S65L6L56iL5bqP44CCCgrnrKzkuIDkuKrm\nlbDlsI/kuo7nrKzkuozkuKrmlbAK5Lik5Liq5pWw55qE5ZKM5pivOiAzCuWI\nl+ihqOeahOWFg+e0oDogMSAyIDMgNCA1IAox5YiwMTDnmoTmlbDlrZc6IDEg\nMiAzIDQgNSA2IDcgOCA5IDEwIArmhJ/osKLkvb/nlKggUHl0aG9uIOaVmeeo\niyEK\n
```

**Python (2.7.17)**

```
# -*- coding: utf-8 -*-

# 欢迎信息函数
def print_welcome_message():
    print "欢迎来到 Python 基础教学!"
    print "这是一个简单的 Python 示例程序。\n"

# 加法函数
def add(a, b):
    return a + b

# 打印列表函数
def print_list(lst):
    for item in lst:
        print item,  # 使用逗号避免换行
    print  # 输出一个换行

# 主程序
def main():
    # 欢迎信息
    print_welcome_message()

    # 基本输入输出
    num1 = 1
    num2 = 2

    # 条件判断
    if num1 == num2:
        print "两个数相等!"
    elif num1 > num2:
        print "第一个数大于第二个数"
    else:
        print "第一个数小于第二个数"

    # 调用加法函数
    result = add(num1, num2)
    print "两个数的和是: {}".format(result)

    # 列表操作
    my_list = [1, 2, 3, 4, 5]
    print "列表的元素: ",
    print_list(my_list)  # 打印列表中的元素

    # 循环打印 1 到 10
    print "1到10的数字: ",
    for i in range(1, 11):
        print i,  # 输出数字，不换行
    print  # 输出一个换行

    # 程序结束
    print "感谢使用 Python 教程!"

# 调用主程序
if __name__ == "__main__":
    main()

```

## 编码

```
IyAtKi0gY29kaW5nOiB1dGYtOCAtKi0KCiMg5qyi6L+O5L+h5oGv5Ye95pWwCmRlZiBwcmludF93ZWxjb21lX21lc3NhZ2UoKToKICAgIHByaW50ICLmrKLov47mnaXliLAgUHl0aG9uIOWfuuehgOaVmeWtpiEiCiAgICBwcmludCAi6L+Z5piv5LiA5Liq566A5Y2V55qEIFB5dGhvbiDnpLrkvovnqIvluo/jgIJcbiIKCiMg5Yqg5rOV5Ye95pWwCmRlZiBhZGQoYSwgYik6CiAgICByZXR1cm4gYSArIGIKCiMg5omT5Y2w5YiX6KGo5Ye95pWwCmRlZiBwcmludF9saXN0KGxzdCk6CiAgICBmb3IgaXRlbSBpbiBsc3Q6CiAgICAgICAgcHJpbnQgaXRlbSwgICMg5L2/55So6YCX5Y+36YG/5YWN5o2i6KGMCiAgICBwcmludCAgIyDovpPlh7rkuIDkuKrmjaLooYwKCiMg5Li756iL5bqPCmRlZiBtYWluKCk6CiAgICAjIOasoui/juS/oeaBrwogICAgcHJpbnRfd2VsY29tZV9tZXNzYWdlKCkKCiAgICAjIOWfuuacrOi+k+WFpei+k+WHugogICAgbnVtMSA9IDEKICAgIG51bTIgPSAyCgogICAgIyDmnaHku7bliKTmlq0KICAgIGlmIG51bTEgPT0gbnVtMjoKICAgICAgICBwcmludCAi5Lik5Liq5pWw55u4562JISIKICAgIGVsaWYgbnVtMSA+IG51bTI6CiAgICAgICAgcHJpbnQgIuesrOS4gOS4quaVsOWkp+S6juesrOS6jOS4quaVsCIKICAgIGVsc2U6CiAgICAgICAgcHJpbnQgIuesrOS4gOS4quaVsOWwj+S6juesrOS6jOS4quaVsCIKCiAgICAjIOiwg+eUqOWKoOazleWHveaVsAogICAgcmVzdWx0ID0gYWRkKG51bTEsIG51bTIpCiAgICBwcmludCAi5Lik5Liq5pWw55qE5ZKM5pivOiB7fSIuZm9ybWF0KHJlc3VsdCkKCiAgICAjIOWIl+ihqOaTjeS9nAogICAgbXlfbGlzdCA9IFsxLCAyLCAzLCA0LCA1XQogICAgcHJpbnQgIuWIl+ihqOeahOWFg+e0oDogIiwKICAgIHByaW50X2xpc3QobXlfbGlzdCkgICMg5omT5Y2w5YiX6KGo5Lit55qE5YWD57SgCgogICAgIyDlvqrnjq/miZPljbAgMSDliLAgMTAKICAgIHByaW50ICIx5YiwMTDnmoTmlbDlrZc6ICIsCiAgICBmb3IgaSBpbiByYW5nZSgxLCAxMSk6CiAgICAgICAgcHJpbnQgaSwgICMg6L6T5Ye65pWw5a2X77yM5LiN5o2i6KGMCiAgICBwcmludCAgIyDovpPlh7rkuIDkuKrmjaLooYwKCiAgICAjIOeoi+W6j+e7k+adnwogICAgcHJpbnQgIuaEn+iwouS9v+eUqCBQeXRob24g5pWZ56iLISIKCiMg6LCD55So5Li756iL5bqPCmlmIF9fbmFtZV9fID09ICJfX21haW5fXyI6CiAgICBtYWluKCkK
```

## 输出结果

```
5qyi6L+O5p2l5YiwIFB5dGhvbiDln7rnoYDmlZnlraYhCui/meaYr+S4gOS4\nqueugOWNleeahCBQeXRob24g56S65L6L56iL5bqP44CCCgrnrKzkuIDkuKrm\nlbDlsI/kuo7nrKzkuozkuKrmlbAK5Lik5Liq5pWw55qE5ZKM5pivOiAzCuWI\nl+ihqOeahOWFg+e0oDogIDEgMiAzIDQgNQox5YiwMTDnmoTmlbDlrZc6ICAx\nIDIgMyA0IDUgNiA3IDggOSAxMArmhJ/osKLkvb/nlKggUHl0aG9uIOaVmeeo\niyEK\n
```

# C++

```
{
    "id": 52,
    "name": "C++ (GCC 7.4.0)"
},
{
    "id": 53,
    "name": "C++ (GCC 8.3.0)"
},
{
    "id": 54,
    "name": "C++ (GCC 9.2.0)"
}
```

```
#include <iostream>
#include <vector>
using namespace std;

// 教学函数：打印欢迎信息
void printWelcomeMessage() {
    cout << "欢迎来到 C++ 基础教学!" << endl;
    cout << "这是一个简单的 C++ 示例程序。" << endl << endl;
}

// 加法函数
int add(int a, int b) {
    return a + b;
}

// 打印数组函数
void printArray(const vector<int>& arr) {
    for (int item : arr) {
        cout << item << " ";  // 输出而不换行
    }
    cout << endl;  // 换行
}

// 主程序
void mainFunction(int num1, int num2) {
    // 欢迎信息
    printWelcomeMessage();

    // 条件判断
    if (num1 == num2) {
        cout << "两个数相等!" << endl;
    } else if (num1 > num2) {
        cout << "第一个数大于第二个数" << endl;
    } else {
        cout << "第一个数小于第二个数" << endl;
    }

    // 调用加法函数
    int result = add(num1, num2);
    cout << "两个数的和是: " << result << endl;

    // 数组操作
    vector<int> arr = {1, 2, 3, 4, 5};
    cout << "数组的元素: ";
    printArray(arr);

    // 循环打印 1 到 10
    cout << "1到10的数字: ";
    for (int i = 1; i <= 10; i++) {
        cout << i << " ";  // 输出而不换行
    }
    cout << endl;  // 换行

    // 程序结束
    cout << "感谢使用 C++ 教程!" << endl;
}

int main() {
    // 调用主程序，传递参数代替用户输入
    mainFunction(5, 3);
    return 0;
}

```

## 编码

```
I2luY2x1ZGUgPGlvc3RyZWFtPgojaW5jbHVkZSA8dmVjdG9yPgp1c2luZyBuYW1lc3BhY2Ugc3RkOwoKLy8g5pWZ5a2m5Ye95pWw77ya5omT5Y2w5qyi6L+O5L+h5oGvCnZvaWQgcHJpbnRXZWxjb21lTWVzc2FnZSgpIHsKICAgIGNvdXQgPDwgIuasoui/juadpeWIsCBDKysg5Z+656GA5pWZ5a2mISIgPDwgZW5kbDsKICAgIGNvdXQgPDwgIui/meaYr+S4gOS4queugOWNleeahCBDKysg56S65L6L56iL5bqP44CCIiA8PCBlbmRsIDw8IGVuZGw7Cn0KCi8vIOWKoOazleWHveaVsAppbnQgYWRkKGludCBhLCBpbnQgYikgewogICAgcmV0dXJuIGEgKyBiOwp9CgovLyDmiZPljbDmlbDnu4Tlh73mlbAKdm9pZCBwcmludEFycmF5KGNvbnN0IHZlY3RvcjxpbnQ+JiBhcnIpIHsKICAgIGZvciAoaW50IGl0ZW0gOiBhcnIpIHsKICAgICAgICBjb3V0IDw8IGl0ZW0gPDwgIiAiOyAgLy8g6L6T5Ye66ICM5LiN5o2i6KGMCiAgICB9CiAgICBjb3V0IDw8IGVuZGw7ICAvLyDmjaLooYwKfQoKLy8g5Li756iL5bqPCnZvaWQgbWFpbkZ1bmN0aW9uKGludCBudW0xLCBpbnQgbnVtMikgewogICAgLy8g5qyi6L+O5L+h5oGvCiAgICBwcmludFdlbGNvbWVNZXNzYWdlKCk7CgogICAgLy8g5p2h5Lu25Yik5patCiAgICBpZiAobnVtMSA9PSBudW0yKSB7CiAgICAgICAgY291dCA8PCAi5Lik5Liq5pWw55u4562JISIgPDwgZW5kbDsKICAgIH0gZWxzZSBpZiAobnVtMSA+IG51bTIpIHsKICAgICAgICBjb3V0IDw8ICLnrKzkuIDkuKrmlbDlpKfkuo7nrKzkuozkuKrmlbAiIDw8IGVuZGw7CiAgICB9IGVsc2UgewogICAgICAgIGNvdXQgPDwgIuesrOS4gOS4quaVsOWwj+S6juesrOS6jOS4quaVsCIgPDwgZW5kbDsKICAgIH0KCiAgICAvLyDosIPnlKjliqDms5Xlh73mlbAKICAgIGludCByZXN1bHQgPSBhZGQobnVtMSwgbnVtMik7CiAgICBjb3V0IDw8ICLkuKTkuKrmlbDnmoTlkozmmK86ICIgPDwgcmVzdWx0IDw8IGVuZGw7CgogICAgLy8g5pWw57uE5pON5L2cCiAgICB2ZWN0b3I8aW50PiBhcnIgPSB7MSwgMiwgMywgNCwgNX07CiAgICBjb3V0IDw8ICLmlbDnu4TnmoTlhYPntKA6ICI7CiAgICBwcmludEFycmF5KGFycik7CgogICAgLy8g5b6q546v5omT5Y2wIDEg5YiwIDEwCiAgICBjb3V0IDw8ICIx5YiwMTDnmoTmlbDlrZc6ICI7CiAgICBmb3IgKGludCBpID0gMTsgaSA8PSAxMDsgaSsrKSB7CiAgICAgICAgY291dCA8PCBpIDw8ICIgIjsgIC8vIOi+k+WHuuiAjOS4jeaNouihjAogICAgfQogICAgY291dCA8PCBlbmRsOyAgLy8g5o2i6KGMCgogICAgLy8g56iL5bqP57uT5p2fCiAgICBjb3V0IDw8ICLmhJ/osKLkvb/nlKggQysrIOaVmeeoiyEiIDw8IGVuZGw7Cn0KCmludCBtYWluKCkgewogICAgLy8g6LCD55So5Li756iL5bqP77yM5Lyg6YCS5Y+C5pWw5Luj5pu/55So5oi36L6T5YWlCiAgICBtYWluRnVuY3Rpb24oNSwgMyk7CiAgICByZXR1cm4gMDsKfQo=
```

## 输出结果

```
5qyi6L+O5p2l5YiwIEMrKyDln7rnoYDmlZnlraYhCui/meaYr+S4gOS4queu\ngOWNleeahCBDKysg56S65L6L56iL5bqP44CCCgrnrKzkuIDkuKrmlbDlpKfk\nuo7nrKzkuozkuKrmlbAK5Lik5Liq5pWw55qE5ZKM5pivOiA4CuaVsOe7hOea\nhOWFg+e0oDogMSAyIDMgNCA1IAox5YiwMTDnmoTmlbDlrZc6IDEgMiAzIDQg\nNSA2IDcgOCA5IDEwIArmhJ/osKLkvb/nlKggQysrIOaVmeeoiyEK\n
```

# Go

```
{
    "id": 60,
    "name": "Go (1.13.5)"
}
```

```
package main

import "fmt"

// 教学函数：打印欢迎信息
func printWelcomeMessage() {
    fmt.Println("欢迎来到 Go 语言基础教学!")
    fmt.Println("这是一个简单的 Go 示例程序。\n")
}

// 加法函数
func add(a, b int) int {
    return a + b
}

// 打印数组函数
func printArray(arr []int) {
    for _, item := range arr {
        fmt.Print(item, " ")  // 输出而不换行
    }
    fmt.Println()  // 换行
}

// 主程序
func main() {
    // 欢迎信息
    printWelcomeMessage()

    // 直接传递参数进行条件判断
    num1 := 5
    num2 := 3

    // 条件判断
    if num1 == num2 {
        fmt.Println("两个数相等!")
    } else if num1 > num2 {
        fmt.Println("第一个数大于第二个数")
    } else {
        fmt.Println("第一个数小于第二个数")
    }

    // 调用加法函数
    result := add(num1, num2)
    fmt.Printf("两个数的和是: %d\n", result)

    // 数组操作
    arr := []int{1, 2, 3, 4, 5}
    fmt.Print("数组的元素: ")
    printArray(arr)

    // 循环打印 1 到 10
    fmt.Print("1到10的数字: ")
    for i := 1; i <= 10; i++ {
        fmt.Print(i, " ")  // 输出而不换行
    }
    fmt.Println()  // 换行

    // 程序结束
    fmt.Println("感谢使用 Go 语言教程!")
}

```

## 编码

```
cGFja2FnZSBtYWluCgppbXBvcnQgImZtdCIKCi8vIOaVmeWtpuWHveaVsO+8muaJk+WNsOasoui/juS/oeaBrwpmdW5jIHByaW50V2VsY29tZU1lc3NhZ2UoKSB7CiAgICBmbXQuUHJpbnRsbigi5qyi6L+O5p2l5YiwIEdvIOivreiogOWfuuehgOaVmeWtpiEiKQogICAgZm10LlByaW50bG4oIui/meaYr+S4gOS4queugOWNleeahCBHbyDnpLrkvovnqIvluo/jgIJcbiIpCn0KCi8vIOWKoOazleWHveaVsApmdW5jIGFkZChhLCBiIGludCkgaW50IHsKICAgIHJldHVybiBhICsgYgp9CgovLyDmiZPljbDmlbDnu4Tlh73mlbAKZnVuYyBwcmludEFycmF5KGFyciBbXWludCkgewogICAgZm9yIF8sIGl0ZW0gOj0gcmFuZ2UgYXJyIHsKICAgICAgICBmbXQuUHJpbnQoaXRlbSwgIiAiKSAgLy8g6L6T5Ye66ICM5LiN5o2i6KGMCiAgICB9CiAgICBmbXQuUHJpbnRsbigpICAvLyDmjaLooYwKfQoKLy8g5Li756iL5bqPCmZ1bmMgbWFpbigpIHsKICAgIC8vIOasoui/juS/oeaBrwogICAgcHJpbnRXZWxjb21lTWVzc2FnZSgpCgogICAgLy8g55u05o6l5Lyg6YCS5Y+C5pWw6L+b6KGM5p2h5Lu25Yik5patCiAgICBudW0xIDo9IDUKICAgIG51bTIgOj0gMwoKICAgIC8vIOadoeS7tuWIpOaWrQogICAgaWYgbnVtMSA9PSBudW0yIHsKICAgICAgICBmbXQuUHJpbnRsbigi5Lik5Liq5pWw55u4562JISIpCiAgICB9IGVsc2UgaWYgbnVtMSA+IG51bTIgewogICAgICAgIGZtdC5QcmludGxuKCLnrKzkuIDkuKrmlbDlpKfkuo7nrKzkuozkuKrmlbAiKQogICAgfSBlbHNlIHsKICAgICAgICBmbXQuUHJpbnRsbigi56ys5LiA5Liq5pWw5bCP5LqO56ys5LqM5Liq5pWwIikKICAgIH0KCiAgICAvLyDosIPnlKjliqDms5Xlh73mlbAKICAgIHJlc3VsdCA6PSBhZGQobnVtMSwgbnVtMikKICAgIGZtdC5QcmludGYoIuS4pOS4quaVsOeahOWSjOaYrzogJWRcbiIsIHJlc3VsdCkKCiAgICAvLyDmlbDnu4Tmk43kvZwKICAgIGFyciA6PSBbXWludHsxLCAyLCAzLCA0LCA1fQogICAgZm10LlByaW50KCLmlbDnu4TnmoTlhYPntKA6ICIpCiAgICBwcmludEFycmF5KGFycikKCiAgICAvLyDlvqrnjq/miZPljbAgMSDliLAgMTAKICAgIGZtdC5QcmludCgiMeWIsDEw55qE5pWw5a2XOiAiKQogICAgZm9yIGkgOj0gMTsgaSA8PSAxMDsgaSsrIHsKICAgICAgICBmbXQuUHJpbnQoaSwgIiAiKSAgLy8g6L6T5Ye66ICM5LiN5o2i6KGMCiAgICB9CiAgICBmbXQuUHJpbnRsbigpICAvLyDmjaLooYwKCiAgICAvLyDnqIvluo/nu5PmnZ8KICAgIGZtdC5QcmludGxuKCLmhJ/osKLkvb/nlKggR28g6K+t6KiA5pWZ56iLISIpCn0K
```

## 输出结果

```
5qyi6L+O5p2l5YiwIEdvIOivreiogOWfuuehgOaVmeWtpiEK6L+Z5piv5LiA\n5Liq566A5Y2V55qEIEdvIOekuuS+i+eoi+W6j+OAggoK56ys5LiA5Liq5pWw\n5aSn5LqO56ys5LqM5Liq5pWwCuS4pOS4quaVsOeahOWSjOaYrzogOArmlbDn\nu4TnmoTlhYPntKA6IDEgMiAzIDQgNSAKMeWIsDEw55qE5pWw5a2XOiAxIDIg\nMyA0IDUgNiA3IDggOSAxMCAK5oSf6LCi5L2/55SoIEdvIOivreiogOaVmeeo\niyEK\n
```

# Ruby

```
{
    "id": 72,
    "name": "Ruby (2.7.0)"
}
```

```
# 教学函数：打印欢迎信息
def print_welcome_message
  puts "欢迎来到 Ruby 基础教学!"
  puts "这是一个简单的 Ruby 示例程序。\n"
end

# 加法函数
def add(a, b)
  return a + b
end

# 打印数组函数
def print_array(arr)
  arr.each { |item| print "#{item} " }  # 输出而不换行
  puts  # 换行
end

# 主程序
def main(num1, num2)
  # 欢迎信息
  print_welcome_message

  # 条件判断
  if num1 == num2
    puts "两个数相等!"
  elsif num1 > num2
    puts "第一个数大于第二个数"
  else
    puts "第一个数小于第二个数"
  end

  # 调用加法函数
  result = add(num1, num2)
  puts "两个数的和是: #{result}"

  # 数组操作
  arr = [1, 2, 3, 4, 5]
  print "数组的元素: "
  print_array(arr)

  # 循环打印 1 到 10
  print "1到10的数字: "
  (1..10).each { |i| print "#{i} " }
  puts  # 换行

  # 程序结束
  puts "感谢使用 Ruby 教程!"
end

# 调用主程序，传递参数代替用户输入
main(5, 3)

```

## 编码

```
IyDmlZnlrablh73mlbDvvJrmiZPljbDmrKLov47kv6Hmga8KZGVmIHByaW50X3dlbGNvbWVfbWVzc2FnZQogIHB1dHMgIuasoui/juadpeWIsCBSdWJ5IOWfuuehgOaVmeWtpiEiCiAgcHV0cyAi6L+Z5piv5LiA5Liq566A5Y2V55qEIFJ1Ynkg56S65L6L56iL5bqP44CCXG4iCmVuZAoKIyDliqDms5Xlh73mlbAKZGVmIGFkZChhLCBiKQogIHJldHVybiBhICsgYgplbmQKCiMg5omT5Y2w5pWw57uE5Ye95pWwCmRlZiBwcmludF9hcnJheShhcnIpCiAgYXJyLmVhY2ggeyB8aXRlbXwgcHJpbnQgIiN7aXRlbX0gIiB9ICAjIOi+k+WHuuiAjOS4jeaNouihjAogIHB1dHMgICMg5o2i6KGMCmVuZAoKIyDkuLvnqIvluo8KZGVmIG1haW4obnVtMSwgbnVtMikKICAjIOasoui/juS/oeaBrwogIHByaW50X3dlbGNvbWVfbWVzc2FnZQoKICAjIOadoeS7tuWIpOaWrQogIGlmIG51bTEgPT0gbnVtMgogICAgcHV0cyAi5Lik5Liq5pWw55u4562JISIKICBlbHNpZiBudW0xID4gbnVtMgogICAgcHV0cyAi56ys5LiA5Liq5pWw5aSn5LqO56ys5LqM5Liq5pWwIgogIGVsc2UKICAgIHB1dHMgIuesrOS4gOS4quaVsOWwj+S6juesrOS6jOS4quaVsCIKICBlbmQKCiAgIyDosIPnlKjliqDms5Xlh73mlbAKICByZXN1bHQgPSBhZGQobnVtMSwgbnVtMikKICBwdXRzICLkuKTkuKrmlbDnmoTlkozmmK86ICN7cmVzdWx0fSIKCiAgIyDmlbDnu4Tmk43kvZwKICBhcnIgPSBbMSwgMiwgMywgNCwgNV0KICBwcmludCAi5pWw57uE55qE5YWD57SgOiAiCiAgcHJpbnRfYXJyYXkoYXJyKQoKICAjIOW+queOr+aJk+WNsCAxIOWIsCAxMAogIHByaW50ICIx5YiwMTDnmoTmlbDlrZc6ICIKICAoMS4uMTApLmVhY2ggeyB8aXwgcHJpbnQgIiN7aX0gIiB9CiAgcHV0cyAgIyDmjaLooYwKCiAgIyDnqIvluo/nu5PmnZ8KICBwdXRzICLmhJ/osKLkvb/nlKggUnVieSDmlZnnqIshIgplbmQKCiMg6LCD55So5Li756iL5bqP77yM5Lyg6YCS5Y+C5pWw5Luj5pu/55So5oi36L6T5YWlCm1haW4oNSwgMykK
```

## 输出结果

```
5qyi6L+O5p2l5YiwIFJ1Ynkg5Z+656GA5pWZ5a2mIQrov5nmmK/kuIDkuKrn\nroDljZXnmoQgUnVieSDnpLrkvovnqIvluo/jgIIK56ys5LiA5Liq5pWw5aSn\n5LqO56ys5LqM5Liq5pWwCuS4pOS4quaVsOeahOWSjOaYrzogOArmlbDnu4Tn\nmoTlhYPntKA6IDEgMiAzIDQgNSAKMeWIsDEw55qE5pWw5a2XOiAxIDIgMyA0\nIDUgNiA3IDggOSAxMCAK5oSf6LCi5L2/55SoIFJ1Ynkg5pWZ56iLIQo=\n
```

# PHP

```
{
    "id": 68,
    "name": "PHP (7.4.1)"
}
```

```
<?php

// 教学函数：打印欢迎信息
function printWelcomeMessage() {
    echo "欢迎来到 PHP 基础教学!\n";
    echo "这是一个简单的 PHP 示例程序。\n\n";
}

// 加法函数
function add($a, $b) {
    return $a + $b;
}

// 打印数组函数
function printArray($arr) {
    foreach ($arr as $item) {
        echo $item . " ";  // 输出而不换行
    }
    echo "\n";  // 换行
}

// 主程序
function main($num1, $num2) {
    // 欢迎信息
    printWelcomeMessage();

    // 条件判断
    if ($num1 == $num2) {
        echo "两个数相等!\n";
    } elseif ($num1 > $num2) {
        echo "第一个数大于第二个数\n";
    } else {
        echo "第一个数小于第二个数\n";
    }

    // 调用加法函数
    $result = add($num1, $num2);
    echo "两个数的和是: $result\n";

    // 数组操作
    $arr = [1, 2, 3, 4, 5];
    echo "数组的元素: ";
    printArray($arr);

    // 循环打印 1 到 10
    echo "1到10的数字: ";
    for ($i = 1; $i <= 10; $i++) {
        echo $i . " ";  // 输出而不换行
    }
    echo "\n";  // 换行

    // 程序结束
    echo "感谢使用 PHP 教程!\n";
}

// 调用主程序，传递参数代替用户输入
main(5, 3);

?>

```

## 编码

```
PD9waHAKCi8vIOaVmeWtpuWHveaVsO+8muaJk+WNsOasoui/juS/oeaBrwpmdW5jdGlvbiBwcmludFdlbGNvbWVNZXNzYWdlKCkgewogICAgZWNobyAi5qyi6L+O5p2l5YiwIFBIUCDln7rnoYDmlZnlraYhXG4iOwogICAgZWNobyAi6L+Z5piv5LiA5Liq566A5Y2V55qEIFBIUCDnpLrkvovnqIvluo/jgIJcblxuIjsKfQoKLy8g5Yqg5rOV5Ye95pWwCmZ1bmN0aW9uIGFkZCgkYSwgJGIpIHsKICAgIHJldHVybiAkYSArICRiOwp9CgovLyDmiZPljbDmlbDnu4Tlh73mlbAKZnVuY3Rpb24gcHJpbnRBcnJheSgkYXJyKSB7CiAgICBmb3JlYWNoICgkYXJyIGFzICRpdGVtKSB7CiAgICAgICAgZWNobyAkaXRlbSAuICIgIjsgIC8vIOi+k+WHuuiAjOS4jeaNouihjAogICAgfQogICAgZWNobyAiXG4iOyAgLy8g5o2i6KGMCn0KCi8vIOS4u+eoi+W6jwpmdW5jdGlvbiBtYWluKCRudW0xLCAkbnVtMikgewogICAgLy8g5qyi6L+O5L+h5oGvCiAgICBwcmludFdlbGNvbWVNZXNzYWdlKCk7CgogICAgLy8g5p2h5Lu25Yik5patCiAgICBpZiAoJG51bTEgPT0gJG51bTIpIHsKICAgICAgICBlY2hvICLkuKTkuKrmlbDnm7jnrYkhXG4iOwogICAgfSBlbHNlaWYgKCRudW0xID4gJG51bTIpIHsKICAgICAgICBlY2hvICLnrKzkuIDkuKrmlbDlpKfkuo7nrKzkuozkuKrmlbBcbiI7CiAgICB9IGVsc2UgewogICAgICAgIGVjaG8gIuesrOS4gOS4quaVsOWwj+S6juesrOS6jOS4quaVsFxuIjsKICAgIH0KCiAgICAvLyDosIPnlKjliqDms5Xlh73mlbAKICAgICRyZXN1bHQgPSBhZGQoJG51bTEsICRudW0yKTsKICAgIGVjaG8gIuS4pOS4quaVsOeahOWSjOaYrzogJHJlc3VsdFxuIjsKCiAgICAvLyDmlbDnu4Tmk43kvZwKICAgICRhcnIgPSBbMSwgMiwgMywgNCwgNV07CiAgICBlY2hvICLmlbDnu4TnmoTlhYPntKA6ICI7CiAgICBwcmludEFycmF5KCRhcnIpOwoKICAgIC8vIOW+queOr+aJk+WNsCAxIOWIsCAxMAogICAgZWNobyAiMeWIsDEw55qE5pWw5a2XOiAiOwogICAgZm9yICgkaSA9IDE7ICRpIDw9IDEwOyAkaSsrKSB7CiAgICAgICAgZWNobyAkaSAuICIgIjsgIC8vIOi+k+WHuuiAjOS4jeaNouihjAogICAgfQogICAgZWNobyAiXG4iOyAgLy8g5o2i6KGMCgogICAgLy8g56iL5bqP57uT5p2fCiAgICBlY2hvICLmhJ/osKLkvb/nlKggUEhQIOaVmeeoiyFcbiI7Cn0KCi8vIOiwg+eUqOS4u+eoi+W6j++8jOS8oOmAkuWPguaVsOS7o+abv+eUqOaIt+i+k+WFpQptYWluKDUsIDMpOwoKPz4K
```

## 输出结果

```
5qyi6L+O5p2l5YiwIFBIUCDln7rnoYDmlZnlraYhCui/meaYr+S4gOS4queu\ngOWNleeahCBQSFAg56S65L6L56iL5bqP44CCCgrnrKzkuIDkuKrmlbDlpKfk\nuo7nrKzkuozkuKrmlbAK5Lik5Liq5pWw55qE5ZKM5pivOiA4CuaVsOe7hOea\nhOWFg+e0oDogMSAyIDMgNCA1IAox5YiwMTDnmoTmlbDlrZc6IDEgMiAzIDQg\nNSA2IDcgOCA5IDEwIArmhJ/osKLkvb/nlKggUEhQIOaVmeeoiyEK\n
```



## html

```html
<!DOCTYPE html>
<html lang="zh-CN">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="icon" href="https://ide.judge0.com/images/icon_var2_rounded_512.png" type="image/png">
    <title>Judge0 专业版在线代码沙箱</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/monaco-editor/0.36.1/min/vs/loader.min.js"></script>
    <style>
        body {
            background-color: #0f172a;
        }

        /* 深色背景更专业 */
        #editor {
            min-height: 500px;
        }

        .result-panel {
            background-color: #1e293b;
            color: #f1f5f9;
        }
    </style>
</head>

<body class="text-slate-200 p-4 md:p-8">

    <div class="max-w-[1600px] mx-auto">
        <div class="flex flex-col md:flex-row justify-between items-center mb-6 gap-4">
            <div>
                <h1 class="text-3xl font-extrabold text-white">Judge0 <span class="text-blue-500">Code Sandbox</span>
                </h1>
                <p class="text-slate-400">高性能、全语种在线执行环境</p>
            </div>

            <div class="flex items-center gap-4 w-full md:w-auto">
                <div class="flex-1">
                    <label class="block text-xs uppercase font-bold text-slate-500 mb-1">编程语言</label>
                    <select id="language-select"
                        class="w-full md:w-64 bg-slate-800 border border-slate-700 p-2 rounded text-white outline-none focus:border-blue-500">
                        <option value="">正在加载语言列表...</option>
                    </select>
                </div>
                <button id="run-btn"
                    class="mt-5 bg-blue-600 hover:bg-blue-500 text-white font-bold py-2.5 px-8 rounded shadow-lg transition-all active:scale-95 disabled:opacity-50">
                    运行代码
                </button>
            </div>
        </div>

        <div class="grid grid-cols-1 xl:grid-cols-3 gap-6">

            <div class="xl:col-span-2 space-y-4">
                <div class="bg-slate-800 rounded-lg overflow-hidden border border-slate-700 shadow-2xl">
                    <div class="bg-slate-700 px-4 py-2 text-xs font-mono flex justify-between">
                        <span>SOURCE CODE</span>
                        <span id="editor-lang-name">Java</span>
                    </div>
                    <div id="editor"></div>
                </div>
            </div>

            <div class="space-y-6">
                <div class="bg-slate-800 p-5 rounded-lg border border-slate-700">
                    <h3 class="text-sm font-bold mb-4 flex items-center gap-2">
                        <span class="w-2 h-2 bg-yellow-500 rounded-full"></span> 运行配置
                    </h3>

                    <div class="space-y-4">
                        <div>
                            <label class="block text-xs text-slate-400 mb-1">命令行参数 (Command Line Args)</label>
                            <input type="text" id="command-args" placeholder="例如: arg1 arg2"
                                class="w-full bg-slate-900 border border-slate-700 p-2 rounded text-sm outline-none focus:border-blue-500">
                        </div>
                        <div>
                            <label class="block text-xs text-slate-400 mb-1">标准输入 (STDIN)</label>
                            <textarea id="stdin" rows="4" placeholder="代码执行时的输入流内容..."
                                class="w-full bg-slate-900 border border-slate-700 p-2 rounded text-sm outline-none focus:border-blue-500"></textarea>
                        </div>
                    </div>
                </div>

                <div class="bg-slate-800 rounded-lg border border-slate-700 overflow-hidden flex-1">
                    <div class="bg-slate-700 px-4 py-2 text-xs font-mono flex justify-between items-center">
                        <span>EXECUTION RESULT</span>
                        <span id="status-badge" class="px-2 py-0.5 rounded bg-slate-600 text-[10px]">IDLE</span>
                    </div>
                    <div class="p-4">
                        <div id="output-meta" class="text-[10px] font-mono text-slate-500 mb-2 flex gap-3">
                        </div>
                        <pre id="output"
                            class="font-mono text-sm h-[260px] overflow-auto whitespace-pre-wrap text-green-400">等待代码提交...</pre>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script>
        const languageMapping = {
            // Web 全家桶
            'typescript': 'typescript',
            'javascript': 'javascript',
            'node.js': 'javascript',
            'html': 'html',
            'css': 'css',

            // C 家族 (注意顺序：先 C++ 再 C)
            'c++': 'cpp',
            'cpp': 'cpp',
            'c#': 'csharp',
            'objective-c': 'objective-c',
            'c ': 'c', // 匹配 "C (GCC...)"

            // 核心后端语言
            'java': 'java',
            'python': 'python',
            'py': 'python',
            'go': 'go',
            'golang': 'go',
            'rust': 'rust',
            'php': 'php',
            'ruby': 'ruby',
            'swift': 'swift',
            'kotlin': 'kotlin',
            'scala': 'scala',

            // 脚本与工具
            'bash': 'shell',
            'shell': 'shell',
            'sql': 'sql',
            'perl': 'perl',
            'r ': 'r',
            'lua': 'lua',

            // 其他
            'dart': 'dart',
            'haskell': 'haskell',
            'erlang': 'erlang',
            'elixir': 'elixir'
        };
        let tokenCache = {}; // 用于存储 { 'hash_key': 'submission_token' }
        // API 基础配置 (替换为你自己的 Judge0 地址)
        const BASE_URL = "http://192.168.31.26:2358";
        const API_KEY = "YOUR_RAPIDAPI_KEY"; // 请在此填入你的 Key

        let editor;
        const langSelect = document.getElementById('language-select');
        const runBtn = document.getElementById('run-btn');
        const outputArea = document.getElementById('output');
        const statusBadge = document.getElementById('status-badge');
        const outputMeta = document.getElementById('output-meta');
        document.getElementById('command-args').value = "arg1 arg2";
        // 1. 初始化编辑器
        require.config({ paths: { 'vs': 'https://cdnjs.cloudflare.com/ajax/libs/monaco-editor/0.36.1/min/vs' } });
        require(['vs/editor/editor.main'], function () {
            editor = monaco.editor.create(document.getElementById('editor'), {
                value: 'public class Main {\n    public static void main(String[] args) {\n        if (args.length > 0) {\n            System.out.println("第一个参数是: " + args[0]);\n            System.out.println("第二个参数是: " + args[1]);\n        } else {\n            System.out.println("没有接收到参数");\n        }\n    }\n}',
                language: 'java',
                theme: 'vs-dark',
                fontSize: 14,
                automaticLayout: true
            });
            fetchLanguages();
        });
        // 2. 修改后的加载语言函数
        async function fetchLanguages() {
            try {
                const response = await fetch(`${BASE_URL}/languages`, {
                    headers: { 'x-rapidapi-key': API_KEY }
                });
                const langs = await response.json();

                langSelect.innerHTML = langs.map(l =>
                    `<option value="${l.id}" data-name="${l.name}">${l.name}</option>`
                ).join('');

                // 默认选择 Java (假设 ID 是 62)
                langSelect.value = "62";
                // 初始化时也同步一次语言
                const selectedOption = langSelect.options[langSelect.selectedIndex];
                if (selectedOption) updateEditorLanguage(selectedOption.getAttribute('data-name'));

            } catch (err) {
                debugger
                langSelect.innerHTML = `<option>加载失败</option>`;
            }
        }

        // 监听下拉框切换
        langSelect.addEventListener('change', (e) => {
            const selectedOption = e.target.options[e.target.selectedIndex];
            const fullLanguageName = selectedOption.getAttribute('data-name');
            updateEditorLanguage(fullLanguageName);
        });

        // 3. 运行代码逻辑
        async function runCode() {
            const langId = langSelect.value;
            const sourceCode = editor.getValue();
            const stdin = document.getElementById('stdin').value;
            const commandArgs = document.getElementById('command-args').value;

            // --- 1. 压缩内容：生成唯一的哈希 Key ---
            // 即使 sourceCode 有几万行，生成的 hash 也只有 64 个字符
            const rawContent = `${langId}_${sourceCode}_${stdin}_${commandArgs}`;
            const contentHash = await generateHash(rawContent);

            // --- 2. 检查缓存 ---
            if (tokenCache[contentHash]) {
                console.log("命中哈希缓存，Token:", tokenCache[contentHash]);
                pollResult(tokenCache[contentHash]);
                return;
            }
            // --- 3. 如果没有缓存，则正常提交 ---
            const payload = {
                source_code: utoa(sourceCode),
                language_id: parseInt(langId),
                stdin: utoa(stdin),
                command_line_arguments: commandArgs
            };

            updateStatus("SUBMITTING", "bg-blue-500");
            runBtn.disabled = true;

            try {
                const res = await fetch(`${BASE_URL}/submissions?base64_encoded=true&wait=false`, {
                    method: 'POST',
                    headers: {
                        'x-rapidapi-key': API_KEY,
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify(payload)
                });
                const { token } = await res.json();

                // 将新 token 存入缓存
                tokenCache[contentHash] = token;

                pollResult(token);
            } catch (err) {
                outputArea.innerText = "Error: " + err.message;
                runBtn.disabled = false;
            }
        }

        // 4. 轮询结果
        async function pollResult(token) {
            updateStatus("PROCESSING", "bg-yellow-600");
            setTimeout(() => {
                const check = async () => {
                    const res = await fetch(`${BASE_URL}/submissions/${token}?base64_encoded=true`, {
                        headers: { 'x-rapidapi-key': API_KEY }
                    });
                    const data = await res.json();

                    if (data.status.id <= 2) {
                        setTimeout(check, 1000);
                    } else {
                        displayResult(data);
                    }
                };
                check();
            }, 500);

        }
        // 编码：将字符串转为 UTF-8 字节后再进行 Base64 编码
        function utoa(str) {
            return btoa(unescape(encodeURIComponent(str)));
        }

        // 解码：对应的解码函数（如果需要显示结果）
        function atou(str) {
            return decodeURIComponent(escape(atob(str)));
        }
        function displayResult(data) {
            runBtn.disabled = false;
            // 解码结果
            const stdout = data.stdout ? atou(data.stdout) : "";
            const stderr = data.stderr ? atou(data.stderr) : "";
            const compileOut = data.compile_output ? atou(data.compile_output) : "";

            outputArea.innerText = stdout || compileOut || stderr || "Finished (No Output)";
            outputArea.className = stderr || compileOut ? "text-red-400" : "text-green-400";

            outputMeta.innerHTML = `
                <span>TIME: ${data.time || 0}s</span>
                <span>MEMORY: ${data.memory || 0}KB</span>
            `;

            const statusColor = data.status.id === 3 ? "bg-green-600" : "bg-red-600";
            updateStatus(data.status.description.toUpperCase(), statusColor);
        }

        function updateStatus(text, colorClass) {
            statusBadge.innerText = text;
            statusBadge.className = `px-2 py-0.5 rounded text-[10px] ${colorClass}`;
        }
        // 识别并更新编辑器语言的函数
        function updateEditorLanguage(langName) {
            if (!langName || !editor) return;
            const name = langName.toLowerCase();
            let monacoLang = 'plaintext';

            // 解决 Java vs JavaScript 问题的最稳妥办法：
            // 检查是否包含 JavaScript，如果是，就直接定死，不再往下匹配 Java
            if (name.includes('javascript')) {
                monacoLang = 'javascript';
            } else {
                // 遍历映射表
                for (let key in languageMapping) {
                    if (name.includes(key)) {
                        monacoLang = languageMapping[key];
                        break; // 匹配到第一个（最匹配的）就跳出
                    }
                }
            }

            const model = editor.getModel();
            if (model) {
                monaco.editor.setModelLanguage(model, monacoLang);
            }
            document.getElementById('editor-lang-name').innerText = monacoLang.toUpperCase();
        }
        // 将长字符串转换为固定的 SHA-256 哈希
        async function generateHash(text) {
            const msgUint8 = new TextEncoder().encode(text);                           // 编码为字节
            const hashBuffer = await crypto.subtle.digest('SHA-256', msgUint8);        // 计算哈希
            const hashArray = Array.from(new Uint8Array(hashBuffer));                  // 转为字节数组
            const hashHex = hashArray.map(b => b.toString(16).padStart(2, '0')).join(''); // 转为 16 进制字符串
            return hashHex;
        }

        runBtn.addEventListener('click', runCode);
    </script>
</body>

</html>
```

