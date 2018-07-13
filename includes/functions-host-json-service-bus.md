```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------| 
|maxConcurrentCalls|16|Maximální počet souběžných volání zpětného volání, které by mělo zahájit pumpu zpráv. Ve výchozím nastavení modul runtime služby Functions zpracovávat více zpráv souběžně. Chcete-li řídit modul runtime najednou zpracovat pouze jedné frontě nebo tématu zprávy, nastavte `maxConcurrentCalls` na hodnotu 1. | 
|prefetchCount|neuvedeno|Výchozí PrefetchCount, který se použije základní MessageReceiver.| 
|autoRenewTimeout|00:05:00|Maximální doba, ve kterém se automatické obnovení zámku zprávy.| 
