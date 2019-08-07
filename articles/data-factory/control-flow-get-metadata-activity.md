---
title: Získat aktivitu metadat v Azure Data Factory | Microsoft Docs
description: Přečtěte si, jak můžete použít aktivitu GetMetadata v kanálu Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: b819a990b9f607aaf70bf2e16a5857de3f7306cc
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827494"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Získat aktivitu metadat v Azure Data Factory

Aktivita GetMetadata lze použít k načtení **metadat** všech dat v Azure Data Factory. Tato aktivita se dá použít v těchto scénářích:

- Ověření informací o metadatech pro libovolná data
- Aktivace kanálu, když jsou data připravena/k dispozici

V toku ovládacích prvků jsou k dispozici následující funkce:

- Výstup z aktivity GetMetadata lze použít v podmíněných výrazech k provedení ověření.
- Kanál se dá aktivovat, když je podmínka splněná přes do smyčky.

## <a name="supported-capabilities"></a>Podporované funkce

Aktivita GetMetadata přebírá datovou sadu jako požadovaný vstup a vypisuje informace o metadatech, které jsou k dispozici jako výstup aktivity. V současné době jsou podporovány následující konektory s odpovídajícími metadaty k dispozici a maximální podporovaná velikost metadat je **1 MB**.

>[!NOTE]
>Pokud spustíte aktivitu GetMetadata na Integration Runtime v místním prostředí, je nejnovější funkce podporovaná ve verzi 3,6 nebo vyšší. 

### <a name="supported-connectors"></a>Podporované konektory

**Úložiště souborů:**

| Konektory/metadata | itemName<br>(soubor/složka) | itemType<br>(soubor/složka) | size<br>souborů | vytvořené<br>(soubor/složka) | lastModified<br>(soubor/složka) |childItems<br>složky |contentMD5<br>souborů | structure<br/>souborů | Sloupců<br>souborů | neexistuje<br>(soubor/složka) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Úložiště Google Cloud](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Azure Blob](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure File Storage](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [Systém souborů](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |

- Pro Amazon S3 a Google Cloud Storage `lastModified` se vztahuje na kontejner a klíč, ale ne na virtuální složku; a na `exists` kontejner a klíč, ale ne na předponu nebo virtuální složku.
- U objektů blob Azure se `lastModified` vztahuje na kontejner a objekty blob, ale ne na virtuální složku.

**Relační databáze:**

| Konektory/metadata | structure | Sloupců | neexistuje |
|:--- |:--- |:--- |:--- |
| [Azure SQL Database](connector-azure-sql-database.md) | √ | √ | √ |
| [Spravovaná Instance Azure SQL Database](connector-azure-sql-database-managed-instance.md) | √ | √ | √ |
| [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>Možnosti metadat

Následující typy metadat lze zadat v seznamu polí aktivita GetMetadata pro načtení:

| Typ metadat | Popis |
|:--- |:--- |
| itemName | Název souboru nebo složky |
| itemType | Typ souboru nebo složky Výstupní hodnota je `File` nebo `Folder`. |
| size | Velikost souboru v bajtech Platí jenom pro soubor. |
| vytvořené | Datum a čas vytvoření souboru nebo složky. |
| lastModified | Datum a čas poslední změny souboru nebo složky |
| childItems | Seznam podsložek a souborů v dané složce Platí jenom pro složku. Výstupní hodnota je seznam názvů a typů jednotlivých podřízených položek. |
| contentMD5 | Algoritmus MD5 souboru Platí jenom pro soubor. |
| structure | Datová struktura v souboru nebo relační tabulce databáze. Výstupní hodnota je seznam názvů sloupců a typu sloupce. |
| Sloupců | Počet sloupců uvnitř souboru nebo relační tabulky |
| neexistuje| Zda existuje soubor nebo složka nebo tabulka. Poznámka: Pokud je v seznamu polí GetaMetadata zadaná možnost EXISTS, aktivita se nezdařila, ani když položka (soubor/složka/tabulka) neexistuje. místo toho se vrátí `exists: false` ve výstupu. |

>[!TIP]
>Pokud chcete ověřit, zda existuje soubor nebo složka nebo tabulka, zadejte `exists` v seznamu pole aktivita GetMetadata a pak můžete `exists: true/false` výsledek zkontrolovat z výstupu aktivity. Pokud `exists` není v seznamu polí nakonfigurovaná, aktivita GetMetadata selže, když se objekt nenajde.

>[!NOTE]
>Když načtete metadata z úložišť souborů a nakonfigurujete `modifiedDatetimeStart` a/nebo `childItems` `modifiedDatetimeEnd`, vrátí ve výstupu pouze soubory v dané cestě s časem poslední změny mezi rozsahem, ale žádné podsložky.

## <a name="syntax"></a>Syntaxe

**Aktivita GetMetadata:**

```json
{
    "name": "MyActivity",
    "type": "GetMetadata",
    "typeProperties": {
        "fieldList" : ["size", "lastModified", "structure"],
        "dataset": {
            "referenceName": "MyDataset",
            "type": "DatasetReference"
        }
    }
}
```

**Integrován**

```json
{
    "name": "MyDataset",
    "properties": {
    "type": "AzureBlob",
        "linkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath":"container/folder",
            "filename": "file.json",
            "format":{
                "type":"JsonFormat"
            }
        }
    }
}
```

## <a name="type-properties"></a>Vlastnosti typu

V současné době by aktivita GetMetadata mohla načíst následující typy informací o metadatech.

Vlastnost | Popis | Požadováno
-------- | ----------- | --------
fieldList | Zobrazuje seznam typů požadovaných informací. Další informace najdete v části [Možnosti metadat](#metadata-options) na podporovaných metadatech. | Ano 
integrován | Referenční datová sada, jejíž aktivitu metadat má být načtena aktivitou GetMetadata. Další informace najdete v části [podporované možnosti](#supported-capabilities) na podporovaných konektorech a v tématu konektoru informace o syntaxi datové sady. | Ano
formatSettings | Použijte při použití datové sady typu Format (Parquet, DelimitedText). | Ne
storeSettings | Použijte při použití datové sady typu Format (Parquet, DelimitedText). | Ne

## <a name="sample-output"></a>Ukázkový výstup

Výsledek GetMetadata se zobrazí ve výstupu aktivity. Níže jsou uvedeny dva vzorky s možnostmi vyčerpávající metadata vybranými v seznamu polí jako reference. Chcete-li použít výsledek v následné aktivitě, použijte vzor `@{activity('MyGetMetadataActivity').output.itemName}`.

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

## <a name="next-steps"></a>Další postup
Podívejte se na další aktivity toku řízení podporované Data Factory: 

- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Pro každou aktivitu](control-flow-for-each-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita webu](control-flow-web-activity.md)
