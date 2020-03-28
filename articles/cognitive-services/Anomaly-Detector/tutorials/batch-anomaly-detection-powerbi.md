---
title: 'Kurz: Vizualizace anomálií pomocí dávkové detekce a Power BI'
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak pomocí rozhraní API detektoru anomálií a Power BI vizualizovat anomálie v datech časových řad.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.openlocfilehash: 1b486aaf0ce33e31433c2c3d0f7a1ff2c7089132
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "78402663"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>Kurz: Vizualizace anomálií pomocí dávkové detekce a Power BI

Tento kurz slouží k vyhledání anomálií v rámci datové sady časových řad jako dávky. Pomocí desktopu Power BI vezmete excelový soubor, připravíte data pro rozhraní API detektoru anomálií a vizualizujete statistické anomálie v něm.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Import a transformace datové sady časových řad pomocí Power BI Desktopu
> * Integrace Power BI Desktopu s rozhraním API pro detektory anomálií pro detekci dávkových anomálií
> * Vizualizujte anomálie nalezené v rámci vašich dat, včetně očekávaných a zobrazených hodnot a hranic detekce anomálií.

## <a name="prerequisites"></a>Požadavky
* [Předplatné Azure](https://azure.microsoft.com/free/)
* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/get-started/), který je k dispozici zdarma.
* Soubor aplikace Excel (.xlsx) obsahující datové body časových řad. Ukázková data pro tento rychlý start lze nalézt na [GitHubu](https://go.microsoft.com/fwlink/?linkid=2090962)
* Jakmile budete mít předplatné <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Azure, vytvořte"  target="_blank">prostředek analýzy <span class="docon docon-navigate-external x-hidden-focus"></span> </a> textu vytvořit prostředek analýzy textu na webu Azure Portal, abyste získali klíč a koncový bod. 
    * Budete potřebovat klíč a koncový bod z prostředku, který vytvoříte pro připojení aplikace k rozhraní API analýzy textu. Uděláte to později v rychlém startu.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>Načtení a formátování dat časových řad

Pokud chcete začít, otevřete Power BI Desktop a načtěte data časových řad, která jste si stáhli z předpokladů. Tento soubor aplikace Excel obsahuje řadu časových razítek a dvojic hodnot koordinovaný světový čas (UTC).  

> [!NOTE]
> Power BI můžete používat data z celé řady zdrojů, jako jsou .csv soubory, SQL databáze, úložiště objektů blob Azure a další.  

V hlavním okně Power BI Desktop klikněte na pás karet **domů.** Ve skupině **Externí data** na pásu karet otevřete rozevírací nabídku **Získat data** a klikněte na **Excel**.

![Obrázek tlačítka Získat data v Power BI](../media/tutorials/power-bi-get-data-button.png)

Po zobrazení dialogového okna přejděte do složky, do které jste stáhli ukázkový soubor XLSX, a vyberte jej. Po otevření dialogového listu **Navigator** klepněte na **položku List1**a potom na **příkaz Upravit**.

![Obrázek obrazovky zdroje dat "Navigátor" v Power BI](../media/tutorials/navigator-dialog-box.png)

Power BI převede časová razítka v `Date/Time` prvním sloupci na datový typ. Tato časová razítka musí být převedeny na text, aby bylo možné odeslat do rozhraní API detektoru anomálií. Pokud se editor Power Query neotevře automaticky, klikněte na kartě Domů na **Upravit dotazy.** 

Klikněte na pás **karet Transformace** v Editoru Power Query. Ve skupině **Libovolný sloupec** otevřete rozevírací nabídku **Datový typ:** a vyberte **Text**.

![Obrázek obrazovky zdroje dat "Navigátor" v Power BI](../media/tutorials/data-type-drop-down.png)

Když se zobrazí oznámení o změně typu sloupce, klikněte na **Nahradit aktuální**. Potom klikněte na **pásu** karet **Domů** na Zavřít & použít nebo **Použít.** 

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>Vytvořit funkci pro odeslání dat a formátování odpovědi

Chcete-li formátovat a odeslat datový soubor do rozhraní API detektoru anomálií, můžete vyvolat dotaz v tabulce vytvořené výše. V Editoru Power Query otevřete na pásu karet **Domů** rozevírací nabídku **Nový zdroj** a klikněte na **Prázdný dotaz**.

Zkontrolujte, zda je vybrán nový dotaz, a klepněte na tlačítko **Upřesnit editor**. 

![Obrázek tlačítka Rozšířený editor v Power BI](../media/tutorials/advanced-editor-screen.png)

V rozšířeném editoru použijte následující fragment Power Query M k extrahování sloupců z tabulky a jeho odeslání do rozhraní API. Poté dotaz vytvoří tabulku z odpovědi JSON a vrátí ji. Nahraďte `apiKey` proměnnou platným klíčem rozhraní `endpoint` API detektoru anomálií a koncovým bodem. Po zadání dotazu do rozšířeného editoru klepněte na **tlačítko Hotovo**.

```M
(table as table) => let

    apikey      = "[Placeholder: Your Anomaly Detector resource access key]",
    endpoint    = "[Placeholder: Your Anomaly Detector resource endpoint]/anomalydetector/v1.0/timeseries/entire/detect",
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

Vyvolá dotaz v datovém listu `Sheet1` tak, že vyberete pod **položkou Zadejte parametr**a klepněte na tlačítko **Vyvolat**. 

![Obrázek tlačítka "Rozšířený editor"](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>Ochrana osobních údajů a ověřování zdroje dat

> [!NOTE]
> Mějte na paměti zásady vaší organizace týkající se ochrany osobních údajů a přístupu k nim. Další informace najdete v [tématu Úrovně ochrany osobních údajů na ploše Power BI.](https://docs.microsoft.com/power-bi/desktop-privacy-levels)

Při pokusu o spuštění dotazu se může zostávat varovná zpráva, protože využívá externí zdroj dat. 

![Obrázek s upozorněním vytvořeným Power BI](../media/tutorials/blocked-function.png)

Chcete-li tento problém vyřešit, klepněte na **položku Soubor**a **možnosti a nastavení**. Potom klepněte na tlačítko **Možnosti**. Pod **položkou Aktuální soubor**vyberte možnost Ochrana **osobních údajů**a Ignorovat úrovně ochrany **osobních údajů a potenciálně zlepšit výkon**. 

Kromě toho se může zobrazí zpráva s dotazem, jak chcete určit, jak se chcete připojit k rozhraní API.

![Obrázek zobrazující požadavek na zadání přístupových pověření](../media/tutorials/edit-credentials-message.png)

Chcete-li tento problém vyřešit, klikněte ve zprávě na **Upravit přihlašovací údaje.** Po zřizovacím poli se anonymně připojte k rozhraní API a vyberte **anonymní.** Pak klikněte na **Connect** (Připojit). 

Potom klikněte na **Zavřít & Použít** na pásu karet **Domů** a aplikujte změny.

## <a name="visualize-the-anomaly-detector-api-response"></a>Vizualizujte odpověď rozhraní API detektoru anomálií

Na hlavní obrazovce Power BI začněte vizualizovat data pomocí výše vytvořených dotazů. Nejprve vyberte **spojnicový graf** ve **vizualizacích**. Potom přidejte časové razítko z vztažné funkce do **osy**spojnicového grafu . Klikněte na něj pravým tlačítkem myši a vyberte **časové razítko**. 

![Kliknutí pravým tlačítkem myši na hodnotu časového razítka](../media/tutorials/timestamp-right-click.png)

Do pole **Hodnoty** grafu přidejte následující pole z **pole Vyvolaná funkce.** Pomocí níže uvedeného snímku obrazovky můžete vytvořit graf.

    * Hodnota
    * Horní okraje
    * Nižší marže
    * Očekávané hodnoty

![Obrázek nové obrazovky s rychlým měřením](../media/tutorials/chart-settings.png)

Po přidání polí klikněte na graf a změňte jeho velikost tak, aby se zobrazily všechny datové body. Graf bude vypadat podobně jako níže uvedený snímek obrazovky:

![Obrázek nové obrazovky s rychlým měřením](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>Zobrazení datových bodů anomálií

Na pravé straně okna Power BI pod podoknem **POLE** klikněte pravým tlačítkem myši na **hodnotu** v **dotazu Vyvolaná funkce**a klikněte na Nový rychlý **rozměr**.

![Obrázek nové obrazovky s rychlým měřením](../media/tutorials/new-quick-measure.png)

Na obrazovce, která se zobrazí, vyberte jako výpočet **filtrovaná hodnota.** Nastavte základní `Sum of Value` **hodnotu** na . Potom `IsAnomaly` tažením z polí **Vztažná funkce** **filtrujte**. Vyberte `True` z rozevírací nabídky **Filtr.**

![Obrázek nové obrazovky s rychlým měřením](../media/tutorials/new-quick-measure-2.png)

Po kliknutí na **tlačítko** `Value for True` Ok budete mít pole v dolní části seznamu polí. Klikněte na něj pravým tlačítkem myši a přejmenujte jej na **Anomálii**. Přidejte jej do **grafu hodnoty**. Pak vyberte nástroj **formát** a nastavte typ osy X na **Kategorický**.

![Obrázek nové obrazovky s rychlým měřením](../media/tutorials/format-x-axis.png)

Barvy v grafu můžete použít kliknutím na nástroj **Formát** a **Barvy dat**. Graf by měl vypadat nějak takto:

![Obrázek nové obrazovky s rychlým měřením](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
>[Streamování detekce anomálií pomocí Azure Databricks](anomaly-detection-streaming-databricks.md)
