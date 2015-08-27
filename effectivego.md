## 簡介

Go 是一門新的程式語言。雖然它從現有的程式語言中借鏡了許多設計理念，但它也有許多與眾不同的特性。若你把用 C++ / Java 撰寫的程式改寫成等價的 Go 程式，恐怕難以得到令人滿意的結果。同樣地，用 Go 語言的角度來解決問題，你可能會寫出有效但不太一樣的程式。換句話說，想要寫出好的 Go 程式，對於 Go 語言特性及慣例的掌握是重要的一環。同時，對於社群慣例 (比如排版、命名方式、程式的建立等等) 也應該要適當的了解，這樣其他使用 Go 語言的開發者才容易理解你寫的程式。

這份文件列舉出一些訣竅，讓你寫出清楚、符合 Go 語言特性的程式。這是對於 [Go 語言規格書](https://golang.org/ref/spec)、[導覽 Go 語言](https://tour.golang.org/) 以及 [如何撰寫 Go 程式](https://golang.org/doc/code.html) 等三份文件的補充，所以你應該先讀過這三份文件。

### 範例

[Go 標準套件庫原始碼](https://golang.org/src/) 除了作為核心程式庫之外，也是如何使用 Go 語言的範例。更進一步地，大部份的套件都會有一些可以直接在 [golang.org](golang.org) 網站上讓你實驗的範例，比如像是 [這個](https://golang.org/pkg/strings/#example_Map) (你也許會需要點擊 Example 字樣來顯示範例)。如果你對於如何呈現問題或是某事物的實作方式有疑問，Go 套件庫的文件、原始碼以及範例可以提供你靈感、解答或是相關的背景知識。

## 排版格式

排版風格問題是最常被討論但很少有結果的問題。人們可以套用各式各樣的風格，但如果大家的風格能統一的話會更好。風格統一最困難的一點，就是如何避免寫出落落長的風格規範。

對於 Go 語言來說，我們使用了一種很少見的解決方式：讓機器來為我們處理排版問題。*gofmt* 工具 (或是 *go fmt*，它會將整個套件的原始碼都重新排版，而非只處理單一源碼檔案) 讀取 Go 程式碼，依照標準的排版風格把程式碼重新排版。如果你想弄清楚某種情況下應該怎麼排版，就執行 *gofmt*。如果結果看起來怪怪的，你可以調整你的程式 (或是提出 *gofmt* 的錯誤回報)，千萬不要削足適履 。

舉個例子，你再也不需要拼命按 Tab 和空白鍵去對齊你的行內註解：

```go
type T struct {
    name string // name of the object
    value int // its value
}
```

*gofmt* 會幫你處理好

```go
type T struct {
    name    string // name of the object
    value   int    // its value
}
```

標準套件庫的原始碼都有使用 *gofmt* 排版過。

當然還有些排版的細節我們沒有提到，簡略的說：

* 縮排：我們使用 Tab 來縮排，*gofmt* 預設會忽略它們。非必要的話別使用空白來縮排。
* 行寬：Go 沒有行寬限制，不用擔心你的程式碼太長。如果你真的覺得它太長了，可以換行並縮排它。
* 括號：Go 需要的括號遠比 C++ / Java 來得少：流程控制敘述 (if, for, switch) 沒有小括號。運算子之間的優先順序也會以排版的方式突顯。比如

```go
x<<8 + y<<16
```

## 註解

Go 提供的 C 風格的區塊註解 /* */ 以及 C++ 風格的行內註解 //。行內註解比較常用到，區塊註解通常是用來說明整個套件，但當你想要暫時停用某段程式碼的時候也很適合。

*godoc* 程式 (它同時也是一個 web server) 會解析原始碼中的註解。在最外層的宣告語句上面的註解，如果沒有被空行隔開的話，會當成是對於那個宣告語句的說明。這些說明的內容決定了 *godoc* 是否能產生出高品質的說明文件。

每個套件都應該要有一份套件說明，也就是在 package 語法上方的區塊註解。如果套件裡有多個源碼檔案，註解可以寫在其中任何一個檔案裡。套件說明應該是整體性的說明，或是提供整個套件的通用資訊。套件說明在 *godoc* 產生出來的文件裡會在頁面的最上方，所以你應該寫詳細點，像是這樣：

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

註解不需要用星號一類的字元做出框框，因為產生出來的文件不一定會用固定寬度的文字來呈現，所以不要用空白一類的方式來排版，*godoc* 會幫你處理排版問題。註解應該是純文字，像 HTML 或是其他標記方式應該要避免。其中一項 *godoc* 的排版方式，是把縮排後的文字用固定寬度文字顯示，方便你在說明中嵌入範例程式碼。[fmt 套件](https://golang.org/pkg/fmt/) 的說明是個好例子。

某些情況下 *godoc* 也許完全不會重新排版，所以你應該讓你的註解容易閱讀：不要寫錯別字、正確使用標點符號，在適當的地方斷句等等。

套件裡的每個最上層的宣告語句，其上方的註解都會被當成是那個語句的說明文件。每個公開的名稱 (exported，就是首字大寫的名稱) 都應該要有一份說明。

說明文件應該要是完整的句子，第一句應該是簡略的說明，並且以宣告語句所宣告的名稱做為起始。

```go
// Compile parses a regular expression and returns, if successful, a Regexp
// object that can be used to match against text.
func Compile(str string) (regexp *Regexp, err error) {
```

這樣的設計是為了方便你使用 *grep* 來搜尋用 *godoc* 產生出來的文件。假設你忘記 regexp 套件中某個函式的名稱，但你記得這個函式會分析 (parse) 正則式，你可能會這樣子搜尋文件：

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

之後，就可以用 `bytes.Buffer` 來存取 bytes 套件中定義的 Buffer 結構。如果每個人都可以用相同的名字來存取相同的套件內容，事情就會簡單許多；這代表了套件的名字要取的簡潔有力。慣例上，套件名稱應該是小寫的單字，避免使用底線或大小寫混雜。考慮到大家都會在程式碼中一再輸入各種名稱，像是 *Err* 這樣的命名方式顯然夠簡短，又能讓人一眼看出來它大概會是什麼。套件名稱只是引用時的預設名稱，所以不用擔心會和其他套件撞名，也不需要強制在每個源碼檔中使用相同的名稱。如果真的發生撞名的情況，你完全可以在引用的時候指定另一個名字給它。通常你不會因此搞混，因為引用時的完整套件名稱讓你可以確定自己是引用了什麼。

另一個慣例是我們會用原始碼的目錄作為套件名稱：放在 *src/encoding/base64* 目錄裡的套件會以 *encoding/base64* 這個名稱引入，不是 *encoding_base64* 也不是 *encodingBase64*。

程式可以用套件名稱來參考套件中公開的資源，這樣可以避免混淆。切勿使用 **.** 來引入，這是設計來簡化一些必須在套件外進行的測試，除此之外的用途應當盡量避免。例如在 *bufio* 套件中，具有緩衝區的可讀取串流會命名為 *Reader* 而不是 *BufReader*，因為 *bufio.Reader* 比 *bufio.BufReader* 更簡短也更精確。進一步來說，因為引用的時候會以套件名稱作為前綴，所以 *bufio.Reader* 不會跟 *io.Reader* 混淆。同樣的，用來產生一個新的 *ring.Ring* 實體的函式 (在 Go 語言裡稱為建構子)，通常會命名為 *NewRing*，但考慮到 *ring* 套件裡只有 *Ring* 這個公開的結構，而且套件名稱跟結構名稱又重複，所以取名為 *New* 可以更加簡潔：其他人會使用 *ring.New* 來呼叫它。總之就是利用套件結構來幫助你選擇一個好名字。

另一個好例子是 *once.Do*：寫 *once.Do(setup)* 很清楚，就算你寫的更長 *once.DoOrWaitUntilDone(setup)* 也不會更清楚。很長的名稱不見得讓人容易理解，清楚又精確的說明文件會更有效果。

### 成員存取方法 (Getter / Setter)

Go 語言並沒有在語言層面支援成員存取方法。使用成員存取方法沒有錯，而且通常是不錯的設計模式，但是在成員存取方法前加上 Get 或 Set 既不必要，也不符合 Go 語言的慣例。如果你有個成員變數叫做 *owner* (小寫字母開頭代表不能公開存取)，那麼相關的成員存取方法應該稱為 *Owner()* (大寫字母開頭) 以及 *SetOwner*。這樣閱讀起來也十分清楚：

```go
owner := obj.Owner()
if owner != user {
    obj.SetOwner(user)
}
```

### 介面名稱

慣例上，只有一個方法的介面，會用方法的名字加上 er 字尾，或是其他類似的方式來命名，這樣可以營造一種代理人的感覺，像是 *Reader*, *Writer*, *Formatter* 等等。

像這樣的名字很多，而遵守這個慣例會讓你更有生產力。像是 *Read*, *Write*, *Close*, *String* 這些都是，它們都有標準化的定義。為了避免混淆，除非你寫的函式和它們有相同的定義，否則就不要使用這些名字。相反地，如果你實做了相同的定義的函式，那麼就應該照它們的方式命名，比如用 *String* 而不是 *ToString*。

### 大小寫混合

最後，如果你需要使用數個單字來做命名，用 *MixedCaps* 或是 *mixedCaps*，不要用底線。

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

Go 程式通常只會在 *for* 敘述裡使用分號，這是用來隔開迴圈的初始、條件、後處理這三個寫在同一行的敘述。

這個自動加分號的規則暗示了你不可以把流程控制語句 (*for*, *if*, *switch*, *select*) 的左大括號放到下一行，因為分號會加到大括號的前面去，而顯然這不會是你想要的結果。所以應該要寫成這樣

```go
if i < f() {
    g()
}
```

## 流程控制

Go 的流程控制語法跟 C 很像，但有很多重要的不同。Go 沒有 *do* 和 *while*，只有比 C 更通用一點的 *for*；*switch* 比較有彈性；*if* 跟 *switch* 可以在條件判斷式之前加上一個初始語句，有點像 *for* 那樣；*break* 和 *continue* 可以指定標記；而且還有一些新的特性：支援型別判斷的 *switch* 和頻道多工器 *select*。語法上也有些微不同：條件式不用加小括號、不能省略大括號。

### if

簡單的 *if* 語句像是這樣

```go
if x > 0 {
    return y
}
```

不能省略的大括號暗示了應該避免寫單行的 *if* 語句，尤其當裡面有 *return* 或 *break* 的時候更是如此。

因為 *if* 像 *for* 一樣可以接受初始語句，所以在 *if* 語句初始一個區域變數是很常見的寫法：

```go
if err := file.Chmod(0664); err != nil {
    log.Print(err)
    return err
}
```

在 Go 的核心程式庫裡，你會發現很多可能不會執行下一行程式的 *if* 語句 (比如說裡面的最後一個敘述是 *return*, *break* continue* 之類的) 都省略掉了 *else* 敘述：

```go
f, err := os.Open(name)
if err != nil {
    return err
}
codeUsing(f)
```

這是一般常見用來偵測、防止例外情況的寫法。如果你的程式一路上把出錯的情況都挑掉，正常的情況執行下去，那麼它就會很容易閱讀。如果錯誤都透過 *return* 敘述離開你的函式，那麼最後寫出來的程式碼就不會有 *else* 敘述：

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

上一節的最後一個範例展示了短式宣告 *:=* 的使用方式。

```go
f, err := os.Open(name)
```

這一行宣告了 *f* 和 *err* 兩個變數。而後面的

```go
d, err := f.Stat()
```

看起來好像是宣告了 *d* 和 *err* 兩個變數。要注意的是，雖然看起來 *err* 好像宣告了兩次，但這是合法的敘述：*err* 在第一個敘述中宣告，而第二個敘述裡只是重新指定了新的值。也就是說在呼叫 *f.Stat* 的時候，*err* 會使用之前宣告的那個變數，只是賦予新的值給它。

所以若是滿足以下三個條件，使用 *:=* 宣告先前宣告過的變數會變成賦予新值：

* 必須要在相同的有效範圍 (scope) 內。如果是不同的有效範圍就會變成宣告一個同名的、全新的區域變數 (註)。
* 型別要相同。
* *:=* 左邊至少要有一個之前沒有宣告過的變數。

這個特性雖然不太常見，但卻很實用。這會讓你簡單地重用相同的 *err* 變數，這種用法在一長串的 *if-else* 中特別常見。

備註：值得注意的是，在 Go 語言中，函式的參數及返回值是寫在函式大括號的外面，但其實它們跟函式本體屬於同一個有效範圍。

### for

Go 裡面的 *for* 跟 C 裡面的有點像又不太像。它把 *for* 跟 *while* 整合在一起 - 因為 Go 沒有 *do-while*。*for* 有三種形式：

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

如果你要遍歷一個 array, slice, string 或是 map，你可以使用 *range* 語法：

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

空識別子 (*_*) 有很多用法，會在稍後的章節介紹。

在遍歷字串 (string) 的時候，*for* 會幫你做些處理：幫你解析 UTF8，斷字在正確的位元。錯誤的編碼會消耗一個位元組 (byte)，並回傳一個特別的 *rune* (*rune* 是 Go 的術語，指的是一個 UTF8 的字元。在 Go 裡有同名的套件專門處理 *rune*) U+FFFD。以下的程式碼：

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

最後，Go 沒有逗號這個運算子，而且 *++* 和 *--* 是完整的語句而不是敘述的一部份。如果你需要在 *for* 裡使用多個變數，你可以用多重指定：

```go
// Reverse a
for i, j := 0, len(a)-1; i < j; i, j = i+1, j-1 {
    a[i], a[j] = a[j], a[i]
}
```

### switch

Go 的 *switch* 比 C 的要泛用。*case* 敘述比對的值不一定要是常數。*case* 敘述會依上到下的順序一一比對，直到找到符合的項目。如果 *switch* 後面沒有東西，那麼就會當成是 *switch true*。所以你可以 (而且這也是慣例) 把一長串的 *if-else if* 改寫成 *switch*：

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

不像 C 語言，Go 的 *switch* 不會跨過 *case* 執行，但你可以使用逗號代表「這些值都可以」：

```go
func shouldEscape(c byte) bool {
    switch c {
    case ' ', '?', '&', '=', '#', '+', '%':
        return true
    }
    return false
}
```

雖然在 Go 裡不常用，但你確實可以用 *break* 跳出 *switch*，就像在其他類 C 的語言裡一樣。有時候你不止想要跳出 *switch*，可能想要連外層的 *for* 迴圈一併跳過，你可以用 *break* 加標記的方式達成：

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

當然，*continue* 也可以加標記，但只能用在迴圈上。

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

### 判斷型別的 switch

*switch* 也可以用來判斷動態型別，這會用到型別斷言 (type assertion) 的語法及 *type* 關鍵字。如果你宣告一個區域變數給它，那麼這個區域變數的型別會和 *case* 敘述比對的型別相同：

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

在 C 語言裡，寫入錯誤會回傳 -1 (寫入了 -1 個位元組，顯然你知道這有問題)，然後把錯誤碼寫進某個傳址進來的參數。在 Go 語言裡，我們會回傳寫入了多少位元組 (這點跟 C 差不多)，同時也回傳錯誤碼：「好，你確實寫了幾 byte 進去，不過不是全部，因為磁碟機滿了」。所以 Go 語言裡的 *Write* 函式的定義是這樣的：

```go
func (file *File) Write(b []byte) (n int, err error)
```

如同說明文件所示，它會回傳已經寫入的位元組數，如果這與 *b* 的長度不符，err 就會是錯誤碼。你可以在稍後錯誤處理的章節看到更多例子。

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

### 返回值的預先命名

返回值可以預先給予名字，這樣就可以在函式內像一般變數那樣地使用它。它們會被初始化為零值，而遇到沒有加上返回值的 *return* 語句時，就會把它們傳回去。

這種寫法不是預設的，但很多時候可以讓你的程式碼清楚不少：這等於在寫說明文件。如果我們把剛剛的 *nextInt* 函式的返回值加上命名，你就能一目瞭然地理解哪個 int 是什麼意義：

```go
func nextInt(b []byte, pos int) (value, nextPos int) {
```

因為這些變數會初始化為零值，如果用的好的話可以讓整段程式都簡化許多。以下以 *io.ReadFull* 做為例子：

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

*defer* 語句是把某個函式的呼叫排程在返回目前函式時才執行。這個特性不是很常用，但在某些情況下，比如必須釋放的資源，會非常有用。典型的例子是釋放同步鎖或是關閉檔案：

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

使用 *defer* 有兩大好處：首先，你不會因為忘記釋放資源或關閉檔案造成程式錯誤；其次，如果你在開啟檔案之後馬上用 *defer* 語句來預約關閉檔案，會比在程式結尾關閉檔案更好閱讀。

使用 *defer* 時，參數會在你寫下 *defer* 本身執行的時候就確定它的值，而不是在函式真正執行的時候。所以不用擔心後續的變數操作，同時也表示你可以用一行 *defer* 來定義數個操作，以下是個不太優雅的例子：

```go
for i := 0; i < 5; i++ {
    defer fmt.Printf("%d ", i)
}
```

*defer* 會以相反的順序執行，也就是最先定義的 *defer* 會最後執行。所以上面的範例最後會印出「4 3 2 1 0」。有個比較可能的例子：用來追蹤函式的執行：

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

由於在 *defer* 執行的時候，參數的值便已經確定，我們可以好好利用這個特性：

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

對於已經習慣以程式區塊作為資源管理單位的開發者，這樣的行為可能會有點詭異，不過事實上 *defer* 的資源管理單位是函式，這也是它強大之處。後續在 *panic* 和 *recover* 的章節我們會看到 *defer* 的其他可能性。

譯註：上一段翻譯品質不佳，需要 PR

## 資料

### 用 *new* 來配置

Go 有兩個配置資源的內建函式：*new* 和 *make*。兩者適用於不同的場合，也許看起來有點讓人容易搞混，但其實很簡單。我們世來看 *new*。雖然它確實會配置記憶體，但不像在其他語言那樣，Go 的 new 函式不會「建構」它 (譯註)，只會填入零值。也就是說，`new(T)` 配置了一塊記憶體，填入符合 *T* 型別的零值，然後把位址傳回來。用 Go 的講法，就是回傳一個 *T* 型別的指標，記憶體內容是零值。

譯註：原文 but unlike its namesakes in some other languages it does not initialize the memory 中的 initialize 通常翻成「初始化」，但此處語意應該是「不像 C++ 或 Java 那樣會幫你呼叫建構子 (constructor) *處理成員的初始值*」，所以改譯為「建構」。

既然 *new* 會為你填入零值，你可以把你的資料結構設計成零值就能用，那麼寫起程式來就會方便很多。比如像 *bytes.Buffer* 的說明文件：the zero value for Buffer is an empty buffer ready to use. (Buffer 的零值就是一個空 buffer 並且可以直接使用)。類似地，*sync.Mutex* 並沒有建構子，但它的零值就是一個解開 (unlock) 的鎖。

這個特性可以一個接一個的擴展下去。考慮下列型別：

```go
type SyncedBuffer struct {
    lock    sync.Mutex
    buffer  bytes.Buffer
}
```

這個型只要 *new* 完或是宣告完就可以馬上使用 (譯註)。以下的程式碼中，*p* 和 *v* 都不需要另外再做初始化：

```go
p := new(SyncedBuffer)  // type *SyncedBuffer
var v SyncedBuffer      // type  SyncedBuffer
```

譯註：因為 *SyncedBuffer* 的所有成員都符合這個零值就能使用的特性，所以它本身也可以直接使用。

### 建構子及複合結構的表達式 (literal)
