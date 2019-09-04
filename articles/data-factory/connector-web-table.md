---
title: Kopírovat data z webové tabulky pomocí Azure Data Factory | Microsoft Docs
description: Přečtěte si o konektoru webových tabulek Azure Data Factory, který umožňuje kopírovat data z webové tabulky do úložišť dat podporovaných Data Factory jako jímky.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 0ee573f26f717c0ed8ad36f1a06fe557d423e3c1
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274716"
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>Kopírování dat z webové tabulky pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-web-table-connector.md)
> * [Aktuální verze](connector-web-table.md)

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z databáze webové tabulky. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

Rozdíl mezi tímto konektorem webové tabulky, [konektorem REST](connector-rest.md) a [konektorem http](connector-http.md) :

- **Konektor webové tabulky** extrahuje obsah tabulky z webové stránky HTML.
- **Konektor REST** podporuje zejména kopírování dat z rozhraní API RESTful.
- **Konektor http** je obecný k načtení dat z libovolného koncového bodu http, třeba ke stažení souboru. 

## <a name="supported-capabilities"></a>Podporované funkce

Data z databáze webové tabulky můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor webové tabulky podporuje **extrakci obsahu tabulky ze stránky HTML**.

## <a name="prerequisites"></a>Požadavky

Chcete-li použít tento konektor webové tabulky, je třeba nastavit Integration Runtime v místním prostředí. Zobrazit [modul Integration Runtime](create-self-hosted-integration-runtime.md) , kde najdete podrobnosti.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro konektor webové tabulky.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu webové tabulky jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na: **Web** |Ano |
| url | Adresa URL webového zdroje |Ano |
| authenticationType | Povolená hodnota je: **Anonymous**. |Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Integration Runtime v místním prostředí se vyžaduje, jak je uvedeno v [požadavcích](#prerequisites). |Ano |

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. V této části najdete seznam vlastností podporovaných datovou sadou webových tabulek.

Chcete-li kopírovat data z webové tabulky, nastavte vlastnost typ datové sady na **webtable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na: **WebTable** | Ano |
| path |Relativní adresa URL k prostředku, který obsahuje tabulku. |Ne. Pokud cesta není zadaná, použije se jenom adresa URL zadaná v definici propojené služby. |
| index |Index tabulky v prostředku Postup pro získání indexu tabulky na stránce HTML najdete v části [získání indexu tabulky v oddílu stránky HTML](#get-index-of-a-table-in-an-html-page) . |Ano |

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. V této části najdete seznam vlastností podporovaných zdrojem webové tabulky.

### <a name="web-table-as-source"></a>Webová tabulka jako zdroj

Pokud chcete kopírovat data z webové tabulky, nastavte typ zdroje v aktivitě kopírování na **websource**, žádné další vlastnosti se nepodporují.

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

Chcete-li získat index tabulky, kterou potřebujete nakonfigurovat ve [vlastnostech datové sady](#dataset-properties), můžete jako nástroj použít například Excel 2016, a to následujícím způsobem:

1. Spusťte **Excel 2016** a přepněte na kartu **data** .
2. Na panelu nástrojů klikněte na **Nový dotaz** , přejděte na **z jiných zdrojů** a klikněte na **z webu**.

    ![Nabídka Power Query](./media/copy-data-from-web-table/PowerQuery-Menu.png)
3. V dialogovém okně **z webu** zadejte **adresu URL** , kterou použijete v kódu JSON propojené služby (například: https://en.wikipedia.org/wiki/) společně s cestou, kterou zadáte pro datovou sadu (například: AFI%27s_100_Years... 100_Movies) a klikněte na **OK**.

    ![Z dialogového okna Web](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    Adresa URL použitá v tomto příkladu: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Pokud se zobrazí dialogové okno **přístup k webovému obsahu** , vyberte správnou **adresu URL**, **ověřování**a klikněte na **připojit**.

   ![Přístup k webovému obsahu – dialogové okno](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. Kliknutím na položku **tabulky** ve stromovém zobrazení zobrazíte obsah z tabulky a potom v dolní části kliknete na tlačítko **Upravit** .  

   ![Dialog navigátor](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. V okně **Editor dotazů** klikněte na panelu nástrojů na tlačítko **Rozšířený editor** .

    ![Rozšířený editor – tlačítko](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. V dialogovém okně Rozšířený editor je číslo vedle možnosti "zdroj" index.

    ![Rozšířený editor – index](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

Pokud používáte Excel 2013, použijte k získání indexu [Microsoft Power Query pro Excel](https://www.microsoft.com/download/details.aspx?id=39379) . Podrobnosti najdete v článku [připojení k webové stránce](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) . Postup je podobný, pokud používáte [Microsoft Power BI pro Desktop](https://powerbi.microsoft.com/desktop/).


## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
