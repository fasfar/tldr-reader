package parser

import (
	"fmt"
	"log"
	"net/url"
	"os"
	"regexp"
	"strings"

	"github.com/antchfx/htmlquery"
	"golang.org/x/net/html"
)

func writeToFile(filename string, body string) {
	file, _ := os.Create(filename)
	defer file.Close()
	linkLineBreakExp := regexp.MustCompile(`=\r?\n`)
	body = linkLineBreakExp.ReplaceAllString(body, "")
	file.WriteString(body)
}

func GetMessageArticles(body string) {
	doc, _ := html.Parse(strings.NewReader(body))
	articleItem := "//table//tbody//tr//td//div"
	nodes, err := htmlquery.QueryAll(doc, articleItem)
	if err != nil {
		log.Fatal("Error querying HTML: ", err)
	}

	var allLines string
	// Iterate through each matching div element
	for _, node := range nodes {
		fmt.Println("Found div class 'text-block'")
		// Extract all span elements within the div
		spans := htmlquery.Find(node, ".//span")
		links := htmlquery.Find(node, ".//a/@href")
		for i, span := range spans {
			if i == 1 {
				title := htmlquery.InnerText(span)
				if strings.Index(title, "Sponsor") > 0 || strings.Index(title, "Sign Up") > 0 || strings.Index(title, "Jenny Xu") > 0 {
					continue
				}
				allLines += fmt.Sprintf("Title: %v\n", strings.TrimSpace(title))
			}

			if i == 2 {
				description := htmlquery.InnerText(span)
				allLines += fmt.Sprintf("Description: %v\n", strings.TrimSpace(description))
			}
		}
		for _, link := range links {
			article, e := url.PathUnescape(htmlquery.InnerText(link))
			if e != nil {
				fmt.Println("Error decoding article link:", err)
			}
			prefixLength := len("https://tracking.tldrnewsletter.com/CL0/")
			suffixStart := strings.Index(article, "?utm_source")
			if prefixLength > 0 && suffixStart > 0 {
				article = article[prefixLength:suffixStart]
				allLines += fmt.Sprintf("Link: %v\n", article)
			}
		}
	}
	writeToFile("myTestFile.txt", allLines)
	fmt.Println("done")
}
