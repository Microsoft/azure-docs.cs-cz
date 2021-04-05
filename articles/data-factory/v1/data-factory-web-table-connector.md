---
title: Přesunout data z webové tabulky pomocí Azure Data Factory
description: Přečtěte si, jak přesunout data z tabulky na webové stránce pomocí Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7663848bd06244de7efb169a576e11a5c78204c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100392897"
---
# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>Přesunutí dat ze zdroje webové tabulky pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-web-table-connector.md)
> * [Verze 2 (aktuální verze)](../connector-web-table.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Web Table Connector v v2](../connector-web-table.md).

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory přesouvat data z tabulky na webové stránce do podporovaného úložiště dat jímky. Tento článek se týká článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , který představuje obecný přehled přesunu dat s aktivitou kopírování a seznam úložišť dat podporovaných jako zdroje a jímky.

Data Factory aktuálně podporuje pouze přesun dat z webové tabulky do jiných úložišť dat, ale nepřesouvá data z jiných úložišť dat do cílové webové tabulky.

> [!IMPORTANT]
> Tento webový konektor aktuálně podporuje pouze extrakci obsahu tabulky ze stránky HTML. K načtení dat z koncového bodu HTTP/s použijte místo toho [konektor http](data-factory-http-connector.md) .

## <a name="prerequisites"></a>Požadavky

Chcete-li použít tento konektor webové tabulky, je nutné nastavit Integration Runtime v místním prostředí (neboli Správa dat bránu) a nakonfigurovat `gatewayName` vlastnost v propojené službě jímky. Pokud například chcete kopírovat z webové tabulky do úložiště objektů BLOB v Azure, nakonfigurujte Azure Storage propojenou službu následujícím způsobem:

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

- Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) . 
- K vytvoření kanálu můžete také použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** a **REST API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v [kurzu kopírování aktivit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) . 

Bez ohledu na to, jestli používáte nástroje nebo rozhraní API, provedete následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **propojené služby** , které propojí vstupní a výstupní úložiště dat s datovou továrnou.
2. Vytvořte datové **sady** , které reprezentují vstupní a výstupní data pro operaci kopírování. 
3. Vytvořte **kanál** s aktivitou kopírování, která převezme datovou sadu jako vstup a datovou sadu jako výstup. 

Při použití Průvodce se automaticky vytvoří definice JSON pro tyto Entity Data Factory (propojené služby, datové sady a kanál). Pokud používáte nástroje/rozhraní API (s výjimkou rozhraní .NET API), definujete tyto Data Factory entit pomocí formátu JSON.  Ukázku s definicemi JSON pro Data Factory entity, které se používají ke kopírování dat z webové tabulky, najdete v části [JSON example: kopírování dat z webové tabulky do objektu blob Azure](#json-example-copy-data-from-web-table-to-azure-blob) v tomto článku. 

Následující části obsahují podrobné informace o vlastnostech JSON, které se používají k definování Data Factory entit specifických pro webovou tabulku:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka uvádí popis pro prvky JSON specifické pro propojenou službu webu.

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| typ |Vlastnost Type musí být nastavená na: **Web** . |Yes |
| URL |Adresa URL webového zdroje |Yes |
| authenticationType |Anonymní. |Yes |

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
Úplný seznam sekcí & vlastností dostupných pro definování datových sad naleznete v článku [vytvoření datových sad](data-factory-create-datasets.md) . Oddíly, jako je například struktura, dostupnost a zásada pro datovou sadu JSON, jsou podobné pro všechny typy datových sad (Azure SQL, Azure Blob, tabulka Azure atd.).

Oddíl **typeProperties** se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Oddíl typeProperties pro datovou sadu typu **webtable** má následující vlastnosti.

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ |Typ datové sady musí být nastavené na **Webtable** . |Yes |
| program |Relativní adresa URL k prostředku, který obsahuje tabulku. |No. Pokud cesta není zadaná, použije se jenom adresa URL zadaná v definici propojené služby. |
| index |Index tabulky v prostředku Postup pro získání indexu tabulky na stránce HTML najdete v části [získání indexu tabulky v oddílu stránky HTML](#get-index-of-a-table-in-an-html-page) . |Yes |

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
Úplný seznam sekcí & vlastností dostupných pro definování aktivit najdete v článku [vytvoření kanálů](data-factory-create-pipelines.md) . Pro všechny typy aktivit jsou k dispozici vlastnosti, jako je název, popis, vstupní a výstupní tabulka a zásada.

V takovém případě se vlastnosti dostupné v části typeProperties v aktivitě liší podle typu aktivity. U aktivity kopírování se liší v závislosti na typech zdrojů a jímky.

V současné době platí, že pokud je zdroj v aktivitě kopírování typu **websource**, nejsou podporovány žádné další vlastnosti.


## <a name="json-example-copy-data-from-web-table-to-azure-blob"></a>Příklad JSON: kopírování dat z webové tabulky do Azure Blob
Následující příklad ukazuje:

1. Propojená služba typu [Web](#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [webtable](#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [websource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka kopíruje data z webové tabulky do objektu blob Azure každou hodinu. Vlastnosti JSON použité v těchto ukázkách jsou popsány v oddílech následujících po ukázkách.

Následující příklad ukazuje, jak kopírovat data z webové tabulky do objektu blob Azure. Data se ale dají zkopírovat přímo do kterékoli z umyvadel uvedených v článku [aktivity přesunu dat](data-factory-data-movement-activities.md) pomocí aktivity kopírování v Azure Data Factory.

**Webová propojená služba** V tomto příkladu se používá webová propojená služba s anonymním ověřováním. Různé typy ověřování, které můžete použít, najdete v části [Webová propojená služba](#linked-service-properties) .

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

**Vstupní datová sada Webtable** Nastavením možnosti **externí** na **hodnotu true** dojde k informování služby Data Factory, že datová sada je pro objekt pro vytváření dat externá a není vytvořená aktivitou v datové továrně.

> [!NOTE]
> Postup pro získání indexu tabulky na stránce HTML najdete v části [získání indexu tabulky v oddílu stránky HTML](#get-index-of-a-table-in-an-html-page) .  
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

Data se zapisují do nového objektu BLOB každou hodinu (frekvence: hodina, interval: 1).

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

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala vstupní a výstupní datové sady a má naplánované spuštění každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **websource** a typ **jímky** je nastavený na **BlobSink**.

Seznam vlastností, které podporuje websource, najdete v tématu vlastnosti typu websource.

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
1. Spusťte **Excel 2016** a přepněte na kartu **data** .  
2. Na panelu nástrojů klikněte na **Nový dotaz** , přejděte na **z jiných zdrojů** a klikněte na **z webu**.

    ![Nabídka Power Query](./media/data-factory-web-table-connector/PowerQuery-Menu.png)
3. V dialogovém okně **z webu** zadejte **adresu URL** , kterou použijete v kódu JSON propojené služby (například: https://en.wikipedia.org/wiki/) společně s cestou, kterou zadáte pro datovou sadu (například: AFI% 27s_100_Years... 100_Movies) a klikněte na **OK**.

    ![Z dialogového okna Web](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

    Adresa URL použitá v tomto příkladu: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Pokud se zobrazí dialogové okno **přístup k webovému obsahu** , vyberte správnou **adresu URL**, **ověřování** a klikněte na **připojit**.

   ![Přístup k webovému obsahu – dialogové okno](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5. Kliknutím na položku **tabulky** ve stromovém zobrazení zobrazíte obsah z tabulky a potom v dolní části kliknete na tlačítko **Upravit** .  

   ![Dialogové okno Navigátor](./media/data-factory-web-table-connector/Navigator-DialogBox.png)
6. V okně **Editor dotazů** klikněte na panelu nástrojů na tlačítko **Rozšířený editor** .

    ![Rozšířený editor – tlačítko](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)
7. V dialogovém okně Rozšířený editor je číslo vedle možnosti "zdroj" index.

    ![Rozšířený editor – index](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)

Pokud používáte Excel 2013, použijte k získání indexu [Microsoft Power Query pro Excel](https://www.microsoft.com/download/details.aspx?id=39379) . Podrobnosti najdete v článku [připojení k webové stránce](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) . Postup je podobný, pokud používáte [Microsoft Power BI pro Desktop](https://powerbi.microsoft.com/desktop/).

> [!NOTE]
> Chcete-li mapovat sloupce ze zdrojové datové sady na sloupce z datové sady jímky, přečtěte si téma [mapování sloupců datové sady v Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Výkon a optimalizace
Další informace o klíčových faktorech, které mají vliv na výkon přesunu dat (aktivita kopírování) v Azure Data Factory a různých způsobech jejich optimalizace, najdete v tématu [Průvodce optimalizací aktivity kopírování &](data-factory-copy-activity-performance.md) .
