## 簡介

Go 是一門新的程式語言。雖然它從現有的程式語言中借鏡了許多設計理念，但它也有許多與眾不同的特性。若你把用 C++ / Java 撰寫的程式改寫成等價的 Go 程式，恐怕難以得到令人滿意的結果。同樣地，用 Go 語言的角度來解決問題，你可能會寫出有效但不太一樣的程式。換句話說，想要寫出好的 Go 程式，對於 Go 語言特性及慣例的掌握是重要的一環。同時，對於社群慣例 (比如排版、命名方式、程式的建立等等) 也應該要適當的了解，這樣其他使用 Go 語言的開發者才容易理解你寫的程式。

這份文件列舉出一些訣竅，讓你寫出清楚、符合 Go 語言特性的程式。這是對於 [Go 語言規格書](https://golang.org/ref/spec)、[導覽 Go 語言](https://tour.golang.org/) 以及 [如何撰寫 Go 程式](https://golang.org/doc/code.html) 等三份文件的補充，所以你應該先讀過這三份文件。

### 範例

[Go 標準套件庫原始碼](https://golang.org/src/) 除了作為核心程式庫之外，也是如何使用 Go 語言的範例。更進一步地，大部份的套件都會有一些可以直接在 [golang.org](golang.org) 網站上讓你實驗的範例，比如像是 [這個](https://golang.org/pkg/strings/#example_Map) (你也許會需要點擊 Example 字樣來顯示範例)。如果你對於如何呈現問題或是某事物的實作方式有疑問，Go 套件庫的文件、原始碼以及範例可以提供你靈感、解答或是相關的背景知識。

## 排版格式

排版風格問題是最常被討論但很少有結果的問題。人們可以套用各式各樣的風格，但如果大家的風格能統一的話會更好。風格統一最困難的一點，就是如何避免寫出落落長的風格規範。

對於 Go 語言來說，我們使用了一種很少見的解決方式：讓機器來為我們處理排版問題。`gofmt` 工具 (或是 `go fmt`，它會將整個套件的原始碼都重新排版，而非只處理單一源碼檔案) 讀取 Go 程式碼，依照標準的排版風格把程式碼重新排版。如果你想弄清楚某種情況下應該怎麼排版，就執行 `gofmt`。如果結果看起來怪怪的，你可以調整你的程式 (或是提出 `gofmt` 的錯誤回報)，千萬不要削足適履 。

舉個例子，你再也不需要拼命按 Tab 和空白鍵去對齊你的行內註解：

```go
type T struct {
    name string // name of the object
    value int // its value
}
```

`gofmt` 會幫你處理好

```go
type T struct {
    name    string // name of the object
    value   int    // its value
}
```

標準套件庫的原始碼都有使用 `gofmt` 排版過。

當然還有些排版的細節我們沒有提到，簡略的說：

* 縮排：我們使用 Tab 來縮排，`gofmt` 預設會忽略它們。非必要的話別使用空白來縮排。
* 行寬：Go 沒有行寬限制，不用擔心你的程式碼太長。如果你真的覺得它太長了，可以換行並縮排它。
* 括號：Go 需要的括號遠比 C++ / Java 來得少：流程控制敘述 (if, for, switch) 沒有小括號。運算子之間的優先順序也會以排版的方式突顯。比如

```go
x<<8 + y<<16
```

## 註解

Go 提供的 C 風格的區塊註解 /* */ 以及 C++ 風格的行內註解 //。行內註解比較常用到，區塊註解通常是用來說明整個套件，但當你想要暫時停用某段程式碼的時候也很適合。

`godoc` 程式 (它同時也是一個 web server) 會解析原始碼中的註解。在最外層的宣告語句上面的註解，如果沒有被空行隔開的話，會當成是對於那個宣告語句的說明。這些說明的內容決定了 `godoc` 是否能產生出高品質的說明文件。

每個套件都應該要有一份套件說明，也就是在 package 語法上方的區塊註解。如果套件裡有多個源碼檔案，註解可以寫在其中任何一個檔案裡。套件說明應該是整體性的說明，或是提供整個套件的通用資訊。套件說明在 `godoc` 產生出來的文件裡會在頁面的最上方，所以你應該寫詳細點，像是這樣：

```go
/*
Package regexp implements a simple library for regular expressions.

The syntax of the regular expressions accepted is:

    regexp:
        concatenation { '|' concatenation }
    concatenation:
        { closure }
    closure:
        term [ '*' | '+' | '?' ]
    term:
        '^'
        '$'
        '.'
        character
        '[' [ '^' ] character-ranges ']'
        '(' regexp ')'
*/
package regexp
```

如果套件很簡單，說明當然也可以簡短些：

```go
// Package path implements utility routines for
// manipulating slash-separated filename paths.
```

註解不需要用星號一類的字元做出框框，因為產生出來的文件不一定會用固定寬度的文字來呈現，所以不要用空白一類的方式來排版，`godoc` 會幫你處理排版問題。註解應該是純文字，像 HTML 或是其他標記方式應該要避免。其中一項 `godoc` 的排版方式，是把縮排後的文字用固定寬度文字顯示，方便你在說明中嵌入範例程式碼。[fmt 套件](https://golang.org/pkg/fmt/) 的說明是個好例子。

某些情況下 `godoc` 也許完全不會重新排版，所以你應該讓你的註解容易閱讀：不要寫錯別字、正確使用標點符號，在適當的地方斷句等等。

套件裡的每個最上層的宣告語句，其上方的註解都會被當成是那個語句的說明文件。每個公開的名稱 (exported，就是首字大寫的名稱) 都應該要有一份說明。

說明文件應該要是完整的句子，第一句應該是簡略的說明，並且以宣告語句所宣告的名稱做為起始。

```go
// Compile parses a regular expression and returns, if successful, a Regexp
// object that can be used to match against text.
func Compile(str string) (regexp *Regexp, err error) {
```

這樣的設計是為了方便你使用 `grep` 來搜尋用 `godoc` 產生出來的文件。假設你忘記 regexp 套件中某個函式的名稱，但你記得這個函式會分析 (parse) 正則式，你可能會這樣子搜尋文件：

```sh
godoc regexp | grep parse
```

如果你的說明文件寫的是「這個函式是用來...」，顯然這樣搜尋不會對你有什麼幫助。但如果你把名稱寫在說明文件的開頭，你就會知道 Compile 函式可能是你想要的東西：

```
$ godoc regexp | grep parse
    Compile parses a regular expression and returns, if successful, a Regexp
    parsed. It simplifies safe initialization of global variables holding
    cannot be parsed. It simplifies safe initialization of global variables
$
```

Go 的宣告語法允許把同質性的宣告包成一個群組，所以你可以在上方加一段註解來進行整體性的說明。

```go
// Error codes returned by failures to parse an expression.
var (
    ErrInternal      = errors.New("regexp: internal error")
    ErrUnmatchedLpar = errors.New("regexp: unmatched '('")
    ErrUnmatchedRpar = errors.New("regexp: unmatched ')'")
    ...
)
```

群組宣告也可以用來表示變數之間有某種關聯，比如某些被同步鎖保護的變數：

```go
var (
    countLock   sync.Mutex
    inputCount  uint32
    outputCount uint32
    errorCount  uint32
)
```

## 命名規則

就像在其他語言一樣，命名規則對 Go 語言來說是十分重要的。更進一步地，它還有語意上的效果：一個變數、常數、函式是否能被其他套件的程式碼取用，端看它的名稱是否是以大寫字母開頭。所以命名規則實在值得我們花點篇幅來好好介紹一番。

### 套件命名

當你引用 (import) 某個套件之後，它的名稱便成為你存取相關資源的憑據。比如你

```go
import "bytes"
```

之後，就可以用 `bytes.Buffer` 來存取 bytes 套件中定義的 Buffer。如果每個人都可以用相同的名字來存取相同的套件內容，事情就會簡單許多；這代表了套件的名字要取的簡潔有力。慣例上，套件名稱應該是小寫的單字，避免使用底線或大小寫混雜。考慮到大家都會在程式碼中一再輸入各種名稱，像是 *Err* 這樣的命名方式顯然夠簡短，又能讓人一眼看出來它大概會是什麼。套件名稱只是引用時的預設名稱，所以不用擔心會和其他套件撞名，也不需要強制在每個源碼檔中使用相同的名稱。如果真的發生撞名的情況，你完全可以在引用的時候指定另一個名字給它。通常你不會因此搞混，因為引用時的完整套件名稱讓你可以確定自己是引用了什麼。

另一個慣例是我們會用原始碼的目錄作為套件名稱：放在 *src/encoding/base64* 目錄裡的套件會以 *encoding/base64* 這個名稱引入，不是 *encoding_base64* 也不是 *encodingBase64*。

程式可以用套件名稱來參考套件中公開的資源，這樣可以避免混淆。切勿使用 `.` 來引入，這是設計來簡化一些必須在套件外進行的測試，除此之外的用途應當盡量避免。例如在 `bufio` 套件中，具有緩衝區的可讀取串流會命名為 `Reader` 而不是 `BufReader`，因為 `bufio.Reader` 比 `bufio.BufReader` 更簡短也更精確。進一步來說，因為引用的時候會以套件名稱作為前綴，所以 `bufio.Reader` 不會跟 `io.Reader` 混淆。同樣的，用來產生一個新的 `ring.Ring` 實體的函式 (在 Go 語言裡稱為建構子)，通常會命名為 `NewRing`，但考慮到 `ring` 套件裡只有 `Ring` 這個公開的 struct，而且套件名稱跟 struct 名稱又重複，所以取名為 `New` 可以更加簡潔：其他人會使用 `ring.New` 來呼叫它。總之就是利用套件結構來幫助你選擇一個好名字。

另一個好例子是 `once.Do`：寫 `once.Do(setup)` 很清楚，就算你寫的更長 `once.DoOrWaitUntilDone(setup)` 也不會更清楚。很長的名稱不見得讓人容易理解，清楚又精確的說明文件會更有效果。

### 成員存取方法 (Getter / Setter)

Go 語言並沒有在語言層面支援成員存取方法。使用成員存取方法沒有錯，而且通常是不錯的設計模式，但是在成員存取方法前加上 Get 或 Set 既不必要，也不符合 Go 語言的慣例。如果你有個成員變數叫做 `owner` (小寫字母開頭代表不能公開存取)，那麼相關的成員存取方法應該稱為 `Owner()` (大寫字母開頭) 以及 `SetOwner()`。這樣閱讀起來也十分清楚：

```go
owner := obj.Owner()
if owner != user {
    obj.SetOwner(user)
}
```

### 介面名稱

慣例上，只有一個方法的介面，會用方法的名字加上 er 字尾，或是其他類似的方式來命名，這樣可以營造一種代理人的感覺，像是 `Reader`, `Writer`, `Formatter` 等等。

像這樣的名字很多，而遵守這個慣例會讓你更有生產力。像是 `Read`, `Write`, `Close`, `String` 這些都是，它們都有標準化的定義。為了避免混淆，除非你寫的函式和它們有相同的定義，否則就不要使用這些名字。相反地，如果你實做了相同的定義的函式，那麼就應該照它們的方式命名，比如用 `String` 而不是 `ToString`。

### 大小寫混合

最後，如果你需要使用數個單字來做命名，用 `MixedCaps` 或是 `mixedCaps`，不要用底線。

## 分號

Go 也像 C 語言一樣使用分號來分隔敘述，但你在源碼檔中看不見它們。Go 的編譯器 (正確來說是語法分析器 lexer) 會依照簡單的規則自動在分析語法的時候幫你加上分號，所以通常你不需要自己動手。

規則很單純，如果一行的最後一個語法單元是識別子 (identifier)、基本常數 (像是數字或字串)、或是以下幾個關鍵字之一

```
break continue fallthrough return ++ -- ) }
```

編譯器會在這些語法單元後面加上分號。簡單來說，如果某一行是以「代表語句終結」的關鍵字結束，就加上分號。

在右大括號前面的分號也可以省略：

```go
go func() { for { dst <- <-src } }()
```

Go 程式通常只會在 `for` 敘述裡使用分號，這是用來隔開迴圈的初始、條件、後處理這三個寫在同一行的敘述。

這個自動加分號的規則暗示了你不可以把流程控制語句 (`for`, `if`, `switch`, `select`) 的左大括號放到下一行，因為分號會加到大括號的前面去，而顯然這不會是你想要的結果。所以應該要寫成這樣

```go
if i < f() {
    g()
}
```

## 流程控制

Go 的流程控制語法跟 C 很像，但有很多重要的不同。Go 沒有 `do` 和 `while`，只有比 C 更通用一點的 `for`；`switch` 比較有彈性；`if` 跟 `switch` 可以在條件判斷式之前加上一個初始語句，有點像 `for` 那樣；`break` 和 `continue` 可以指定標記；而且還有一些新的特性：支援型別判斷的 `switch` 和頻道多工器 `select`。語法上也有些微不同：條件式不用加小括號、不能省略大括號。

### if

簡單的 `if` 語句像是這樣

```go
if x > 0 {
    return y
}
```

不能省略的大括號暗示了應該避免寫單行的 `if` 語句，尤其當裡面有 `return` 或 `break` 的時候更是如此。

因為 `if` 像 `for` 一樣可以接受初始語句，所以在 `if` 語句初始一個區域變數是很常見的寫法：

```go
if err := file.Chmod(0664); err != nil {
    log.Print(err)
    return err
}
```

在 Go 的核心程式庫裡，你會發現很多可能不會執行下一行程式的 `if` 語句 (比如說裡面的最後一個敘述是 `return`, `break` continue` 之類的) 都省略掉了 `else` 敘述：

```go
f, err := os.Open(name)
if err != nil {
    return err
}
codeUsing(f)
```

這是一般常見用來偵測、防止例外情況的寫法。如果你的程式一路上把出錯的情況都挑掉，正常的情況執行下去，那麼它就會很容易閱讀。如果錯誤都透過 `return` 敘述離開你的函式，那麼最後寫出來的程式碼就不會有 `else` 敘述：

```go
f, err := os.Open(name)
if err != nil {
    return err
}
d, err := f.Stat()
if err != nil {
    f.Close()
    return err
}
codeUsing(f, d)
```

### 重新宣告與重新指定

上一節的最後一個範例展示了短式宣告 `:=` 的使用方式。

```go
f, err := os.Open(name)
```

這一行宣告了 `f` 和 `err` 兩個變數。而後面的

```go
d, err := f.Stat()
```

看起來好像是宣告了 `d` 和 `err` 兩個變數。要注意的是，雖然看起來 `err` 好像宣告了兩次，但這是合法的敘述：`err` 在第一個敘述中宣告，而第二個敘述裡只是重新指定了新的值。也就是說在呼叫 `f.Stat` 的時候，`err` 會使用之前宣告的那個變數，只是賦予新的值給它。

所以若是滿足以下三個條件，使用 `:=` 宣告先前宣告過的變數會變成賦予新值：

* 必須要在相同的有效範圍 (scope) 內。如果是不同的有效範圍就會變成宣告一個同名的、全新的區域變數 (註)。
* 型別要相同。
* `:=` 左邊至少要有一個之前沒有宣告過的變數。

這個特性雖然不太常見，但卻很實用。這會讓你簡單地重用相同的 `err` 變數，這種用法在一長串的 *if-else* 中特別常見。

備註：值得注意的是，在 Go 語言中，函式的參數及回傳值是寫在函式大括號的外面，但其實它們跟函式本體屬於同一個有效範圍。

### for

Go 裡面的 `for` 跟 C 裡面的有點像又不太像。它把 `for` 跟 `while` 整合在一起 - 因為 Go 沒有 *do-while*。`for` 有三種形式：

```go
// Like a C for
for init; condition; post { }

// Like a C while
for condition { }

// Like a C for(;;)
for { }
```

短式宣告讓你可以輕易宣告一個只在迴圈中有效的變數：

```go
sum := 0
for i := 0; i < 10; i++ {
    sum += i
}
```

如果你要遍歷一個 array, slice, string 或是 map，你可以使用 `range` 語法：

```go
for key, value := range oldMap {
    newMap[key] = value
}
```

如果只要 key 值的話：

```go
for key := range m {
    if key.expired() {
        delete(m, key)
    }
}
```

相反地，不要 key 值的話：

```go
sum := 0
for _, value := range array {
    sum += value
}
```

空識別子 (`_`) 有很多用法，會在稍後的章節介紹。

在遍歷字串 (string) 的時候，`for` 會幫你做些處理：幫你解析 UTF8，斷字在正確的位元。錯誤的編碼會消耗一個位元組 (byte)，並回傳一個特別的 `rune` (`rune` 是 Go 的術語，指的是一個 UTF8 的字元。在 Go 裡有同名的套件專門處理 `rune`) U+FFFD。以下的程式碼：

```go
for pos, char := range "日本\x80語" { // \x80 is an illegal UTF-8 encoding
    fmt.Printf("character %#U starts at byte position %d\n", char, pos)
}
```

會顯示

```go
character U+65E5 '日' starts at byte position 0
character U+672C '本' starts at byte position 3
character U+FFFD '�' starts at byte position 6
character U+8A9E '語' starts at byte position 7
```

最後，Go 沒有逗號這個運算子，而且 `++` 和 `--` 是完整的語句而不是敘述的一部份。如果你需要在 `for` 裡使用多個變數，你可以用多重指定：

```go
// Reverse a
for i, j := 0, len(a)-1; i < j; i, j = i+1, j-1 {
    a[i], a[j] = a[j], a[i]
}
```

### switch

Go 的 `switch` 比 C 的要泛用。`case` 敘述比對的值不一定要是常數。`case` 敘述會依上到下的順序一一比對，直到找到符合的項目。如果 `switch` 後面沒有東西，那麼就會當成是 `switch true`。所以你可以 (而且這也是慣例) 把一長串的 `if-else if` 改寫成 `switch`：

```go
func unhex(c byte) byte {
    switch {
    case '0' <= c && c <= '9':
        return c - '0'
    case 'a' <= c && c <= 'f':
        return c - 'a' + 10
    case 'A' <= c && c <= 'F':
        return c - 'A' + 10
    }
    return 0
}
```

不像 C 語言，Go 的 `switch` 不會跨過 `case` 執行，但你可以使用逗號代表「這些值都可以」：

```go
func shouldEscape(c byte) bool {
    switch c {
    case ' ', '?', '&', '=', '#', '+', '%':
        return true
    }
    return false
}
```

雖然在 Go 裡不常用，但你確實可以用 `break` 跳出 `switch`，就像在其他類 C 的語言裡一樣。有時候你不止想要跳出 `switch`，可能想要連外層的 `for` 迴圈一併跳過，你可以用 `break` 加標記的方式達成：

```go
Loop:
	for n := 0; n < len(src); n += size {
		switch {
		case src[n] < sizeOne:
			if validateOnly {
				break
			}
			size = 1
			update(src[n])

		case src[n] < sizeTwo:
			if n+1 >= len(src) {
				err = errShortInput
				break Loop
			}
			if validateOnly {
				break
			}
			size = 2
			update(src[n] + src[n+1]<<shift)
		}
	}
```

當然，`continue` 也可以加標記，但只能用在迴圈上。

讓我們用一個例子來結束這一節：比對兩個 []byte 是否具有相同的內容

```go
// Compare returns an integer comparing the two byte slices,
// lexicographically.
// The result will be 0 if a == b, -1 if a < b, and +1 if a > b
func Compare(a, b []byte) int {
    for i := 0; i < len(a) && i < len(b); i++ {
        switch {
        case a[i] > b[i]:
            return 1
        case a[i] < b[i]:
            return -1
        }
    }
    switch {
    case len(a) > len(b):
        return 1
    case len(a) < len(b):
        return -1
    }
    return 0
}
```

### 判斷型別的 switch (type switch)

`switch` 也可以用來判斷動態型別，這會用到型別斷言 (type assertion) 的語法及 `type` 關鍵字。如果你宣告一個區域變數給它，那麼這個區域變數的型別會和 `case` 敘述比對的型別相同：

```go
var t interface{}
t = functionOfSomeType()
switch t := t.(type) {
default:
    fmt.Printf("unexpected type %T\n", t)     // %T prints whatever type t has
case bool:
    fmt.Printf("boolean %t\n", t)             // t has type bool
case int:
    fmt.Printf("integer %d\n", t)             // t has type int
case *bool:
    fmt.Printf("pointer to boolean %t\n", *t) // t has type *bool
case *int:
    fmt.Printf("pointer to integer %d\n", *t) // t has type *int
}
```

## 函式

### 多重傳回值

Go 有個很不尋常的特性：函式及方法可以回傳多個值。這個特性可以改善一些 C 語言的不良慣例：出錯時回傳負值，並改變某個傳址的參數。

在 C 語言裡，寫入錯誤會回傳 -1 (寫入了 -1 個位元組，顯然你知道這有問題)，然後把錯誤碼寫進某個傳址進來的參數。在 Go 語言裡，我們會回傳寫入了多少位元組 (這點跟 C 差不多)，同時也回傳錯誤碼：「好，你確實寫了幾 byte 進去，不過不是全部，因為磁碟機滿了」。所以 Go 語言裡的 `Write` 函式的定義是這樣的：

```go
func (file *File) Write(b []byte) (n int, err error)
```

如同說明文件所示，它會回傳已經寫入的位元組數，如果這與 `b` 的長度不符，err 就會是錯誤碼。你可以在稍後 *錯誤處理* 的章節看到更多例子。

這樣的形式消除了回傳參考的需求：

```go
func nextInt(b []byte, i int) (int, int) {
    for ; i < len(b) && !isDigit(b[i]); i++ {
    }
    x := 0
    for ; i < len(b) && isDigit(b[i]); i++ {
        x = x*10 + int(b[i]) - '0'
    }
    return x, i
}

for i := 0; i < len(b); {
    x, i = nextInt(b, i)
    fmt.Println(x)
}
```

### 回傳值的預先命名

回傳值可以預先給予名字，這樣就可以在函式內像一般變數那樣地使用它。它們會被初始化為零值，而遇到沒有加上回傳值的 `return` 語句時，就會把它們傳回去。

這種寫法不是預設的，但很多時候可以讓你的程式碼清楚不少：這等於在寫說明文件。如果我們把剛剛的 `nextInt` 函式的回傳值加上命名，你就能一目瞭然地理解哪個 int 是什麼意義：

```go
func nextInt(b []byte, pos int) (value, nextPos int) {
```

因為這些變數會初始化為零值，如果用的好的話可以讓整段程式都簡化許多。以下以 `io.ReadFull` 做為例子：

```go
func ReadFull(r Reader, buf []byte) (n int, err error) {
    for len(buf) > 0 && err == nil {
        var nr int
        nr, err = r.Read(buf)
        n += nr
        buf = buf[nr:]
    }
    return
}
```

### Defer

`defer` 語句是把某個函式的呼叫排程在返回目前函式時才執行。這個特性不是很常用，但在某些情況下，比如必須釋放的資源，會非常有用。典型的例子是釋放同步鎖或是關閉檔案：

```go
// Contents returns the file's contents as a string.
func Contents(filename string) (string, error) {
    f, err := os.Open(filename)
    if err != nil {
        return "", err
    }
    defer f.Close()  // f.Close will run when we're finished.

    var result []byte
    buf := make([]byte, 100)
    for {
        n, err := f.Read(buf[0:])
        result = append(result, buf[0:n]...) // append is discussed later.
        if err != nil {
            if err == io.EOF {
                break
            }
            return "", err  // f will be closed if we return here.
        }
    }
    return string(result), nil // f will be closed if we return here.
}
```

使用 `defer` 有兩大好處：首先，你不會因為忘記釋放資源或關閉檔案造成程式錯誤；其次，如果你在開啟檔案之後馬上用 `defer` 語句來預約關閉檔案，會比在程式結尾關閉檔案更好閱讀。

使用 `defer` 時，參數會在你寫下 `defer` 本身執行的時候就確定它的值，而不是在函式真正執行的時候。所以不用擔心後續的變數操作，同時也表示你可以用一行 `defer` 來定義數個操作，以下是個不太優雅的例子：

```go
for i := 0; i < 5; i++ {
    defer fmt.Printf("%d ", i)
}
```

`defer` 會以相反的順序執行，也就是最先定義的 `defer` 會最後執行。所以上面的範例最後會印出「4 3 2 1 0」。有個比較可能的例子：用來追蹤函式的執行：

```go
func trace(s string)   { fmt.Println("entering:", s) }
func untrace(s string) { fmt.Println("leaving:", s) }

// Use them like this:
func a() {
    trace("a")
    defer untrace("a")
    // do something....
}
```

由於在 `defer` 執行的時候，參數的值便已經確定，我們可以好好利用這個特性：

```go
func trace(s string) string {
    fmt.Println("entering:", s)
    return s
}

func un(s string) {
    fmt.Println("leaving:", s)
}

func a() {
    defer un(trace("a"))
    fmt.Println("in a")
}

func b() {
    defer un(trace("b"))
    fmt.Println("in b")
    a()
}

func main() {
    b()
}
```

這會印出

```
entering: b
in b
entering: a
in a
leaving: a
leaving: b
```

對於已經習慣以程式區塊作為資源管理單位的開發者，這樣的行為可能會有點詭異，不過事實上 `defer` 的資源管理單位是函式，這也是它強大之處。後續在 *panic 和 recover* 的章節我們會看到 `defer` 的其他可能性。

譯註：上一段翻譯品質不佳，需要 PR

## 資料

### 用 *new* 來配置

Go 有兩個配置資源的內建函式：`new` 和 `make`。兩者適用於不同的場合，也許看起來有點讓人容易搞混，但其實很簡單。我們世來看 `new`。雖然它確實會配置記憶體，但不像在其他語言那樣，Go 的 new 函式不會「建構」它 (譯註)，只會填入零值。也就是說，`new(T)` 配置了一塊記憶體，填入符合 `T` 型別的零值，然後把位址傳回來。用 Go 的講法，就是回傳一個 `T` 型別的指標，記憶體內容是零值。

譯註：原文 *but unlike its namesakes in some other languages it does not initialize the memory* 中的 *initialize* 通常翻成「初始化」，但此處語意應該是「不像 C++ 或 Java 那樣會幫你呼叫建構子 (constructor) *處理成員的初始值*」，所以改譯為「建構」。

既然 `new` 會為你填入零值，你可以把你的資料結構設計成零值就能用，那麼寫起程式來就會方便很多。比如像 `bytes.Buffer` 的說明文件：the zero value for Buffer is an empty buffer ready to use. (Buffer 的零值就是一個空 buffer 並且可以直接使用)。類似地，`sync.Mutex` 並沒有建構子，但它的零值就是一個解開 (unlock) 的鎖。

這個特性可以一個接一個的擴展下去。考慮下列型別：

```go
type SyncedBuffer struct {
    lock    sync.Mutex
    buffer  bytes.Buffer
}
```

這個型只要 `new` 完或是宣告完就可以馬上使用 (譯註)。以下的程式碼中，`p` 和 `v` 都不需要另外再做初始化：

```go
p := new(SyncedBuffer)  // type *SyncedBuffer
var v SyncedBuffer      // type  SyncedBuffer
```

譯註：因為 `SyncedBuffer` 的所有成員都符合這個零值就能使用的特性，所以它本身也可以直接使用。

### 建構子及複合結構的表達式 (literal)

有時候零值實在無法符合我們的需求，這時候就要乖乖寫建構子了。以下是從 `os` 套件節錄出來的一小段程式：

```go
func NewFile(fd int, name string) *File {
    if fd < 0 {
        return nil
    }
    f := new(File)
    f.fd = fd
    f.name = name
    f.dirinfo = nil
    f.nepipe = 0
    return f
}
```

這寫法真是又臭又長，而我們可以用複合結構表達式來簡化它。複合結構表達式是一段敘述，當執行這段敘述的時候會產生一個實體。

```go
func NewFile(fd int, name string) *File {
    if fd < 0 {
        return nil
    }
    f := File{fd, name, nil, 0}
    return &f
}
```

有件事值得注意：在 Go 語言中回傳區域變數的位址是很正常的，它不會因為函式結束而被釋放掉。事實上，對複合結構表達式取址的時候，會產生另一個新的實體，所以我們應該把兩行再縮成一行

```go
return &File{fd, name, nil, 0}
```

當你使用上面那種複合結構表達式的時候，struct 中所有的屬性都必須依順序給予一個值。如果你想省略零值，可以用 **label: value** 這樣的形式來為非零的屬性賦值。

```go
return &File{fd: fd, name: name}
```

如果在複合結構表達式中沒有對任何屬性賦值，那麼產生出來的實體就會是零值。也就是說 `new(File)` 跟 `&File{}` 是等價的。

複合結構表達式也可以用來產生 array, slice 或是 map：屬性的標籤是索引 (當然型別必須相符)。

```go
a := [...]string   {Enone: "no error", Eio: "Eio", Einval: "invalid argument"}
s := []string      {Enone: "no error", Eio: "Eio", Einval: "invalid argument"}
m := map[int]string{Enone: "no error", Eio: "Eio", Einval: "invalid argument"}
```

### 用 make 來配置

內建的 `make(T, args)` 函式雖然也是配置，但與 `new(T)` 完全不同。它是專門用來配置 slice, map 以及 channel 用的，而且它回傳的會是「初始化」過的 `T` 型別 (不是 `*T` 型別，也不是零值)。這三種型別會參考到一些必須要初始化的內部資料結構，所以需要另外處理。像是 slice，其實是一個資料結構，記載了指標 (指向陣列中的資料)，長度和容量。如果這三者沒有被初始化的話
， slice 就是 nil。所以 `make` 會初始化 slice, map 和 channel 的內部資料結構以備使用。例如：

```go
make([]int, 10, 100)
```

會配置一個承載 int 的 slice，長度是 10，最大容量是 100，並且有一個指標指向最前面的 10 個 int。配置 slice 的時候可以省略容量參數，之後在有關 slice 的章節會有更詳細的討論。反過來說，`new([]int)` 回傳的是一個指標，指向了新配置的，填入零值的 slice 內部結構；也就是一個指向 nil slice 的指標。

以下說明了 `new` 和 `make` 的差別

```go
var p *[]int = new([]int)       // allocates slice structure; *p == nil; rarely useful
var v  []int = make([]int, 100) // the slice v now refers to a new array of 100 ints

// Unnecessarily complex:
var p *[]int = new([]int)
*p = make([]int, 100, 100)

// Idiomatic:
v := make([]int, 100)
```

要注意的是 `make` 只能配置 slice, map 或是 channel，而且它回傳的不是指標。如果一定要指標的話，你得用 `new` 去處理。(譯註：像上面那個例子中，比較複雜的那部份一樣)

### Array

譯註：array 通常譯為陣列，但在 Go 語言中，array 十分容易與 slice (通常譯為片段)搞混。兩者在 Go 語言中的意涵與其他語言不大相同，可以算是專有名詞，故以下都不做翻譯，藉此提升讀者對這兩個名詞的熟悉度。而「陣列」一詞則會泛指類 C 語言中的陣列的行為。

Array 在規劃記憶體的時候相當實用，有時候還可以用來避免不必要的資源配置動作。不過它最主要的功能還是做為 slice 的基底，slice 在下一節會討論。做為 slice 的先備知識，這裡提出一些 array 的特點。

Array 在 Go 和 C 語言之間有些重大的不同：

* Go 語言的 array 是單純的值。把 array 指定給變數的時候會複製一份副本。
* 同樣的，把 array 當成參數傳入函式的時候，函式內收到的會是副本。
* array 的長度是型別的一部份，也就是說 `[10]int` 和 `[20]int` 屬於不同型別。

Array 是值這件事有時候很有用，有時候卻代價高昂。如果你需要類似 C 語言陣列那樣的行為，你可以用指標：

```go
func Sum(a *[3]float64) (sum float64) {
    for _, v := range *a {
        sum += v
    }
    return
}

array := [...]float64{7.0, 8.5, 9.1}
x := Sum(&array)  // Note the explicit address-of operator
```

不過這不是 Go 語言的慣例，我們應該用 slice。

### Slice

Slice 可以說是在 array 外面包了一層，提供更方便有效的介面存取一連串的資料。除了像是處理矩陣這種事之外，在 Go 語言中大部份的陣列操作都是用 slice 完成的。

Slice 會有一個指向內部 array 的指標，如果你把一個 slice 指定給另一個，兩個都會指向同一個 array。如果你把 slice 當成參數傳入函式，那麼在函式內部對 slice 元素做的改變，函式之外也會存取得到，如同你傳入的是指標一樣。所以 `Read` 函式接受 slice 當參數，而不是一個指標配上一個整數：slice 的長度指出了有多少資料可以讀取。以下是在 `os` 套件中，`File` 型別對於 `Read` 方法的定義：

```go
func (f *File) Read(buf []byte) (n int, err error)
```

這個方法回傳「讀到了幾 bytes」和一個錯誤值 (如果有錯誤的話)。如果你想把資料讀進某個緩衝區的前 32 bytes，就該把它切成 slice：

```go
n, err := f.Read(buf[0:32])
```

這種用法很常見，效率也很高。老實說，如果不考慮效率的話，以下的程式碼也可以達成相同的事

```go
var n int
var err error
for i := 0; i < 32; i++ {
    nbytes, e := f.Read(buf[i:i+1])  // Read one byte.
    if nbytes == 0 || e != nil {
        err = e
        break
    }
    n += nbytes
}
```

在不超出內部 array 的範圍之內，你可以任意的改變 slice 的長度：只要把它切成另一個 slice 就好了。而 slice 的容量可以透過內建的 `cap` 函式取得，它會回傳這個 slice 目前可能的最大長度。以下是一個範例，它可以把資料新增到 slice 的末端；如果超過 slice 容量的話，也會為你重新配置適合的大小，最後把新的 slice 傳回來。這個範例使用了 `len` 和 `cap` 這兩個函式的特性：nil slice 的長度和容量都是 0。

```go
func Append(slice, data []byte) []byte {
    l := len(slice)
    if l + len(data) > cap(slice) {  // reallocate
        // Allocate double what's needed, for future growth.
        newSlice := make([]byte, (l+len(data))*2)
        // The copy function is predeclared and works for any slice type.
        copy(newSlice, slice)
        slice = newSlice
    }
    slice = slice[0:l+len(data)]
    for i, c := range data {
        slice[l+i] = c
    }
    return slice
}
```

在這個範例中，我們一定得把新的 slice 傳回去。雖然我們可以 (透過指標) 直接修改 slice 裡的資料，但是在這裡我們要變更的是 slice 本身 (也就是指向 array 的指標、slice 的長度及容量等資料)，而這些資料都是單純的值，所以在函式內只能收到副本。

這個範例實用到我們決定內建一個類似的函式 `append`。我們還需要了解一些其他的知識才能理解 `append` 的設計，所以之後的章節會再次討論 `append`。

### 二維的 slice

Array 和 slice 都是一維的。Go 語言中所謂二維的 array 或是 slice，其實是「裝載 array 的 array」以及「裝載 slice 的 slice」：

```go
type Transform [3][3]float64  // A 3x3 array, really an array of arrays.
type LinesOfText [][]byte     // A slice of byte slices.
```

而因為 slice 的大小是可以動態改變的，這意味著「被裝載在 slice 裡的那些 slice」不需要有相同的長度或容量。這寫法還滿常用的，以上面那個 LinesOfText 作例子：

```go
text := LinesOfText{
	[]byte("Now is the time"),
	[]byte("for all good gophers"),
	[]byte("to bring some fun to the party."),
}
```

有時候這種技巧是必要的，比如掃描一個圖形中每一列裡的每個點。有兩種方法可以達成這件事：你可以把為每一列都配置一個 slice，或是依序把每個點放進一個 array 裡，然後把每一列都切成不同的 slice。兩種方式各有優缺點，端看你的程式適合的是那一種。如果 slice 的長度會變的話，第一種方法可以避免覆蓋到不同列的資料。如果不會變的話，第二種方法可以避免一再配置記憶體造成的效能損耗。以下是這兩種做法的示意，首先是第一種做法：

```go
// Allocate the top-level slice.
picture := make([][]uint8, YSize) // One row per unit of y.
// Loop over the rows, allocating the slice for each row.
for i := range picture {
	picture[i] = make([]uint8, XSize)
}
```

然後是第二種：

```go
// Allocate the top-level slice, the same as before.
picture := make([][]uint8, YSize) // One row per unit of y.
// Allocate one large slice to hold all the pixels.
pixels := make([]uint8, XSize*YSize) // Has type []uint8 even though picture is [][]uint8.
// Loop over the rows, slicing each row from the front of the remaining pixels slice.
for i := range picture {
	picture[i], pixels = pixels[:XSize], pixels[XSize:]
}
```

### Map

Map 是非常好用的內建型別，它將某種型別的值 (這個值稱為索引) 跟另一種型別的值 (稱為元素，或是直接稱為值) 配成一對。任何可以使用 `==` 比對的型別都可以當做索引，像是整數、浮點數、複數、字串、指標、介面 (如果這個介面可以用 `==` 比對的話)、struct 或 array。Slice 不能用來當索引，因為它不支援 `==`。Map 也像 slice 那樣，有個內部的資料結構。如果你把 map 當成參數傳入某個函式中，在函式內修改 map 的元素也會反映到函式外。

你可以用複合結構表達式來初始化一個 map 的元素，語法很簡單：

```go
var timeZone = map[string]int{
    "UTC":  0*60*60,
    "EST": -5*60*60,
    "CST": -6*60*60,
    "MST": -7*60*60,
    "PST": -8*60*60,
}
```

賦值和取值的語法跟 array 或 slice 一樣，只是索引的型別不限於整數。

```go
offset := timeZone["EST"]
```

如果索引不存在的話，你會取到零值。所以集合 (數學中的 Set) 可以用 `map[某型別]bool` 來實現。當你要把某個元素加進集合的時候，就把那個元素所索引到的值設成 `true`。這樣透過對 map 取值的動作就可以知道某元素是否在集合中：

```go
attended := map[string]bool{
    "Ann": true,
    "Joe": true,
    ...
}

if attended[person] { // will be false if person is not in the map
    fmt.Println(person, "was at the meeting")
}
```

有時候分辨我們得確實分辨究竟是索引不存在還是這個值剛好是零值：上方 timeZone 的例子裡，`timezone["UTC"]` 是不存在還是剛好為零呢？我們可以用多重傳回值的方式確定：

```go
var seconds int
var ok bool
seconds, ok = timeZone[tz]
```

這個慣例很直觀的稱為「逗號 ok」。在這個例子裡，如果 tz 這個索引存在，second 會取到值，ok 會是 true。如果 tz 不存在，second 會是零值，而 ok 會是 false。底下的例子總合了之前我們介紹的東西來實作錯誤回報機制：

```go
func offset(tz string) int {
    if seconds, ok := timeZone[tz]; ok {
        return seconds
    }
    log.Println("unknown time zone:", tz)
    return 0
}
```

如果只是要測試索引是否存在，你可以用空白識別子：

```go
_, present := timeZone[tz]
```

要刪除某個索引，內建的 `delete` 函式可以做到。它的參數是 map 跟要刪除的索引。索引不存在的狀況下呼叫 `delete` 並不會造成錯誤。

```go
delete(timeZone, "PDT")  // Now on Standard Time
```

### 格式化輸出

(譯註：原文 printing 通常譯為列印，但本節內容係介紹類似 C 語言中 printf 的函式，故譯作格式化輸出)

Go 語言中的格式化輸出函式跟 C 語言中的 `printf` 系列函式很像，但支援了更多語法。相關的函式都在 `fmt` 套件中，函式名稱的首字母都是大寫，比如 `fmt.Printf`, `fmt.Fprintf` 等等。像 `Sprintf` 等處理字串的函式會回傳一個新的字串，不會修改原有的字串。

格式字串不一定是必需的，對於 `Printf`, `Fprintf` 和 `Sprintf`，各自都有對應的不同版本 - 不需要格式化字串的版本，比如 `Print` 和 `Println`。這些縃殊版本不需格式字串，而是把每一個參數照預設的格式輸出。`Println` 還會在輸出每個參數之後加個換行符號，而 `Print` 則是會在兩個字串參數之間空一格。以下四行程式的輸出是一樣的

```go
fmt.Printf("Hello %d\n", 23)
fmt.Fprint(os.Stdout, "Hello ", 23, "\n")
fmt.Println("Hello", 23)
fmt.Println(fmt.Sprint("Hello ", 23))
```

而 `fmt.Fprint` 系列則需要一個特別的參數：第一個參數必需要是實作 `io.Writer` 介面的任意物件，比如 `os.Stdout` 和 `os.Stderr`。

接下來開始跟 C 語言產生分岐了。首先，數值格式，像是 `%d`，不接受正負號或是位元長度的修飾詞，Go 會透過參數的型別來決定該輸出什麼。

```go
var x uint64 = 1<<64 - 1
fmt.Printf("%d %x; %d %x\n", x, x, int64(x), int64(x))
```

會輸出

```
18446744073709551615 ffffffffffffffff; -1 -1
```

如果你想指定預設格式，可以用 `%v`，輸出的格式會跟你用 `Print` 或 `Println` 函式一樣。同樣地，`%v` 可以接受 **任何** 型別，就算是 array, slice, map 和 struct 都沒問題。以上一節的時區 map 為例：

```go
fmt.Printf("%v\n", timeZone)  // or just fmt.Println(timeZone)
```

輸出會是

```
map[CST:-21600 PST:-28800 EST:-18000 UTC:0 MST:-25200]
```

輸出 map 的時候，索引順序不是固定的。在輸出 struct 的時候，你可以用 `%+v` 來一併輸出屬性名稱。而 `%#v` 則是輸出任何型別的完整 Go 語法。

```go
type T struct {
    a int
    b float64
    c string
}
t := &T{ 7, -2.35, "abc\tdef" }
fmt.Printf("%v\n", t)
fmt.Printf("%+v\n", t)
fmt.Printf("%#v\n", t)
fmt.Printf("%#v\n", timeZone)
```

會輸出 (注意那個取值符號 `&`)

```
&{7 -2.35 abc   def}
&{a:7 b:-2.35 c:abc     def}
&main.T{a:7, b:-2.35, c:"abc\tdef"}
map[string] int{"CST":-21600, "PST":-28800, "EST":-18000, "UTC":0, "MST":-25200}
```

`%q` 會輸出 Go 的字串語法，所以它接受的是字串和 []byte 型別。`%#q` 跟 `%q` 差不多，只是它用的不是雙引號，而是反引號。其實 `%q` 系列也接受整數和 rune，只是輸出的是用單引號包起來的單一 rune。還有 `%x` 可以接受字串、[]byte 和整數。`% x` 則會在每個 byte 之間加上空白。

另一個很好用的格式是 `%T`，會輸出參數的型別

```go
fmt.Printf("%T\n", timeZone)
```

會輸出

```
map[string] int
```

如果你想要控制某個自訂型別的預設輸出格式，只要為那個型別定義一個 `String() string` 方法就好。比如有某個自訂型別 T

```go
func (t *T) String() string {
    return fmt.Sprintf("%d/%g/%q", t.a, t.b, t.c)
}
fmt.Printf("%v\n", t)
```

輸出

```
7/-2.35/"abc\tdef"
```

如果你同時要控制 `T` 和 `*T` 的格式，那 `String` 方法的接收器必須是 `T` 型別；上面的範例用 `*T` 型別的原因是，對 struct 使用指標是慣例，因為這樣效率比較高。這在 `指標與數值接收器` 的章節會有更深入的討論

在我們自訂的 `String` 方法中可以呼叫 `Sprintf` 函式，這是因為 `Print` 系列函式都是可重複進入的。只有一種情況要避免：在 `String` 方法裡使用 `Sprintf` 的時候，不慎讓它又自動呼叫了同一個 `String` 方法。如果你要求 `Sprintf` 輸出接收到的 struct，就會發生這種事。這是相當常見的錯誤，下面是錯誤示範。

```go
type MyString string

func (m MyString) String() string {
    return fmt.Sprintf("MyString=%s", m) // Error: will recur forever.
}
```

不過這很容易修正：強制轉成字串型別 (因為字串型別裡不會有我們自訂的 `String` 方法)就好了

```go
type MyString string
func (m MyString) String() string {
    return fmt.Sprintf("MyString=%s", string(m)) // OK: note conversion.
}
```

在 *初始化* 的章節中，我們會討論另一種技巧來避免這個無限遞迴問題。

還有一個技巧，是把要輸出的參數直接轉傳給另一個輸出函式。`Printf` 的定義中用了 `...interface{}` 作為最後的參數，代表了它可以接受任意數量、任意型別的參數。

```go
func Printf(format string, v ...interface{}) (n int, err error) {
```

在 `Printf` 函式裡，參數 `v` 是 `[]interface{}` 型別。但如果把它傳給其他參數數量不固定的函式時，卻又會像是參數列表一樣。以下是 `log.Println` 的實作方式，它把參數轉傳給 `fmt.Sprintln` 以取得預需的輸出格式。

```go
// Println prints to the standard logger in the manner of fmt.Println.
func Println(v ...interface{}) {
    std.Output(2, fmt.Sprintln(v...))  // Output takes parameters (int, string)
}
```

我們呼叫 `Sprintln` 的時候用了 `v...` 這樣的語法，讓編譯器知道我們想把 `v` 當成是參數列表。否則 `v` 會被當成單一個 `[]interface{}` 型別的參數。

其實還有很多相關的東西我們這裡沒有提到，細節你可以看 `godoc` 產生的 `fmt` 套件說明文件。

題外話，`...` 語法可以用在任何 slice 上，例如你想寫一個函式，從數個整數中找出最小的值

```go
func Min(a ...int) int {
    min := int(^uint(0) >> 1)  // largest int
    for _, i := range a {
        if i < min {
            min = i
        }
    }
    return min
}
```


### Append

現在我們已經準備好要解決 `append` 函式最後的謎團。內建的 `append` 函式的定義和上面我們自訂的 `Append` 函式不一樣。概念上，`append` 的定義可以說是

```go
func append(slice []T, elements ...T) []T
```

`T` 只是一個代表了你想要的型別的記號。但這不是正確的 Go 語法。函式的定義裡，所有的型別都要是確定的，不能臨時在呼叫時隨著需要改變。這就是我們內建 `append` 函式的原因：這件事需要編譯器來處理。

`append` 函式會把傳進來的參數加到 slice 的後面然後傳回去。之所以要把 slice 回傳的原因在之前已經提過：它會更動內部的資料結構。以下這個簡單的範例

```go
x := []int{1,2,3}
x = append(x, 4, 5, 6)
fmt.Println(x)
```

會輸出 `[1 2 3 4 5 6]`。你應該會發現這點跟 `Printf` 很像，都可以接受任意數量的參數。

那如果我們把一個 slice 裡的所有元素依序加到另一個 slice 的後面呢？很簡單，就用我們剛剛才提到的 `...`。所以下面這段程式碼和上面的例子會有一樣的結果：

```go
x := []int{1,2,3}
y := []int{4,5,6}
x = append(x, y...)
fmt.Println(x)
```

如果沒有 `...` 的話就會發生編譯錯誤，因為 `y` 的型別是 `[]int` 而不是 `int`。

## 初始化

雖然 Go 語言的初始化看起來跟 C/C++ 的差別不大，但事實上它強大多了。初始化的時候可以建立複雜的結構，在物件之間，甚至是套件之間的順序問題也能正確的解決。

### 常數

在 Go 語言裡，常數就是個常數。不論是全域的還是區域的，它們在編譯的時候就已經產生好了，型別也只能是數值、字元、rune、字串或是 bool。因為編譯時期的限制，定義常數只能用固定的表達式，而編譯器會執行這些表達式。舉例來說， `1<<3` 是固定的表達式，但 `math.Sin(math.Pi/4)` 則不是：因為要到執行的時候才會做函式呼叫。

一系列的常數可以用 `iota` 運算元來定義。`iota` 可以是敘述的一部份，而敘述可以省略，所以定義起來很省事：

```go
type ByteSize float64

const (
    _           = iota // ignore first value by assigning to blank identifier
    KB ByteSize = 1 << (10 * iota)
    MB
    GB
    TB
    PB
    EB
    ZB
    YB
)
```

由於你可以為任何自訂型別加上自訂的方法，所以透過加上 `String` 方法，我們就可以讓常數有個漂亮的預設格式。這個技巧對於單純的數值型別也很有用，比如原本是浮點數的 `ByteSize` 型別。

```go
func (b ByteSize) String() string {
    switch {
    case b >= YB:
        return fmt.Sprintf("%.2fYB", b/YB)
    case b >= ZB:
        return fmt.Sprintf("%.2fZB", b/ZB)
    case b >= EB:
        return fmt.Sprintf("%.2fEB", b/EB)
    case b >= PB:
        return fmt.Sprintf("%.2fPB", b/PB)
    case b >= TB:
        return fmt.Sprintf("%.2fTB", b/TB)
    case b >= GB:
        return fmt.Sprintf("%.2fGB", b/GB)
    case b >= MB:
        return fmt.Sprintf("%.2fMB", b/MB)
    case b >= KB:
        return fmt.Sprintf("%.2fKB", b/KB)
    }
    return fmt.Sprintf("%.2fB", b)
}
```

這樣 `YB` 會輸出 `1.00YB`，而 `ByteSize(1e13)` 則是 `9.09TB`。

這段程式碼不會造成無限遞迴，因為 `%f` 不會呼叫到 `String` - 它接受的是浮點數而非字串。

### 變數

變數也可以初始化，而且可以使用一般的敘述。

```go
var (
    home   = os.Getenv("HOME")
    user   = os.Getenv("USER")
    gopath = os.Getenv("GOPATH")
)
```

### *init* 函式

最後，每個源碼檔都可以定義自己的 `init` 函式，這個函式不接受任何參數。事實上，每個源碼檔可以有複數個 `init` 函式。`init` 是整個初始化環節的最後一步：要等到套件中為變數初始化的敘述都執行完，而且 `import` 進來的套件也初始化完畢之後，才會執行 `init`。

除了進行一些無法簡單地用敘述完成的初始化工作之外，另外一種常見的用法是在 `init` 裡確認套件的狀態，或在必要時進行修復

```go
func init() {
    if user == "" {
        log.Fatal("$USER not set")
    }
    if home == "" {
        home = "/home/" + user
    }
    if gopath == "" {
        gopath = home + "/go"
    }
    // gopath may be overridden by --gopath flag on command line.
    flag.StringVar(&gopath, "gopath", gopath, "override default GOPATH")
}
```

## 方法

### 指標與值

回想一下我們先前提過的 `ByteSize`，我們可以為任何型別定義方法 (除了指標型別或是介面型別)；接收器不一定要是 struct。

先前討論 slice 的時候，我們寫了一個 `Append` 函式作為範例。現在我們要試著把它改寫成 slice 的一個方法。首先，我們得要定義一個有命名的型別，這樣才能為它加上方法。至於方法的接收器，當然是我們剛定義的型別了。

```go
type ByteSlice []byte

func (slice ByteSlice) Append(data []byte) []byte {
    // Body exactly the same as above
}
```

這樣還是得回傳一個新的 slice 回去，我們可以把接收器改成指標，那麼我們就可以在方法裡改變 slice。

```go
func (p *ByteSlice) Append(data []byte) {
    slice := *p
    // Body as above, without the return.
    *p = slice
}
```

這樣就完美了嗎？其實還可以寫得更好。如果我們把方法的定義改成像標準的 `Write` 方法的話：

```go
func (p *ByteSlice) Write(data []byte) (n int, err error) {
    slice := *p
    // Again as above.
    *p = slice
    return len(data), nil
}
```

那麼 `*ByteSlice` 這個型別就滿足了 `io.Writer` 這個介面的要求 (譯註：就是實作了這個介面的意思), 這下可有趣了。比如說，現在我們可以把 `*ByteSlice` 當成串流來寫入。

```go
var b ByteSlice
fmt.Fprintf(&b, "This hour has %d days\n", 7)
```

注意那個取址符號，因為只有 `*ByteSlice` 型別實作了 `io.Writer`。如果你的接收器是接收值的，那麼實體不論是值型別 (`T`) 還是指標型別 (`*T`)，都可以呼叫這個方法；當接收器是接收指標的時候，只有在指標型別裡才有定義這個方法。

這個規則是因為用指標型別的話就可以修改原本的實體，然而 Go 在傳遞值的時候會產生副本，這代表你在方法中做的變更其實都改到副本上去了。所以我們直接在語言層面上杜絕發生這種錯誤的可能。對於接收指標的方法，如果呼叫的時候需要而且可以取址，那麼 Go 會幫你自動加上取址符號。比如上面的例子，我們可以直接寫 `b.Write`，而 Go 會幫我們把它改成 `(&b).Write`。

題外話，上面那個改寫成 `Write` 的主意就是我們在實作 `bytes.Buffer` 時的中心思想。

## 介面以及其他型別

### 介面

在 Go 語言中，介面是用來指定物件的行為：如果某個物件可以做「這件事」，那麼它就可以用來當成是「這種東西」。先前我們已經看到好幾個簡單的例子：我們可以透過 `String` 方法來實作自訂的輸出，而 `Fprintf` 可以輸出到任何有實作 `Write` 方法的物件裡。

一個型別可以實作好幾個介面。舉例來說，一個 collection 可以藉由實作 `sort.Interface` 這個介面來讓 `sort` 套件為它排序，同時也可以提供一個自訂的輸出格式：

```go
type Sequence []int

// Methods required by sort.Interface.
func (s Sequence) Len() int {
    return len(s)
}
func (s Sequence) Less(i, j int) bool {
    return s[i] < s[j]
}
func (s Sequence) Swap(i, j int) {
    s[i], s[j] = s[j], s[i]
}

// Method for printing - sorts the elements before printing.
func (s Sequence) String() string {
    sort.Sort(s)
    str := "["
    for i, elem := range s {
        if i > 0 {
            str += " "
        }
        str += fmt.Sprint(elem)
    }
    return str + "]"
}
```

### 型別轉換

上面的 `String` 方法其實是把 `Sprint` 函式對 slice 做的事重寫了一遍。我們可以把 `Sequence` 轉成 `[]int` 型別來享受 `fmt` 套件的勞動成果。

```go
func (s Sequence) String() string {
    sort.Sort(s)
    return fmt.Sprint([]int(s))
}
```

這也是靠型別轉換來避免在 `String` 方法中產生無限遞迴的好例子。因為 `Sequence` 跟 `[]int` 本質上是同樣的型別，所以它們彼此之間可以安全轉換。型別的轉換不會產生新的實體，只是暫時假裝它是另一種相容的型別而已。此外也有會產生新的實體的型別轉換，比如整數和浮點數之間的轉換。

靠型別轉換來存取不同的方法，這是 Go 的慣例。比如我們可以用 `sort.IntSlice` 把上面的例子整個簡化成

```go
type Sequence []int

// Method for printing - sorts the elements before printing
func (s Sequence) String() string {
    sort.IntSlice(s).Sort()
    return fmt.Sprint([]int(s))
}
```

原本我們是在 `Sequence` 實作特定介面來達成排序的需求，現在是利用型別轉換來取用在 `sort.IntSlice` 中定義的排序方法。這樣的技巧在實際環境中比較少用，但是非常有效率。

### 介面的轉換以及型別斷言

早先提到的 type switch 也是一種型別轉換：針對某個介面的實體，視它符合哪個 `case` 所指定的型別，然後把它轉換成那個型別。以下是一個簡化的例子，示範了 `fmt.Prinf` 是如何用 type switch 把值變成字串。如果值本來就是字串，那就直接輸出不用轉換；如果它有實作 `String` 方法，那就用 `String` 方法回傳的結果。

```go
type Stringer interface {
    String() string
}

var value interface{} // Value provided by caller.
switch str := value.(type) {
case string:
    return str
case Stringer:
    return str.String()
}
```

第一個 `case` 配對到實值，第二個則是把一個介面轉成另一個。像這樣子混用型別是安全的。

如果我們只想要特定一種型別呢？比如我很確定這個值一定會是字串，而我想直接取得它的值呢？只有一個 `case` 的 type switch 當然是個解法，但型別斷言也行。型別斷言會從一個介面中擷取出特定型別的值來。型別斷言的語法和 type switch 很像，只是把 `type` 關鍵字換成了型別：

```go
value.(typeName)
```

結果會是一個新的值，型別是 `typeName`。這個 `typeName` 必須是這個介面的實體本身的型別，或是另一個可以安全轉換的介面。如果我們知道 `value` 真的會是字串的話：

```go
str := value.(string)
```

但如果不是的話，這會造成執行時期的錯誤。你可以用「逗號 ok」這個慣例來確認。

```go
str, ok := value.(string)
if ok {
    fmt.Printf("string value is: %q\n", str)
} else {
    fmt.Printf("value is not a string\n")
}
```

如果型別斷言失敗，`str` 仍然會是字串型別，但值會是零值，也就是空字串。

作為示意，以下是用 *if-else* 實作的 type switch

```go
if str, ok := value.(string); ok {
    return str
} else if str, ok := value.(Stringer); ok {
    return str.String()
}
```

### 一般性

如果某個型別完全只是為了實作特定介面而存在的，那我們完全不必讓它可以公開存取，只要讓介面可以公開存取就夠了。這樣可以明白表示重要的是它的行為，而不是實作它的那個東西；這樣其他具有不同特性的實作方式也能保持相同的行為。同時這也讓你避過了一份說明要寫兩次的痛苦。

在這種情況下，建構子應當要回傳介面。比如在 hash 相關的套件中， `crc32.NewIEEE` 與 `adler32.New` 都是回傳 `hash.Hash32` 介面。所以如果你的程式會用到 crc32，而事後想要換成 adler32，只要把建構子換掉就好了，其他部份的程式碼都不會動到。

這種模式讓串流式的加密法得以和區塊加密器分離開來。在 `crypto/cipher` 套件中的 `Block` 介面定義了區塊加密器的行為：把一塊資料加密。只要再配合 *bufio* 套件，實作這個介面的加密器就可以用來建構串流式的加密器 (同時也是 `Stream` 介面)，而不用深入研究每個區塊加密器的實作方式。

`crypto/cipher` 介面看起來大致像這樣

```go
type Block interface {
    BlockSize() int
    Encrypt(src, dst []byte)
    Decrypt(src, dst []byte)
}

type Stream interface {
    XORKeyStream(dst, src []byte)
}
```

而以下是 counter mode 的串流的定義，用來把區塊加密轉成串流加密。你可以注意到區塊加密的細節被隱藏起來了

```go
// NewCTR returns a Stream that encrypts/decrypts using the given Block in
// counter mode. The length of iv must be the same as the Block's block size.
func NewCTR(block Block, iv []byte) Stream
```

這個函式不止可以用在特定某種區塊加密法，而是可以用任意區塊加密器建立不同的串流加密器。因為它回傳的是介面，所以只要換個建構子便能把加密方式換掉。

### 介面與方法

既然幾乎所有的東西都可以加上方法，那就表示幾乎所有東西都可以實作介面。一個很明顯的例子就是 `http` 套件的 `Handler` 介面。任何實做這個介面的東西都可能用來處理 HTTP 請求。

```go
type Handler interface {
    ServeHTTP(ResponseWriter, *Request)
}
```

`ResponseWriter` 本身就是個介面，提供一些方法讓你可以回應 HTTP 請求；這包括了標準的 `Write` 方法，所以它也可以用在任何需要 `io.Writer` 的地方。而 `Request` 則是把 HTTP 請求轉成一個方便你存取的 struct。

為了便於說明，讓我們把 HTTP 簡化成只有 GET 沒有 POST，這樣的簡化不會影響我們設定 HTTP 請求的處理程式。以下是個很直觀但是也很完整的處理程式，用來記錄有多少人次來看過這一頁。

```go
// Simple counter server.
type Counter struct {
    n int
}

func (ctr *Counter) ServeHTTP(w http.ResponseWriter, req *http.Request) {
    ctr.n++
    fmt.Fprintf(w, "counter = %d\n", ctr.n)
}
```

(思考一下，為什麼 `Fprintf` 可以變成 HTTP 回應呢？) 做為參考，這是把這個處理程式綁定在某個網址的方法。

```go
import "net/http"
...
ctr := new(Counter)
http.Handle("/counter", ctr)
```

但為什麼要用 struct 呢？用整數就夠了！ (接收器要是指標型別，這樣才不會只是改到副本)

```go
// Simpler counter server.
type Counter int

func (ctr *Counter) ServeHTTP(w http.ResponseWriter, req *http.Request) {
    *ctr++
    fmt.Fprintf(w, "counter = %d\n", *ctr)
}
```

那如果你的程式有一些內部狀態，要在訪客拜訪這個頁面的時候處理呢？那就用 channel 吧

```go
// A channel that sends a notification on each visit.
// (Probably want the channel to be buffered.)
type Chan chan *http.Request

func (ch Chan) ServeHTTP(w http.ResponseWriter, req *http.Request) {
    ch <- req
    fmt.Fprint(w, "notification sent")
}
```

最後，假設我們要在 `/args` 這個頁面裡顯示我們用來啟動伺服器的命令列參數。要輸出命令列參數很簡單：

```go
func ArgServer() {
    fmt.Println(os.Args)
}
```

但是要怎樣讓它變成 HTTP 伺服器呢？我們當然可以把 `ArgServer` 變成某種自訂型別的方法，這樣只要忽略那個型別的值就好了。但還有更優雅的解決方式。既然除了指標和介面之外的東西都可以加上方法，那我們當然也可以為函式加上方法。在 `http` 套件中有這樣的一段程式碼：

```go
// The HandlerFunc type is an adapter to allow the use of
// ordinary functions as HTTP handlers.  If f is a function
// with the appropriate signature, HandlerFunc(f) is a
// Handler object that calls f.
type HandlerFunc func(ResponseWriter, *Request)

// ServeHTTP calls f(c, req).
func (f HandlerFunc) ServeHTTP(w ResponseWriter, req *Request) {
    f(w, req)
}
```

`HandlerFunc` 是一個擁有 `ServeHTTP` 方法的型別，所以這個型別當然可以處理 HTTP 請求。注意看實作：接收器接收的是一個函式 `f`，然後去呼叫這個函式。看起來可能有點奇怪，但其實這跟上面那個接收 channel的例子沒有什麼不同。

所以要把 `ArgServer` 變成 HTTP 伺服器，首先我們要把它的定義改成正確的樣式

```go
// Argument server.
func ArgServer(w http.ResponseWriter, req *http.Request) {
    fmt.Fprintln(w, os.Args)
}
```

現在 `ArgServer` 的定義跟 `HandlerFunc` 的定義是相容的了，這表示可以把它強制轉型成 `HandlerFunc` 型別好存取 `ServeHTTP` 方法，就像我們之前把 `Sequence` 轉成 `IntSlice` 一樣。

```go
http.Handle("/args", http.HandlerFunc(ArgServer))
```

當有人拜訪這個網址的時候，處理這個網址的處理程式會是 `ArgServer`，而它的型別會是 `HandlerFunc`。因為型別是 `HandlerFunc`，所以 HTTP 伺服器會執行它的 `ServeHTTP` 方法，而在 `ServeHTTP` 裡又會呼叫 `ArgServer` 本身，所以命令列參數就輸出到 HTTP 回應裡了。

在這節裡，我們用了 struct、整數、channel 和函式來當 HTTP 伺服器。之所以能這樣用，是因為介面只是定義一組方法，而方法幾乎可以定義在任何東西上。

## 空白識別子

我們之前已經在 *for range* 迴圈和 *map* 的章節提過這個名詞好幾次。你可以指定任意型別的任何值給空白識別子，這個值會被忽略。這有點像是 unix 的 */dev/null*：只能寫入，通常是用在你需要有一個變數，但它的值一點都不重要的時候。之前我們有看過幾次這種用法了。

### 空白識別子與多重指定

*for range* 迴圈那節的例子，就是空白識別子在多重指定裡的應用。

如果在等號的左邊需要好幾個變數，但其中一個不會被用到，你就可以用空白識別子取代它，這樣可以避免配置多餘的變數，也可以清楚顯示這個值會被忽略。比如我們呼叫一個會回值某值和錯誤碼的函式，但我們只需要錯誤碼：

```go
if _, err := os.Stat(path); os.IsNotExist(err) {
	fmt.Printf("%s does not exist\n", path)
}
```

有時候你會看到有人用空白識別子忽略錯誤碼，這是非常糟的錯誤示範。務必要檢察錯誤碼，每個錯誤碼的存在都是有理由的。

```go
// Bad! This code will crash if path does not exist.
fi, _ := os.Stat(path)
if fi.IsDir() {
    fmt.Printf("%s is a directory\n", path)
}
```

### 沒有用到的 import 和變數

如宗定義了一個變數，或是引入了一個套件，卻沒有真的用到它們，那麼就會發生編譯錯誤。引入用不到的套件會讓程式變肥、編譯變慢，而定義用不到的變數至少會浪廢記憶體，也會拖慢效能，甚至可能是某種 bug 的跡象。當程式正在密集開發的時候，這種情況會比較常見。為了要讓編譯成功，你得一直來來回回的加上、刪除相關的引用和定義，實在很擾人。空白識別子提供了一個暫時的解決方案。

以下這個寫到一半的程式引用了兩個目前沒有用到的套件 `fmt` 和 `os`，以及一個沒有用到的變數 `fd`，所以編譯不會過，但如果有什麼辦法可以知道程式現在有沒有錯誤的話當然會更好。

```go
package main

import (
    "fmt"
    "io"
    "log"
    "os"
)

func main() {
    fd, err := os.Open("test.go")
    if err != nil {
        log.Fatal(err)
    }
    // TODO: use fd.
}
```

如果想暫時跳過這個麻煩，你把套件中的任意識別子指定給空白識別子。同樣地，你也可以透過把變數指定給空白識別子的方式來避開變數使用的問題。以下的程式就可以成功編譯了：

```go
package main

import (
    "fmt"
    "io"
    "log"
    "os"
)

var _ = fmt.Printf // For debugging; delete when done.
var _ io.Reader    // For debugging; delete when done.

func main() {
    fd, err := os.Open("test.go")
    if err != nil {
        log.Fatal(err)
    }
    // TODO: use fd.
    _ = fd
}
```

慣例上，用來避開引用錯誤的這兩行宣告，要放在引用的正下方，也要加上註解。這是為了未來可以容易發現程式中有這樣的情況。

### 為了副作用而引用

像上一個例子中 `fmt` 和 `os` 的引用，要嘛補上使用它們的程式碼，要嘛就該刪除掉，空白識別子只是標識出我們還有相關工作要作。不過有時我們引用套件只是為了它的副作用，套件本身我們不會用到。舉例來說，`net/http/pprof` 套件在它的 `init` 函式中會註冊一個 HTTP 處理程式，用來提供相關的調校資訊。它有提供 API，不同通常你並不會用到，只會從 web 頁面上存取它。如果只需要一個套件的副作用，你可以用空白識別子作它的別名

```go
import _ "net/http/pprof"
```

這樣子你就沒有辦法在程式中使用這個套件中任何的識別子：在這個源碼檔中，這個套件沒有名字。這表明了我們是為了它的副作用才引用的。如果你給了它名字而又沒有用到它，那就會有編譯錯誤。

### 檢查型別

如同我們在 *介面* 章節中提到的，型別不需要定義它實作了些什麼介面，只要實作那個介面定義的所有方法就夠了。實作中，大部份的介面型別轉換都是靜態的，也就是在編譯時完成的。比如你傳一個 `*os.File` 型別的變數給需要 `io.Reader` 的函式當參數，除非 `*os.File` 有實作 `io.Reader` 定義的所有方法，否則編譯的時候就會出錯了。

也是有些型別檢查是執行時才做的，像 `encoding/json` 套件中的 `Marshal` 函式就是一個例子。當 JSON 編碼器收到一個有實作編碼介面的值，編碼器就會呼叫介面中定義的編碼方法；否則會用預設的方式來編碼。編碼器會在執行時才用型別斷言來確認型別：

```go
m, ok := val.(json.Marshaler)
```

像是在檢查的時候，其實根本不會用到 `m`，我們只關心 `ok` 還是不ok，這時候就是空白識別子的出場時機：

```go
if _, ok := val.(json.Marshaler); ok {
    fmt.Printf("value %v of type %T implements json.Marshaler\n", val, val)
}
```

當你想保證某變數確實有實作某介面的時候，可以用型別轉換來處理。例如有個型別，假設是 `json.RawMessage` 好了，它需要自訂的 JSON 格式，它就應該要實作 `json.Marshaler` 介面，但編譯器沒辦法幫你自動檢查 (譯註：因為 `json.Marshal` 並不限制參數一定要實作這個介面)。 如果它沒有實作正確的介面， JSON 編碼器還是可以把它轉成 JSON，但顯然結果不會是我們想要的。如果要保證我們的實作沒有問題，可以用空白識別子作全域的定義：

```go
var _ json.Marshaler = (*RawMessage)(nil)
```

這個定義把 `*RawMessage` 指定給某個 `json.Mashaler` 型別的變數 (空白識別子)，這會強制把 `*RawMessage` 轉型成 `json.Marshaler`，所以如果 `*RawMessage` 沒有實作 `json.Marshaler` 的話，編譯就會出錯，我們也就知道事情不對勁了。

這種寫法指明了我們只是想做型別檢查，而不是要定義一個新的變數。不過也不要看到黑影就開槍，真的不需要把每個型別都加上這種檢查。慣例上我們只在編譯器沒辦法為我們檢查的時候才會用這招，不過這也是很少見的事。

## 嵌入

Go 沒有提供常見的、基於型別的繼承，不過確實可以像繼承那樣，借用先人的智慧來完成工作：把某個型別嵌入到 struct 或介面中。

嵌入介面很簡單，我們之前提過了 `io.Reader` 和 `io.Writer` 兩個介面：

```go
type Reader interface {
    Read(p []byte) (n int, err error)
}

type Writer interface {
    Write(p []byte) (n int, err error)
}
```

`io` 套件中還定義了許多其他介面，讓你的型別可以實作這兩個方法。比如 `io.ReadWriter`，它就只是把 `io.Reader` 跟 `io.Writer` 合併成一個介面。我們當然可以把這兩個方法的定義複製貼上，好定義新的介面。不過比較理想的方法是直接把它們嵌進去。

```go
// ReadWriter is the interface that combines the Reader and Writer interfaces.
type ReadWriter interface {
    Reader
    Writer
}
```

這很好懂： *ReadWriter* 既可以當成是 *Reader*，也可以當成是 *Writer*。它是這兩個介面的聯集 (當然這兩個介面必須定義不同的方法)。你只能在介面中嵌入介面。

類似的狀況也可以套用在 struct 的嵌入上，但 struct 的嵌入還有更多值得注意的部份。`bufio` 套件裡有兩個 struct 型別：`bufio.Reader` 和 `bufio.Writer`。如同你所猜測的，它們實作了 `io` 套件中的同名介面。而 `bufio` 也有實作 *ReadWriter*：把 `Reader` 跟 `Writer` 嵌進來。它列出了這兩個型別，但沒有給它們屬性名稱。

```go
// ReadWriter stores pointers to a Reader and a Writer.
// It implements io.ReadWriter.
type ReadWriter struct {
    *Reader  // *bufio.Reader
    *Writer  // *bufio.Writer
}
```

由於嵌進來的是指標，所以得先初始化之後才能用。如果你把 `ReadWriter` struct 寫成這樣：

```go
type ReadWriter struct {
    reader *Reader
    writer *Writer
}
```

那麼相應的方法會綁定在各自的屬性上，你就得自己重寫 `Read` 和 `Write` 方法，把工作轉發給內層，才能符合 `io.ReadWriter` 的要求：

```go
func (rw *ReadWriter) Read(p []byte) (n int, err error) {
    return rw.reader.Read(p)
}
```

透過嵌入，我們就可以避免這樣的複製貼上問題。你將可以直接存取內嵌的型別所定義的方法，這代表我們一次符合了 `io.Reader`, `io.Writer` 和 `io.ReadWriter` 的定義。

嵌入跟繼承有個很重要的差異。雖然我們可以從外層的型別去呼叫內層定義的方法，但方法的接收器所接收到的會是內層的型別。當我們呼叫 `bufio.ReadWriter` 的 `Read` 方法時，它的情況會跟上上個例子中轉發的動作一樣：`Read` 接收到的不會是 `ReadWriter` 實體，而是 `ReadWriter` 實體內部的某個不具名的 `*Reader` 實體。

你也可以單純為了方便而使用嵌入。下面示範了一個嵌入的屬性，和一個正常的屬性。

```go
type Job struct {
    Command string
    *log.Logger
}
```

`Job` 型別現在也有 `Log`, `Logf` 和其他 `*log.Logger` 定義的方法了。當然我們也可以給它一個屬性名稱，不過顯然沒有這個必要。現在只要初始化完之後，我們就可以

```go
job.Log("starting now...")
```

`Logger` 也是 `Job` 裡的一個正常屬性，所以我們可以在建構子中初始化它

```go
func NewJob(command string, logger *log.Logger) *Job {
    return &Job{command, logger}
}
```

或是用複合結構表達式

```go
job := &Job{command, log.New(os.Stderr, "Job: ", log.Ldate)}
```

如果我們要直接存取內嵌的型別，那麼它的型別 (忽略套件的部份) 就是它的屬性名稱，就像上面例子中的 `ReadWriter` 一樣。這個例子裡，如果我們要某個 `Job` 型別的變數 `job` 裡的 `log.Logger`，那應該要用 `job.Logger`。當需要覆寫它的方法時會很有用：

```go
func (job *Job) Logf(format string, args ...interface{}) {
    job.Logger.Logf("%q: %s", job.Command, fmt.Sprintf(format, args...))
}
```

嵌入機制有造成命名衝突的可能，但解決的規則也很簡單。首先，外層的屬性和方法會蓋掉內層的屬性和方法。如果 `log.Logger` 有一個屬性也叫 `Command`，那它就只能透過 `job.Logger.Command` 的方式存取。

其次，如果在同一層出現命名衝突，通常這會是個錯誤。如果 `Job` 型別裡已經有 `Logger` 屬性的情況下嵌了 `log.Logger` 進去，這八成是哪裡出了問題。然而，如果這衝突的命名只出現在定義的時候，外部程式不會存取到的話就沒問題。這是為了保護內層的型別不會被外部修改；顯然在內嵌型別中，不會被外部使用的那些資料就不需要這種保護。

## 並行運算

### 以溝通來共享

並行運算是深奧的問題，這裡只討論一些 Go 語言中相關的重點。

在許多環境中，要如何實作正確的資料共享方式是困難的，這讓並行運算也跟著困難了起來。Go 語言推行另一種模式來解決這個問題：把需要共享的資料透過 channel 來互相傳遞，而不是讓每個程序自行存取它。這樣一次只會有一個 goroutine 可以存取它，那麼在設計層面上就杜絕了資料存取競爭的問題。我們想了一句標語來益你用這種方式思考：

    不要透過共享的資料來互相溝通，要用互相溝通的方式來共享資料

這種方式可以擴及非常多層面，比如參考計數模式可以用「把一個同步鎖加在整數上」的方式處理。不過讓我們從上而下來看，用 channel 做存取控制可以讓你寫出比較簡單明瞭的程式。

要理解為什麼，我們先假設現在有一個程式，跑在一台單核的電腦上。它絕對不會有同步問題，你知道，我知道，獨眼龍也知道。然後我們把這個程式再跑一份起來，顯然也不會有同步問題。現在，讓這兩份程式互相溝通。如果它們是用彼此溝通的那個管道來做同步控制，那它們還是不會有同步問題。Unix 的管道正是這種模式的完美詮釋。雖然 Go 對於並行運算的處理是從 Hoare 的 Communicating Sequential Processes (CSP) 發想的，但你也可以把它想成是比較一般化、不會有型別問題的 Unix 管道。

### Goroutine

之所以會取這個名字，是因為現有的各種類似名稱：thread、副程序、程序…等等，都不能精準描述它。Goroutine 的模型很簡單：它是一個函式，和其他 goroutine 在同一個位址空間中做並行運算。它很小，只需要比配置堆疊再多一點的資源。它的堆疊一開始也會很小，所以它真的很小。但當需要的時候，它也會靠在 heap 中配置空間來增長。

所有的 Goroutine 會自動分配到數個作業系統層級的 thread 中執行，所以某個 goroutine 如果因為等待 I/O 動作而需要暫停的時候，其他 goroutine 還是會繼續執行。它的設計可以隱藏掉很多 thread 控制的細節。

當你呼叫函式或是方法的時候，前面加上 `go` 關鍵字，就可以把它扔進一個新的 goroutine 裡執行。函式結束的時候，goroutine 也會悄悄地跟著結束，效果很像在 Unix 的 shell 裡用 `&` 把程式丟到背景執行。

```go
go list.Sort()  // run list.Sort concurrently; don't wait for it.
```

函式表達式也是很常配合 `go` 使用的

```go
func Announce(message string, delay time.Duration) {
    go func() {
        time.Sleep(delay)
        fmt.Println(message)
    }()  // Note the parentheses - must call the function.
}
```

Go 語言的函式表達式就是 closure：被函式參考到的變數，在函式結束前是不會釋放的。

這些範例沒什麼實用價值，因為你不知道它何時才會結束。要做到這點，就得靠 channel 了。

### Channel

像 `map` 一樣，channel 要用 `make` 配置，結果會是一個指標，指向某種內部的資料結構。如果你加上一個整數當參數，它會是 channel 的緩衝區的大小。

```go
ci := make(chan int)            // unbuffered channel of integers
cj := make(chan int, 0)         // unbuffered channel of integers
cs := make(chan *os.File, 100)  // buffered channel of pointers to Files
```

若是用無緩衝區的 channel 來溝通 - 就是用它來同步交換資料 - 可以保證兩端的 goroutine 的狀態都會是確定的。

我們有很多使用 channel 的慣例。首先可以從這個開始。上一節我們把一個排序的動作扔到了背景，我們可以用 channel 來讓外部程式等待排序完畢。

```go
c := make(chan int)  // Allocate a channel.
// Start the sort in a goroutine; when it completes, signal on the channel.
go func() {
    list.Sort()
    c <- 1  // Send a signal; value does not matter.
}()
doSomethingForAWhile()
<-c   // Wait for sort to finish; discard sent value.
```

接收端會暫停到有資料可以接收為止。如果 channel 沒有緩衝區的話，發送端也會暫停到有人來接收為止。如果有緩衝區的話，那就會暫停到資料放進緩衝區為止：如果緩衝區滿了，就要等到有人來接收資料，讓緩衝區騰出空間來，資料才能放進緩衝區。

有緩衝區的 channel 有點像是同步旗標，比如可以用來限制流量。下個範例中，連入的請求會傳給 `handle`，而它會傳送一個值給 channel，處理請求，然後再從 channel 中讀取一個值以表示「我處理好了，可以換下一位了」。channel 緩衝區的大小決定了同時可以處理幾個請求。

```go
var sem = make(chan int, MaxOutstanding)

func handle(r *Request) {
    sem <- 1    // Wait for active queue to drain.
    process(r)  // May take a long time.
    <-sem       // Done; enable next request to run.
}

func Serve(queue chan *Request) {
    for {
        req := <-queue
        go handle(req)  // Don't wait for handle to finish.
    }
}
```

一旦有 `MaxOutStanding` 個請求同時在處理中，後來連入的請求就會在傳送資料給 channel 的時候被強制暫停，直到有前面的請求處理完為止。

但這個設計有個問題：`Serve` 會為每個連入的請求建立一個 goroutine，就算現在連線滿了也一樣。所以如果連入的速度太快，那 goroutine 就會越來越多，直到系統資源耗盡當機。我們可以改寫 `Serve` 來管制 goroutine 的產生。以下的範例很直觀，但我們留了一個 bug 之後再修：

```go
func Serve(queue chan *Request) {
    for req := range queue {
        sem <- 1
        go func() {
            process(req) // Buggy; see explanation below.
            <-sem
        }()
    }
}
```

在 *for* 迴圈上定義的變數是會重覆使用的，這讓上一個範例產生了一個 bug：所有的 goroutine 共享了同一個 `req` 變數。這顯然不是我們想的，所以我們得確定每個 goroutine 取得的 `req` 都是獨一無二的。其中一個方式是把它用參數傳進去。

```go
func Serve(queue chan *Request) {
    for req := range queue {
        sem <- 1
        go func(req *Request) {
            process(req)
            <-sem
        }(req)
    }
}
```

你可以上下比對一下，幫助你理解 closure 是怎麼運作的。另一種方式是乾脆定義一個同名的新變數給它：

```go
func Serve(queue chan *Request) {
    for req := range queue {
        req := req // Create new instance of req for the goroutine.
        sem <- 1
        go func() {
            process(req)
            <-sem
        }()
    }
}
```

也許 `req := req` 看起來很詭異，但這在 Go 是合乎慣例的。你配置了一個全新的區域變數，使用跟外部變數相同的名稱，避開了迴圈變數的影響，也讓每個 goroutine 都有自己獨立的 `req`。

回到原本伺服器的例子，另一種資源管理的方式是一開始就啟動固定數量的 `handle`，而每一個 `handle` 都會從 channel 裡讀取連入的連線。而 `Serve` 也會從另一個 channel 讀取是否該結束，所以啟動所有的 `handle` 之後它就停在那等下班了。

```go
func handle(queue chan *Request) {
    for r := range queue {
        process(r)
    }
}

func Serve(clientRequests chan *Request, quit chan bool) {
    // Start handlers
    for i := 0; i < MaxOutstanding; i++ {
        go handle(clientRequests)
    }
    <-quit  // Wait to be told to exit.
}
```

### 傳送 channel 的 channel

Go 語言中一個極重要的特性是：channel 是 first-class value，你可以任意的配置、傳遞它。常見的用法是用來實作安全的平行處理環境。

在上一節的範例中，`handle` 是一個理想狀態的處理程式，但我們沒有定義它是處理什麼型別的資料。如果它處理的型別裡，包含了一個 channel 可以用來回傳處理結果，那麼每個客戶端都可以提供它們所需要的回傳方式。

```go
type Request struct {
    args        []int
    f           func([]int) int
    resultChan  chan int
}
```

客戶端會提供一個函式、函式所需的參數，以及一個用來回傳結果的 channel。

```go
func sum(a []int) (s int) {
    for _, v := range a {
        s += v
    }
    return
}

request := &Request{[]int{3, 4, 5}, sum, make(chan int)}
// Send request
clientRequests <- request
// Wait for response.
fmt.Printf("answer: %d\n", <-request.resultChan)
```

在伺服器端，我們只需要修改 `handle` 函式就好

```go
func handle(queue chan *Request) {
    for req := range queue {
        req.resultChan <- req.f(req.args)
    }
}
```

雖然這個範例還需要很多修改才能符合現實狀況，但這段程式已經是一個提供流量控管、平行處理的非阻斷式 RPC 系統，而且裡面沒有半個同步鎖。

### 平行處理

另外一種應用是我們可以在多個 CPU 核心間進行平行處理。如果計算可以分成好幾個彼此獨立的部份，那就可以同時進行，只要用一個 channel 來確認哪個部份已經執行完畢就好。

假設我們要做一些很耗資源的向量運算，每個向量的運算彼此間互不關聯。像是下面這個理想狀態的例子

```go
type Vector []float64

// Apply the operation to v[i], v[i+1] ... up to v[n-1].
func (v Vector) DoSome(i, n int, u Vector, c chan int) {
    for ; i < n; i++ {
        v[i] += u.Op(v[i])
    }
    c <- 1    // signal that this piece is done
}
```

我們把每一個運算用迴圈一一啟動，每一個 CPU 核心處理一個。它們完成的順序並不固定，不過那不要緊，我們只要透過讀取 channel 來確定運算是否通通完成就好。

```go
const numCPU = 4 // number of CPU cores

func (v Vector) DoAll(u Vector) {
    c := make(chan int, numCPU)  // Buffering optional but sensible.
    for i := 0; i < numCPU; i++ {
        go v.DoSome(i*len(v)/numCPU, (i+1)*len(v)/numCPU, u, c)
    }
    // Drain the channel.
    for i := 0; i < numCPU; i++ {
        <-c    // wait for one task to complete
    }
    // All done.
}
```

而 CPU 數量可以透過 `runtime.NumCPU` 取得

```go
var numCPU = runtime.NumCPU()
```

另外還有一個函式 `runtime.GOMAXPROCS`，它會回傳使用者定義的，一個 Go 程式最多可佔用的 CPU 核心數，預設值是 `runtime.NumCPU` 的結果。你可以透過設定特定的環境變數，或是傳個正整數給它來做調整。如果我們決定使用使用者定義的值

```go
var numCPU = runtime.GOMAXPROCS(0)
```

要注意，別把並行運算 (把程式結構調整成數個彼此獨立執行的組件) 和平行運算 (把一個運算分拆成幾個彼此獨立的小型運算，交給不同的 CPU 核心同時進行) 給搞混了。雖然 Go 語言適合並行運算的特性會讓你比較容易把問題用平行運算處理，但 Go 是適合並行運算的語言，不完全適合平行運算，所以某些平行運算的模式不適合用 Go。你可以看看[這個部落格裡的這份演講](http://blog.golang.org/2013/01/concurrency-is-not-parallelism.html)來深入了解兩者之間的差異。

### Leaky buffer

這些適合並行運算的工具還能讓非並行的運算變的更簡單明瞭。以下是個從 RPC 套件中抽象化之後的範例。客戶端不停地行某些來源取得資料，也許是網路。為了避免重複配置、釋放緩衝區，所以我們準備了一個閒置區，它是個 channel，裡面存放閒置的緩衝區。如果 channel 是空的，那我們就配置一個新的緩衝區。把資料放進緩衝區之後，我們就把整個緩衝區丟給伺服器做運算。

```go
var freeList = make(chan *Buffer, 100)
var serverChan = make(chan *Buffer)

func client() {
    for {
        var b *Buffer
        // Grab a buffer if available; allocate if not.
        select {
        case b = <-freeList:
            // Got one; nothing more to do.
        default:
            // None free, so allocate a new one.
            b = new(Buffer)
        }
        load(b)              // Read next message from the net.
        serverChan <- b      // Send to server.
    }
}
```

伺服器則是不停地從客戶端接收緩衝區，處理裡面的資料，然後把用完的緩衝區放到閒置區。

```go
func server() {
    for {
        b := <-serverChan    // Wait for work.
        process(b)
        // Reuse buffer if there's room.
        select {
        case freeList <- b:
            // Buffer on free list; nothing more to do.
        default:
            // Free list full, just carry on.
        }
    }
}
```

客戶端從閒置區取得現成的緩衝區，拿不到的話就自己產生一個新的。伺服器把用完的緩衝區放回閒置區，如果閒置區滿了，就直接把這多餘的緩衝區丟棄，垃圾處理機制會負責釋放它。(如果`select` 裡所有的 `case` 部份都不成立，就會執行 `default` 那部份，所以 `select` 不會暫停) 這樣的實作只用短短幾行就作了一個 leaky buffer 式的閒置列表，靠的是有緩衝區的 channel 和垃圾處理機制。

## 錯誤處理

程式庫常常會回傳某種錯誤通知給呼叫者。如同前面提到的，多重回傳值讓你可以簡單的同時把正常的回傳值和錯誤狀況同時傳回去，你也應該使用這個方式回傳錯誤碼。舉例來說，`os.Open` 發生錯誤的時候不只是回傳 `nil`，它還同時會回傳一個錯誤碼。

慣例上，錯誤碼會是內建的 `error` 型別：

```go
type error interface {
    Error() string
}
```

撰寫程式庫的人可以實作這個介面，並提供更完整、豐富的內容。如同前面所說，在回傳正常的值之外，`os.Open` 還會回傳一個錯誤碼。如果一切正常，那錯誤碼就會是 `nil`，而出問題的時候會是 `os.PathError`。

```go
// PathError records an error and the operation and
// file path that caused it.
type PathError struct {
    Op string    // "open", "unlink", etc.
    Path string  // The associated file.
    Err error    // Returned by the system call.
}

func (e *PathError) Error() string {
    return e.Op + " " + e.Path + ": " + e.Err.Error()
}
```

`PathError` 的 `Error` 方法會產生像這樣的字串

```
open /etc/passwx: no such file or directory
```

像這樣在錯誤訊息中囊括了檔名、動作和實際獨發的系統錯誤，就算是顯示在八竿子打不著邊的遠處，也能看出來是出了什麼問題，因為它比單純顯示「找不到檔案」要更詳細。

如果可以的話，錯誤訊息應該要能指明來源，比如前面加上套件名稱或是動作。比如在 `image` 套件中，解碼錯誤的訊息是 "image: unknown format"。

如果要針對不同的錯誤做處理的話，你可以用 type switch 或是型別斷言來取得詳細資料。比如說想要取出 `PathError` 裡的 `Err` 來做錯誤回復

```go
for try := 0; try < 2; try++ {
    file, err = os.Create(filename)
    if err == nil {
        return
    }
    if e, ok := err.(*os.PathError); ok && e.Err == syscall.ENOSPC {
        deleteTempFiles()  // Recover some space.
        continue
    }
    return
}
```

在第二個 `if` 這裡我們用了型別斷言。如果型別不對，那 `ok` 會是 `false`，`e` 也會是 `nil`。如果型別正確，`ok` 會是 `true`，所以 錯誤碼和 `e` 都會是 `*os.PathError` 型別，那麼我們就可以取出並驗證相關資訊了。

### Panic

通常我們會透過回傳錯誤值來回報錯誤，像是 `Read` 就會回傳它讀取了幾 byte，以及錯誤碼。但如果錯誤是無法回復的呢？有時候程式會發生無論如何都要中斷的錯誤。

因此，Go 語言中內建了 `panic` 函式，效果是發出一個足以中斷程式的執行時期錯誤 (注意下一節)。這個函式接受一個任意型別的參數，通常是字串，輸出之後中斷程式。這也可以指出發生了某種不可能的情況，比如跳出了無窮迴圈。

```go
// A toy implementation of cube root using Newton's method.
func CubeRoot(x float64) float64 {
    z := x/3   // Arbitrary initial value
    for i := 0; i < 1e6; i++ {
        prevz := z
        z -= (z*z*z-x) / (3*z*z)
        if veryClose(z, prevz) {
            return z
        }
    }
    // A million iterations has not converged; something is wrong.
    panic(fmt.Sprintf("CubeRoot(%g) did not converge", x))
}
```

這只是範例，但程式庫應該避免使用 `panic`。如果問題有任何方式可以解決或忽略，中斷程式肯定就是最差的做法。不過也是有反例：如果程式庫在初始化的時候發生錯誤，無法成功初始化，那使用 panic 中斷程式通常是合理的。

```go
var user = os.Getenv("USER")

func init() {
    if user == "" {
        panic("no value for $USER")
    }
}
```

### Recover

當你呼叫 `panic` 的時候，包括執行時期的錯誤 (像是陣列索引超過範圍或是型別斷言失敗) 造成的 `panic` 在內，它會循著現在的堆疊一路返回，執行所有 `defer` 函式。如果堆疊沒有更多的返回資訊的話，程式就會結束。然而，你還是可以使用內建的 `recover` 函式來重新取得控制，甚至是繼續正常的執行。

`recover` 會停止，不再繼續延著堆疊一路返回，同時回傳當初傳給 `panic` 的參數。由於處理堆疊的時候只會執行 `defer` 裡的程式，所以 `recover` 要跟 `defer` 一起用才有意義。


`recover` 的其中一種用法，是把伺服器的中，出錯的 goroutine 中斷，但不去影響其他的 goroutine。

```g
func server(workChan <-chan *Work) {
    for work := range workChan {
        go safelyDo(work)
    }
}

func safelyDo(work *Work) {
    defer func() {
        if err := recover(); err != nil {
            log.Println("work failed:", err)
        }
    }()
    do(work)
}
```

在這個範例中，如果 `do(work)` 發生了 panic，結果會記錄下來，並且這個 goroutine 會結束執行，不會中斷其他的 goroutine。你不需要再 `defer` 中加上什麼東西來完成這件事，一個 `recover` 就夠了。

除非是在 `defer` 裡直接呼叫，不然 `recover` 一律會回傳 `nil`。所以你可以在 `defer` 裡呼叫那些有使用到 `panic` 和 `recover` 的程式庫。舉例來說，`safelyDo` 裡的 `defer` 可以在 `recover` 之前先呼叫 `log.Println` 而不會受到目前 `panic` 的狀態干擾。

靠著這個模式，`do` 函式 (和它所呼叫的其他程式庫) 可以用 `panic` 來處理各種問題。在複雜程式中，我們可以用這種模式簡化錯誤處理。我們用一個理想化的 `regex` 套件來作例子，它會透過 `panic` 回報一個代表解析失敗的自訂錯誤型別。以下是這個錯誤型別 `Error`, `error` 方法 和 `Compile` 函式的定義：

```go
// Error is the type of a parse error; it satisfies the error interface.
type Error string
func (e Error) Error() string {
    return string(e)
}

// error is a method of *Regexp that reports parsing errors by
// panicking with an Error.
func (regexp *Regexp) error(err string) {
    panic(Error(err))
}

// Compile returns a parsed representation of the regular expression.
func Compile(str string) (regexp *Regexp, err error) {
    regexp = new(Regexp)
    // doParse will panic if there is a parse error.
    defer func() {
        if e := recover(); e != nil {
            regexp = nil    // Clear return value.
            err = e.(Error) // Will re-panic if not a parse error.
        }
    }()
    return regexp.doParse(str), nil
}
```

如果 `doParse` 發生錯誤，呼叫 `recover` 的那段程式會把回傳值設成 `nil`，因為 `defer` 裡可以修改有預先命名的回傳值。而下一行會用型別斷言的方式檢查錯誤是不是我們自訂的那個型別。如果不是的話，型別斷言會失敗，產生一個新的執行時期錯誤，於是又會繼續循著堆疊返回，就像沒有呼叫過 `recover` 那樣。這代表如果是一些意料外的錯誤，比如陣列索引超出範圍一類的，這種錯誤不會被我們捕捉到。

`error` 方法 (這不會跟內建的 `error` 型別衝突，因為方法是綁定在某個型別裡面的) 可以讓你產生解析錯誤，又不用去花費精神思考堆疊處理順序的問題。

```go
if pos == 0 {
    re.error("'*' illegal at start of expression")
}
```

這個模式只應該在套件的內部使用。`Parse` 把 `panic` 轉成了錯誤碼，而非把它傳到套件之外。這是個該遵守的好原則。

另外，這個慣例會改變真正的錯誤。然而，改變前後的錯誤都會列在錯誤回報中，所以問題的根源還是可以找得到。通常這樣已經很足夠，不論如何程式還是中斷了。但你若想要保留原本的錯誤類型，你可以再多寫幾行程式來過濾並重新發出原本的錯誤。這就當成作業留給讀者自己練習了。

## 一個 web 伺服器

讓我們用一個完整的 Go 程式來當作結束。這個程式其實比較像是在轉發其他的 web 服務。Google 提供了 [http://chart.apis.google.com](http://chart.apis.google.com) 這個把資料轉成圖表的服務。這個服務不太方便從瀏覽器中使用，因為你得把資料用 URL 參數傳過去。我們的程式提供了一個比較簡便的作法：提供一個表單，讓你透過表單把文字轉成二維條碼。你可以透過用手機掃描二維條碼來拜訪你輸入的網址。

以下是完整的程式碼：

```go
package main

import (
    "flag"
    "html/template"
    "log"
    "net/http"
)

var addr = flag.String("addr", ":1718", "http service address") // Q=17, R=18

var templ = template.Must(template.New("qr").Parse(templateStr))

func main() {
    flag.Parse()
    http.Handle("/", http.HandlerFunc(QR))
    err := http.ListenAndServe(*addr, nil)
    if err != nil {
        log.Fatal("ListenAndServe:", err)
    }
}

func QR(w http.ResponseWriter, req *http.Request) {
    templ.Execute(w, req.FormValue("s"))
}

const templateStr = `
<html>
<head>
<title>QR Link Generator</title>
</head>
<body>
{{if .}}
<img src="http://chart.apis.google.com/chart?chs=300x300&cht=qr&choe=UTF-8&chl={{.}}" />
<br>
{{.}}
<br>
<br>
{{end}}
<form action="/" name=f method="GET"><input maxLength=1024 size=70
name=s value="" title="Text to QR Encode"><input type=submit
value="Show QR" name=qr>
</form>
</body>
</html>
`
```

到 `main` 函式之前的程式應該都算好懂。有一個命令列參數是設定我們的伺服器的 HTTP port。`templ` 變數則是 HTML 樣版，我們稍後會解釋它。

`main` 函式解析命令列參數，然後用我們之前提到的技巧把 `QR` 函式註冊成 HTTP 請求的處理程式。`http.ListenAndServe` 會啟動 HTTP 伺服器，在伺服器運作期間主程式是暫停的。

`QR` 接收請求，然後把表單資料塞進樣版裡。


`html/template` 套件是一個強大的樣版引擎，而我們現在只用到一點皮毛。大致上，它會即時地根據你傳給 `templ.Execute` 的參數把改寫 HTML 樣版。在樣版 (templaStr) 中，樣版引擎會依照雙重大括號包住的部份執行特定的動作。從 `{{if .}}` 到 `{{end}}` 這段代表如果目前的資料，又稱為 `.`，不是空的，那就顯示或執行裡面的東西。也就是說如果目前資料是空的，這段就不會顯示出來。

接下來的兩個 `{{.}}` 會把 (表單傳來的) 資料顯示出來。樣版引擎會幫你處理好跳脫特殊字元的問題。

剩下的是就單純的 HTML。如果覺得我們講解的太簡單，你可以參考 [template 套件的說明文件](http://golang.org/pkg/html/template/)。

我們只用了幾行程式加上一些 HTML 碼，就做好了一個實用的小程式。Go 語言強大之處讓你可以用短短幾行程式就做到很多事情。
