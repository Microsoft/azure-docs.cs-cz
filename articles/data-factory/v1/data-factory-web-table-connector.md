---
title: Přesun dat z tabulky webové pomocí Azure Data Factory | Microsoft Docs
description: Další informace o tom, jak přesunout data z tabulky na webové stránce pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: f54a26a4-baa4-4255-9791-5a8f935898e2
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: eb26e7a6ae4a0756ec9d20d7a3d9f9b37e8ab779
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622895"
---
# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>Přesun dat z webové zdroje tabulky pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](data-factory-web-table-connector.md)
> * [Verze 2 – Preview](../connector-web-table.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [konektor webových tabulky v V2](../connector-web-table.md).

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory pro přesun dat z tabulky na webové stránce do úložiště dat podporovaných jímky. Tento článek vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který uvádí obecný přehled přesun dat s aktivitou kopírování a seznam úložiště dat, které jsou podporované jako zdroje nebo jímky.

Objekt pro vytváření dat aktuálně podporuje pouze přesunutí dat z tabulky webové k jiným úložištím dat, ale není přesouvání dat od ostatních dat ukládá do cílového umístění v tabulce Web.

> [!IMPORTANT]
> Tento konektor webové aktuálně podporuje pouze extrakce obsahu tabulky z stránku HTML. Pokud chcete načíst data z koncového bodu protokolu HTTP/s, použijte [HTTP konektor](data-factory-http-connector.md) místo.

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít tento konektor webové tabulky, budete muset nastavit Self-hosted integrace modulu Runtime (neboli Data Management Gateway) a nakonfigurovat `gatewayName` vlastnost jímky propojené služby. Například pokud chcete kopírovat z webové tabulky do úložiště objektů Azure Blob, nakonfigurujte propojenou službu úložiště Azure jako následující:

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>",
      "gatewayName": "<gateway name>"
    }
  }
}
```

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z úložiště místní Cassandra data pomocí různých nástrojů nebo rozhraní API. 

- Nejjednodušší způsob, jak vytvořit kanál je použití **Průvodce kopírováním**. V tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) podrobný rychlé vytvoření kanálu pomocí Průvodce kopírováním data. 
- Tyto nástroje můžete také použít k vytvoření kanálu: **portál Azure**, **Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru**, **.NET API**, a **REST API**. V tématu [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování. 

Jestli používáte nástroje nebo rozhraní API, je třeba provést následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat podřízený:

1. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do data factory.
2. Vytvoření **datové sady** představují vstupní a výstupní data pro kopírování. 
3. Vytvoření **kanálu** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. 

Když použijete průvodce, jsou automaticky vytvoří definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál). Při použití nástroje nebo rozhraní API (s výjimkou .NET API), definujete tyto entity služby Data Factory pomocí formátu JSON.  Příklad s definicemi JSON entit služby Data Factory, které se používají ke zkopírování dat z tabulky web, naleznete v tématu [JSON příklad: kopírování dat z tabulky webové do objektu Blob Azure](#json-example-copy-data-from-web-table-to-azure-blob) tohoto článku. 

Následující části obsahují podrobnosti o vlastnostech formátu JSON, které slouží k určení entit služby Data Factory konkrétní do tabulky Web:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro webové propojené služby.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost typu musí být nastavena na: **Web** |Ano |
| URL |Adresa URL pro webové zdroje |Ano |
| authenticationType. |Anonymní. |Ano |

### <a name="using-anonymous-authentication"></a>Pomocí anonymní ověřování

```json
{
    "name": "web",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny typy datovou sadu (Azure SQL Azure blob, tabulky Azure, atd.).

**Rámci typeProperties** oddílu se liší pro jednotlivé typy datovou sadu a informace o umístění dat v úložišti dat. Rámci typeProperties část datové sady typ **WebTable** má následující vlastnosti

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type |Typ datové sady. musí být nastavena na **WebTable** |Ano |
| path |Relativní adresa URL prostředek, který obsahuje tabulku. |Ne. Pokud cesta není zadána, je použít jenom adresu URL, zadaný v definici propojené služby. |
| index |Index tabulky v prostředku. V tématu [Get index tabulky v stránku HTML](#get-index-of-a-table-in-an-html-page) části Postup získání index tabulky v stránku HTML. |Ano |

**Příklad:**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, například název, popis, vstupní a výstupní tabulky a zásad jsou dostupné pro všechny typy aktivit.

Vzhledem k tomu, vlastnosti dostupné v rámci typeProperties části aktivity se liší podle každý typ aktivity. Pro aktivitu kopírování budou lišit v závislosti na typech zdrojů a jímky.

V současné době po zdroji v aktivitě kopírování typu **WebSource**, jsou podporovány žádné další vlastnosti.


## <a name="json-example-copy-data-from-web-table-to-azure-blob"></a>Příklad JSON: kopírování dat z tabulky webové do objektu Blob Azure
Následující příklad ukazuje:

1. Propojené služby typu [webové](#linked-service-properties).
2. Propojené služby typu [azurestorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstup [datovou sadu](data-factory-create-datasets.md) typu [WebTable](#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [WebSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data z tabulky webové do objektu blob Azure každou hodinu. Vlastnostech JSON použitých ve tyto ukázky jsou popsané v části následující ukázky.

Následující příklad ukazuje, jak zkopírovat data z tabulky webové do objektu blob Azure. Ale data se dají zkopírovat přímo do jakéhokoli z jímky uvádí [aktivity přesunu dat](data-factory-data-movement-activities.md) článek pomocí aktivity kopírování v Azure Data Factory.

**Webové propojená služba** tento příklad používá webové propojené služby s anonymní ověřování. V tématu [webové propojená služba](#linked-service-properties) části pro různé typy ověřování můžete použít.

```json
{
    "name": "WebLinkedService",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

**Propojená služba Azure Storage**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>",
      "gatewayName": "<gateway name>"
    }
  }
}
```

**Vstupní datové sady WebTable** nastavení **externí** k **true** služba Data Factory informuje, že datová sada je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně.

> [!NOTE]
> V tématu [Get index tabulky v stránku HTML](#get-index-of-a-table-in-an-html-page) části Postup získání index tabulky v stránku HTML.  
>
>

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```


**Výstupní datová sada Azure Blob**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodiny, interval: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```



**Kanál s aktivitou kopírování**

Kanál obsahuje aktivitu kopírování, který je nakonfigurovaný na použití vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **WebSource** a **podřízený** je typ nastaven na **BlobSink**.

V tématu [vlastnosti typu WebSource](#copy-activity-type-properties) pro seznam vlastností WebSource nepodporuje.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "WebTableToAzureBlob",
        "description": "Copy from a Web table to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "WebTableInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "WebSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```

## <a name="get-index-of-a-table-in-an-html-page"></a>Na stránce HTML získat index tabulky.
1. Spusťte **Excel 2016** a přepněte do **Data** kartě.  
2. Klikněte na tlačítko **nový dotaz** na panelu nástrojů, přejděte na **z jiných zdrojů** a klikněte na tlačítko **z webové**.

    ![Power Query nabídky](./media/data-factory-web-table-connector/PowerQuery-Menu.png)
3. V **z webové** dialogovém okně zadejte **URL** , kterou použijete v propojené službě JSON (například: https://en.wikipedia.org/wiki/) společně s cesty zadáte pro datovou sadu (například: AFI % 27s_100_Years... 100_Movies) a klikněte na tlačítko **OK**.

    ![Z webové dialogového okna](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

    Adresa URL použitá v tomto příkladu: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Pokud se zobrazí **přístup k webovému obsahu** dialogovém okně vyberte právo **adresa URL**, **ověřování**a klikněte na tlačítko **Connect**.

   ![Přístup k dialogové okno webového obsahu](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5. Klikněte na tlačítko **tabulky** položky ve stromovém zobrazení zobrazit obsah z tabulky a klikněte na tlačítko **upravit** tlačítko dole.  

   ![Dialogové okno Navigátor](./media/data-factory-web-table-connector/Navigator-DialogBox.png)
6. V **Editor dotazů** okně klikněte na tlačítko **Upřesnit** tlačítka na panelu nástrojů.

    ![Tlačítko Rozšířené editoru](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)
7. V dialogovém okně Upřesnit číslo vedle "Zdroj" je index.

    ![Rozšířené Editor - indexu](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)

Pokud používáte Excel 2013, použijte [Microsoft Power Query pro Excel](https://www.microsoft.com/download/details.aspx?id=39379) získat index. V tématu [připojit k webové stránce](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) článku. Kroky jsou podobné, pokud používáte [Microsoft Power BI pro plochu](https://powerbi.microsoft.com/desktop/).

> [!NOTE]
> Mapování sloupců z datové sady zdroje na sloupce ze sady jímku dat naleznete v tématu [mapování sloupců datovou sadu v Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Výkon a ladění
V tématu [výkonu kopie aktivity & ladění průvodce](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů, že dopad výkon přesun dat (aktivita kopírování) v Azure Data Factory a různé způsoby, jak optimalizovat ho.
