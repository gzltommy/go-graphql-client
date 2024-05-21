## Installation

`go-graphql-client` requires Go version 1.13 or later.

```bash
go get -u github.com/gzltommy/go-graphql-client
```

## Usage
postman 请求
```
query {
  addressInfo(address: "0xb262844a841c5a3fde9a962bd8996c3814896a65") {
    nfts(option: {
      campaignID: "GComvUjWMG"
      nftCoreAddress: "0xADc466855ebe8d1402C5F7e6706Fccc3AEdB44a0"
      orderBy: ID
      order: ASC
    }) {
      totalCount
      list {
        id
        nftCore {
          contractAddress
        }
      }
    }
  }
}
```

go 代码请求
```go
package main

import (
	"context"
	"encoding/json"
	"fmt"

	"github.com/gzltommy/go-graphql-client"
)

func main() {
	client := graphql.NewClient("https://graphigo.prd.galaxy.eco/query", nil)
	client.Debug()
	var query struct {
		AddressInfo struct {
			Nfts struct {
				TotalCount int `graphql:"totalCount" json:"totalCount"`
				List       []struct {
					Id      string `graphql:"id" json:"id"`
					NftCore struct {
						ContractAddress string `graphql:"contractAddress" json:"contractAddress"`
					} `graphql:"nftCore" json:"nftCore"`
				} `graphql:"list" json:"list"`
			} `graphql:"nfts(option:{campaignID:$campaignID,nftCoreAddress:$nftCoreAddress,orderBy:ID,order:ASC})" json:"nfts"`
		} `graphql:"addressInfo(address:$address)" json:"addressInfo"`
	}

	variables := map[string]any{
		"address":        graphql.String("0xb262844a841c5a3fde9a962bd8996c3814896a65"),
		"campaignID":     graphql.ID("GComvUjWMG"),
		"nftCoreAddress": graphql.String("0xADc466855ebe8d1402C5F7e6706Fccc3AEdB44a0"),
	}

	jsonByte, err := client.QueryRaw(context.Background(), &query, variables)
	if err != nil || jsonByte == nil || len(*jsonByte) == 0 {
		fmt.Println("QueryProposals error:", err, jsonByte == nil)
		return
	}
	err = json.Unmarshal(*jsonByte, &query)
	if err != nil {
		fmt.Println("Unmarshal error:", err)
		return
	}

	fmt.Printf("%#v", query)

	return
}

```