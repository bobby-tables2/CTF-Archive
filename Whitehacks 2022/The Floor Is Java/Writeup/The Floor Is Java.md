# Whitehacks 2022 | The Floor Is Java
## The contestant is given a Java class file _Reverse.class_ and a text file _message.txt_ that was encrypted by it.
---
>It was a manageable and fun challenge. I got my way through decompiling the java class and reverse engineering the source code.
* Decompile the class file
  * I used a website like [https://jdec.app/](https://jdec.app/). This is the result, _Reverse.java_:
  ```Java
  /* Decompiler 12ms, total 1401ms, lines 82 */
    import java.util.Scanner;

    public class Reverse {
    public static String encode_1(String var0) {
        String[] var1 = var0.split("");
        String var2 = "";

        int var3;
        for(var3 = 0; var3 < var0.length(); var3 += 2) {
            var2 = var2 + var1[var3];
        }

        for(var3 = 1; var3 < var0.length(); var3 += 2) {
            var2 = var2 + var1[var3];
        }

        return var2;
    }

    public static String encode_2(String var0) {
        char[] var1 = var0.toCharArray();
        int[] var2 = new int[]{39, 18, 16, 3, 2, 10, 14, 4, 11, 37, 8, 5, 6, 31, 9, 12};
        int var3 = var2.length;

        for(int var4 = 0; var4 < var1.length; ++var4) {
            var1[var4] = (char)(var1[var4] - var2[var4 % var3]);
        }

        String var6 = "";

        for(int var5 = 0; var5 < var1.length; ++var5) {
            var6 = var6 + var1[var5];
        }

        return var6;
    }

    public static String encode_3(String var0) {
        String[] var1 = var0.split("");
        String var2 = "";

        int var3;
        for(var3 = var0.length() / 2; var3 < var0.length(); ++var3) {
            var2 = var2 + var1[var3];
        }

        for(var3 = 0; var3 < var0.length() / 2; ++var3) {
            var2 = var2 + var1[var3];
        }

        return var2;
    }

    public static String encode_4(String var0) {
        char[] var1 = var0.toCharArray();

        int var3;
        for(int var2 = 0; var2 < var0.length() / 2; ++var2) {
            var3 = var0.length() - var2 - 1;
            char var4 = var1[var2];
            var1[var2] = var1[var3];
            var1[var3] = var4;
        }

        String var5 = "";

        for(var3 = 0; var3 < var1.length; ++var3) {
            var5 = var5 + var1[var3];
        }

        return var5;
    }

    public static void main(String[] var0) {
        Scanner var1 = new Scanner(System.in);
        System.out.print("Enter your string: ");
        String var2 = var1.nextLine();
        var1.close();
        System.out.println(encode_3(encode_4(encode_1(encode_2(var2)))));
    }
    }
  ```
* Reverse engineer Java code
  * I was not a Java programmer, so I first converted it into a Python script. The way I did this was by translating Java code into whatever I thought was the shortest and closest equivilant, and encoding the same string repeatedly to check.
  * Next, I wrote decode functions that did the reverse of whatever the encode function did. Funnily enough, 2 of the functions were reversed by simply running them again. I checked them by encoding before decoding a string, ensuring that the string remained unchanged. This is the result:
    ```Python 
            
        def encode_1(s):
            return s[0::2] + s[1::2]
        def decode_1(s):
            left = s[:len(s)//2]
            right = s[len(s)//2:]
            out = ""
            for i in range(len(left)):
                out += (left[i] + right[i])
            return out

        def encode_2(s):
            s_list = list(s)
            int_list = [39, 18, 16, 3, 2, 10, 14, 4, 11, 37, 8, 5, 6, 31, 9, 12]
            for i in range(len(s_list)):
                s_list[i] = chr((ord(s_list[i]) - int_list[i % len(int_list)]))
            return ''.join(s_list)
        def decode_2(s):
            s_list = list(s)
            int_list = [39, 18, 16, 3, 2, 10, 14, 4, 11, 37, 8, 5, 6, 31, 9, 12]
            for i in range(len(s_list)):
                s_list[i] = chr((ord(s_list[i]) + int_list[i % len(int_list)]))
            return ''.join(s_list)

        def encode_3(s):
            return s[int(len(s)/2):] + s[0:int(len(s)/2)]
        def decode_3(s):
            return encode_3(s)

        def encode_4(s):
            s_list = list(s)
            for i1 in range(int(len(s) / 2)):
                s_list[i1], s_list[len(s) - i1 - 1] = s_list[len(s) - i1 - 1], s_list[i1]
            return ''.join(s_list)
        def decode_4(s):
            return encode_4(s)

        def main():
            print(decode_1(encode_1("encode_1")))
            print(decode_2(encode_2("encode_2")))
            print(decode_3(encode_3("encode_3")))
            print(decode_4(encode_4("encode_4")))
            print("Enter your string: ", end ="")
            var2 = input()
            print(encode_3(encode_4(encode_1(encode_2(var2)))))
            print(decode_2(decode_1(decode_4(decode_3(var2)))))

        main()
    ```
* Decoding the text file
  * Entering the contents of the text file into the final Python script, this was the result:
  ```
  encode_1
  encode_2
  encode_3
  encode_4
  Enter your string: ,hj*Y/bOi-(Tm0"0qH,O[d2!'@qG-(-6
  $'i$YJg ^TWZ*   BL6$K%'V
  WH2022{1_l0v3_r3v3r51ng_5tr1ng5}
  ```

**WH2022{1_l0v3_r3v3r51ng_5tr1ng5}**