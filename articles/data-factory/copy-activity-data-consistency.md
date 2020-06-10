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
ms.openlocfilehash: a45c8ce820532d11f18758924dc3399818cb9158
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84610215"
---
#  <a name="data-consistency-verification-in-copy-activity-preview"></a>Ověření konzistence dat v aktivitě kopírování (Preview)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Když přesunete data ze zdrojového do cílového úložiště, Azure Data Factory aktivita kopírování poskytuje možnost provést další ověření konzistence dat, aby se zajistilo, že data nebudou úspěšně zkopírována ze zdroje do cílového úložiště, ale také ověřena tak, aby byla konzistentní mezi zdrojovým a cílovým úložištěm. Po nalezení nekonzistentních dat během přesunu dat můžete buď přerušit aktivitu kopírování, nebo pokračovat ve kopírování zbývajících možností tak, že povolíte nastavení odolnosti proti chybám, abyste mohli přeskočit nekonzistentní data. Vynechané názvy objektů můžete získat povolením nastavení protokol relace v aktivitě kopírování. 

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview s následujícími omezeními, na kterých aktivně pracujeme:
>- Ověření konzistence dat je k dispozici pouze u binárních souborů kopírování mezi úložišti založenými na souborech s chováním ' PreserveHierarchy ' v aktivitě kopírování. Pro kopírování tabulkových dat není ověření konzistence dat v aktivitě kopírování k dispozici.
>- Když v aktivitě kopírování povolíte nastavení protokolu relace, aby se zaprotokoloval nekonzistentní soubory, jejichž platnost se přeskočila, nemůže být soubor protokolu 100% garantuje se, pokud se aktivita kopírování nezdařila.
>- Protokol relace obsahuje pouze nekonzistentní soubory, kde byly úspěšně zkopírovány soubory, které dosud nebyly zaznamenány.

## <a name="supported-data-stores"></a>Podporované zdroje dat

### <a name="source-data-stores"></a>Zdrojová úložiště dat

-   [Azure Blob Storage](connector-azure-blob-storage.md)
-   [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
-   [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
-   [Azure File Storage](connector-azure-file-storage.md)
-   [Amazon S3](connector-amazon-simple-storage-service.md)
-   [Systém souborů](connector-file-system.md)
-   [HDFS](connector-hdfs.md)

### <a name="destination-data-stores"></a>Cílová úložiště dat

-   [Azure Blob Storage](connector-azure-blob-storage.md)
-   [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
-   [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
-   [Azure File Storage](connector-azure-file-storage.md)
-   [Systém souborů](connector-file-system.md)


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
    "logStorageSettings": { 
        "linkedServiceName": { 
            "referenceName": "ADLSGen2_storage", 
            "type": "LinkedServiceReference" 
        }, 
        "path": "/sessionlog/" 
} 
} 
```

Vlastnost | Popis | Povolené hodnoty | Vyžadováno
-------- | ----------- | -------------- | -------- 
validateDataConsistency | Pokud pro tuto vlastnost nastavíte hodnotu true, aktivita kopírování bude kontrolovat velikost souboru, lastModifiedDate a kontrolní součet MD5 pro každý objekt zkopírovaný ze zdrojového do cílového úložiště, aby se zajistila konzistence dat mezi zdrojovým a cílovým úložištěm. Mějte na paměti, že pokud tuto možnost povolíte, bude to mít vliv na výkon kopírování.  | True<br/>False (výchozí) | No
dataInconsistency | Jedna z párů klíč-hodnota v kontejneru vlastností skipErrorFile k určení, jestli chcete přeskočit nekonzistentní data.<br/> -True: chcete zkopírovat zbývající data vynecháním nekonzistentních dat.<br/> -False: chcete přerušit aktivitu kopírování po nalezení nekonzistentních dat.<br/>Počítejte s tím, že tato vlastnost je platná, pouze pokud nastavíte validateDataConsistency jako true.  | True<br/>False (výchozí) | No
logStorageSettings | Skupina vlastností, které lze zadat pro povolení protokolu relace pro přeskočení objektů. | | No
linkedServiceName | Propojená služba [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) nebo [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) ukládat soubory protokolu relace. | Názvy `AzureBlobStorage` `AzureBlobFS` propojených služeb typu nebo, které odkazují na instanci, kterou používáte k ukládání souborů protokolu. | No
cesta | Cesta souborů protokolu. | Zadejte cestu, do které chcete ukládat soubory protokolu. Pokud cestu nezadáte, služba vytvoří kontejner. | No

>[!NOTE]
>- Konzistence dat není podporována ve scénáři pracovní kopírování. 
>- Při kopírování souborů z nebo do objektů BLOB nebo Azure Data Lake Storage Gen2 v Azure, ADF provede ověřování kontrolního součtu MD5 na úrovni bloku s využitím [rozhraní Azure Blob API](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions?view=azure-dotnet-legacy) a [Azure Data Lake Storage Gen2 API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update#request-headers). Pokud se ContentMD5 soubory v objektu blob Azure nebo Azure Data Lake Storage Gen2 jako zdroje dat, ADF provede ověření kontrolního součtu MD5 na úrovni souboru i po přečtení souborů. Po zkopírování souborů do objektu blob Azure nebo Azure Data Lake Storage Gen2 jako cíle dat zapíše ADF ContentMD5 do Azure Blob nebo Azure Data Lake Storage Gen2, které se můžou dál využívat pro ověřování konzistence dat v podřízených aplikacích.
>- ADF zajišťuje ověřování velikosti souboru při kopírování souborů mezi libovolnými úložišti úložiště.

## <a name="monitoring"></a>Sledování

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
            "logPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "dataConsistencyVerification": 
           { 
                "VerificationResult": "Verified", 
                "InconsistentData": "Skipped" 
           } 
        }

```
Podrobnosti o ověření konzistence dat najdete v tématu "dataConsistencyVerification Property".

Hodnota **VerificationResult**: 
-   **Ověřeno**: vaše zkopírovaná data byla ověřena pro zajištění konzistence mezi zdrojovým a cílovým úložištěm. 
-   **NotVerified**: vaše zkopírovaná data nebyla ověřena jako konzistentní, protože jste v aktivitě kopírování nepovolili validateDataConsistency. 
-   **Nepodporováno**: vaše zkopírovaná data nebyla ověřena jako konzistentní, protože ověření konzistence dat není pro tuto konkrétní dvojici kopírování podporováno. 

Hodnota **InconsistentData**: 
-   **Nalezeno**: aktivita kopírování ADF našla nekonzistentní data. 
-   **Přeskočeno**: aktivita kopírování ADF byla nalezena a byla vynechána nekonzistentní data. 
-   **Žádné**: aktivita kopírování ADF nenašla žádná nekonzistentní data. Může to být buď proto, že vaše data byla ověřena tak, aby byla konzistentní mezi zdrojovým a cílovým úložištěm, nebo protože jste v aktivitě kopírování validateDataConsistency. 

### <a name="session-log-from-copy-activity"></a>Protokol relace z aktivity kopírování

Pokud nakonfigurujete, aby protokoloval nekonzistentní soubor, můžete najít soubor protokolu z této cesty: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` .  Soubory protokolu budou soubory CSV. 

Schéma souboru protokolu je následující:

Sloupec | Popis 
-------- | -----------  
Časové razítko | Časové razítko, když ADF přeskočí soubory, které nejsou konzistentní
Úroveň | Úroveň protokolu této položky. U položky, která zobrazuje přeskočení souboru, bude na úrovni upozornění.
OperationName | Provozní chování aktivity kopírování ADF u každého souboru Pokud chcete určit soubor, který se má přeskočit, bude přeskočena.
OperationItem | Název souboru, který se má přeskočit.
Zpráva | Další informace, které znázorňují, proč se soubory přeskočily.

Příklad souboru protokolu je následující: 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
```
V souboru protokolu výše vidíte, že sample1. CSV se přeskočil, protože se nepovedlo ověřit, jestli se nezdařila konzistence mezi zdrojovým a cílovým úložištěm. Můžete získat další podrobnosti o tom, proč se sample1. csv neshoduje, protože se změnily jinými aplikacemi při kopírování aktivity kopírování ADF. 



## <a name="next-steps"></a>Další kroky
Další články o aktivitě kopírování najdete v článcích:

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Odolnost proti chybám aktivity kopírování](copy-activity-fault-tolerance.md)


