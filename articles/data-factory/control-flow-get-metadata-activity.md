---
title: Získat aktivitu metadat v Azure Data Factory
description: Naučte se, jak používat aktivitu získat metadata v kanálu Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/25/2021
ms.author: jingwang
ms.openlocfilehash: bd8fc3383d6d9a0afb7733cb94643623e6879d23
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102178537"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Získat aktivitu metadat v Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Můžete použít aktivitu získat metadata k načtení metadat všech dat v Azure Data Factory. Můžete použít výstup z aktivity získat metadata v podmíněných výrazech k provedení ověření nebo využít metadata v následných aktivitách.

## <a name="supported-capabilities"></a>Podporované funkce

Aktivita získat metadata přebírá datovou sadu jako vstup a vrací informace o metadatech jako výstup. V současné době jsou podporovány následující konektory a odpovídající metadata k dispozici. Maximální velikost vrácených metadat je **4 MB**.

### <a name="supported-connectors"></a>Podporované konektory

**File Storage**

| Konektory/metadata | itemName<br>(soubor/složka) | itemType<br>(soubor/složka) | size<br>souborů | vytvářejí<br>(soubor/složka) | lastModified<sup>1</sup><br>(soubor/složka) |childItems<br>složky |contentMD5<br>souborů | struktura<sup>2</sup><br/>souborů | počet sloupců<sup>2</sup><br>souborů | existuje<sup>3</sup><br>(soubor/složka) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Cloudové úložiště Googlu](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Blob Storage](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | √ | √ | √ | √/√ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | √ | √ | √ | √/√ |
| [Azure Files](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [Systém souborů](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | x/x | √ | x | √ | √ | √/√ |

<sup>1</sup> metadata `lastModified` :
- Pro Amazon S3 a Google Cloud Storage se `lastModified` vztahuje na kontejner a klíč, ale ne na virtuální složku, a to na `exists` kontejner a klíč, ale ne na předponu nebo virtuální složku. 
- Pro úložiště objektů BLOB v Azure `lastModified` platí pro kontejner a objekt blob, ale ne do virtuální složky.

<sup>2</sup> metadata `structure` a `columnCount` nejsou podporovaná při získávání metadat z binárních, JSON nebo souborů XML.

<sup>3</sup> metadata `exists` : pro Amazon S3 a Google Cloud Storage se `exists` vztahují na kontejner a klíč, ale ne na předponu nebo virtuální složku.

Všimněte si, že:

- Když použijete aktivitu získat metadata na určitou složku, ujistěte se, že máte oprávnění k zobrazení seznamu/spuštění pro danou složku.
- Pro aktivitu získat metadata není podporován filtr zástupných znaků u složek nebo souborů.
- `modifiedDatetimeStart` a `modifiedDatetimeEnd` filtr nastavený na konektoru:

    - Tyto dvě vlastnosti slouží k filtrování podřízených položek při získávání metadat ze složky. Neplatí při získávání metadat ze souboru.
    - Při použití takového filtru `childItems` obsahuje výstup ve výstupu jenom soubory, které se změnily v zadaném rozsahu, ale ne složky.
    - Pro použití takového filtru aktivita GetMetadata vyčíslí všechny soubory v zadané složce a zkontroluje změněný čas. Neodkazujte na složku s velkým počtem souborů i v případě, že očekávaný počet kvalifikovaných souborů je malý. 

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

> [!TIP]
> Pokud chcete ověřit, zda existuje soubor, složka nebo tabulka, zadejte `exists` v seznamu pole aktivity získat metadata. Pak můžete `exists: true/false` výsledek ověřit ve výstupu aktivity. Pokud `exists` není zadaný v seznamu polí, aktivita získat metadata se nezdaří, pokud se objekt nenajde.

> [!NOTE]
> Když získáte metadata z úložišť souborů a nakonfigurujete je `modifiedDatetimeStart` nebo `modifiedDatetimeEnd` , `childItems` obsahuje ve výstupu pouze soubory v zadané cestě, jejichž čas poslední změny se nachází v zadaném rozsahu. Položky v podsložkách nejsou zahrnuty.

> [!NOTE]
> Aby seznam polí **struktury** poskytoval skutečnou datovou strukturu pro datové sady s oddělovači textu a Excelu, musíte povolit `First Row as Header` vlastnost, která je podporovaná jenom pro tyto zdroje dat.

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

**Integrován**

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
integrován | Referenční datová sada, jejíž metadata mají být načtena aktivitou získání metadat. Informace o podporovaných konektorech najdete v části [Možnosti](#supported-capabilities) . Podrobnosti o syntaxi datové sady najdete v tématech konkrétního konektoru. | Yes
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
