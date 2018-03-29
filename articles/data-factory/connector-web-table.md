---
title: Kopírování dat z tabulky webové pomocí Azure Data Factory | Microsoft Docs
description: Další informace o webové tabulky konektor služby Azure Data Factory umožňující kopírování dat z tabulky webové k úložištím dat podporovaných službou Data Factory jako jímky.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: jingwang
ms.openlocfilehash: f1fa79ed32969f5087107d6105fd2f4baf7640e3
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>Kopírování dat z tabulky webové pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](v1/data-factory-web-table-connector.md)
> * [Verze 2 – Preview](connector-web-table.md)

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat z tabulky databáze webové. Vychází [zkopírujte aktivity přehled](copy-activity-overview.md) článek, který představuje obecný přehled aktivity kopírování.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [konektor webové tabulky v V1](v1/data-factory-web-table-connector.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Z webové tabulky databáze můžete zkopírovat data do úložiště dat žádné podporované jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento Web tabulky konektor podporuje **extrahování obsahu tabulky z stránku HTML**. Pokud chcete načíst data z koncového bodu protokolu HTTP/s, použijte [HTTP konektor](connector-http.md) místo.

## <a name="prerequisites"></a>Předpoklady

Pokud chcete použít tento konektor tabulky Web, musíte nastavit Self-hosted integrace Runtime. V tématu [Self-hosted integrace Runtime](create-self-hosted-integration-runtime.md) článku.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

Následující části obsahují podrobnosti o vlastnosti, které slouží k určení konkrétní entity služby Data Factory pro konektor webových tabulky.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Následující vlastnosti jsou podporovány pro webové tabulky propojené služby:

| Vlastnost | Popis | Požadováno |
|:--- |:--- |:--- |
| typ | Vlastnost typu musí být nastavena na: **Web** |Ano |
| Adresa URL | Adresa URL pro webové zdroje |Ano |
| authenticationType. | Povolená hodnota je: **anonymní**. |Ano |
| connectVia | [Integrace Runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. Modul Runtime Self-hosted integrace se vyžaduje, jak je uvedeno v [požadavky](#prerequisites). |Ano |

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností, které podporuje webové tabulky datovou sadu.

Ke zkopírování dat z tabulky webové, nastavte vlastnost typu datové sady, která **RelationalTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požadováno |
|:--- |:--- |:--- |
| typ | Vlastnost typu datové sady musí být nastavena na: **WebTable** | Ano |
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
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností, které podporuje webové zdroje tabulky.

### <a name="web-table-as-source"></a>Webové tabulky jako zdroj

Ke zkopírování dat z tabulky webové, nastavte typ zdroje v aktivitě kopírování do **WebSource**, jsou podporovány žádné další vlastnosti.

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

## <a name="get-index-of-a-table-in-an-html-page"></a>Na stránce HTML získat index tabulky.

Chcete-li získat index tabulky, který je nutné nakonfigurovat v [vlastnosti datové sady](#dataset-properties), například aplikace Excel 2016 můžete použít jako nástroj následujícím způsobem:

1. Spusťte **Excel 2016** a přepněte do **Data** kartě.
2. Klikněte na tlačítko **nový dotaz** na panelu nástrojů, přejděte na **z jiných zdrojů** a klikněte na tlačítko **z webové**.

    ![Power Query nabídky](./media/copy-data-from-web-table/PowerQuery-Menu.png)
3. V **z webové** dialogovém okně zadejte **URL** , kterou použijete v propojené službě JSON (například: https://en.wikipedia.org/wiki/) společně s cesty zadáte pro datovou sadu (například: AFI % 27s_100_Years... 100_Movies) a klikněte na tlačítko **OK**.

    ![Z webové dialogového okna](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    Adresa URL použitá v tomto příkladu: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Pokud se zobrazí **přístup k webovému obsahu** dialogovém okně vyberte právo **adresa URL**, **ověřování**a klikněte na tlačítko **Connect**.

   ![Přístup k dialogové okno webového obsahu](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. Klikněte na tlačítko **tabulky** položky ve stromovém zobrazení zobrazit obsah z tabulky a klikněte na tlačítko **upravit** tlačítko dole.  

   ![Dialogové okno Navigátor](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. V **Editor dotazů** okně klikněte na tlačítko **Upřesnit** tlačítka na panelu nástrojů.

    ![Tlačítko Rozšířené editoru](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. V dialogovém okně Upřesnit číslo vedle "Zdroj" je index.

    ![Rozšířené Editor - indexu](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

Pokud používáte Excel 2013, použijte [Microsoft Power Query pro Excel](https://www.microsoft.com/download/details.aspx?id=39379) získat index. V tématu [připojit k webové stránce](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) článku. Kroky jsou podobné, pokud používáte [Microsoft Power BI pro plochu](https://powerbi.microsoft.com/desktop/).


## <a name="next-steps"></a>Další postup
Seznam úložišť dat jako zdroje a jímky nepodporuje aktivitu kopírování v Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).