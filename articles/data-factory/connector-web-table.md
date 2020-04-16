---
title: Kopírování dat z webové tabulky pomocí Azure Data Factory
description: Další informace o konektoru webové tabulky azure data factory, který vám umožní kopírovat data z webové tabulky do datových úložišť podporovaných Data Factory jako jímky.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 0eb4d37342685c13027a69bb6cb85f618fa63f20
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81410220"
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>Kopírování dat z webové tabulky pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-web-table-connector.md)
> * [Aktuální verze](connector-web-table.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí kopírovat aktivitu v Azure Data Factory ke kopírování dat z databáze webové tabulky. Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

Rozdíl mezi tímto konektorem webové tabulky, [konektorem REST](connector-rest.md) a [konektorem HTTP](connector-http.md) jsou:

- **Konektor webové tabulky** extrahuje obsah tabulky z webové stránky HTML.
- **REST konektor** konkrétně podporují kopírování dat z ROZHRANÍ API RESTful.
- **Http konektor** je obecný pro načtení dat z libovolného koncového bodu HTTP, například ke stažení souboru. 

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor webové tabulky je podporován pro následující aktivity:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Data z databáze webové tabulky můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitou kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Konkrétně tento konektor webové tabulky podporuje **extrahování obsahu tabulky ze stránky HTML**.

## <a name="prerequisites"></a>Požadavky

Chcete-li použít tento konektor webové tabulky, je třeba nastavit prostředí Runtime integrace s vlastním hostitelem. Podrobnosti najdete v článku [runtime integrace s vlastním hostitelem.](create-self-hosted-integration-runtime.md)

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Data Factory specifických pro konektor webové tabulky.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro webovou tabulkovou propojenou službu jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na: **Web** |Ano |
| url | Adresa URL webového zdroje |Ano |
| authenticationType | Povolená hodnota je: **Anonymní**. |Ano |
| connectVia | [Prostředí Integrace Runtime,](concepts-integration-runtime.md) které se má použít k připojení k úložišti dat. Runtime integrace hostované samostatně je vyžadován, jak je uvedeno v [požadavky](#prerequisites). |Ano |

**Příklad:**

```json
{
    "name": "WebLinkedService",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "url" : "https://en.wikipedia.org/wiki/",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datových sad.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou webové tabulky.

Chcete-li kopírovat data z webové tabulky, nastavte vlastnost type datové sady na **WebTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na: **WebTable.** | Ano |
| cesta |Relativní adresa URL prostředku, který obsahuje tabulku. |Ne. Pokud cesta není zadána, je použita pouze adresa URL zadaná v definici propojené služby. |
| index |Index tabulky v prostředku. Postup získání indexu tabulky na stránce HTML [najdete v tématu Získání indexu tabulky na](#get-index-of-a-table-in-an-html-page) stránce HTML. |Ano |

**Příklad:**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Web linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem webové tabulky.

### <a name="web-table-as-source"></a>Webová tabulka jako zdroj

Chcete-li kopírovat data z webové tabulky, nastavte typ zdroje v aktivitě kopírování na **websource**, nejsou podporovány žádné další vlastnosti.

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromWebTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Web table input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "WebSource"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="get-index-of-a-table-in-an-html-page"></a>Získání indexu tabulky na stránce HTML

Chcete-li získat index tabulky, kterou je třeba nakonfigurovat ve [vlastnostech datové sady](#dataset-properties), můžete použít například Excel 2016 jako nástroj následujícím způsobem:

1. Spusťte **Excel 2016** a přepněte na kartu **Data.**
2. Klepněte na tlačítko **Nový dotaz** na panelu nástrojů, přejděte na **položku Z jiných zdrojů** a klepněte na příkaz Z **webu**.

    ![Nabídka Power Query](./media/copy-data-from-web-table/PowerQuery-Menu.png)
3. V dialogovém **okně Z webu** zadejte adresu **URL,** kterou byste https://en.wikipedia.org/wiki/) použili v propojené službě JSON (například: spolu s cestou, kterou byste zadali pro datovou sadu (například AFI%27s_100_Years... 100_Movies) a klepněte na tlačítko **OK**.

    ![Z webového dialogového okna](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    Adresa URL použitá v tomto příkladu:https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Pokud se zobrazí dialogové okno **Webový obsah aplikace Access,** vyberte správnou **adresu URL**, **ověřování**a klepněte na tlačítko **Připojit**.

   ![Dialogové okno Přístup k webovému obsahu](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. Kliknutím na položku **tabulky** ve stromovém zobrazení zobrazíte obsah z tabulky a v dolní části klikněte na **tlačítko Upravit.**  

   ![Dialogové okno Navigátor](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. V okně **Editor dotazů** klepněte na tlačítko **Rozšířený editor** na panelu nástrojů.

    ![Tlačítko Rozšířený editor](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. V dialogovém okně Rozšířený editor je číslo vedle "Zdroj" index.

    ![Rozšířený editor - index](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

Pokud používáte Excel 2013, použijte k získání indexu [Microsoft Power Query pro Excel.](https://www.microsoft.com/download/details.aspx?id=39379) Podrobnosti najdete v článku [Připojení k webové stránce.](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) Postup je podobný, pokud používáte [Microsoft Power BI pro stolní počítače](https://powerbi.microsoft.com/desktop/).


## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
