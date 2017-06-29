# gRPCSample
REST 的另一個選擇：gRPC

Google 為此推出了一個新的程式呼叫協議 gRPC，簡單說就是能夠從你的 Protobuf 檔案生出一個伺服器出來，這個好處就是不需要像傳統 RESTful API 伺服器ㄧ樣撰寫一堆路由。
當我們撰寫傳統 RESTful API 伺服器時，我們會花費許多的時間在路徑命名上。除此之外，兩台 REST 伺服器如果要進行溝通，就必須知道對方的 RESTful API 路由⋯⋯等，步驟十分麻煩，這些在 gRPC 中都有所改善。

# 結構就是伺服器
還記得 .proto 檔案可以用來定義資料結構嗎？現在我們在裡面新增了一個 service 用來定義一個服務，而其中的 rpc Plus(.. 定義著這個服務有什麼樣的功能。當你用上這些方法之後，你的 Protobuf 就升級成 gRPC 了。

// Calculator 定義了一個計算用的服務。
service Calculator {  
    // Plus 會接收 CalcRequest 資料作加總，最終會回傳 CalcReply。
    rpc Plus (CalcRequest) returns (CalcReply) {}
}

// CalcRequest 包含了兩個數字，將會傳送至計算服務並對兩個數字進行計算。
message CalcRequest {  
    int32 number_a = 1;
    int32 number_b = 2;
}

// CalcReply 是計算結果，將會回傳給客戶端。
message CalcReply {  
    int32 result = 1;
}

你的伺服器具有哪些功能、接收哪些資料現在都已經寫得十分明確了，是時候透過這個結構建立伺服器了。

# 1. 安裝 gRPC 所需工具
在安裝之前請記得先安裝主要的 Protobuf 生成工具，接著透過下列指令安裝 gRPC 套件。
$ go get google.golang.org/grpc

然後是安裝擴充插件，這樣 Protobuf 才能夠將其中 gRPC 的用法轉化成程式語言。
$ go get -u github.com/golang/protobuf/{proto,protoc-gen-go}

接著宣告環境變數，這樣才能直接在終端機使用 Go 的程式。
$ export PATH=$PATH:$GOPATH/bin

# 2. 建立資料夾
先依照下列結構建立三個資料夾。
.
├── client  // gRPC 客戶端
├── pb      // 擺放 Protobuf 文件
└── server  // gRPC 伺服器

在這裡稍微解釋一下這三個資料夾的分別用途：

- client：這裡會放置 gRPC 客戶端，客戶端就是用來和伺服器溝通的程式。如果你是撰寫網路服務，那麼就有點像是瀏覽器對伺服器的感覺。

（要注意的是瀏覽器現在還不能直接跟 gRPC 伺服器溝通，所以你需要安插一個 Gateway 將請求轉到 gRPC 客戶端）

pb：這個資料夾會擺放我們定義的 Protobuf 文件，而這裡也會放置轉化後的 Protobuf 程式。

server：這裡則是擺置 gRPC 伺服器，就像是平常所撰寫的 RESTful API 伺服器一般。

# 在前幾個星期的文章中我們提到了「比起 JSON 更方便、更快速、更簡短的 Protobuf 格式」，但這還沒完。Google 為此推出了一個新的程式呼叫協議 gRPC，簡單說就是能夠從你的 Protobuf 檔案生出一個伺服器出來，這個好處就是不需要像傳統 RESTful API 伺服器ㄧ樣撰寫一堆路由。

當我們撰寫傳統 RESTful API 伺服器時，我們會花費許多的時間在路徑命名上。除此之外，兩台 REST 伺服器如果要進行溝通，就必須知道對方的 RESTful API 路由⋯⋯等，步驟十分麻煩，這些在 gRPC 中都有所改善。

結構就是伺服器

還記得 .proto 檔案可以用來定義資料結構嗎？現在我們在裡面新增了一個 service 用來定義一個服務，而其中的 rpc Plus(.. 定義著這個服務有什麼樣的功能。當你用上這些方法之後，你的 Protobuf 就升級成 gRPC 了。

// Calculator 定義了一個計算用的服務。
service Calculator {  
    // Plus 會接收 CalcRequest 資料作加總，最終會回傳 CalcReply。
    rpc Plus (CalcRequest) returns (CalcReply) {}
}

// CalcRequest 包含了兩個數字，將會傳送至計算服務並對兩個數字進行計算。
message CalcRequest {  
    int32 number_a = 1;
    int32 number_b = 2;
}

// CalcReply 是計算結果，將會回傳給客戶端。
message CalcReply {  
    int32 result = 1;
}



你的伺服器具有哪些功能、接收哪些資料現在都已經寫得十分明確了，是時候透過這個結構建立伺服器了。

1. 安裝 gRPC 所需工具

在安裝之前請記得先安裝主要的 Protobuf 生成工具，接著透過下列指令安裝 gRPC 套件。

$ go get google.golang.org/grpc


然後是安裝擴充插件，這樣 Protobuf 才能夠將其中 gRPC 的用法轉化成程式語言。

$ go get -u github.com/golang/protobuf/{proto,protoc-gen-go}



接著宣告環境變數，這樣才能直接在終端機使用 Go 的程式。

$ export PATH=$PATH:$GOPATH/bin


2. 建立資料夾

先依照下列結構建立三個資料夾。

.
├── client  // gRPC 客戶端
├── pb      // 擺放 Protobuf 文件
└── server  // gRPC 伺服器



在這裡稍微解釋一下這三個資料夾的分別用途：

- client：這裡會放置 gRPC 客戶端，客戶端就是用來和伺服器溝通的程式。如果你是撰寫網路服務，那麼就有點像是瀏覽器對伺服器的感覺。

（要注意的是瀏覽器現在還不能直接跟 gRPC 伺服器溝通，所以你需要安插一個 Gateway 將請求轉到 gRPC 客戶端）

- pb：這個資料夾會擺放我們定義的 Protobuf 文件，而這裡也會放置轉化後的 Protobuf 程式。
- server：這裡則是擺置 gRPC 伺服器，就像是平常所撰寫的 RESTful API 伺服器一般。

理解了之後我們就可以到下一步啦。

# 3. 轉化 Proto 文件
將下列的內容存檔成 calc.proto，並擺至於 /pb 資料夾內。
// 撰寫格式是 Proto v3。
syntax = "proto3";  
// 生成的程式在 Golang 中將會屬於 `pb` 套件。
package pb;

// Calculator 定義了一個計算用的服務。
service Calculator {  
    // Plus 會接收 CalcRequest 資料作加總，最終會回傳 CalcReply。
    rpc Plus (CalcRequest) returns (CalcReply) {}
}

// CalcRequest 包含了兩個數字，將會傳送至計算服務並對兩個數字進行計算。
message CalcRequest {  
    int32 number_a = 1;
    int32 number_b = 2;
}

// CalcReply 是計算結果，將會回傳給客戶端。
message CalcReply {  
    int32 result = 1;
}

現在我們要將這個 Protobuf 文件轉化成 Golang 程式，這樣我們才能夠在 Golang 中使用，先確定終端機進入了 /pb 資料夾，然後執行下列指令。
$ protoc --go_out=plugins=grpc:. *.proto

現在你的目錄結構看起來會像這樣。
.
├── client
├── pb
│  ├── calc.pb.go
│  └── calc.proto
└── server

接著我們要透過這個生成的 .pb.go 檔案來建造出我們的 gRPC 伺服器。


# 4. 建立 gRPC 伺服器
我們將會建立一個 gRPC 伺服器，這個伺服器將會有一個 Plus() 函式，會接收 CalcRequest 然後做處理，最終回傳 CalcReply。
先在你的 /server 目錄下建立一個新的 main.go，然後引用這些套件。

package main

import (
	"log"
	"net"

	"golang.org/x/net/context"
	"google.golang.org/grpc"

	"gRPCSample/pb"
	"google.golang.org/grpc/reflection"
)

接著我們就要開始實作剛才 Protobuf 內所定義的功能啦！

// server 建構體會實作 Calculator 的 gRPC 伺服器。
type server struct{}

// Plus 會將傳入的數字加總。
func (s *server) Plus(ctx context.Context, in *pb.CalcRequest) (*pb.CalcReply, error) {

    // 計算傳入的數字。
    result := in.NumberA + in.NumberB

    // 包裝成 Protobuf 建構體並回傳。
    return &pb.CalcReply{Result: result}, nil
}

現在讓我們在 main() 函式中監聽 :50051 埠口，然後在這個埠口部署我們的 Calculator 伺服器。
func main() {  
    // 監聽指定埠口，這樣服務才能在該埠口執行。
    lis, err := net.Listen("tcp", ":50051")
    if err != nil {
        log.Fatalf("無法監聽該埠口：%v", err)
    }

    // 建立新 gRPC 伺服器並註冊 Calculator 服務。
    s := grpc.NewServer()
    pb.RegisterCalculatorServer(s, &server{})

    // 在 gRPC 伺服器上註冊反射服務。
    reflection.Register(s)

    // 開始在指定埠口中服務。
    if err := s.Serve(lis); err != nil {
        log.Fatalf("無法提供服務：%v", err)
    }
}

接下來這樣就完成了你的第一個 gRPC 伺服器，不過先別急著啟動⋯⋯。這看起來可能很茫然，因為比起傳統的 RESTful API 伺服器來說，你並不需要在 gRPC 裡面定義路由。但是別擔心！等一下寫到客戶端的時候你可能就會理解了。

# 5. 建立 gRPC 客戶端
當你有了伺服器之後，你就需要有能夠和伺服器互動的程式，而這就是客戶端。同樣地，我們可以透過剛才生成的 Protobuf 程式輕易地直接呼叫伺服器的程式，而不用像以前使用 RESTful API 伺服器時還要自己翻閱 API 文件。

現在到 /client 目錄下建立一個 main.go 檔案，並引用下列套件。
package main

import (
	"log"
	"golang.org/x/net/context"
	"google.golang.org/grpc"

	"gRPCSample/pb"
)

然後在 main() 函式中初始化與遠端 gRPC 伺服器的連線，然後直接使用遠端的 Plus() 函式，像這樣。
func main() {  
    // 連線到遠端 gRPC 伺服器。
    conn, err := grpc.Dial("localhost:50051", grpc.WithInsecure())
    if err != nil {
        log.Fatalf("連線失敗：%v", err)
    }
    defer conn.Close()

    // 建立新的 Calculator 客戶端，所以等一下就能夠使用 Calculator 的所有方法。
    c := pb.NewCalculatorClient(conn)

    // 傳送新請求到遠端 gRPC 伺服器 Calculator 中，並呼叫 Plus 函式，讓兩個數字相加。
    r, err := c.Plus(context.Background(), &pb.CalcRequest{NumberA: 32, NumberB: 32})
    if err != nil {
        log.Fatalf("無法執行 Plus 函式：%v", err)
    }
    log.Printf("回傳結果：%d", r.Result)
}

接下來就是時候看看我們的 gRPC 成果了。

# 6. 測試 gRPC 伺服器與客戶端
透過下列指令啟動伺服器和客戶端。

# 啟動 gRPC 伺服器。
go run ./server/main.go  
# 開啟客戶端與伺服器溝通。
go run ./client/main.go

接著就能看見下列結果，這個結果是客戶端傳遞 32 和 32 到 gRPC 伺服器作為加總計算所回傳的結果。

2017/03/12 06:37:53 回傳結果：64
