---
title: Vizualizace pomocí Power BI a služby batch zjišťování anomálií
titlesuffix: Azure Cognitive Services
description: Použijte rozhraní API detekce anomálií a Power BI k vizualizaci anomálie v rámci vašich datech časových řad.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 04/30/2019
ms.author: aahi
ms.openlocfilehash: 48257782bd71f6145adc7107a5fe70b4cc4f8305
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790440"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>Kurz: Vizualizace pomocí Power BI a služby batch zjišťování anomálií

Pokud chcete vyhledat anomálie v rámci sady dat řady čas v dávce pomocí tohoto kurzu. Pomocí Power BI desktopu, bude trvat Excelový soubor, připravit data pro rozhraní API detekce anomálií a vizualizovat statistických anomálií v jeho průběhu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Můžete importovat a transformovat čas řady datové sady Power BI Desktopu
> * Integrace Power BI Desktopu s rozhraním API detekce anomálií pro detekci anomálií služby batch
> * Vizualizujte zjištěných ve vašich datech, včetně očekávaný a v zobrazené hodnoty a hranice detekce anomálií.

## <a name="prerequisites"></a>Požadavky

* [Microsoft Power BI Desktopu](https://powerbi.microsoft.com/get-started/), která je dostupná zdarma.
* Odkazuje excelový soubor (.xlsx) obsahující data časových řad. Ukázková data pro účely tohoto rychlého startu můžete najít na [Githubu](https://go.microsoft.com/fwlink/?linkid=2090962)

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>Zatížení a formát data časových řad

Abyste mohli začít, otevřete Power BI Desktopu a načíst data časových řad, kterou jste stáhli z požadavků. Tento excelový soubor obsahuje řadu koordinovaný univerzální čas (UTC) časové razítko a párů hodnot.  

> [!NOTE]
> Power BI můžete použít data z nejrůznějších zdrojů, jako je například souborů .csv s daty, SQL databáze, úložiště objektů blob v Azure a další.  

V hlavním okně Power BI Desktopu klikněte na tlačítko **Domů** pásu karet. V **externí data** skupiny pásu karet, otevřete **získat Data** rozevírací nabídky a klikněte na tlačítko **Excel**.

![Obrázek na tlačítko "Získat Data" v Power BI](../media/tutorials/power-bi-get-data-button.png)

Po zobrazení dialogového okna, přejděte do složky, kam jste stáhli soubor XLSX příklad a vyberte ji. Po **Navigátor** dialogu klikněte na tlačítko **List1**a potom **upravit**.

![Snímek obrazovky "Navigátor" zdroje dat v Power BI](../media/tutorials/navigator-dialog-box.png)

Power BI převede časová razítka v prvním sloupci `Date/Time` datového typu. Tato časová razítka musí být převeden na text, aby mohla být odeslána do rozhraní API detekce anomálií. Editor Power Query neotevře automaticky, klikněte na tlačítko **upravit dotazy** na kartě Domů. 

Klikněte na tlačítko **transformace** pásu karet v editoru Power Query. V **libovolný sloupec** skupinu, otevřete **datový typ:** rozevírací nabídky a vybereme **Text**.

![Snímek obrazovky "Navigátor" zdroje dat v Power BI](../media/tutorials/data-type-drop-down.png)

Když dostanete oznámení o změně typ sloupce, klikněte na tlačítko **nahradit aktuální**. Později, klikněte na tlačítko **zavřít a použít** nebo **použít** v **Domů** pásu karet. 

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>Vytvoření funkce pro odesílání dat a formátování odpovědi

Chcete-li naformátuje a odešle datový soubor do rozhraní API detekce anomálií, vyvoláte dotazu na tabulky vytvořené výše. V editoru Power Query z **Domů** pásu karet, otevřete **nový zdroj** rozevírací nabídky a klikněte na tlačítko **prázdný dotaz**.

Ujistěte se, že je vybraný nový dotaz a pak klikněte na **rozšířený Editor**. 

![Obrázek na tlačítko "Rozšířeného editoru" v Power BI](../media/tutorials/advanced-editor-screen.png)

V rámci rozšířeného editoru pomocí následujícího fragmentu kódu Power Query M extrahujte sloupce z tabulky a jejich odesílání do rozhraní API. Dotaz později, se vytvoří tabulku ze odpověď JSON a vrátit zpět. Nahradit `apiKey` proměnné s platný klíč rozhraní API detekce anomálií, a `endpoint` s váš koncový bod. Po zadání dotazu do rozšířeného editoru, klikněte na tlačítko **provádí**.

```M
(table as table) => let

    apikey      = "[Placeholder: Your Anomaly Detector resource access key]",
    endpoint    = "[Placeholder: Your Anomaly Detector resource endpoint]/anomalydetector/v1.0/timeseries/entire/detect,
    inputTable = Table.TransformColumnTypes(table,{{"Timestamp", type text},{"Value", type number}}),
    jsontext    = Text.FromBinary(Json.FromValue(inputTable)),
    jsonbody    = "{ ""Granularity"": ""daily"", ""Sensitivity"": 95, ""Series"": "& jsontext &" }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Content-Type" = "application/json", #"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),

    respTable = Table.FromColumns({
                    
                     Table.Column(inputTable, "Timestamp")
                     ,Table.Column(inputTable, "Value")
                     , Record.Field(jsonresp, "IsAnomaly") as list
                     , Record.Field(jsonresp, "ExpectedValues") as list
                     , Record.Field(jsonresp, "UpperMargins")as list
                     , Record.Field(jsonresp, "LowerMargins") as list
                     , Record.Field(jsonresp, "IsPositiveAnomaly") as list
                     , Record.Field(jsonresp, "IsNegativeAnomaly") as list

                  }, {"Timestamp", "Value", "IsAnomaly", "ExpectedValues", "UpperMargin", "LowerMargin", "IsPositiveAnomaly", "IsNegativeAnomaly"}
               ),
    
    respTable1 = Table.AddColumn(respTable , "UpperMargins", (row) => row[ExpectedValues] + row[UpperMargin]),
    respTable2 = Table.AddColumn(respTable1 , "LowerMargins", (row) => row[ExpectedValues] -  row[LowerMargin]),
    respTable3 = Table.RemoveColumns(respTable2, "UpperMargin"),
    respTable4 = Table.RemoveColumns(respTable3, "LowerMargin"),

    results = Table.TransformColumnTypes(

                respTable4,
                {{"Timestamp", type datetime}, {"Value", type number}, {"IsAnomaly", type logical}, {"IsPositiveAnomaly", type logical}, {"IsNegativeAnomaly", type logical},
                 {"ExpectedValues", type number}, {"UpperMargins", type number}, {"LowerMargins", type number}}
              )

 in results
```

Vyvolání dotaz na informační list tak, že vyberete `Sheet1` níže **zadejte parametr**a klikněte na tlačítko **Invoke**. 

![Obrázek na tlačítko "Rozšířeného editoru"](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>Ochrana osobních údajů zdroje dat a ověřování

> [!NOTE]
> Mějte na zásady vaší organizace pro přístup k a ochrany osobních údajů. Zobrazit [úrovně ochrany osobních údajů Power BI Desktopu](https://docs.microsoft.com/power-bi/desktop-privacy-levels) Další informace.

Při pokusu o spuštění dotazu, protože využívá externího zdroje dat se může zobrazit zpráva s upozorněním. 

![Obrázek znázorňující upozornění vytvořené pomocí Power BI](../media/tutorials/blocked-function.png)

Chcete-li opravit, klikněte na tlačítko **souboru**, a **možnosti a nastavení**. Pak klikněte na tlačítko **možnosti**. Pod **aktuální soubor**vyberte **ochrany osobních údajů**, a **ignorovat úrovně ochrany osobních údajů a potenciálně tak vylepšit výkon**. 

Kromě toho může zobrazit zpráva s výzvou k určení, jak chcete připojit k rozhraní API.

![Obrázek znázorňující žádost zadat přihlašovací údaje pro přístup](../media/tutorials/edit-credentials-message.png)

Chcete-li opravit, klikněte na tlačítko **upravit přihlašovací údaje** ve zprávě. Po dokončení se zobrazí dialogové okno, vyberte **anonymní** anonymního připojení k rozhraní API. Pak klikněte na **Connect** (Připojit). 

Později, klikněte na tlačítko **zavřít a použít** v **Domů** pásu karet, aby se změny projevily.

## <a name="visualize-the-anomaly-detector-api-response"></a>Vizualizujte odpovědi rozhraní API detekce anomálií

Na hlavní obrazovce Power BI začněte používat dotazy k vizualizaci dat vytvořili výše. Nejprve vyberte **spojnicový graf** v **vizualizace**. Pak přidejte časové razítko z volaná funkce do spojnicového grafu **osy**. Klepněte na něj pravým tlačítkem myši a vyberte **časové razítko**. 

![Pravým tlačítkem myši na hodnotu časového razítka](../media/tutorials/timestamp-right-click.png)

Přidejte následující pole z **vyvolána funkce** v grafu **hodnoty** pole. Používá následující snímek obrazovky, která pomáhá začlenit grafu.

    * Hodnota
    * UpperMargins
    * LowerMargins
    * ExpectedValues

![Obrázek obrazovky nová rychlá míra](../media/tutorials/chart-settings.png)

Po přidání polí, klikněte na graf a velikost tak, aby zobrazit všechny datové body. Graf bude vypadat podobně jako následující snímek obrazovky:

![Obrázek obrazovky nová rychlá míra](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>Zobrazení anomálií datových bodů

Na pravé straně okna Power BI pod **pole** podokně klikněte pravým tlačítkem na **hodnotu** pod **vyvolána funkce dotazu**a klikněte na tlačítko **nové rychlé míra**.

![Obrázek obrazovky nová rychlá míra](../media/tutorials/new-quick-measure.png)

Na obrazovce, která se zobrazí, vyberte **filtrovat hodnotu** jako výpočet. Nastavte **základní hodnotu** k `Sum of Value`. Přetáhněte `IsAnomaly` z **vyvolána funkce** polím **filtr**. Vyberte `True` z **filtr** rozevírací nabídky.

![Obrázek obrazovky nová rychlá míra](../media/tutorials/new-quick-measure-2.png)

Po kliknutí na tlačítko **Ok**, budete mít `Value for True` pole, v dolní části seznamu polí. Pravým tlačítkem myši a přejmenujte ho na **anomálií**. Přidejte do grafu **hodnoty**. Vyberte **formátu** nástrojů a nastavte typ osy x **zařazené do kategorií**.

![Obrázek obrazovky nová rychlá míra](../media/tutorials/format-x-axis.png)

Kliknutím na použít barvy grafu **formátu** nástroj a **barvy dat**. Graf by měl vypadat přibližně takto:

![Obrázek obrazovky nová rychlá míra](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
>[Streamování detekce anomálií pomocí služby Azure Databricks](anomaly-detection-streaming-databricks.md)
