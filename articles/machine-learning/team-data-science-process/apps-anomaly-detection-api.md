---
title: Azure Machine Learning API – vědecké zpracování týmových dat pro detekci anomálií
description: Rozhraní API pro detekci anomálií je příkladem vytvořených pomocí Microsoft Azure Machine Learning, který detekuje anomálie v datech časové řady s číselné hodnoty, které jsou rovnoměrně rozloženy v čase.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=alokkirpal, previous-ms.author=alok
ms.openlocfilehash: a09094cf0d1bd3c2e299e968d7de8410dcd9c3cb
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721876"
---
# <a name="machine-learning-anomaly-detection-api"></a>Ve službě Machine Learning rozhraní API pro detekci anomálií

> [!NOTE]
> Probíhá údržba této položky. Doporučujeme vám používat [službu rozhraní API](https://azure.microsoft.com/services/cognitive-services/anomaly-detector/) pro detekci anomálií, která využívá galerii Machine Learning algoritmů v Azure Cognitive Services ke zjištění anomálií z metrik obchodních, provozních a IoT.

## <a name="overview"></a>Přehled
[Rozhraní API pro detekci anomálií](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2) je příkladem vytvořené pomocí Azure Machine Learning, který detekuje anomálie v datech časové řady s číselné hodnoty, které jsou rovnoměrně rozloženy v čase.

Toto rozhraní API můžete detekovat následující typy anomálních vzorů v datech časové řady:

* **Pozitivní a negativní trendy**: například při monitorování využití paměti ve výpočetním prostředí stoupající trend může být zajímavé může být indikátorem nevracení paměti
* **Změny dynamických rozsahů hodnot**: například při monitorování výjimky vyvolané v cloudové službě, všechny změny dynamických rozsahů hodnot může znamenat nestabilitu ve službě health service, a
* **Špičky a poklesy**: například při monitorování počtu chyb přihlášení ve službě nebo počtu rezervací na webu elektronického obchodování, špičky nebo poklesy může znamenat neobvyklé chování.

Tyto detektory machine learning sledovat tyto změny hodnot v průběhu času a zprávy probíhající změny v jejich hodnoty jako anomálií skóre. Nevyžadují ladění ad hoc prahové hodnoty a jejich skóre, které lze použít k řízení míru falešně pozitivních výsledků. Detekce anomálií, rozhraní API je užitečné v několika situacích, jako je sledování služeb díky sledování klíčových ukazatelů výkonu v čase, monitorování využití prostřednictvím metriky, jako je počet hledání, počet kliknutí, monitorování výkonu prostřednictvím počítadla například paměť, procesor, načte soubor, atd. v čase.

Detekce anomálií nabídka obsahuje užitečných nástrojů, které vám pomůžou začít.

* [Webovou aplikaci](https://anomalydetection-aml.azurewebsites.net/) vám pomůže vyhodnotit a vizualizaci výsledků rozhraní API pro detekci anomálií na vašich datech.

> [!NOTE]
> Zkuste **řešení přehledů anomálií IT** technologii [toto rozhraní API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2)
>
<!-- This Solution is no longer available
> To get this end to end solution deployed to your Azure subscription <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">**Start here >**</a>
-->

## <a name="api-deployment"></a>Nasazení rozhraní API
Pokud chcete používat rozhraní API, musíte nasadit na vaše předplatné Azure, kde bude hostovaný jako webové služby Azure Machine Learning.  Udělat z [galerii Azure AI](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2).  Tím dojde k nasazení dvou Azure Machine Learning Studio (klasických) webových služeb (a jejich souvisejících prostředků) do předplatného Azure – jeden pro detekci anomálií s detekcí sezónnost a druhý bez detekce sezónnost.  Po dokončení nasazení budete moci spravovat vaše rozhraní API ze stránky [Azure Machine Learning Studio (klasické) webové služby](https://services.azureml.net/webservices/) .  Z této stránky budete moct najít vaše umístění koncových bodů, klíče rozhraní API, stejně jako ukázkový kód pro volání rozhraní API.  Podrobné pokyny jsou k dispozici [tady](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice).

## <a name="scaling-the-api"></a>Škálování rozhraní API
Ve výchozím nastavení bude mít vaše nasazení bezplatný fakturační plán pro vývoj/testování, který zahrnuje 1 000 transakcí za měsíc a 2 výpočetní hodiny/měsíc.  Můžete upgradovat na jiný plán, podle vašich potřeb.  Podrobnosti o cenách za různé plány jsou k dispozici [tady](https://azure.microsoft.com/pricing/details/machine-learning/) v části "Ceny za produkční webové rozhraní API".

## <a name="managing-aml-plans"></a>Správa AML plány
Můžete spravovat plán [tady](https://services.azureml.net/plans/).  Název plánu bude zakládat na název skupiny prostředků, kterou jste zvolili při nasazování rozhraní API a řetězec, který je jedinečný pro vaše předplatné.  Pokyny o tom, jak upgradovat plán jsou k dispozici [tady](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice) v části "Správa fakturační plány".

## <a name="api-definition"></a>Definice rozhraní API
Webová služba poskytuje rozhraní API založené na REST prostřednictvím protokolu HTTPS, které lze spotřebovat různými způsoby, včetně webové nebo mobilní aplikace, R, Pythonu, Excelu atd.  Data časové řady odešlete do této služby prostřednictvím volání REST API a spustí se kombinace tří typů anomálií popsaných níže.

## <a name="calling-the-api"></a>Volání rozhraní API
Aby bylo možné volat rozhraní API, musíte znát umístění koncového bodu a klíče rozhraní API.  Tyto dvě požadavky spolu s ukázkovým kódem pro volání rozhraní API jsou k dispozici na stránce [Azure Machine Learning Studio (klasické) webové služby](https://services.azureml.net/webservices/) .  Přejděte do požadovaného rozhraní API a potom klikněte na kartu "Využívání" je vyhledat.  Rozhraní API můžete volat jako rozhraní Swagger API (to znamená s parametrem URL `format=swagger`) nebo jako rozhraní API jiného typu než Swagger (to znamená bez parametru `format` adresy URL).  Vzorový kód používá formátu Swagger.  Níže je příklad žádosti a odpovědi ve formátu bez Swagger.  Tyto příklady jsou ke koncovému bodu sezónnosti.  Koncový bod není sezónnosti je podobné.

### <a name="sample-request-body"></a>Ukázkový text žádosti
Požadavek obsahuje dva objekty: `Inputs` a `GlobalParameters`.  V příkladu níže uvedenou žádost, některé parametry jsou odesílány explicitně některé nikoli (posuňte se dolů zobrazit úplný seznam parametrů pro každý koncový bod).  Parametry, které nejsou explicitně odeslány v žádosti budou používat výchozí hodnoty uvedena níže.

    {
                "Inputs": {
                        "input1": {
                                "ColumnNames": ["Time", "Data"],
                                "Values": [
                                        ["5/30/2010 18:07:00", "1"],
                                        ["5/30/2010 18:08:00", "1.4"],
                                        ["5/30/2010 18:09:00", "1.1"]
                                ]
                        }
                },
        "GlobalParameters": {
            "tspikedetector.sensitivity": "3",
            "zspikedetector.sensitivity": "3",
            "bileveldetector.sensitivity": "3.25",
            "detectors.spikesdips": "Both"
        }
    }

### <a name="sample-response"></a>Ukázková odezva
Aby se zobrazilo pole `ColumnNames`, musíte do žádosti zahrnout `details=true` jako parametr URL.  Najdete v následujících tabulkách pro význam za každé z těchto polí.

    {
        "Results": {
            "output1": {
                "type": "table",
                "value": {
                    "Values": [
                        ["5/30/2010 6:07:00 PM", "1", "1", "0", "0", "-0.687952590518378", "0", "-0.687952590518378", "0", "-0.687952590518378", "0"],
                        ["5/30/2010 6:08:00 PM", "1.4", "1.4", "0", "0", "-1.07030497733224", "0", "-0.884548154298423", "0", "-1.07030497733224", "0"],
                        ["5/30/2010 6:09:00 PM", "1.1", "1.1", "0", "0", "-1.30229513613974", "0", "-1.173800281031", "0", "-1.30229513613974", "0"]
                    ],
                    "ColumnNames": ["Time", "OriginalData", "ProcessedData", "TSpike", "ZSpike", "BiLevelChangeScore", "BiLevelChangeAlert", "PosTrendScore", "PosTrendAlert", "NegTrendScore", "NegTrendAlert"],
                    "ColumnTypes": ["DateTime", "Double", "Double", "Double", "Double", "Double", "Int32", "Double", "Int32", "Double", "Int32"]
                }
            }
        }
    }


## <a name="score-api"></a>Rozhraní API skóre
Rozhraní API skóre se používá pro spuštění detekce anomálií v datech-sezónní časových řad. Rozhraní API spouští řadu detektory anomálií na data a vrátí jejich skóre anomálií.
Následující obrázek znázorňuje příklad anomálie, které dokáží detekovat rozhraní API skóre. Tato časová řada má dvě různé změny úrovně a tři špičky. Červené tečky zobrazit čas, kdy je zjištěna změna úrovně, zatímco černé tečky zobrazit zjištěné provozní špičky.
![Rozhraní API skóre][1]

### <a name="detectors"></a>Detektory
Rozhraní API pro detekci anomálií podporuje detektory ve třech hlavních kategoriích. Podrobnosti o konkrétní vstupních parametrů a výstupů pro každou detektor najdete v následující tabulce.

| Detektor kategorie | Detektor | Popis | Vstupní parametry | Výstupy |
| --- | --- | --- | --- | --- |
| Detektory zásobníku |Detektor TSpike |Zjistit provozní špičky a vyhrazené IP adresy na základě hodnot daleko jsou od první a třetí Kvartily |*tspikedetector.Sensitivity:* přebírá hodnotu celého čísla v rozsahu 1-10, výchozí: 3; Vyšší hodnoty zachytí další extrémní hodnoty, díky čemuž míň citlivými informacemi |TSpike: binární hodnoty – 1, pokud se zjistí zásobníku/dip, '0' jinak |
| Detektory zásobníku | Detektor ZSpike |Zjistit provozní špičky a vyhrazené IP adresy na základě toho, jak daleko se datapoints od jejich střední hodnoty |*zspikedetector.Sensitivity:* trvat celočíselnou hodnotu v rozsahu 1 až 10, výchozí: 3; Vyšší hodnoty zachytí další extrémní hodnoty díky tomu je míň citlivými informacemi |ZSpike: binární hodnoty – 1, pokud se zjistí zásobníku/dip, '0' jinak |
| Pomalé Trend Detectoru |Pomalé Trend Detectoru |Zjišťování pomalého pozitivní trend podle nastavení citlivosti |*trenddetector. Citlivost:* prahová hodnota skóre detektoru (výchozí: 3,25, 3,25 – 5 je přiměřený rozsah, ze kterého lze vybírat; Čím vyšší je méně citlivá) |tscore: číslo s plovoucí představující hodnocení anomálie na trend |
| Změna úrovně detektory | Úroveň obousměrného změnu Detectoru |Zjištění Změna úrovně nahoru i dolů podle nastavení citlivosti |*bileveldetector. Citlivost:* prahová hodnota skóre detektoru (výchozí: 3,25, 3,25 – 5 je přiměřený rozsah, ze kterého lze vybírat; Čím vyšší je méně citlivá) |rpscore: číslo s plovoucí představující hodnocení anomálie při změně úrovně nahoru a dolů |

### <a name="parameters"></a>Parametry
Podrobnější informace o těchto vstupní parametry jsou uvedeny v následující tabulce:

| Vstupní parametry | Popis | Výchozí nastavení | Typ | Platný rozsah | Navrhované rozsahu |
| --- | --- | --- | --- | --- | --- |
| detectors.historywindow |Historie (v počet datových bodů), které jsou používány pro výpočty skóre anomálií |500 |celé číslo |10-2000 |Závislé časových řad |
| detectors.spikesdips | Jestli se má zjišťovat pouze špičky, jenom vyhrazené IP adresy, nebo obojí |Obojí |ve výčtu |Obě špičky, vyhrazené IP adresy |Obojí |
| bileveldetector.Sensitivity |Citlivost na úroveň obousměrného změnit detektoru. |3.25 |double |Žádné |3,25-5 (nižší hodnoty znamenají citlivější) |
| trenddetector.Sensitivity |Citlivost na detektor pozitivní trend. |3.25 |double |Žádné |3,25-5 (nižší hodnoty znamenají citlivější) |
| tspikedetector.Sensitivity |Citlivost na TSpike Detectoru |3 |celé číslo |1-10 |3 – 5 (nižší hodnoty znamenají citlivější) |
| zspikedetector.Sensitivity |Citlivost na ZSpike Detectoru |3 |celé číslo |1-10 |3 – 5 (nižší hodnoty znamenají citlivější) |
| postprocess.tailRows |Počet nejnovější datové body uchovávat ve výsledcích výstupu |0 |celé číslo |0 (zachovat všechny datové body), nebo zadejte počet bodů, aby se ve výsledcích |Nevztahuje se |

### <a name="output"></a>Výstup
Rozhraní API pro všechny detektory běží na vašich datech časových řad a vrátí skóre anomálií a indikátory binární zásobníku pro každý bod v čase. Následující tabulka uvádí výstupů z rozhraní API.

| Výstupy | Popis |
| --- | --- |
| Time |Časové razítko z nezpracovaných dat nebo data agregovaná (a/nebo) imputované Pokud agregace (a/nebo) chybí imputace dat se použije. |
| Data |Pokud hodnoty z nezpracovaných dat nebo data agregovaná (a/nebo) imputované agregace (a/nebo) chybí imputace data platí |
| TSpike |Binární indikátor označující, zda je prudký nárůst zjištěných TSpike Detectoru |
| ZSpike |Binární indikátor označující, zda je prudký nárůst zjištěných ZSpike Detectoru |
| rpscore |Při změně úrovně obousměrné skóre plovoucí desetinné číslo představující anomálií |
| rpalert |Hodnota 1/0 označující, že je úroveň obousměrného změnit anomálií na základě vstupní citlivosti |
| tscore |Plovoucí desetinné číslo představující anomálií skóre na kladné trend |
| talert |Hodnota 1/0 označující, že je pozitivní trend anomálií na základě citlivosti vstupní |

## <a name="scorewithseasonality-api"></a>ScoreWithSeasonality rozhraní API
Rozhraní API ScoreWithSeasonality se používá pro spuštění detekce anomálií v časové řadě, které mají sezónní vzory. Toto rozhraní API je užitečný ke zjišťování odchylek sezónní vzory.
Následující obrázek znázorňuje příklad zjištěných v sezónní časové řady. Časová řada má jednu špičku (první černou tečka), dvě DIP (druhá černá tečka a jedna na konci) a jednu změnu úrovně (červenou tečkou). DIP uprostřed časové řady a změna úrovně se discernable jenom po odebrání sezónních komponent z řady.
![Sezónnost rozhraní API][2]

### <a name="detectors"></a>Detektory
Detektory sezónnosti koncový bod se podobají těm, které jsou v koncovém bodě bez sezónnost, ale s mírně různé názvy parametrů (uvedené níže).

### <a name="parameters"></a>Parametry

Podrobnější informace o těchto vstupní parametry jsou uvedeny v následující tabulce:

| Vstupní parametry | Popis | Výchozí nastavení | Typ | Platný rozsah | Navrhované rozsahu |
| --- | --- | --- | --- | --- | --- |
| preprocess.aggregationInterval |Agregační interval v sekundách pro agregaci vstupní časové řady |0 (neprovádí žádné agregace) |celé číslo |0: v opačném případě přeskočte agregace > 0 |5 minut, než 1 den, závislé na časových řad |
| preprocess.aggregationFunc |Funkce použitá pro agregaci dat do zadaného AggregationInterval |střední hodnotu |ve výčtu |Střední, sum a délka |Nevztahuje se |
| preprocess.replaceMissing |Hodnoty použité k dává chybějící data |lkv (poslední známá hodnota) |ve výčtu |nula, lkv, střední hodnotu |Nevztahuje se |
| detectors.historywindow |Historie (v počet datových bodů), které jsou používány pro výpočty skóre anomálií |500 |celé číslo |10-2000 |Závislé časových řad |
| detectors.spikesdips | Jestli se má zjišťovat pouze špičky, jenom vyhrazené IP adresy, nebo obojí |Obojí |ve výčtu |Obě špičky, vyhrazené IP adresy |Obojí |
| bileveldetector.Sensitivity |Citlivost na úroveň obousměrného změnit detektoru. |3.25 |double |Žádné |3,25-5 (nižší hodnoty znamenají citlivější) |
| postrenddetector.Sensitivity |Citlivost na detektor pozitivní trend. |3.25 |double |Žádné |3,25-5 (nižší hodnoty znamenají citlivější) |
| negtrenddetector.Sensitivity |Citlivost na detektor negativní trend. |3.25 |double |Žádné |3,25-5 (nižší hodnoty znamenají citlivější) |
| tspikedetector.Sensitivity |Citlivost na TSpike Detectoru |3 |celé číslo |1-10 |3 – 5 (nižší hodnoty znamenají citlivější) |
| zspikedetector.Sensitivity |Citlivost na ZSpike Detectoru |3 |celé číslo |1-10 |3 – 5 (nižší hodnoty znamenají citlivější) |
| seasonality.enable |Určuje, zda se má provést analýzu sezónnosti |true |Boolean |Hodnota TRUE, false |Závislé časových řad |
| seasonality.numSeasonality |Maximální počet cyklů pravidelné detekovanou |1\. místo |celé číslo |1, 2 |1-2 |
| seasonality.Transform |Zda sezónní (a) trend součásti se musí odebrat před použitím detekce anomálií |deseason |ve výčtu |NONE, deseason, deseasontrend |Nevztahuje se |
| postprocess.tailRows |Počet nejnovější datové body uchovávat ve výsledcích výstupu |0 |celé číslo |0 (zachovat všechny datové body), nebo zadejte počet bodů, aby se ve výsledcích |Nevztahuje se |

### <a name="output"></a>Výstup
Rozhraní API pro všechny detektory běží na vašich datech časových řad a vrátí skóre anomálií a indikátory binární zásobníku pro každý bod v čase. Následující tabulka uvádí výstupů z rozhraní API.

| Výstupy | Popis |
| --- | --- |
| Time |Časové razítko z nezpracovaných dat nebo data agregovaná (a/nebo) imputované Pokud agregace (a/nebo) chybí imputace dat se použije. |
| OriginalData |Pokud hodnoty z nezpracovaných dat nebo data agregovaná (a/nebo) imputované agregace (a/nebo) chybí imputace data platí |
| ProcessedData |Jednu z následujících možností: <ul><li>Pokud významné sezónnosti byl zjištěn a deseason možnost; očištěných časové řady</li><li>ročních období upravit a kolísání časových řad, pokud byl zjištěn významné sezónnosti a vybranou možností deseasontrend</li><li>v opačném případě je tato možnost stejná jako OriginalData</li> |
| TSpike |Binární indikátor označující, zda je prudký nárůst zjištěných TSpike Detectoru |
| ZSpike |Binární indikátor označující, zda je prudký nárůst zjištěných ZSpike Detectoru |
| BiLevelChangeScore |Plovoucí desetinné číslo představující anomálií skóre při změně úrovně |
| BiLevelChangeAlert |1 nebo 0 hodnotu, která existuje je změna úrovně anomálií na základě vstupní citlivosti |
| PosTrendScore |Plovoucí desetinné číslo představující anomálií skóre na kladné trend |
| PosTrendAlert |Hodnota 1/0 označující, že je pozitivní trend anomálií na základě citlivosti vstupní |
| NegTrendScore |Plovoucí desetinné číslo představující anomálií skóre na záporné trend |
| NegTrendAlert |Hodnota 1/0 označující, že je negativní trend anomálií na základě citlivosti vstupní |

[1]: ./media/apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/apps-anomaly-detection-api/anomaly-detection-seasonal.png

