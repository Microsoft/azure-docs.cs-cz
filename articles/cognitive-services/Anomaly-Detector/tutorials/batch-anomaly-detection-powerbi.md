---
title: 'Kurz: vizualizace anomálií pomocí zjišťování dávek a Power BI'
titleSuffix: Azure Cognitive Services
description: Naučte se používat rozhraní API detektoru anomálií a Power BI k vizualizaci anomálií v rámci vašich dat časových řad.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: fc2667dfddf4442acaf39f6783190e8640b22a89
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561725"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>Kurz: vizualizace anomálií pomocí zjišťování dávek a Power BI

Pomocí tohoto kurzu můžete najít anomálie v rámci datové sady časových řad jako dávku. Pomocí Power BI plochy převezmete excelový soubor, připravíte data pro rozhraní API detektoru anomálií a vizualizujete statistické anomálie v celém IT.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Import a transformace datové sady časových řad pomocí Power BI Desktop
> * Integrace Power BI Desktop s rozhraním API detektoru anomálií pro detekci anomálií služby Batch
> * Vizualizujte anomálie nalezené v rámci vašich dat, včetně očekávaných a zobrazených hodnot a hranic detekce anomálií.

## <a name="prerequisites"></a>Požadavky
* [Předplatné Azure](https://azure.microsoft.com/free/cognitive-services)
* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/get-started/), k dispozici zdarma.
* Excelový soubor (. xlsx), který obsahuje datové body časové řady. Ukázková data pro tento rychlý Start najdete na [GitHubu](https://go.microsoft.com/fwlink/?linkid=2090962) .
* Jakmile budete mít předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" vytvořte prostředek pro detekci anomálií "  target="_blank"> vytvořením prostředku detektoru anomálií </a> v Azure Portal, abyste získali svůj klíč a koncový bod.
    * K připojení aplikace k rozhraní API detektoru anomálií budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Provedete to později v rychlém startu.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>Načtení a formátování dat časových řad

Začněte tím, že otevřete Power BI Desktop a načtete data časových řad, která jste stáhli z požadovaných součástí. Tento excelový soubor obsahuje řadu koordinovaných časových razítek a hodnot UTC (Universal Time).  

> [!NOTE]
> Power BI můžou používat data z nejrůznějších zdrojů, jako jsou například soubory. csv, databáze SQL, úložiště objektů blob Azure a další.  

V hlavním Power BI Desktop okně klikněte na pás karet **Domů** . Ve skupině **externí data** na pásu karet otevřete rozevírací nabídku **získat data** a klikněte na možnost **Excel**.

![Obrázek tlačítka získat data v Power BI](../media/tutorials/power-bi-get-data-button.png)

Po zobrazení dialogového okna přejděte do složky, do které jste stáhli soubor example. xlsx a vyberte ho. Po zobrazení dialogu **navigátor** klikněte na **List1** a pak na **Upravit**.

![Obrázek obrazovky datového zdroje "navigátor" v Power BI](../media/tutorials/navigator-dialog-box.png)

Power BI převede časová razítka v prvním sloupci na `Date/Time` datový typ. Tato časová razítka je nutné převést na text, aby bylo možné je odeslat do rozhraní API detektoru anomálií. Pokud se Editor Power Query automaticky neotevře, klikněte na tlačítko **Upravit dotazy** na kartě Domů.

V editoru Power Query klikněte na pás karet **transformace** . Ve skupině **libovolný sloupec** otevřete položku **datový typ:** rozevírací nabídka a vyberte **text**.

![Obrázek rozevíracího seznamu datového typu](../media/tutorials/data-type-drop-down.png)

Po zobrazení oznámení o změně typu sloupce klikněte na **Nahradit aktuální**. Pak klikněte na tlačítko **zavřít & použít** nebo **použít** na pásu karet **Domů** .

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>Vytvoření funkce pro odeslání dat a naformátování odpovědi

K naformátování a odeslání datového souboru do rozhraní API detektoru anomálií můžete vyvolat dotaz na tabulku vytvořenou výše. V editoru Power Query v pásu karet **Domů** otevřete rozevírací nabídku **nový zdroj** a klikněte na příkaz **prázdný dotaz**.

Ujistěte se, že je vybraný nový dotaz, a pak klikněte na **Rozšířený editor**.

![Obrázek obrazovky Rozšířený editor](../media/tutorials/advanced-editor-screen.png)

V Rozšířený editor použijte následující fragment kódu Power Query M k extrakci sloupců z tabulky a jejich odeslání do rozhraní API. Následně dotaz vytvoří tabulku z odpovědi JSON a vrátí ji. Nahraďte `apiKey` proměnnou platným klíčem rozhraní API detektoru anomálií a `endpoint` vaším koncovým bodem. Po zadání dotazu do Rozšířený editor klikněte na **Hotovo**.

```M
(table as table) => let

    apikey      = "[Placeholder: Your Anomaly Detector resource access key]",
    endpoint    = "[Placeholder: Your Anomaly Detector resource endpoint]/anomalydetector/v1.0/timeseries/entire/detect",
    inputTable = Table.TransformColumnTypes(table,{{"Timestamp", type text},{"Value", type number}}),
    jsontext    = Text.FromBinary(Json.FromValue(inputTable)),
    jsonbody    = "{ ""Granularity"": ""daily"", ""Sensitivity"": 95, ""Series"": "& jsontext &" }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Content-Type" = "application/json", #"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody, ManualStatusHandling={400}]),
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

Vyvolejte dotaz na datovou tabulku výběrem `Sheet1` níže uvedeného **parametru** a kliknutím na **vyvolat**.

![Obrázek funkce Invoke](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>Soukromí a ověřování zdroje dat

> [!NOTE]
> Uvědomte si zásady vaší organizace na ochranu osobních údajů a přístup k datům. Další informace najdete v tématu [Power BI Desktop úrovně ochrany osobních údajů](/power-bi/desktop-privacy-levels) .

Při pokusu o spuštění dotazu se může zobrazit zpráva s upozorněním, že se používá externí zdroj dat.

![Obrázek ukazující upozornění vytvořené nástrojem Power BI](../media/tutorials/blocked-function.png)

Pokud to chcete opravit, klikněte na **soubor** a vyberte **Možnosti a nastavení**. Pak klikněte na **Možnosti**. Pod **aktuálním souborem** vyberte možnost **soukromí** a **ignorujte úroveň ochrany osobních údajů a potenciálně Vylepšete výkon**.

Kromě toho se může zobrazit zpráva s výzvou, abyste určili, jak se chcete připojit k rozhraní API.

![Obrázek ukazující požadavek na zadání přihlašovacích údajů pro přístup](../media/tutorials/edit-credentials-message.png)

Pokud to chcete opravit, klikněte ve zprávě na **Upravit přihlašovací údaje** . Po zobrazení dialogového okna vyberte **anonymní** a připojte se k rozhraní API anonymně. Pak klikněte na **Connect** (Připojit).

Potom kliknutím na **zavřít & použít** na pásu karet **Domů** , aby se změny projevily.

## <a name="visualize-the-anomaly-detector-api-response"></a>Vizualizace odpovědi rozhraní API detektoru anomálií

Na hlavní obrazovce Power BI začněte používat dotazy vytvořené výše k vizualizaci dat. Nejprve vyberte **Spojnicový graf** v **vizualizacích**. Pak přidejte časové razítko z vyvolané funkce na **osu** čárového grafu. Klikněte na něj pravým tlačítkem myši a vyberte **časové razítko**.

![Kliknutí pravým tlačítkem na hodnotu časového razítka](../media/tutorials/timestamp-right-click.png)

Do pole **hodnoty** v grafu přidejte následující pole z **vyvolané funkce** . K vytvoření grafu můžete použít následující snímek obrazovky.

* Hodnota
* UpperMargins
* LowerMargins
* ExpectedValues

![Obrázek nastavení grafu](../media/tutorials/chart-settings.png)

Po přidání polí klikněte na graf a změňte jeho velikost tak, aby se zobrazily všechny datové body. Graf bude vypadat podobně jako na následujícím snímku obrazovky:

![Obrázek vizualizace grafu](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>Zobrazit datové body anomálií

Na pravé straně okna Power BI pod podoknem **pole** klikněte pravým tlačítkem myši na **hodnotu** pod **vyvolaným dotazem funkce** a klikněte na možnost **Nová rychlá míra**.

![Obrázek nové obrazovky rychlé míry](../media/tutorials/new-quick-measure.png)

Na obrazovce, která se zobrazí, vyberte **filtrovaná hodnota** jako výpočet. Nastavte **základní hodnotu** na `Sum of Value` . Potom přetáhněte `IsAnomaly` z **vyvolaných polí funkce** na **Filtr**. `True`V rozevírací nabídce **Filtr** vyberte.

![Druhý obrázek nové obrazovky rychlá míra](../media/tutorials/new-quick-measure-2.png)

Po kliknutí na **OK** budete mít pole na konci `Value for True` seznamu polí. Klikněte na něj pravým tlačítkem a přejmenujte ho na **anomálii**. Přidejte ho do **hodnot** grafu. Pak vyberte nástroj pro **formátování** a nastavte typ osy X na **kategorií**.

![Obrázek formátu osy x](../media/tutorials/format-x-axis.png)

Nastavte barvy na svůj graf kliknutím na možnost **Formátovat** nástroj a **barvy dat**. Váš graf by měl vypadat nějak takto:

![Obrázek finálního grafu](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
>[Detekce anomálií streamování pomocí Azure Databricks](../overview.md)