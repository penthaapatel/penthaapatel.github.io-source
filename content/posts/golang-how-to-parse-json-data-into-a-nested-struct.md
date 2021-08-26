---
title: "Golang-How to parse JSON data into a nested struct"
date: 2019-05-22
draft: false
tags: 
  - golang
  - json
categories:
  - programming
cover: 
  image: "/golang-parse-json-into-nested-struct.jpeg"
---
*This post was originally published on my Medium profile : [Golang-How to parse JSON data into a nested struct](https://penthaa.medium.com/golang-how-to-parse-json-data-into-a-nested-struct-29be89ce2ae8)*

Say you are fetching data from a RESTful API using GET requests and want to store the JSON responses. One of the ways to achieve this is by parsing the JSON data into a Go struct. The following example will fetch data related to standings of a football league, UEFA Champions League in this case.

RESTful API used : [https://www.football-data.org/](https://www.football-data.org/)  
You’ll need a free API key to access the data.

## Construct a blueprint of the struct:
Here’s a useful tool that converts [JSON to a Go struct type](https://mholt.github.io/json-to-go/). I have removed a few fields to make the struct simpler. Each field is associated with a JSON tag.

> Construct separate struct types for JSON objects that are arrays. In the following example a separate type is constructed for `Standing` and `Table` and the fields `Standings` is a slice of `Standing` i.e. `[]Standing` and `Tables` is a slice of `Table` i.e. `[]Table` .


```go
type Competitions struct {
	Competition struct {
		Name string `json:"name"`
	} `json:"competition"`
	Season struct {
		CurrentMatchday int         `json:"currentMatchday"`
		Winner          interface{} `json:"winner"`
	} `json:"season"`
	Standings []Standing `json:"standings"`
}
type Standing struct {
	Stage  string  `json:"stage"`
	Type   string  `json:"type"`
	Group  string  `json:"group"`
	Tables []Table `json:"table"`
}
type Table struct {
	Position int `json:"position"`
	Team     struct {
		Name string `json:"name"`
	} `json:"team"`
	PlayedGames    int `json:"playedGames"`
	Won            int `json:"won"`
	Draw           int `json:"draw"`
	Lost           int `json:"lost"`
	Points         int `json:"points"`
	GoalsFor       int `json:"goalsFor"`
	GoalsAgainst   int `json:"goalsAgainst"`
	GoalDifference int `json:"goalDifference"`
}
```

## Get JSON data from the API:
`GetData` function returns a slice of byte. It does the following tasks:  
* Makes an HTTP GET request;  
* Sets the `“X-Auth-Token”` in request header;  
* Sets up an HTTP Client that makes an HTTP request and returns an HTTP response, following the auth-token as configured.  
* Reads HTTP response body that contains JSON data and converts it to a `[]byte`.

The `[]byte` returned can be used to parse JSON encoded data into any struct type using `[json.Unmarshal](https://golang.org/pkg/encoding/json/#Unmarshal)`.

```go
func GetData(url, apiKey string) []byte {
	request, err := http.NewRequest("GET", url, nil)
	if err != nil {
		panic(err)
	}

	request.Header.Set("X-Auth-Token", apiKey)

	var client = http.Client{}
	response, err := client.Do(request)
	if err != nil {
		panic(err)
	}
	defer response.Body.Close()

	jsonByte, err := ioutil.ReadAll(response.Body)
	if err != nil {
		panic(err)
	}

	return jsonByte

}
```

## Parse JSON into the struct

**Parse JSON into** `**Competitions**` **struct:**`GetStandings` function takes the league `id` as an argument to build the final url and returns a `Competitions` struct that has the parsed JSON data in it.

> `[json.Unamrshal](https://golang.org/pkg/encoding/json/#Unmarshal)` parses the JSON-encoded data that is in `[]byte` and stores the result in the struct whose address is given by `&s` .

_Note_: To make successful GET requests, set the variable [apikey](https://www.football-data.org/).

```go
func GetStandings(id string) (s Competitions) {
	/* id can be
	BSA Brazilian Division One League
	PL Premiere League League
	ELC Championship League
	CL Champions League Cup
	EC European Championships Cup
	FL1 France League 1 League
	BL1 Bundesliga League
	SA Italy Serie A League
	DED Eredivise League
	PPL Portuguese Primera Division League
	PD Primera Division League
	WC World Cup */
  
	url := "https://api.football-data.org/v2/competitions/" + id + "/standings"
	apikey := "SetYourAPIkeyHere"
	d := GetData(url, apikey)
	err := json.Unmarshal(d, &s)
	if err != nil {
		panic(err)
	}
	//Uncomment this section to pretty print json on terminal
	/* 	data, err := json.MarshalIndent(s, "", "  ")
	   	if err != nil {
	   		panic(err)
	   	}
	   	fmt.Println(string(data)) */

	return
}
```

_Voila!_  
Print the struct on terminal using the format verb `%+v` variant that will include the struct’s field names or use [package spew](https://godoc.org/github.com/davecgh/go-spew/spew) that prints some additional information about the struct.

```go
func main() {
  //id = CL for Champions League Standings
	fmt.Printf("%+v", GetStandings("CL"))
  //Or using package spew 
	spew.Dump(GetStandings("CL"))
}
```

