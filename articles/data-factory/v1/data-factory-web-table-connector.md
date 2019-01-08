---
title: Přesun dat z tabulky Web pomocí Azure Data Factory | Dokumentace Microsoftu
description: Další informace o tom, jak přesunout data z tabulky na webové stránce pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: f54a26a4-baa4-4255-9791-5a8f935898e2
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1ba8db3ebe2caf4c37d147f744326b6e631cb556
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54022049"
---
# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>Přesun dat z tabulky webovému zdroji pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-web-table-connector.md)
> * [Verze 2 (aktuální verze)](../connector-web-table.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [webový konektor tabulky ve verzi V2](../connector-web-table.md).

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory pro přesun dat z tabulky na webové stránce do úložiště dat jímky podporované. Tento článek vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který nabízí obecný přehled o přesun dat pomocí aktivity kopírování a seznam úložišť dat podporovaných jako zdroje a jímky.

Služby data factory v současné době podporuje pouze přesouvá data z tabulky webové do jiných úložišť dat, ale není přesouvá data z jiných dat ukládá do cílové tabulky Web.

> [!IMPORTANT]
> Tento konektor Web aktuálně podporuje pouze extrahování obsahu tabulky ze stránky HTML. Pro načtení dat z koncového bodu HTTP/s, použijte [konektor HTTP](data-factory-http-connector.md) místo.

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít tento konektor Web table, budete muset nastavit modul Integration Runtime (označuje se také jako brána pro správu dat) a nakonfigurovat `gatewayName` vlastnost v jímce propojenou službu. Například pro kopírování z Webová tabulka do úložiště objektů Blob v Azure, nakonfigurujte propojenou službu Azure Storage jako následující:

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
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z úložiště dat místní Cassandra pomocí různých nástrojů a rozhraní API. 

- Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Zobrazit [kurzu: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním data. 
- Tyto nástroje můžete také použít k vytvoření kanálu: **Azure portal**, **sady Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru**, **rozhraní .NET API**a  **Rozhraní REST API**. Zobrazit [kurz aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování. 

Ať už používáte, nástrojů nebo rozhraní API, proveďte následující kroky k vytvoření kanálu pro přesouvání dat ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do služby data factory.
2. Vytvoření **datových sad** k představují vstupní a výstupní data pro operaci kopírování. 
3. Vytvoření **kanálu** s aktivitou kopírování, která přijímá jako vstupní datovou sadu a datovou sadu jako výstup. 

Při použití Průvodce definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál) se automaticky vytvoří za vás. Při použití nástroje a rozhraní API (s výjimkou rozhraní .NET API), můžete definovat tyto entity služby Data Factory ve formátu JSON.  Tady je příklad s definice JSON entit služby Data Factory, které se používají ke kopírování dat z tabulky web, naleznete v tématu [příklad JSON: Kopírování dat z tabulky Web do objektů Blob v Azure](#json-example-copy-data-from-web-table-to-azure-blob) části tohoto článku. 

Následující části obsahují podrobnosti o vlastnostech JSON, které se používají k definování entit služby Data Factory konkrétní Webová tabulka:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro webové propojené služby.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type musí být nastavená na: **Web** |Ano |
| URL |Adresa URL k webovému zdroji |Ano |
| authenticationType. |Anonymní. |Ano |

### <a name="using-anonymous-authentication"></a>Anonymní ověřování

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
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny datové sady typy (Azure SQL, Azure blob, tabulky Azure, atd.).

**TypeProperties** oddílu se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti. TypeProperties části datové sady typu **WebTable** má následující vlastnosti

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type |Typ datové sady. musí být nastaveno na **WebTable** |Ano |
| path |Relativní adresa URL k prostředku, který obsahuje tabulku. |Ne. Když není zadána cesta, použije se pouze adresu URL, které jsou určené v definici propojené služby. |
| index |Index tabulky ve zdroji. Zobrazit [Get index tabulky na stránce HTML](#get-index-of-a-table-in-an-html-page) najdete kroky pro získání indexu tabulky na stránce HTML. |Ano |

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
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit.

Vzhledem k tomu, vlastnosti v části typeProperties aktivity se liší s jednotlivými typu aktivity. Pro aktivitu kopírování se liší v závislosti na typy zdroje a jímky.

Pokud v současné době zdroje v aktivitě kopírování je typu **WebSource**, jsou podporovány žádné další vlastnosti.


## <a name="json-example-copy-data-from-web-table-to-azure-blob"></a>Příklad JSON: Kopírování dat z tabulky Web do objektů Blob v Azure
Následující příklad ukazuje:

1. Propojené služby typu [webové](#linked-service-properties).
2. Propojené služby typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [WebTable](#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [WebSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázce kopíruje data z Webová tabulka do objektu blob Azure každou hodinu. Vlastnostech JSON použitých v tyto ukázky jsou popsány v části podle ukázky.

Následující příklad ukazuje, jak kopírovat data z tabulky Web do objektu blob Azure. Ale data se dají zkopírovat přímo do libovolné jímky uvádí [aktivity přesunu dat](data-factory-data-movement-activities.md) článku pomocí aktivity kopírování ve službě Azure Data Factory.

**Webové propojené služby** tento příklad používá webové propojené služby s anonymní ověřování. Zobrazit [webové propojené služby](#linked-service-properties) oddílu pro různé typy ověřování můžete použít.

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

**Vstupní datová sada WebTable** nastavení **externí** k **true** služby Data Factory informuje, že datová sada je externí do služby data factory a není vytvořen aktivitou ve službě data factory.

> [!NOTE]
> Zobrazit [Get index tabulky na stránce HTML](#get-index-of-a-table-in-an-html-page) najdete kroky pro získání indexu tabulky na stránce HTML.  
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

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1).

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

Kanálu obsahujícího aktivitu kopírování, který je nakonfigurován na použití vstupních a výstupních datových sad a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **WebSource** a **jímky** je typ nastaven na **BlobSink**.

Zobrazit [vlastnosti typu WebSource](#copy-activity-type-properties) pro seznam vlastností, které jsou podporovány WebSource.

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

## <a name="get-index-of-a-table-in-an-html-page"></a>Získat index tabulky na stránce HTML
1. Spuštění **Excelu 2016** a přejděte **Data** kartu.  
2. Klikněte na tlačítko **nový dotaz** na panelu nástrojů, přejděte na **z jiných zdrojů** a klikněte na tlačítko **z webu**.

    ![Nabídky Power Query](./media/data-factory-web-table-connector/PowerQuery-Menu.png)
3. V **z webu** dialogového okna zadejte **URL** použijete v propojené službě JSON (například: https://en.wikipedia.org/wiki/) spolu s cestu, zadali byste pro datovou sadu (například: AFI % 27s_100_Years... 100_Movies) a klikněte na tlačítko **OK**.

    ![V dialogovém okně Web](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

    Adresa URL používaná v tomto příkladu: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Pokud se zobrazí **přístup k webovému obsahu** dialogového okna, vyberte vpravo **adresy URL**, **ověřování**a klikněte na tlačítko **připojit**.

   ![Přístup k dialogové okno webového obsahu](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5. Klikněte na tlačítko **tabulky** položky ve stromovém zobrazení na Zobrazit obsah z tabulky a pak klikněte na tlačítko **upravit** tlačítko dole.  

   ![Dialogové okno Navigátor](./media/data-factory-web-table-connector/Navigator-DialogBox.png)
6. V **editoru dotazů** okna, klikněte na tlačítko **rozšířený Editor** tlačítko na panelu nástrojů.

    ![Tlačítko Upřesnit editoru](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)
7. V dialogovém okně Upřesnit se číslo vedle "Zdroj" index.

    ![Rozšířený Editor - indexu](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)

Pokud používáte Excel 2013, použijte [Microsoft Power Query pro Excel](https://www.microsoft.com/download/details.aspx?id=39379) získat index. Zobrazit [připojit k webové stránce](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) , kde najdete podrobnosti. Kroky jsou podobné, pokud používáte [Microsoft Power BI desktopu](https://powerbi.microsoft.com/desktop/).

> [!NOTE]
> Pokud chcete namapovat sloupce ze zdrojové datové sady na sloupce z datové sady jímky, najdete v článku [mapování sloupců v datové sadě ve službě Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Výkon a optimalizace
Zobrazit [výkonem aktivity kopírování & Průvodci optimalizací](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů této ovlivnit výkon přesouvání dat (aktivita kopírování) ve službě Azure Data Factory a různé způsoby, jak optimalizovat.
