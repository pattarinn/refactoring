# Refactoring

from https://github.com/OOP2020/pa2-pattarinn

Refactoring: https://github.com/OOP2020/pa2-pattarinn/blob/master/encryptdecrypt/Main.java

## Extract Class

The code is only in `main` and doing a lot of things; get the information, encode, decode, and print. These code can be seperate into classes.
- Class for get information
- Class for encoding
- Class for decoding
- Class for printing

So main is only control the flow of program.

## Extract Method

```java
    private int changeForShift(int old)
    private void shift(int key)
    private void unicode(int key)
    private void input(String[] args)
```
- Refactoring signs: methods are too long
- Refactoring: 
    ```java 
        private int changeForShift(int old)
    ```

    - The method that comes from the first `if` statement <br>
    Before refactoring:
    ```java
    if (!getData().equals("")) {
            StringBuilder sb = new StringBuilder();
            int old;
            for (int i = 0; i < getData().length(); i++) {
                old = getData().charAt(i);
                sb.append((char) changeForShift(old));
            }
            System.out.println(sb);
            System.exit(0);
        }
    ```
    After:
    ```java
        private int shiftSentence(int old) {
            StringBuilder sb = new StringBuilder();
            int old;
            for (int i = 0; i < getData().length(); i++) {
                old = getData().charAt(i);
                sb.append((char) changeForShift(old));
            }
            System.out.println(sb);
            System.exit(0);
        }
    ```
    From else condition, make a new method
    ```java
        private int shiftFile(int old) {
            File file = new File(getFile());
            byte[] buff = new byte[1024];
            int c = 0;
            try (InputStream in = new FileInputStream(file)) {
                if (!(getOut() == null)) {
                    
                    ...
    ```
    The final version will look like: 
    ```java
    private void shift(int key) {
        if (!getData().equals("")){
            shiftSentence(key)
        }
        else {
            shiftFile(key)
        }
    ```
    The others methods
    ```java
    private void shift(int key)
    private void unicode(int key)
    private void input(String[] args)
    ```
    will be refectored similar to 
    ```java 
    private int changeForShift(int old) 
    ```

