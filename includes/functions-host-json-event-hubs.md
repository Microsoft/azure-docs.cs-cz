```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------| 
|maxBatchSize|64|Maximální počet přijatých událostí za smyčka příjmu.|
|prefetchCount|neuvedeno|Výchozí PrefetchCount, který se použije základní třídy EventProcessorHost.| 
|batchCheckpointFrequency|1|Počet událostí zpracovávaných dávek před vytvořením kontrolního bodu EventHub kurzoru.| 
