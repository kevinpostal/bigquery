# bigquery:

Higher level Go wrapper for the google Big Query API 

Wraps the core big query google API exposing a simple client interface

# Usage

    // basic use
    bqClient := client.New(PEM_PATH, SERVICE_ACCOUNT_EMAIL, SERVICE_ACCOUNT_CLIENT_ID, SECRET)

    // run a sync query
    query := "select * from publicdata:samples.shakespeare limit 100;"

    rows, headers, err := bqClient.Query("shakespeare", DATASET, query)
    if err != nil {
      fmt.Println("Error: ", err)
    } else {
      fmt.Println("Got rows: ", len(rows))
      fmt.Println("Headers: ", headers)
      fmt.Println("Rows: ", rows)
    }

    // =================================================================
    query := "select * from publicdata:samples.shakespeare limit 100;"

    bqClient := client.New(PEM_PATH, SERVICE_ACCOUNT_EMAIL, SERVICE_ACCOUNT_CLIENT_ID, SECRET)

    // run a sync query      
    query := "select * from publicdata:samples.shakespeare limit 500;"

    dataChan := make(chan client.Data)
    go bqClient.AsyncQuery(100, DATASET, PROJECTID, query, dataChan)

    L:
        for {
          select {
          case d, ok := <-dataChan:
              if d.Err != nil {
                  fmt.Println("Error with data: ", d.Err)
                  break L
              }

              if d.Rows != nil && d.Headers != nil {
                  fmt.Println("Got rows: ", len(d.Rows))
                  fmt.Println("Headers: ", d.Headers)
              }

              if !ok {
                  fmt.Println("Data channel closed")
                  break L
              }
          }
        }        

    