---
title: Kopírování dat z webové tabulky pomocí Azure Data Factory
description: Přečtěte si o konektoru webových tabulek Azure Data Factory, který umožňuje kopírovat data z webové tabulky do úložišť dat podporovaných Data Factory jako jímky.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 1ab45868afd8d1b7ba1f61f5eaacca283817e6d6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100367006"
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>Kopírování dat z webové tabulky pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-web-table-connector.md)
> * [Aktuální verze](connector-web-table.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z databáze webové tabulky. Sestaví se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

Rozdíl mezi tímto konektorem webové tabulky, [konektorem REST](connector-rest.md) a [konektorem http](connector-http.md) :

- **Konektor webové tabulky** extrahuje obsah tabulky z webové stránky HTML.
- **Konektor REST** podporuje zejména kopírování dat z rozhraní API RESTful.
- **Konektor http** je obecný k načtení dat z libovolného koncového bodu http, třeba ke stažení souboru. 

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor webové tabulky se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)

Data z databáze webové tabulky můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje a jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Konkrétně tento konektor webové tabulky podporuje **extrakci obsahu tabulky ze stránky HTML**.

## <a name="prerequisites"></a>Předpoklady

Chcete-li použít tento konektor webové tabulky, je třeba nastavit Integration Runtime v místním prostředí. Podrobnosti najdete v článku [Integration runtime](create-self-hosted-integration-runtime.md) v místním prostředí.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro konektor webové tabulky.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu webové tabulky jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na: **Web** . |Yes |
| url | Adresa URL webového zdroje |Yes |
| authenticationType | Povolená hodnota je: **anonymní**. |Yes |
| connectVia | [Integration runtime](concepts-integration-runtime.md) , která se má použít pro připojení k úložišti dat Integration Runtime v místním prostředí se vyžaduje, jak je uvedeno v [požadavcích](#prerequisites). |Yes |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných datovou sadou webových tabulek.

Chcete-li kopírovat data z webové tabulky, nastavte vlastnost typ datové sady na **webtable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type datové sady musí být nastavená na: **webtable** . | Yes |
| program |Relativní adresa URL k prostředku, který obsahuje tabulku. |No. Pokud cesta není zadaná, použije se jenom adresa URL zadaná v definici propojené služby. |
| index |Index tabulky v prostředku Postup pro získání indexu tabulky na stránce HTML najdete v části [získání indexu tabulky v oddílu stránky HTML](#get-index-of-a-table-in-an-html-page) . |Yes |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných zdrojem webové tabulky.

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
3. V dialogovém okně **z webu** zadejte **adresu URL** , kterou použijete v kódu JSON propojené služby (například: https://en.wikipedia.org/wiki/) společně s cestou, kterou zadáte pro datovou sadu (například: AFI% 27s_100_Years... 100_Movies) a klikněte na **OK**.

    ![Z dialogového okna Web](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    Adresa URL použitá v tomto příkladu: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Pokud se zobrazí dialogové okno **přístup k webovému obsahu** , vyberte správnou **adresu URL**, **ověřování** a klikněte na **připojit**.

   ![Přístup k webovému obsahu – dialogové okno](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. Kliknutím na položku **tabulky** ve stromovém zobrazení zobrazíte obsah z tabulky a potom v dolní části kliknete na tlačítko **Upravit** .  

   ![Dialogové okno Navigátor](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. V okně **Editor dotazů** klikněte na panelu nástrojů na tlačítko **Rozšířený editor** .

    ![Rozšířený editor – tlačítko](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. V dialogovém okně Rozšířený editor je číslo vedle možnosti "zdroj" index.

    ![Rozšířený editor – index](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

Pokud používáte Excel 2013, použijte k získání indexu [Microsoft Power Query pro Excel](https://www.microsoft.com/download/details.aspx?id=39379) . Podrobnosti najdete v článku [připojení k webové stránce](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) . Postup je podobný, pokud používáte [Microsoft Power BI pro Desktop](https://powerbi.microsoft.com/desktop/).


## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
