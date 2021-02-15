---
title: Protokol relace v aktivitě kopírování
description: Přečtěte si, jak povolit protokol relace v aktivitě kopírování v Azure Data Factory.
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: yexu
ms.openlocfilehash: 7cb00d62556babbd8e43e2fac2faa815a63943ed
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385264"
---
#  <a name="session-log-in-copy-activity"></a>Protokol relace v aktivitě kopírování

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Zkopírované názvy souborů můžete protokolovat v aktivitě kopírování, což vám může pomáhat zajistit, aby data nebyla úspěšně zkopírována ze zdroje do cílového úložiště, ale také konzistentní mezi zdrojovým a cílovým úložištěm, a to kontrolou zkopírovaných souborů v protokolech relace aktivity kopírování.  

Když povolíte nastavení odolnosti proti chybám v aktivitě kopírování pro přeskočení vadných dat, můžou se zaprotokolovat i Vynechané soubory a vynechané řádky.  [V aktivitě kopírování](copy-activity-fault-tolerance.md)můžete získat další podrobnosti o odolnosti proti chybám. 

## <a name="configuration"></a>Konfigurace
Následující příklad poskytuje definici JSON pro povolení protokolu relace v aktivitě kopírování: 

```json
"typeProperties": {
    "source": {
        "type": "BinarySource",
        "storeSettings": {
            "type": "AzureDataLakeStoreReadSettings",
            "recursive": true
        },
        "formatSettings": {
            "type": "BinaryReadSettings"
        }
    },
    "sink": {
        "type": "BinarySink",
        "storeSettings": {
            "type": "AzureBlobFSWriteSettings"
        }
    },                    
    "skipErrorFile": {
        "fileForbidden": true,
        "dataInconsistency": true
    },
    "validateDataConsistency": true,
    "logSettings": {
        "enableCopyActivityLog": true,
        "copyActivityLogSettings": {
            "logLevel": "Warning",
            "enableReliableLogging": false
        },
        "logLocationSettings": {
            "linkedServiceName": {
               "referenceName": "ADLSGen2",
               "type": "LinkedServiceReference"
            },
            "path": "sessionlog/"
        }
    }
}
```

Vlastnost | Popis | Povolené hodnoty | Vyžadováno
-------- | ----------- | -------------- | -------- 
enableCopyActivityLog | Když nastavíte hodnotu true, budete mít možnost protokolovat zkopírované soubory, přeskočené soubory nebo vynechané řádky.  | Ano<br/>False (výchozí) | No
logLevel | V části informace se zaprotokolují všechny zkopírované soubory, Vynechané soubory a vynechané řádky. "Upozornění" bude protokolovat přeskočené soubory a pouze vynechané řádky.  | Informace<br/>Upozornění (výchozí) | No
enableReliableLogging | Pokud je hodnota true, aktivita kopírování v spolehlivém režimu vyprázdní protokoly ihned po zkopírování každého souboru do cílového umístění.  Pokud kopírujete velké množství souborů s povoleným spolehlivým režimem protokolování v aktivitě kopírování, měli byste očekávat, že by to ovlivnilo propustnost kopírování, protože pro každé kopírování souborů jsou vyžadovány dvojité operace zápisu. Jedna žádost se nachází do cílového úložiště a další požadavek se ukládá do úložiště úložiště protokolu.  Aktivita kopírování v režimu nejlepšího úsilí vyprázdní protokoly se dávkou záznamů v časovém intervalu, kde propustnost kopírování bude mnohem méně ovlivněna. Úplnost a časová osa protokolování není v tomto režimu zaručena, protože je k dispozici několik možností, že poslední dávka událostí protokolu nebyla po selhání aktivity kopírování vyprázdněna do souboru protokolu. V tuto chvíli se zobrazí několik souborů zkopírovaných do cíle, které se zkopírují do protokolu.  | Ano<br/>False (výchozí) | No
logLocationSettings | Skupina vlastností, které lze použít k určení umístění pro uložení protokolů relací. | | No
linkedServiceName | Propojená služba [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) nebo [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) ukládat soubory protokolu relace. | Názvy `AzureBlobStorage` `AzureBlobFS` propojených služeb typu nebo, které odkazují na instanci, kterou používáte k ukládání souborů protokolu. | No
program | Cesta souborů protokolu. | Zadejte cestu, do které chcete ukládat soubory protokolu. Pokud cestu nezadáte, služba vytvoří kontejner. | No


## <a name="monitoring"></a>Monitorování

### <a name="output-from-copy-activity"></a>Výstup aktivity kopírování
Po úplném spuštění aktivity kopírování uvidíte cestu souborů protokolu z výstupu každé spuštění aktivity kopírování. Soubory protokolu můžete najít z cesty: `https://[your-blob-account].blob.core.windows.net/[logFilePath]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` .  Soubory protokolu budou soubory CSV. 

```json
"output": {
            "dataRead": 695,
            "dataWritten": 186,
            "filesRead": 3,  
            "filesWritten": 1, 
            "filesSkipped": 2, 
            "throughput": 297,
            "logFilePath": "myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "dataConsistencyVerification": 
           { 
                "VerificationResult": "Verified", 
                "InconsistentData": "Skipped" 
           } 
        }

```

> [!NOTE]
> Pokud `enableCopyActivityLog` je vlastnost nastavena na hodnotu `Enabled` , názvy souborů protokolu jsou vygenerovány systémem.

### <a name="the-schema-of-the-log-file"></a>Schéma souboru protokolu

Následuje schéma souboru protokolu.

Sloupec | Popis 
-------- | -----------  
Timestamp | Časové razítko při čtení, zápisu nebo přeskočení objektu ADF.
Level | Úroveň protokolu této položky. Může to být upozornění nebo informace.
OperationName | Provozní chování aktivity kopírování ADF u každého objektu. Může to být Reada, Write, Skip nebo TabularRowSkip (jen pro čtení).
OperationItem | Názvy souborů nebo vynechané řádky.
Zpráva | Další informace, které ukazují, zda byl soubor načten ze zdrojového úložiště nebo zapsán do cílového úložiště. Může to také být důvod, proč se soubor nebo řádky přeskočí.

Následuje příklad souboru protokolu. 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-10-19 08:39:13.6688152,Info,FileRead,"sample1.csv","Start to read file: {""Path"":""sample1.csv"",""ItemType"":""File"",""Size"":104857620,""LastModified"":""2020-10-19T08:22:31Z"",""ETag"":""\""0x8D874081F80C01A\"""",""ContentMD5"":""dGKVP8BVIy6AoTtKnt+aYQ=="",""ObjectName"":null}"
2020-10-19 08:39:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
2020-10-19 08:40:13.6688152,Info,FileRead,"sample2.csv","Start to read file: {""Path"":""sample2.csv"",""ItemType"":""File"",""Size"":104857620,""LastModified"":""2020-10-19T08:22:31Z"",""ETag"":""\""0x8D874081F80C01A\"""",""ContentMD5"":""dGKVP8BVIy6AoTtKnt+aYQ=="",""ObjectName"":null}"
2020-10-19 08:40:13.9003981,Info,FileWrite,"sample2.csv","Start to write file from source file: sample2.csv."
2020-10-19 08:45:17.6508407,Info,FileRead,"sample2.csv","Complete reading file successfully. "
2020-10-19 08:45:28.7390083,Info,FileWrite,"sample2.csv","Complete writing file from source file: sample2.csv. File is successfully copied."
```
V souboru protokolu výše vidíte, že sample1.csv bylo přeskočeno, protože se nepodařilo ověřit, zda se nezdařila konzistence mezi zdrojovým a cílovým úložištěm. Můžete získat další podrobnosti o tom, proč se sample1.csv nekonzistence, protože se změnily jinými aplikacemi při kopírování aktivity kopírování ADF. Můžete také zobrazit sample2.csv bylo úspěšně zkopírováno ze zdrojového do cílového úložiště.

K další analýze souborů protokolu můžete použít více analytických modulů.  K analýze souboru protokolu pomocí příkazu jazyka SQL se používá několik příkladů, které naimportují soubor protokolu CSV do SQL Database, kde může být název tabulky SessionLogDemo.  

-   Uveďte seznam kopírovaných souborů. 
```sql
select OperationItem from SessionLogDemo where Message like '%File is successfully copied%'
```

-   Dát mi seznam souborů zkopírovaný v určitém časovém rozsahu. 
```sql
select OperationItem from SessionLogDemo where TIMESTAMP >= '<start time>' and TIMESTAMP <= '<end time>' and Message like '%File is successfully copied%'
```

-   Dejte mi konkrétní soubor s jeho zkopírovaným časem a metadaty. 
```sql
select * from SessionLogDemo where OperationItem='<file name>'
```

-   Dejte mi seznam souborů s jejich metadaty zkopírovanými v časovém rozsahu. 
```sql
select * from SessionLogDemo where OperationName='FileRead' and Message like 'Start to read%' and OperationItem in (select OperationItem from SessionLogDemo where TIMESTAMP >= '<start time>' and TIMESTAMP <= '<end time>' and Message like '%File is successfully copied%')
```

-   Pojmenujte seznam vynechaných souborů. 
```sql
select OperationItem from SessionLogDemo where OperationName='FileSkip'
```

-   Dejte mi důvod, proč určitý soubor přeskočil. 
```sql
select TIMESTAMP, OperationItem, Message from SessionLogDemo where OperationName='FileSkip'
```

-   Dát mi seznam souborů přeskočených z důvodu stejného důvodu: "soubor BLOB neexistuje". 
```sql
select TIMESTAMP, OperationItem, Message from SessionLogDemo where OperationName='FileSkip' and Message like '%UserErrorSourceBlobNotExist%'
```

-   Dát mi název souboru, který vyžaduje nejdelší čas ke zkopírování.
```sql
select top 1 OperationItem, CopyDuration=DATEDIFF(SECOND, min(TIMESTAMP), max(TIMESTAMP)) from SessionLogDemo group by OperationItem order by CopyDuration desc
```


## <a name="next-steps"></a>Další kroky
Další články o aktivitě kopírování najdete v článcích:

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Odolnost proti chybám aktivity kopírování](copy-activity-fault-tolerance.md)
- [Konzistence dat aktivity kopírování](copy-activity-data-consistency.md)
