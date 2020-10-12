---
title: Získat aktivitu metadat v Azure Data Factory
description: Naučte se, jak používat aktivitu získat metadata v kanálu Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: jingwang
ms.openlocfilehash: e32115c590d73f5c93f322d3bd542096f2964a4c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91297602"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Získat aktivitu metadat v Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Můžete použít aktivitu získat metadata k načtení metadat všech dat v Azure Data Factory. Tuto aktivitu můžete použít v následujících scénářích:

- Ověřte metadata jakýchkoli dat.
- Aktivovat kanál, když jsou data připravena/k dispozici.

V toku ovládacích prvků jsou k dispozici následující funkce:

- K provedení ověřování můžete použít výstup z aktivity získat metadata v podmíněných výrazech.
- Kanál můžete aktivovat, pokud je splněna podmínka prostřednictvím klauzule do smyčky.

## <a name="capabilities"></a>Možnosti

Aktivita získat metadata přebírá datovou sadu jako vstup a vrací informace o metadatech jako výstup. V současné době jsou podporovány následující konektory a odpovídající metadata k dispozici. Maximální velikost vrácených metadat je okolo 4 MB.

>[!NOTE]
>Pokud spustíte aktivitu získat metadata v místním prostředí Integration runtime, jsou nejnovější funkce podporované ve verzi 3,6 nebo novější.

### <a name="supported-connectors"></a>Podporované konektory

**File Storage**

| Konektory/metadata | itemName<br>(soubor/složka) | itemType<br>(soubor/složka) | size<br>souborů | vytvářejí<br>(soubor/složka) | lastModified<br>(soubor/složka) |childItems<br>složky |contentMD5<br>souborů | – struktura<br/>souborů | Sloupců<br>souborů | neexistuje<br>(soubor/složka) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Cloudové úložiště Googlu](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Azure Blob Storage](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | √ | √ | √ | √/√ |
| [Soubory Azure](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [Systém souborů](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | x/x | √ | x | √ | √ | √/√ |

- Když použijete aktivitu získat metadata na určitou složku, ujistěte se, že máte oprávnění k zobrazení seznamu/spuštění pro danou složku.
- Pro Amazon S3 a Google Cloud Storage se `lastModified` vztahuje na kontejner a klíč, ale ne na virtuální složku, a to na `exists` kontejner a klíč, ale ne na předponu nebo virtuální složku.
- Pro úložiště objektů BLOB v Azure `lastModified` platí pro kontejner a objekt blob, ale ne do virtuální složky.
- `lastModified` filtr aktuálně platí pro filtrování podřízených položek, ale ne do zadané složky nebo souboru.
- Pro aktivitu získat metadata není podporován filtr zástupných znaků u složek nebo souborů.
- `structure` a `columnCount` nejsou podporovány při získávání metadat z binárních, JSON nebo souborů XML.

**Relační databáze**

| Konektory/metadata | – struktura | Sloupců | neexistuje |
|:--- |:--- |:--- |:--- |
| [Azure SQL Database](connector-azure-sql-database.md) | √ | √ | √ |
| [Spravovaná instance Azure SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) | √ | √ | √ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>Možnosti metadat

Můžete zadat následující typy metadat v seznamu pole získat aktivitu metadat pro načtení odpovídajících informací:

| Typ metadat | Description |
|:--- |:--- |
| itemName | Název souboru nebo složky |
| itemType | Typ souboru nebo složky Vrácená hodnota je `File` nebo `Folder` . |
| size | Velikost souboru (v bajtech) Platí pouze pro soubory. |
| vytvářejí | Datum a čas vytvoření souboru nebo složky. |
| lastModified | Datum a čas poslední změny souboru nebo složky |
| childItems | Seznam podsložek a souborů v dané složce. Platí pouze pro složky. Vrácená hodnota je seznam názvů a typů jednotlivých podřízených položek. |
| contentMD5 | Algoritmus MD5 souboru Platí pouze pro soubory. |
| – struktura | Datová struktura tabulky souborů nebo relačních databází. Vrácená hodnota je seznam názvů sloupců a typů sloupců. |
| Sloupců | Počet sloupců v souboru nebo relační tabulce |
| neexistuje| Zda existuje soubor, složka nebo tabulka. Pokud `exists` je zadaný v seznamu pole získat metadata, aktivita se nezdařila ani v případě, že soubor, složka nebo tabulka neexistují. Místo toho `exists: false` se vrátí ve výstupu. |

>[!TIP]
>Pokud chcete ověřit, zda existuje soubor, složka nebo tabulka, zadejte `exists` v seznamu pole aktivity získat metadata. Pak můžete `exists: true/false` výsledek ověřit ve výstupu aktivity. Pokud `exists` není zadaný v seznamu polí, aktivita získat metadata se nezdaří, pokud se objekt nenajde.

>[!NOTE]
>Když načtete metadata z úložišť souborů a nakonfigurujete `modifiedDatetimeStart` nebo `modifiedDatetimeEnd` , bude `childItems` ve výstupu obsaženo pouze soubory v dané cestě s časem poslední změny v zadaném rozsahu. V nezahrnuje položky v podsložkách.

## <a name="syntax"></a>Syntax

**Aktivita získání metadat**

```json
{
    "name":"MyActivity",
    "type":"GetMetadata",
    "dependsOn":[

    ],
    "policy":{
        "timeout":"7.00:00:00",
        "retry":0,
        "retryIntervalInSeconds":30,
        "secureOutput":false,
        "secureInput":false
    },
    "userProperties":[

    ],
    "typeProperties":{
        "dataset":{
            "referenceName":"MyDataset",
            "type":"DatasetReference"
        },
        "fieldList":[
            "size",
            "lastModified",
            "structure"
        ],
        "storeSettings":{
            "type":"AzureBlobStorageReadSettings"
        },
        "formatSettings":{
            "type":"JsonReadSettings"
        }
    }
}
```

**Datová sada**

```json
{
    "name":"MyDataset",
    "properties":{
        "linkedServiceName":{
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[

        ],
        "type":"Json",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "fileName":"file.json",
                "folderPath":"folder",
                "container":"container"
            }
        }
    }
}
```

## <a name="type-properties"></a>Vlastnosti typu

V současné době aktivita získat metadata může vracet následující typy informací o metadatech:

Vlastnost | Popis | Povinné
-------- | ----------- | --------
fieldList | Typy informací o metadatech, které jsou požadovány. Podrobnosti o podporovaných metadatech najdete v části [Možnosti metadat](#metadata-options) v tomto článku. | Yes 
integrován | Referenční datová sada, jejíž metadata mají být načtena aktivitou získání metadat. Informace o podporovaných konektorech najdete v části [Možnosti](#capabilities) . Podrobnosti o syntaxi datové sady najdete v tématech konkrétního konektoru. | Yes
formatSettings | Použijte při použití typu DataSet typu Format. | No
storeSettings | Použijte při použití typu DataSet typu Format. | No

## <a name="sample-output"></a>Ukázkový výstup

Výsledky získání metadat se zobrazí ve výstupu aktivity. Následují dvě ukázky, které zobrazují rozsáhlé možnosti metadat. Chcete-li použít výsledky v následující aktivitě, použijte tento model: `@{activity('MyGetMetadataActivity').output.itemName}` .

### <a name="get-a-files-metadata"></a>Získání metadat souboru

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>Získat metadata složky

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>Další kroky
Seznamte se s dalšími aktivitami toku řízení, které podporuje Data Factory:

- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Aktivita ForEach](control-flow-for-each-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita webu](control-flow-web-activity.md)
