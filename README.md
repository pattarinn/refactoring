# Refactoring

Note: This README has 2 refactoring works; PA2 and PA4. At first, I misunderstood about the problem. I thought it can be anything in my Github repo, so I did refactoring for my PA2. I also included my PA2 work at the bottom of README.md.

<h1> Refactoring for PA4 </h1>
From https://github.com/OOP2020/pa4-pattarinn

Refactoring: https://github.com/OOP2020/pa4-pattarinn/blob/master/src/tracker/InformationHandle.java

## Inline temp
```java
    public int sentencePosition(String countryToSearch, String type){
        ...
        if (s.contains(countryToSearch)) {
                int atSentence = mapToSearch.get(s);
                return atSentence;
        }
        ...
    }
```
This code can be changed to
```java
    public int sentencePosition(String countryToSearch, String type){
        ...
        if (s.contains(countryToSearch)) {
                return mapToSearch.get(s);
        }
        ...
    }
```

## Extract Method
- Most of methods in the file has to open csv file. Those methods contain 
    ```java
        File file = new File(sourceFile);
            try (InputStream in = new FileInputStream(file);
                    Reader reader = new InputStreamReader(in);
                    BufferedReader br = new BufferedReader(reader);) {
                        ...
                    }
    ```
    To shorten the code, make a new method
    ```java
        private boolean canOpen(String sourceFile){
            File file = new File(sourceFile);
            try (InputStream in = new FileInputStream(file);
                    Reader reader = new InputStreamReader(in);
                    BufferedReader br = new BufferedReader(reader);) {
                        ...
                    }
        }
    ```
    In the code, use ```canOpen``` with if statement.
    ```java
        if canOpen(sourceFile){
            ...
        }
        else {
            ... // Throw some exception
        }
    ```

## Clean up
1. ```@return``` should return instance of InformationHandle.
```java
    /**
     * Get instance of InformationHandle.
     * 
     * @return
     */
    public static InformationHandle getInstance() {
        if (ih == null)
            ih = new InformationHandle();
        return ih;
    }
```
2. ```@param top5``` should be deleted.
```java 
    /**
     * Load the information
     * 
     * @param allCountries map a country with its sentence number
     * @param top5         of the most case
     * @param sourceFile   file to get the information
     */
    private void getFundamentalInfo(Map<String, Integer> allCountries, String sourceFile) {
        File file = new File(sourceFile);
        ...
        String name = null;
```
3. From getFundamentalInfo above, ```String name = null;``` can be reduced to ```String name```

4. ```line = br.readLine();``` can be reduced to br.readline()
```java
    public int[] latestWeekGlobal(String sourceFile) {
        String line;
        int[] lastWeek = new int[7];
        String[] lineSplit;
        File file = new File(sourceFile);
        try (InputStream in = new FileInputStream(file);
                Reader reader = new InputStreamReader(in);
                BufferedReader br = new BufferedReader(reader);) {
            line = br.readLine();
            int number;
```

<h1>Refactoring for PA2</h1>
From https://github.com/OOP2020/pa2-pattarinn

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


