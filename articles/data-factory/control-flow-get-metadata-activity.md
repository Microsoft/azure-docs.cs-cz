---
title: Získání aktivity metadat v Azure Data Factory
description: Přečtěte si, jak používat aktivitu Získat metadata v kanálu datové továrny.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250270"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Získání aktivity metadat v Azure Data Factory

Aktivitu Získat metadata můžete použít k načtení metadat všech dat v Azure Data Factory. Tuto aktivitu můžete použít v následujících scénářích:

- Ověřte metadata všech dat.
- Aktivuj kanál, když jsou data připravená/dostupná.

V toku řízení je k dispozici následující funkce:

- K ověření můžete použít výstup z aktivity získat metadata v podmíněných výrazech.
- Můžete spustit potrubí, pokud je splněna podmínka prostřednictvím opakování Do Do.

## <a name="capabilities"></a>Možnosti

Get Metadata aktivita trvá datovou sadu jako vstup a vrátí informace metadat jako výstup. V současné době jsou podporovány následující konektory a odpovídající navštívená metadata. Maximální velikost vrácených metadat je 2 MB.

>[!NOTE]
>Pokud spustíte aktivitu získat metadata na modul runtime integrace s vlastním hostitelem, nejnovější funkce jsou podporovány ve verzi 3.6 nebo novější.

### <a name="supported-connectors"></a>Podporované konektory

**File Storage**

| Konektor/metadata | název_položky<br>(soubor/složka) | Itemtype<br>(soubor/složka) | velikost<br>(soubor) | Vytvořen<br>(soubor/složka) | lastModified<br>(soubor/složka) |podřízené položky<br>(složka) |obsahMD5<br>(soubor) | – struktura<br/>(soubor) | Columncount<br>(soubor) | Existuje<br>(soubor/složka) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Cloudové úložiště Googlu](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Úložiště objektů blob Azure](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Soubory Azure](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [Systém souborů](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | x/x | √ | x | √ | √ | √/√ |

- Pro Amazon S3 a `lastModified` Google Cloud Storage, se vztahuje na kbelík a `exists` klíč, ale ne na virtuální složku, a platí pro kbelík a klíč, ale ne na předponu nebo virtuální složku.
- Pro úložiště objektů `lastModified` blob Azure platí pro kontejner a objekt blob, ale ne pro virtuální složku.
- `lastModified`filtr se aktuálně vztahuje na podřízené položky filtru, ale ne na samotnou zadanou složku nebo soubor.
- Filtr se zástupnými kódy ve složkách nebo souborech není pro aktivitu získání metadat podporován.

**Relační databáze**

| Konektor/metadata | – struktura | Columncount | Existuje |
|:--- |:--- |:--- |:--- |
| [Azure SQL Database](connector-azure-sql-database.md) | √ | √ | √ |
| [Spravovaná instance Azure SQL Database](connector-azure-sql-database-managed-instance.md) | √ | √ | √ |
| [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>Možnosti metadat

Chcete-li načíst odpovídající informace, můžete v seznamu polí Získat metadata zadat následující typy metadat:

| Typ metadat | Popis |
|:--- |:--- |
| název_položky | Název souboru nebo složky. |
| Itemtype | Typ souboru nebo složky. Vrácená `File` hodnota `Folder`je nebo . |
| velikost | Velikost souboru v bajtech. Platí pouze pro soubory. |
| Vytvořen | Byl vytvořen čas data souboru nebo složky. |
| lastModified | Datum poslední změny souboru nebo složky. |
| podřízené položky | Seznam podsložek a souborů v dané složce. Platí pouze pro složky. Vrácená hodnota je seznam názvu a typu každé podřízené položky. |
| obsahMD5 | MD5 souboru. Platí pouze pro soubory. |
| – struktura | Datová struktura tabulky souboru nebo relační databáze. Vrácená hodnota je seznam názvů sloupců a typů sloupců. |
| Columncount | Počet sloupců v souboru nebo relační tabulce. |
| Existuje| Zda existuje soubor, složka nebo tabulka. Všimněte `exists` si, že pokud je zadán v seznamu polí Získat metadata, aktivita se nezdaří, i když soubor, složka nebo tabulka neexistuje. Místo `exists: false` toho je vrácena ve výstupu. |

>[!TIP]
>Pokud chcete ověřit, zda existuje soubor, složka nebo `exists` tabulka, zadejte v seznamu polí Získat metadata. Potom můžete zkontrolovat `exists: true/false` výsledek ve výstupu aktivity. Pokud `exists` není zadán v seznamu polí, získat metadata aktivity se nezdaří, pokud objekt nebyl nalezen.

>[!NOTE]
>Když získáte metadata z úložišť `modifiedDatetimeEnd`souborů `childItems` a nakonfigurujete `modifiedDatetimeStart` nebo , výstup ve výstupu bude obsahovat pouze soubory v dané cestě, které mají naposledy upravený čas v zadaném rozsahu. V in nebude obsahovat položky v podsložkách.

## <a name="syntax"></a>Syntaxe

**Získat aktivitu metadat**

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

**Dataset**

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

V současné době získat metadata aktivity můžete vrátit následující typy informací metadat:

Vlastnost | Popis | Požaduje se
-------- | ----------- | --------
seznam polí | Typy požadovaných informací metadat. Podrobnosti o podporovaných metadatech naleznete v části [Možnosti metadat](#metadata-options) v tomto článku. | Ano 
Dataset | Referenční datová sada, jejíž metadata mají být načtena aktivitou Získat metadata. Informace o podporovaných konektorech naleznete v části [Možnosti.](#capabilities) Podrobnosti o syntaxi datové sady naleznete v konkrétních tématech konektoru. | Ano
formatSettings | Použít při použití datové sady typu formátu. | Ne
storeSettings | Použít při použití datové sady typu formátu. | Ne

## <a name="sample-output"></a>Ukázkový výstup

Výsledky získat metadata jsou zobrazeny ve výstupu aktivity. Následují dvě ukázky zobrazující rozsáhlé možnosti metadat. Chcete-li výsledky použít v následné aktivitě, použijte tento vzor: `@{activity('MyGetMetadataActivity').output.itemName}`.

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

### <a name="get-a-folders-metadata"></a>Získání metadat složky

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
Další informace o dalších aktivitách toku řízení podporovaných společností Data Factory:

- [Spustit aktivitu kanálu](control-flow-execute-pipeline-activity.md)
- [Aktivita ForEach](control-flow-for-each-activity.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)
- [Webová aktivita](control-flow-web-activity.md)
