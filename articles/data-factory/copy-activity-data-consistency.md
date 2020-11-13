---
title: Ověření konzistence dat v aktivitě kopírování
description: Přečtěte si, jak povolit ověřování konzistence dat v aktivitě kopírování v Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: ''
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 3/27/2020
ms.author: yexu
ms.openlocfilehash: e7c66518cd62ef1debd8ceb1c38ba93101c8395d
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565649"
---
#  <a name="data-consistency-verification-in-copy-activity"></a>Ověření konzistence dat v aktivitě kopírování

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Když přesunete data ze zdrojového do cílového úložiště, Azure Data Factory aktivita kopírování poskytuje možnost provést další ověření konzistence dat, aby se zajistilo, že data nebudou úspěšně zkopírována ze zdroje do cílového úložiště, ale také ověřena tak, aby byla konzistentní mezi zdrojovým a cílovým úložištěm. Po nalezení nekonzistentních souborů během přesunu dat můžete buď přerušit aktivitu kopírování, nebo pokračovat ve kopírování zbývajících souborů tak, že povolíte nastavení odolnosti proti chybám, aby se přeskočily nekonzistentní soubory. Vynechané názvy souborů můžete získat povolením nastavení protokol relace v aktivitě kopírování. Další podrobnosti najdete v tématu věnovaném [protokolu relace v aktivitě kopírování](copy-activity-log.md) .

## <a name="supported-data-stores-and-scenarios"></a>Podporovaná úložiště dat a scénáře

-   Ověřování konzistence dat je podporováno všemi konektory kromě FTP, sFTP a HTTP. 
-   Ověření konzistence dat není podporováno ve scénáři pracovní kopírování.
-   Při kopírování binárních souborů je ověření konzistence dat dostupné pouze v případě, že je chování PreserveHierarchy nastaveno v aktivitě kopírování.
-   Při kopírování více binárních souborů v rámci jedné aktivity kopírování s povoleným ověřením konzistence dat máte možnost přerušit aktivitu kopírování nebo pokračovat ve kopírování zbývajících souborů nastavením odolnosti proti chybám, aby bylo možné přeskočit nekonzistentní soubory. 
-   Když kopírujete tabulku v aktivitě kopírování s povoleným ověřením konzistence dat, aktivita kopírování se nezdařila, pokud se počet řádků načte ze zdroje liší od počtu zkopírovaných řádků do cílového umístění a počtu nekompatibilních řádků, které byly přeskočeny.


## <a name="configuration"></a>Konfigurace
Následující příklad poskytuje definici JSON pro povolení ověření konzistence dat v aktivitě kopírování: 

```json
"typeProperties": { 
"source": { 
        "type": "BinarySource", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreReadSettings", 
            "recursive": true 
        } 
    }, 
    "sink": { 
        "type": "BinarySink", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreWriteSettings" 
        } 
}, 
    "validateDataConsistency": true, 
    "skipErrorFile": { 
        "dataInconsistency": true 
    }, 
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
            }
        }
    }
} 
```

Vlastnost | Popis | Povolené hodnoty | Vyžadováno
-------- | ----------- | -------------- | -------- 
validateDataConsistency | Pokud pro tuto vlastnost nastavíte hodnotu true, při kopírování binárních souborů bude aktivita kopírování kontrolovat velikost souboru, lastModifiedDate a kontrolní součet MD5 pro každý binární soubor zkopírovaný ze zdrojového do cílového úložiště, aby se zajistila konzistence dat mezi zdrojovým a cílovým úložištěm. Při kopírování tabulkových dat zkontroluje aktivita kopírování celkový počet řádků po dokončení úlohy, aby se zajistilo, že celkový počet řádků načtených ze zdroje je stejný jako počet řádků zkopírovaných ze zdroje a počet nekompatibilních řádků, které se přeskočily. Mějte na paměti, že pokud tuto možnost povolíte, bude to mít vliv na výkon kopírování.  | Ano<br/>False (výchozí) | Ne
dataInconsistency | Jedna z párů klíč-hodnota v kontejneru vlastností skipErrorFile k určení, jestli chcete přeskočit nekonzistentní soubory. <br/> -True: chcete zkopírovat zbytek vynecháním nekonzistentních souborů.<br/> -False: chcete přerušit aktivitu kopírování po nalezení nekonzistentního souboru.<br/>Počítejte s tím, že tato vlastnost je platná, pouze pokud kopírujete binární soubory a nastavíte validateDataConsistency na hodnotu true.  | Ano<br/>False (výchozí) | Ne
logSettings | Skupina vlastností, které lze zadat pro povolení protokolu relace pro přeskočení souborů. | | Ne
linkedServiceName | Propojená služba [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) nebo [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) ukládat soubory protokolu relace. | Názvy `AzureBlobStorage` `AzureBlobFS` propojených služeb typu nebo, které odkazují na instanci, kterou používáte k ukládání souborů protokolu. | Ne
program | Cesta souborů protokolu. | Zadejte cestu, do které chcete ukládat soubory protokolu. Pokud cestu nezadáte, služba vytvoří kontejner. | Ne

>[!NOTE]
>- Při kopírování binárních souborů z nebo do objektů BLOB nebo Azure Data Lake Storage Gen2 v Azure využívá ADF k ověřování kontrolního součtu MD5 na úrovni bloku využití [rozhraní Azure Blob API](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions?view=azure-dotnet-legacy) a [Azure Data Lake Storage Gen2 API](/rest/api/storageservices/datalakestoragegen2/path/update#request-headers). Pokud se ContentMD5 soubory v objektu blob Azure nebo Azure Data Lake Storage Gen2 jako zdroje dat, ADF provede ověření kontrolního součtu MD5 na úrovni souboru i po přečtení souborů. Po zkopírování souborů do objektu blob Azure nebo Azure Data Lake Storage Gen2 jako cíle dat zapíše ADF ContentMD5 do Azure Blob nebo Azure Data Lake Storage Gen2, které se můžou dál využívat pro ověřování konzistence dat v podřízených aplikacích.
>- ADF zajišťuje ověřování velikosti souboru při kopírování binárních souborů mezi všemi úložišti úložiště.

## <a name="monitoring"></a>Monitorování

### <a name="output-from-copy-activity"></a>Výstup aktivity kopírování
Po úplném spuštění aktivity kopírování se můžete podívat na výsledek ověření konzistence dat z výstupu každého spuštění aktivity kopírování:

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
Podrobnosti o ověření konzistence dat najdete v tématu "dataConsistencyVerification Property".

Hodnota **VerificationResult** : 
-   **Ověřeno** : vaše zkopírovaná data byla ověřena pro zajištění konzistence mezi zdrojovým a cílovým úložištěm. 
-   **NotVerified** : vaše zkopírovaná data nebyla ověřena jako konzistentní, protože jste v aktivitě kopírování nepovolili validateDataConsistency. 
-   **Nepodporováno** : vaše zkopírovaná data nebyla ověřena jako konzistentní, protože ověření konzistence dat není pro tuto konkrétní dvojici kopírování podporováno. 

Hodnota **InconsistentData** : 
-   **Nalezeno** : aktivita kopírování ADF našla nekonzistentní data. 
-   **Přeskočeno** : aktivita kopírování ADF byla nalezena a byla vynechána nekonzistentní data. 
-   **Žádné** : aktivita kopírování ADF nenašla žádná nekonzistentní data. Může to být buď proto, že vaše data byla ověřena tak, aby byla konzistentní mezi zdrojovým a cílovým úložištěm, nebo protože jste v aktivitě kopírování validateDataConsistency. 

### <a name="session-log-from-copy-activity"></a>Protokol relace z aktivity kopírování

Pokud nakonfigurujete, aby protokoloval nekonzistentní soubor, můžete najít soubor protokolu z této cesty: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` .  Soubory protokolu budou soubory CSV. 

Schéma souboru protokolu je následující:

Sloupec | Popis 
-------- | -----------  
Timestamp | Časové razítko, když ADF přeskočí soubory, které nejsou konzistentní
Úroveň | Úroveň protokolu této položky. U položky, která zobrazuje přeskočení souboru, bude na úrovni upozornění.
OperationName | Provozní chování aktivity kopírování ADF u každého souboru Pokud chcete určit soubor, který se má přeskočit, bude přeskočena.
OperationItem | Název souboru, který se má přeskočit.
Zpráva | Další informace, které znázorňují, proč se soubory přeskočily.

Příklad souboru protokolu je následující: 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
```
V souboru protokolu výše vidíte, že sample1.csv bylo přeskočeno, protože se nepodařilo ověřit, zda se nezdařila konzistence mezi zdrojovým a cílovým úložištěm. Můžete získat další podrobnosti o tom, proč se sample1.csv nekonzistence, protože se změnily jinými aplikacemi při kopírování aktivity kopírování ADF. 



## <a name="next-steps"></a>Další kroky
Další články o aktivitě kopírování najdete v článcích:

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Odolnost proti chybám aktivity kopírování](copy-activity-fault-tolerance.md)