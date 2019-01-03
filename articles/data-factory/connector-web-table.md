---
title: Kopírování dat z tabulky Web pomocí Azure Data Factory | Dokumentace Microsoftu
description: Další informace o Web tabulky konektor z Azure Data Factory, která umožňuje kopírování dat z tabulky web do úložišť dat podporovaných službou Data Factory jako jímky.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 3e62dbc31976187f4bd37a3567169da2ffa0909b
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2018
ms.locfileid: "53807648"
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>Kopírování dat z tabulky Web pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-web-table-connector.md)
> * [Aktuální verze](connector-web-table.md)

Tento článek ukazuje, jak použije aktivitu kopírování ke kopírování dat z databáze tabulek Web ve službě Azure Data Factory. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

Rozdíly mezi tabulku tohoto webového konektoru [REST konektor](connector-rest.md) a [konektor HTTP](connector-http.md) jsou:

- **Webový konektor tabulky** výpisy tabulky obsah webové stránce HTML.
- **Konektor REST** konkrétně podpora kopírování dat z rozhraní RESTful API.
- **Konektor HTTP** je obecný k načtení dat z jakékoli koncového bodu HTTP, třeba ke stažení souboru. 

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z webové tabulky databáze do libovolné podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento Web tabulky konektor podporuje **extrahování obsahu tabulky ze stránky HTML**.

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít tento konektor Web table, budete muset nastavit modul Integration Runtime. Zobrazit [modul Integration Runtime](create-self-hosted-integration-runtime.md) , kde najdete podrobnosti.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní webový konektor tabulky.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Následující vlastnosti jsou podporovány pro tabulky webové propojené služby:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **Web** |Ano |
| url | Adresa URL k webovému zdroji |Ano |
| authenticationType. | Povolená hodnota je: **Anonymní**. |Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Modul Integration Runtime je povinný, jak je uvedeno v [požadavky](#prerequisites). |Ano |

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností, které podporuje webové datová sada tabulky.

Pro kopírování dat z tabulky Web, nastavte vlastnost typ datové sady na **WebTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na: **WebTable** | Ano |
| path |Relativní adresa URL k prostředku, který obsahuje tabulku. |Ne. Když není zadána cesta, použije se pouze adresu URL, které jsou určené v definici propojené služby. |
| index |Index tabulky ve zdroji. Zobrazit [Get index tabulky na stránce HTML](#get-index-of-a-table-in-an-html-page) najdete kroky pro získání indexu tabulky na stránce HTML. |Ano |

**Příklad:**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": {
            "referenceName": "<Web linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností, které podporuje webový zdroj tabulky.

### <a name="web-table-as-source"></a>Webová tabulka jako zdroj

Pro kopírování dat z tabulky Web, nastavte typ zdroje v aktivitě kopírování do **WebSource**, jsou podporovány žádné další vlastnosti.

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

## <a name="get-index-of-a-table-in-an-html-page"></a>Získat index tabulky na stránce HTML

Chcete-li získat index tabulky, které je potřeba nakonfigurovat v [vlastnosti datové sady](#dataset-properties), třeba Excelu 2016 můžete použít jako nástroj následujícím způsobem:

1. Spuštění **Excelu 2016** a přejděte **Data** kartu.
2. Klikněte na tlačítko **nový dotaz** na panelu nástrojů, přejděte na **z jiných zdrojů** a klikněte na tlačítko **z webu**.

    ![Nabídky Power Query](./media/copy-data-from-web-table/PowerQuery-Menu.png)
3. V **z webu** dialogového okna zadejte **URL** použijete v propojené službě JSON (například: https://en.wikipedia.org/wiki/) spolu s cestu, zadali byste pro datovou sadu (například: AFI % 27s_100_Years... 100_Movies) a klikněte na tlačítko **OK**.

    ![V dialogovém okně Web](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    Adresa URL používaná v tomto příkladu: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Pokud se zobrazí **přístup k webovému obsahu** dialogového okna, vyberte vpravo **adresy URL**, **ověřování**a klikněte na tlačítko **připojit**.

   ![Přístup k dialogové okno webového obsahu](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. Klikněte na tlačítko **tabulky** položky ve stromovém zobrazení na Zobrazit obsah z tabulky a pak klikněte na tlačítko **upravit** tlačítko dole.  

   ![Dialogové okno Navigátor](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. V **editoru dotazů** okna, klikněte na tlačítko **rozšířený Editor** tlačítko na panelu nástrojů.

    ![Tlačítko Upřesnit editoru](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. V dialogovém okně Upřesnit se číslo vedle "Zdroj" index.

    ![Rozšířený Editor - indexu](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

Pokud používáte Excel 2013, použijte [Microsoft Power Query pro Excel](https://www.microsoft.com/download/details.aspx?id=39379) získat index. Zobrazit [připojit k webové stránce](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) , kde najdete podrobnosti. Kroky jsou podobné, pokud používáte [Microsoft Power BI desktopu](https://powerbi.microsoft.com/desktop/).


## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
