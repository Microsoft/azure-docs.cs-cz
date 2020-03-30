---
title: Přesunutí dat z webové tabulky pomocí Azure Data Factory
description: Přečtěte si, jak přesunout data z tabulky na webové stránce pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: f54a26a4-baa4-4255-9791-5a8f935898e2
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d2ea038c7d7212529185d77a6ba9e64deacb1c9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265711"
---
# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>Přesunutí dat ze zdroje webové tabulky pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-web-table-connector.md)
> * [Verze 2 (aktuální verze)](../connector-web-table.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte [si téma Konektor webové tabulky ve verzi 2](../connector-web-table.md).

Tento článek popisuje, jak pomocí kopírovat aktivitu v Azure Data Factory přesunout data z tabulky na webové stránce do úložiště dat podporované jímky. Tento článek vychází z článku [aktivity přesundat dat,](data-factory-data-movement-activities.md) který představuje obecný přehled přesunu dat s aktivitou kopírování a seznam úložišť dat podporovaných jako zdroje/jímky.

Data Factory v současné době podporuje pouze přesouvání dat z webové tabulky do jiných úložišť dat, ale ne přesouvání dat z jiných úložišť dat do cíle webové tabulky.

> [!IMPORTANT]
> Tento webový konektor v současné době podporuje pouze extrahování obsahu tabulky ze stránky HTML. Chcete-li načíst data z koncového bodu HTTP/s, použijte místo toho [konektor HTTP.](data-factory-http-connector.md)

## <a name="prerequisites"></a>Požadavky

Chcete-li použít tento konektor webové tabulky, musíte nastavit prostředí Runtime integrace s vlastním hostitelem (aka Brána pro správu dat) a nakonfigurovat `gatewayName` vlastnost v propojené službě jímky. Chcete-li například kopírovat z webové tabulky do úložiště objektů Blob Azure, nakonfigurujte propojenou službu Azure Storage takto:

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
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data z místního úložiště dat Cassandra pomocí různých nástrojů nebo rozhraní API. 

- Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Viz [Kurz: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) pro rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat. 
- K vytvoření kanálu můžete taky použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Šablona Azure Resource Manager**, Rozhraní **.NET API**a REST **API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v tématu [Kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aktivity. 

Bez ohledu na to, zda používáte nástroje nebo api, provedete následující kroky k vytvoření kanálu, který přesune data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **propojené služby** pro propojení vstupních a výstupních úložišť dat s vaší továrně dat.
2. Vytvořte **datové sady** představující vstupní a výstupní data pro operaci kopírování. 
3. Vytvořte **kanál** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. 

Při použití průvodce jsou automaticky vytvořeny definice JSON pro tyto entity Data Factory (propojené služby, datové sady a kanál). Při použití nástrojů nebo rozhraní API (s výjimkou rozhraní .NET API) definujete tyto entity Data Factory pomocí formátu JSON.  Ukázka s definicemi JSON pro entity Data Factory, které se používají ke kopírování dat z webové tabulky, najdete v [článku příklad JSON: Kopírování dat z webové tabulky do](#json-example-copy-data-from-web-table-to-azure-blob) části Azure Blob tohoto článku. 

V následujících částech jsou uvedeny podrobnosti o vlastnostech JSON, které se používají k definování entit Factory dat specifických pro webovou tabulku:

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb
V následující tabulce je uveden popis prvků JSON specifických pro webovou propojenou službu.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type musí být nastavena na: **Web** |Ano |
| URL |Adresa URL webového zdroje |Ano |
| authenticationType |Anonymní. |Ano |

### <a name="using-anonymous-authentication"></a>Použití anonymního ověřování

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
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datových sad, naleznete v článku [Vytváření datových sad.](data-factory-create-datasets.md) Oddíly, jako je struktura, dostupnost a zásady datové sady JSON, jsou podobné pro všechny typy datových sad (Azure SQL, Azure blob, Tabulka Azure atd.).

Sekce **typeProperties** se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Oddíl TypeProperties pro datovou sadu typu **WebTable** má následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type |typ datové sady. musí být nastavena na **WebTable** |Ano |
| cesta |Relativní adresa URL prostředku, který obsahuje tabulku. |Ne. Pokud cesta není zadána, je použita pouze adresa URL zadaná v definici propojené služby. |
| index |Index tabulky v prostředku. Postup získání indexu tabulky na stránce HTML [najdete v tématu Získání indexu tabulky na](#get-index-of-a-table-in-an-html-page) stránce HTML. |Ano |

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
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit, naleznete v článku [Vytváření kanálů.](data-factory-create-pipelines.md) Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit.

Vzhledem k tomu, vlastnosti, které jsou k dispozici v typeProperties části aktivity se liší s každým typem aktivity. U aktivity kopírování se liší v závislosti na typech zdrojů a propadů.

V současné době, pokud zdroj v aktivitě kopírování je typu **WebSource**, nejsou podporovány žádné další vlastnosti.


## <a name="json-example-copy-data-from-web-table-to-azure-blob"></a>Příklad JSON: Kopírování dat z webové tabulky do objektu Blob Azure
Následující ukázka ukazuje:

1. Propojená služba typu [Web](#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [WebTable](#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [WebSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data z webové tabulky do objektu blob Azure každou hodinu. Vlastnosti JSON použité v těchto vzorcích jsou popsány v následujících částech.

Následující ukázka ukazuje, jak zkopírovat data z webové tabulky do objektu blob Azure. Data však můžete zkopírovat přímo do některého z jímky uvedené v článku [aktivity přesunu dat](data-factory-data-movement-activities.md) pomocí aktivity kopírování v Azure Data Factory.

**Webová propojená služba** Tento příklad používá webovou propojenou službu s anonymním ověřováním. V části [Weblinked service](#linked-service-properties) naleznete různé typy ověřování, které můžete použít.

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

**Vstupní datová sada webtable** Nastavení **externí** **na hodnotu true** informuje službu Data Factory, že datová sada je externí pro datovou továrnu a není vytvářena aktivitou v datové továrně.

> [!NOTE]
> Postup získání indexu tabulky na stránce HTML [najdete v tématu Získání indexu tabulky na](#get-index-of-a-table-in-an-html-page) stránce HTML.  
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



**Kanál s aktivitou Kopírování**

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití vstupních a výstupních datových sad a je naplánována na každou hodinu. V definici kanálu JSON je **typ zdroje** nastaven na **WebSource** a typ **jímky** je nastaven na **Objekt blobSink**.

V seznamu vlastností podporovaných websource viz vlastnosti typu WebSource.

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

## <a name="get-index-of-a-table-in-an-html-page"></a>Získání indexu tabulky na stránce HTML
1. Spusťte **Excel 2016** a přepněte na kartu **Data.**  
2. Klepněte na tlačítko **Nový dotaz** na panelu nástrojů, přejděte na **položku Z jiných zdrojů** a klepněte na příkaz Z **webu**.

    ![Nabídka Power Query](./media/data-factory-web-table-connector/PowerQuery-Menu.png)
3. V dialogovém **okně Z webu** zadejte adresu **URL,** kterou byste https://en.wikipedia.org/wiki/) použili v propojené službě JSON (například: spolu s cestou, kterou byste zadali pro datovou sadu (například AFI%27s_100_Years... 100_Movies) a klepněte na tlačítko **OK**.

    ![Z webového dialogového okna](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

    Adresa URL použitá v tomto příkladu:https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Pokud se zobrazí dialogové okno **Webový obsah aplikace Access,** vyberte správnou **adresu URL**, **ověřování**a klepněte na tlačítko **Připojit**.

   ![Dialogové okno Přístup k webovému obsahu](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5. Kliknutím na položku **tabulky** ve stromovém zobrazení zobrazíte obsah z tabulky a v dolní části klikněte na **tlačítko Upravit.**  

   ![Dialogové okno Navigátor](./media/data-factory-web-table-connector/Navigator-DialogBox.png)
6. V okně **Editor dotazů** klepněte na tlačítko **Rozšířený editor** na panelu nástrojů.

    ![Tlačítko Rozšířený editor](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)
7. V dialogovém okně Rozšířený editor je číslo vedle "Zdroj" index.

    ![Rozšířený editor - index](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)

Pokud používáte Excel 2013, použijte k získání indexu [Microsoft Power Query pro Excel.](https://www.microsoft.com/download/details.aspx?id=39379) Podrobnosti najdete v článku [Připojení k webové stránce.](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) Postup je podobný, pokud používáte [Microsoft Power BI pro stolní počítače](https://powerbi.microsoft.com/desktop/).

> [!NOTE]
> Pokud chcete mapovat sloupce ze zdrojové datové sady na sloupce z datové sady jímky, přečtěte si témat [mapování sloupců datových sad v Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Výkon a ladění
[V tématu Průvodce sledováním výkonu & optimalizací se](data-factory-copy-activity-performance.md) dozvíte o klíčových faktorech, které ovlivňují výkon přesunu dat (aktivita kopírování) ve Službě Azure Data Factory, a o různých způsobech jeho optimalizace.
