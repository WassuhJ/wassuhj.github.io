# OO Pre3

* 主要训练字符串的处理能力，以及正则表达式相关知识



## task 1

#### String 类的方法（部分）

- `split` 方法，可以根据**给定分隔符**来将字符串**分割**为若干个字符串；
- `indexOf` 方法，可以在给定字符串中**搜索给定字符串出现的位置**；
- `substring` 方法，可以按照下标位置区间来从给定字符串**截取子字符串**;
- `trim` 方法，可以**移除**给定字符串开头和结尾处的**空格以及换行符**。

**请注意：**在各主流IDE中，你可以通过将鼠标悬停在方法名上来阅读方法的文档。这可以让你精确地了解该方法的具体作用，为熟悉java类库的使用提供很大的帮助。

例：

假设我们已经得到了含有如下信息的字符串：

```java
{
"code":"iVBORw@0KGgoAAAANSU#hEUgAAAAgAAA$AECAYAAACzzX7wAAAAGUlEQVQImW%NggID/DKjgPzYOLpqwAr^xWAAAbkwv1&EmB71QAAAABJRU5*ErkJggg==",
"key":"64",
"type":"png"
}
```

现在我们想要将其中的code部分提取出来进行进一步的处理。我们可以通过字符串查找的方式来完成该任务：

```java
public static String extractCode(String jsonString) {
    int start = jsonString.indexOf("\"code\"");           //找到"code"所在之处；
    int end = jsonString.indexOf("\n", start);            //从上一次搜索结果开始向后搜索换行符
    return jsonString.substring(start + 8, end - 2);      //根据以上信息截取子串并返回
}
```

当然，这种方法并没有对字符串进行实际的解析，仅仅是进行了提取。

```java
public static String extractCode(String jsonString) {
    String[] records = jsonString.substring(1, jsonString.length() - 1).split(","); //将输入的字符串按照逗号分割开
    for (String record : records) {
        String[] details = record.split(":");                                       //分离每个分割出的字符串中的名称与值（按照冒号进一步分割）
        if ("\"code\"".equals(details[0])) {                                        //判断数据名称是否为"code"
            return details[1].substring(1, details[1].length() - 1);                //返回该数据对应的值。
        }
    }
    throw new RuntimeException("keyword code not found!");                          //未找到，报错
}
```



## task 2

* 通过简单的正则表达式对字符串信息进行处理



1. 预定义字符集合：`\d`：所有数字字符；`\s`：所有空白字符；`\w`：字母、数字或下划线字符
2. * `*`符号与`+`符号提供了不定次数重复功能；
   * `{}`可以重复指定次数；
   * `[]`提供了构造自己的字符集合的方法；
   * `()`则可以将字符串进行划分。

3. `String` 的 `matches` 方法：检验字符串是否属于正则表达式所表示的集合；`split` 函数中的分割符也可以填入正则表达式。

4. `Pattern` 类专门用来表示正则表达式。你可以使用 `Pattern.complie()` 方法将字符串转换为正则表达式。



```java
0123/7/1-Jack@JayChou :"Hello!";2021/7/3-JayChou@buaaer :"Hahaha";
123/7/5-JayChou@Mike :"emmmm";         123/7/8-JayChou@buaaer :"Hahaha";
0123/6/8-JayChou:"Hahaha";
END_OF_MESSAGE
qdate 123//
qdate 0123//
```

```java
import java.util.ArrayList;
import java.util.Objects;
import java.util.Scanner;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class MainClass {
    public static final String INFO_P1 =
                    "(?<year>\\d+)/(?<month>\\d+)/(?<date>\\d+)-" +
                    "(?<sender>[A-Za-z0-9]+)@(?<receiver>[A-Za-z0-9]+) :" +
                    "[\"](?<content>[A-Za-z0-9 ?!,.])+[\"];";
    public static final String INFO_P2 =
                    "(?<year>\\d+)/(?<month>\\d+)/(?<date>\\d+)-" +
                    "(?<sender>[A-Za-z0-9]+):" +
                    "[\"](?<content>[A-Za-z0-9 ?!,.@])+[\"];";
    public static final String INFO_P3 =
                    "(?<year>\\d+)/(?<month>\\d+)/(?<date>\\d+)-" +
                    "(?<sender>[A-Za-z0-9]+):" +
                    "[\"](?<content>[A-Za-z0-9 ?!,.])+[\"];";

    public static void main(String[] args) {
        ArrayList<String> records = new ArrayList<>();
        Scanner sc = new Scanner(System.in);
        read(sc, records);
        while (sc.hasNext()) {
            String qst = sc.next();
            String str1 = sc.next();
            if (Objects.equals(qst, "qdate")) {
                Pattern p1 = Pattern.compile(INFO_P1);
                Pattern p2 = Pattern.compile(INFO_P2);
                Pattern p3 = Pattern.compile(INFO_P3);
                for (String record: records) {
                    Matcher m1 = p1.matcher(record);
                    Matcher m2 = p2.matcher(record);
                    Matcher m3 = p3.matcher(record);
                    Matcher mm = null;
                    if (m1.find()) {
                        mm = m1;
                    } else if (m2.find()) {
                        mm = m2;
                    } else if (m3.find()) {
                        mm = m3;
                    }
                    assert mm != null;
                    String str2 = "";
                    if (mm.find()) {
                        str2 = mm.group("year") + "/" + mm.group("month") + "/" + mm.group("date");
                    }
                    Date d1 = new Date(str1);
                    Date d2 = new Date(str2);
                    if (d1.isMatch(d2)) {
                        System.out.println(record);
                    }
                }
            } else if (Objects.equals(qst, "qsend")) {
                for (String record: records) {
                    if (record.contains("-" + str1 + "@") || record.contains("-" + str1 + ":")) {
                        System.out.println(record);
                    }
                }
            } else if (Objects.equals(qst, "qrecv")) {
                for (String record: records) {
                    if (record.contains("@" + str1 + " ")) {
                        System.out.println(record);
                    }
                }
            }
        }
    }

    static void read(Scanner sc, ArrayList<String> records) {
        String str;
        while (!Objects.equals((str = sc.nextLine()), "END_OF_MESSAGE")) {
            for (String s: str.split(";")) {
                if (!s.trim().isEmpty()) {
                    records.add(s.trim() + ";");
                }
            }
        }
    }

}

```

