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
ms.date: 03/02/2020
ms.author: jingwang
ms.openlocfilehash: a0c07aaf27825254f776a03b9b9ca2cbeddca02d
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250270"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Získat aktivitu metadat v Azure Data Factory

Můžete použít aktivitu získat metadata k načtení metadat všech dat v Azure Data Factory. Tuto aktivitu můžete použít v následujících scénářích:

- Ověřte metadata jakýchkoli dat.
- Aktivovat kanál, když jsou data připravena/k dispozici.

V toku ovládacích prvků jsou k dispozici následující funkce:

- K provedení ověřování můžete použít výstup z aktivity získat metadata v podmíněných výrazech.
- Kanál můžete aktivovat, pokud je splněna podmínka prostřednictvím klauzule do smyčky.

## <a name="capabilities"></a>Možnosti

Aktivita získat metadata přebírá datovou sadu jako vstup a vrací informace o metadatech jako výstup. V současné době jsou podporovány následující konektory a odpovídající metadata k dispozici. Maximální velikost vrácených metadat je 2 MB.

>[!NOTE]
>Pokud spustíte aktivitu získat metadata v místním prostředí Integration runtime, jsou nejnovější funkce podporované ve verzi 3,6 nebo novější.

### <a name="supported-connectors"></a>Podporované konektory

**Úložiště souborů**

| Konektory/metadata | itemName<br>(soubor/složka) | itemType<br>(soubor/složka) | size<br>souborů | vytvářejí<br>(soubor/složka) | lastModified<br>(soubor/složka) |childItems<br>složky |contentMD5<br>souborů | structure<br/>souborů | Sloupců<br>souborů | neexistuje<br>(soubor/složka) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Úložiště Google Cloud](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Azure Blob Storage](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Files](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [Systém souborů](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | x/x | √ | x | √ | √ | √/√ |

- Pro Amazon S3 a Google Cloud Storage se `lastModified` vztahuje na kontejner a klíč, ale ne na virtuální složku, a `exists` se vztahuje na kontejner a klíč, ale ne na předponu nebo virtuální složku.
- Pro úložiště objektů BLOB v Azure se `lastModified` vztahuje na kontejner a objekt blob, ale ne na virtuální složku.
- filtr `lastModified` aktuálně platí pro filtrování podřízených položek, ale ne pro určenou složku nebo soubor.
- Pro aktivitu získat metadata není podporován filtr zástupných znaků u složek nebo souborů.

**Relační databáze**

| Konektory/metadata | structure | Sloupců | neexistuje |
|:--- |:--- |:--- |:--- |
| [Azure SQL Database](connector-azure-sql-database.md) | √ | √ | √ |
| [Spravovaná instance Azure SQL Database](connector-azure-sql-database-managed-instance.md) | √ | √ | √ |
| [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>Možnosti metadat

Můžete zadat následující typy metadat v seznamu pole získat aktivitu metadat pro načtení odpovídajících informací:

| Typ metadat | Popis |
|:--- |:--- |
| itemName | Název souboru nebo složky |
| itemType | Typ souboru nebo složky Vrácená hodnota je `File` nebo `Folder`. |
| size | Velikost souboru (v bajtech) Platí pouze pro soubory. |
| vytvářejí | Datum a čas vytvoření souboru nebo složky. |
| lastModified | Datum a čas poslední změny souboru nebo složky |
| childItems | Seznam podsložek a souborů v dané složce. Platí pouze pro složky. Vrácená hodnota je seznam názvů a typů jednotlivých podřízených položek. |
| contentMD5 | Algoritmus MD5 souboru Platí pouze pro soubory. |
| structure | Datová struktura tabulky souborů nebo relačních databází. Vrácená hodnota je seznam názvů sloupců a typů sloupců. |
| Sloupců | Počet sloupců v souboru nebo relační tabulce |
| neexistuje| Zda existuje soubor, složka nebo tabulka. Všimněte si, že pokud je `exists` zadán v seznamu pole získat metadata, aktivita nebude úspěšná, i když soubor, složka nebo tabulka neexistují. Místo toho se ve výstupu vrátí `exists: false`. |

>[!TIP]
>Chcete-li ověřit, zda existuje soubor, složka nebo tabulka, zadejte `exists` v seznamu polí získat metadata pro aktivitu. Pak můžete zaškrtnout `exists: true/false` výsledek ve výstupu aktivity. Pokud není v seznamu polí zadána možnost `exists`, aktivita získat metadata selže, pokud objekt nebude nalezen.

>[!NOTE]
>Když načtete metadata z úložišť souborů a nakonfigurujete `modifiedDatetimeStart` nebo `modifiedDatetimeEnd`, bude `childItems` ve výstupu obsahovat jenom soubory v dané cestě, které mají čas poslední změny v zadaném rozsahu. V nezahrnuje položky v podsložkách.

## <a name="syntax"></a>Syntaxe

**Aktivita získání metadat**

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

V současné době aktivita získat metadata může vracet následující typy informací o metadatech:

Vlastnost | Popis | Požaduje se
-------- | ----------- | --------
fieldList | Typy informací o metadatech, které jsou požadovány. Podrobnosti o podporovaných metadatech najdete v části [Možnosti metadat](#metadata-options) v tomto článku. | Ano 
datová sada | Referenční datová sada, jejíž metadata mají být načtena aktivitou získání metadat. Informace o podporovaných konektorech najdete v části [Možnosti](#capabilities) . Podrobnosti o syntaxi datové sady najdete v tématech konkrétního konektoru. | Ano
formatSettings | Použijte při použití typu DataSet typu Format. | Ne
storeSettings | Použijte při použití typu DataSet typu Format. | Ne

## <a name="sample-output"></a>Ukázkový výstup

Výsledky získání metadat se zobrazí ve výstupu aktivity. Následují dvě ukázky, které zobrazují rozsáhlé možnosti metadat. Chcete-li použít výsledky v následující aktivitě, použijte tento vzor: `@{activity('MyGetMetadataActivity').output.itemName}`.

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
- [Aktivita Lookup](control-flow-lookup-activity.md)
- [Webová aktivita](control-flow-web-activity.md)
