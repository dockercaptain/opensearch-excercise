package main

import (
	"context"
	"crypto/tls"
	"fmt"
	"net/http"
	"strings"

	"github.com/opensearch-project/opensearch-go"
	"github.com/opensearch-project/opensearch-go/opensearchapi"
)

type Fields struct {
	IP []string //`json:"ip"`
}
type Hitss struct {
	Fields Fields //`json:"fields"`
}
type Hits struct {
	Hits []Hitss //`json:"hits"`
}
type BackendResponse struct {
	Hits Hits //`json:"hits"`
}

func main() {

	client, _ := opensearch.NewClient(opensearch.Config{
		Transport: &http.Transport{
			TLSClientConfig: &tls.Config{InsecureSkipVerify: true},
		},
		Addresses: []string{"https://192.168.1.49:9200"},
		Username:  "admin", // For testing only. Don't store credentials in code.
		Password:  "admin",
	})

	content := strings.NewReader(`{
		"query": {
		  "match_all": {
		  }
		},
		"fields": [
			"ip"
		],
		"_source": false
	}`)

	max_doc := 10 //max 10000 it can go.
	size := max_doc / 10
	page := max_doc / size
	from := 0
	current_page := 1
	for i := 0; i <= page; i++ {
		from = size * (current_page - 1)
		fmt.Println("###############from: ", from, "current_page:", current_page, "total_page:", page)
		searchObj := opensearchapi.SearchRequest{
			Index:  []string{"opensearch_dashboards_sample_data_logs"},
			Size:   &size,
			From:   &from,
			Pretty: true,
			Body:   content,
		}

		searchRes, _ := searchObj.Do(context.Background(), client)
		// searchResByte, _ := io.ReadAll(searchRes.Body)
		fmt.Println(searchRes.String())

		// var data BackendResponse
		// err := json.Unmarshal(searchResByte, &data)
		// if err != nil {
		// 	fmt.Println(err)
		// }
		// for _, j := range data.Hits.Hits {
		// 	fmt.Println(j.Fields.IP)

		// }
		current_page = current_page + 1

	}
}
