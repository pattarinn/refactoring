# Refactoring

from https://github.com/OOP2020/pa2-pattarinn

Refactoring: https://github.com/OOP2020/pa2-pattarinn/blob/master/encryptdecrypt/Main.java

## Extract Class

The code is only in `main` and doing a lot of things; get the information, encode, decode, and print. These code can be seperated into classes that have their own functionality. So `main` only controls the flow of program.
- Class for get information -> get information from main
- Class for encode and decode -> encode/decode information from get information class and send to main
- Class for main -> controls the overall program

## Extract Method

```java
    private void shift(int key)
    private int changeForShift(int old)
    private void unicode(int key)
    private void input(String[] args)
```
- Refactoring signs: methods are too long
- Refactoring: 
    ```java 
        private void shift(int key)
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
        private void shiftSentence(int key) {
            StringBuilder sb = new StringBuilder();
            int old;
            for (int i = 0; i < getData().length(); i++) {
                old = getData().charAt(i);
                sb.append((char)changeForShift(old));
            }
            System.out.println(sb);
            System.exit(0);
        }
    ```
    From else condition, make a new method
    ```java
        private void shiftFile(int key) {
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

    For 
    ```java
    private void unicode(int key)
    ```
    Before:
    ```java
        private void unicode(int key) {
            if (!getData().equals("")) {
                StringBuilder sb = new StringBuilder();
                int old;
                for (int i = 0; i < getData().length(); i++) {
                    old = (int) getData().charAt(i);
                    sb.append((char) changeForUnicode(old));
                }
                System.out.println(sb);
                System.exit(0);
            } else {
                File file = new File(getFile());
                byte[] buff = new byte[1024];
                int c = 0;
                try (InputStream in = new FileInputStream(file)) {
                    if (!(getOut() == null)) {
                        File outFile = new File(getOut());
                        OutputStream out = new FileOutputStream(outFile);
                        {
                            while ((c = in.read(buff)) >= 0) {
                                int old;
                                for (int i = 0; i < buff.length; i++) {
                                    old = buff[i];
                                    if (old != 10) {
                                        buff[i] = (byte) changeForUnicode(old);
                                    }
                                }
                                out.write(buff, 0, c);
                                out.flush();
                            }
                            out.close();
                        }
                        System.exit(0);
                    } else {
                        BufferedReader br = new BufferedReader(new InputStreamReader(in));
                        StringBuilder sb = new StringBuilder();
                        String line;
                        while ((line = br.readLine()) != null) {
                            int old;
                            for (int i = 0; i < line.length(); i++) {
                                old = line.charAt(i);
                                sb.append((char) changeForUnicode(old));
                            }
                            System.out.println(sb);
                            sb.delete(0, line.length());
                        }
                        System.exit(0);
                    }
                } catch (IOException e) {
                    System.out.println("invalid input file.");
                }
            }
        }
    ```
    After:
    ```java
        private void unicode(int key) {
        if (!getData().equals("")) {
            unicodeSentence(key)
        } else {
            unicodeFile(key)
    }

        private void unicodeSentence(int key){
            StringBuilder sb = new StringBuilder();
                int old;
                for (int i = 0; i < getData().length(); i++) {
                    old = (int) getData().charAt(i);
                    sb.append((char) changeForUnicode(old));
                }
                System.out.println(sb);
                System.exit(0);
        }

        private void unicodeFile(int key) {
            File file = new File(getFile());
                byte[] buff = new byte[1024];
                int c = 0;
                try (InputStream in = new FileInputStream(file)) {
                    if (!(getOut() == null)) {
                        File outFile = new File(getOut());
                        OutputStream out = new FileOutputStream(outFile);
                        {
                            while ((c = in.read(buff)) >= 0) {
                                int old;
                                for (int i = 0; i < buff.length; i++) {
                                    old = buff[i];
                                    if (old != 10) {
                                        buff[i] = (byte) changeForUnicode(old);
                                    }
                                }
                                out.write(buff, 0, c);
                                out.flush();
                            }
                            out.close();
                        }
                        System.exit(0);
                    } else {
                        BufferedReader br = new BufferedReader(new InputStreamReader(in));
                        StringBuilder sb = new StringBuilder();
                        String line;
                        while ((line = br.readLine()) != null) {
                            int old;
                            for (int i = 0; i < line.length(); i++) {
                                old = line.charAt(i);
                                sb.append((char) changeForUnicode(old));
                            }
                            System.out.println(sb);
                            sb.delete(0, line.length());
                        }
                        System.exit(0);
                    }
                } catch (IOException e) {
                    System.out.println("invalid input file.");
                }
            }
        }
    ```

    ## Move Method
    After creating classes and extracting methods, those methods should be moved to its own class.
    - Class for get information <br>
    Lists of methods that should be moved to:
    ```java
        private String getAlgorithm()
        private String getMode()
        private int getKey()
        private String getData()
        private String getOut()
        private String getFile()
        private void setAlgorithm(String v)
        private void setMode(String v)
        private void setKey(int v)
        private void setData(String v)
        private void setOut(String v)
        private void setFile(String v)
    ```
    All of the methods should be changed to ```public``` to give access to outer classes.

    - Class for encoding and decoding <br>
    Lists of methods that should be moved to:
    All the extracted methods from 
    ```java
        private void shift(int key)
        private void unicode(int key)
        private int changeForUnicode(int old)
        private int changeForShift(int old)
    ```
    ```shift``` and ```unicode``` should be ```public``` to give access to Main class

    - Class for main <br>
    Lists of remaining methods:
    ```java
        public static void main(String[] args)
        private void execute()
        private void input(String[] args)
        private void algorithm(String alg)
    ```


    ## Remove Redundancy
    ```java
    private int changeForShift(int old) {
        int change = (int) old + getKey();
        ...
    ```
    ```(int)old``` (int) can be removed.


