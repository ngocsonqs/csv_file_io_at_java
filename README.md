# csv_file_io_at_java
Java でCSVファイルを入出力するラッパークラスを作ってみたのでメモ。

---
### CSVファイルの書き込み
- 書き込みには、`PrintWriter` クラスを使用。  
- 参考： [PrintWriter (Java Platform SE8)](https://docs.oracle.com/javase/jp/8/docs/api/java/io/PrintWriter.html)  
- コンストラクタでは、とりあえずファイル名（String)　とファイル（File）に対応させた。  
- `.print` または `.println`の引数に、CSVに書きたい値を渡すだけで書き込みできるようにした。  
- 可変長引数にしているので以下のように書ける。    
``` Java
CSVPrintWriter csvpw = new CSVPrintWriter("test.csv");
csvpw.println(100, "ngocsonqs", 20);
csvpw.println(101, "duongvan", 30);
```
**結果:**　*test.csv*  
``` Java
100,ngocsonqs,20,
101,duongvan,30,
```  

``` Java
public class CSVPrintWriter {
  PrintWriter pw_;

  // Constructor
  public CSVPrintWriter(String fileName) {
      try {
          this.pw_ = new PrintWriter(fileName);
      } catch (FileNotFoundException fnex) {
          fnex.printStackTrace();
      }
  }

  public CSVPrintWriter(File file) {
      try {
          this.pw_ = new PrintWriter(file);
      } catch (FileNotFoundException fnex) {
          fnex.printStackTrace();
      }
  }

  public void print(Object arg) {
      synchronized (this.pw_) {
          this.pw_.print(arg);
          this.pw_.print(",");
      }
  }

  public void println(Object... args) {
      synchronized (this.pw_) {
          for (int i = 0; i < args.length; i++) {
              this.pw_.print(args[i]);
              this.pw_.print(",");
          }
          this.pw_.println();
      }
  }
}
```

---
### CSVファイルの読み込み
- 読込には、`Scanner`クラスを使った。  
- 参考: [Scanner (Java Platform SE 8)](https://docs.oracle.com/javase/jp/8/docs/api/java/util/Scanner.html)  
- コンストラクタは、`File`、`String`、`InputStream`に対応させた。
- `.read`メソッドで、CSVデータを `ArrayList<String[]>`で返すようにした。`ArrayList`のサイズはCSVデータの行数になり、`String[]`のサイズは、カラム数になる。
- Scannerの`.nextLine()`でCSVの各行にアクセスする。文字列で返ってくるので、`.split(",")`でカンマ区切りでString[]化している。  

``` Java
public class CSVScanner {
    Scanner scan_;

    // Constructor
    public CSVScanner(File source) {
        try {
            this.scan_ = new Scanner(source);
        } catch (FileNotFoundException fnex) {
            fnex.printStackTrace();
        }
    }

    public CSVScanner(String source) {
        this.scan_ = new Scanner(source);
    }

    public CSVScanner(InputStream source) {
        this.scan_ = new Scanner(source);
    }

    public ArrayList<String[]> read() {
        ArrayList<String[]> csv_list = new ArrayList<>();
        String[] line_str_arr;

        synchronized (this.scan_) {
            while (this.scan_.hasNext()) {
                line_str_arr = this.scan_.nextLine().split(",");
                csv_list.add(line_str_arr);
            }
        }
        return csv_list;
    }
}
```
