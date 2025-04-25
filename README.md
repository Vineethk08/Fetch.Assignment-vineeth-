Please follow below steps to execute the code written in go:
1. Install Go by following the instructions in below URL for Windows, Linux, MacOS
    Visit: https://golang.org/dl/
2. Set up the workspace
    Create a directory
      mkdir dirname
      cd dirname
    Create a Go Module
      go mod init dirname
3. install yaml package as it is used in code
    go get gopkg.in/yaml.v3
4. Add required files like main.go, sample.yaml in the directory
5. Execute the go file after doing necessary changes
    go run main.go sample.yaml


Issue Identified and Resolved:

- Added required imports and remove unnecessary imports as they are not used
    Added :
      "net/url" 
    Removed:
      "encoding/json"
      "strings"

-  Removed below line to avoid sending entire endpoint instead of sending only body in it
    Added:
      bodyBytes := []byte(endpoint.Body)
    Removed:
      bodyBytes, err := json.Marshal(endpoint)
      if err != nil {
        return
      } 
- Made below changes to close the request after reading it to avoid leakages, also added check for duration  less than 500ms

    before:
      if err == nil && resp.StatusCode >= 200 && resp.StatusCode < 300 && duration <= 500*time.Millisecond {
        stats[domain].Success++
      }
    after:
      if err == nil && resp.StatusCode >= 200 && resp.StatusCode < 300 && duration <= 500*time.Millisecond {
        defer resp.Body.Close()
        stats[domain].Success++
      }

- Modified the extractDomain function to avoid port reading:

    before:
      func extractDomain(url string) string {
        urlSplit := strings.Split(url, "//")
        fmt.printf("%s%s",urlSplit,urlSplit[len(urlSplit)-1])
        domain := strings.Split(urlSplit[len(urlSplit)-1], "/").split("::")[0]
        return domain
      }
    after:
      func extractDomain(urls string) string {

          parsedurl, err := url.Parse(urls)
          if err!=nil{
          fmt.Printf("Error parsing the url:%s\n",urls)
          return urls
          }
          domain:=parsedurl.Hostname()
          
          return domain
      }
- Modified logResults function to avoid zerodivision error
    before:
      func logResults() {
        for domain, stat := range stats {
          if stat.Total{
          percentage := math.Round(10000 * float64(stat.Success) / float64(stat.Total))/100
          fmt.Printf("%s has %.2f%% availability\n", domain, percentage)
          }
          else{
            fmt.printf("No data is available",domain)
          }
        }
      }
    after:
      func logResults() {
        for domain, stat := range stats {
          if stat.Total >0{
            percentage := math.Round(10000 * float64(stat.Success) / float64(stat.Total))/100
            fmt.Printf("%s has %.2f%% availability\n", domain, percentage)
          }else{
            fmt.Printf("No data is available%s\n",domain)
          }
        }
      }
