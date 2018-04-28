---
title: Azure Machine Learning detekce anomálií rozhraní API | Microsoft Docs
description: Rozhraní API detekce anomálií je příklad vytvořené s nástroji Microsoft Azure Machine Learning, který zjistí anomálie v časových řad dat se číselné hodnoty, které jsou rozloženy rovnoměrně v čase.
services: machine-learning
documentationcenter: ''
author: alokkirpal
manager: jhubbard
editor: cgronlun
ms.assetid: 52fafe1f-e93d-47df-a8ac-9a9a53b60824
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/05/2017
ms.author: alok
ms.openlocfilehash: 7633d2bd15e5bc4620a4980623f3883c162f4331
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="machine-learning-anomaly-detection-api"></a>Strojového učení detekce anomálií rozhraní API
## <a name="overview"></a>Přehled
[Rozhraní API detekce anomálií](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2) je příklad vytvořené s nástroji Azure Machine Learning, který zjistí anomálie v časových řad dat se číselné hodnoty, které jsou rozloženy rovnoměrně v čase.

Toto rozhraní API můžete zjistit následující typy neobvyklé vzory v časových řad dat:

* **Kladné a záporné trendy**: při monitorování využití paměti v oblasti výpočetních vzestupný trend může být například týkající se může být naznačuje výslednou nevracení paměti
* **Změny v dynamické rozsahu hodnot**: například při monitorování výjimky vyvolané Cloudová služba, všechny změny v dynamické rozsahu hodnot může znamenat nestabilitě stavu služby, a
* **Špičky a klesne**: například při monitorování počet neúspěšných přihlášení ve službě nebo počet rezervace web elektronického obchodu, špičky nebo vyhrazené IP adresy by mohly být známkou neobvyklé chování.

Tyto machine learning detektory sledovat tyto změny v hodnotách průběhu čas a sestava probíhající změny v jejich hodnoty jako anomálií skóre. Nevyžadují ad hoc prahová hodnota ladění a jejich skóre můžete použít k řízení false kladné rychlost. Detekce anomálií rozhraní API je užitečné v několika scénářích jako monitorování služby pomocí funkce sledování klíčových ukazatelů výkonu v čase, sledování využití prostřednictvím metriky počet vyhledávání, například počet kliknutí, monitorování výkonu pomocí čítačů jako paměť, procesor, přečte soubor atd. v čase.

Detekce anomálií nabídky se dodává s užitečné nástroje, které vám pomůžou začít.

* [Webové aplikace](http://anomalydetection-aml.azurewebsites.net/) vám pomůže vyhodnotit a zobrazit výsledky detekce anomálií rozhraní API na vaše data.

> [!NOTE]
> Zkuste **řešení IT anomálií Insights** používá technologii [toto rozhraní API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2)
> 
> Toto řešení koncová nasazené do vašeho předplatného Azure <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank"> **Začněte zde >**</a>
> 
>

## <a name="api-deployment"></a>Rozhraní API nasazení
Chcete-li použít rozhraní API, je nutné nasadit k předplatnému Azure, kde se bude hostovat jako webové služby Azure Machine Learning.  Můžete to provést z [Azure AI Galerie](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2).  To nasadí dvě AzureML webové služby (a jejich související prostředky) k předplatnému Azure – jeden pro zjišťování anomálií s sezónnosti detekce a jeden bez detekce sezónnosti.  Po dokončení nasazení budou moci spravovat vaše rozhraní API z [AzureML webové služby](https://services.azureml.net/webservices/) stránky.  Z této stránky bude možné najít váš koncový bod umístění, klíče rozhraní API a také ukázkový kód pro volání rozhraní API.  Podrobnější pokyny jsou k dispozici [zde](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice).

## <a name="scaling-the-api"></a>Škálování rozhraní API
Ve výchozím nastavení bude mít vaše nasazení plán fakturace volné vývoje/testování, která zahrnuje 1000 transakce za měsíc a 2 výpočetních hodin za měsíc.  Můžete upgradovat na jiný plán, podle potřeb.  Podrobnosti o cenách různé plány jsou k dispozici [sem](https://azure.microsoft.com/pricing/details/machine-learning/) v části "Produkční webové rozhraní API ceny".

## <a name="managing-aml-plans"></a>Správa AML plány 
Můžete spravovat cenového plánu [zde](https://services.azureml.net/plans/).  Název plánu budou založeny na název skupiny prostředků, které jste zvolili při nasazování rozhraní API plus řetězec, který je jedinečné pro vaše předplatné.  Pokyny o tom, jak upgradovat plán jsou k dispozici [sem](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice) v části "Správa fakturace plány".

## <a name="api-definition"></a>Definice rozhraní API
Webová služba poskytuje rozhraní založené na REST API přes protokol HTTPS, které mohou být využívány různými způsoby, včetně webových a mobilních aplikací, R, Python, Excel, atd.  Odeslání dat v řadě čas k této službě prostřednictvím volání rozhraní REST API a běží kombinaci typů tři anomálií, které jsou popsané dál.

## <a name="calling-the-api"></a>Volání rozhraní API
Aby bylo možné volat rozhraní API, musíte znát umístění koncového bodu a klíč rozhraní API.  Obě tyto, společně s ukázkový kód pro volání rozhraní API, jsou k dispozici na [AzureML webové služby](https://services.azureml.net/webservices/) stránky.  Přejděte na požadovanou rozhraní API a poté klikněte na kartu "Spotřebě", kde je najít.  Všimněte si, že můžete volat rozhraní API jako rozhraní API Swaggeru (tj. s parametr adresy URL `format=swagger`) nebo jako jiný – rozhraní API Swaggeru (tj. bez `format` parametr URL).  Ukázkový kód používá formát Swagger.  Níže je příklad požadavku a odpovědi ve formátu bez Swagger.  Tyto příklady jsou ke koncovému bodu sezónnosti.  Koncový bod není sezónnosti je podobný.

### <a name="sample-request-body"></a>Ukázkový text žádosti
Žádost obsahuje dva objekty: `Inputs` a `GlobalParameters`.  V příkladu níže uvedenou žádost, některé parametry se odešlou explicitně zatímco jiné nejsou (přejděte dolů pro úplný seznam parametrů pro každý koncový bod).  Parametry, které nejsou výslovně odeslaných v požadavku použije výchozí hodnoty vypsáni níže.

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
Všimněte si, že, chcete-li zobrazit `ColumnNames` pole, je nutné zahrnout `details=true` jako parametr adresy URL ve vaší žádosti.  Podívejte se na tabulky pod pro význam za každé z těchto polí.

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
Rozhraní API skóre se používá pro spouštění na data řady-sezónní čas detekce anomálií. Rozhraní API spustí počet detektory anomálií na data a vrátí jejich anomálií skóre. Následující obrázek znázorňuje příklad anomálií, které může zjistit rozhraní API skóre. Toto časové řady má 2 odlišné úrovně změny a 3 špičky. Červené tečky zobrazit čas, kdy je zjištěna změna úrovně, zatímco černé tečky zobrazit zjištěné špičky.
![Rozhraní API skóre][1]

### <a name="detectors"></a>Detektory
Detekce anomálií rozhraní API podporuje detektory ve 3 rozsáhlé kategorie. Podrobnosti o konkrétní vstupní parametry a výstupy pro každý detektor naleznete v následující tabulce.

| Detektor kategorie | Detektor | Popis | Vstupní parametry | Výstupy |
| --- | --- | --- | --- | --- |
| Detektory Špička |Detektor TSpike |Zjištění špičky a vyhrazené IP adresy na základě daleko hodnot se z první a třetí Kvartily |*tspikedetector.Sensitivity:* trvá celočíselná hodnota v rozsahu 1 – 10, výchozí: 3; Vyšší hodnoty zachytí další extrémní hodnoty, díky čemuž méně citlivou |TSpike: binární hodnoty – 1, pokud je zjištěn Špička/dip, '0' jinak hodnota |
| Detektory Špička | Detektor ZSpike |Zjištění špičky a vyhrazené IP adresy na základě toho, jak daleko datapoints se od jejich střední hodnoty |*zspikedetector.Sensitivity:* trvat celé číslo v rozsahu 1 – 10, výchozí: 3; Vyšší hodnoty zachytí další extrémní hodnoty, což méně citlivou |ZSpike: binární hodnoty – 1, pokud je zjištěn Špička/dip, '0' jinak hodnota | |
| Detektor pomalé trendu |Detektor pomalé trendu |Zjištění pomalé pozitivní trend podle citlivosti sady |*trenddetector.Sensitivity:* prahovou hodnotu na detektor skóre (výchozí: 3,25, 3,25 – 5 je možné logicky rozsahu z; tuto možnost vyberte, Čím vyšší je menší velká a malá písmena) |tscore: plovoucí desetinné číslo představující skóre anomálií na trendu |
| Změna úrovně detektory | Detektor změnit úroveň obousměrného |Zjištění směrem nahoru a dolů Změna úrovně podle citlivosti sady |*bileveldetector.Sensitivity:* prahovou hodnotu na detektor skóre (výchozí: 3,25, 3,25 – 5 je možné logicky rozsahu z; tuto možnost vyberte, Čím vyšší je menší velká a malá písmena) |rpscore: plovoucí desetinné číslo představující anomálií skóre při změně úrovně směrem nahoru a dolů | |

### <a name="parameters"></a>Parametry
Podrobnější informace o těchto vstupních parametrů jsou uvedeny v následující tabulce:

| Vstupní parametry | Popis | Výchozí nastavení | Typ | Platný rozsah | Navržená oblast |
| --- | --- | --- | --- | --- | --- |
| detectors.historyWindow |Historie (v počet datových bodů), které jsou používány pro výpočty anomálií skóre |500 |celé číslo |10-2000 |Závislé na časové řady |
| detectors.spikesdips | Jestli se má zjišťovat pouze špičky, pouze vyhrazené IP adresy, nebo obojí |Obojí |vytvořit její výčet. |Obě špičky, vyhrazené IP adresy |Obojí |
| bileveldetector.Sensitivity |Velkých a malých písmen pro úroveň obousměrného změnit detektor. |3.25 |double |Žádný |3,25-5 (hodnoty menší význam citlivější) |
| trenddetector.Sensitivity |Velkých a malých písmen pro pozitivní trend detektor. |3.25 |double |Žádný |3,25-5 (hodnoty menší význam citlivější) |
| tspikedetector.Sensitivity |Velkých a malých písmen pro TSpike detektor |3 |celé číslo |1-10 |3 až 5 (hodnoty menší význam citlivější) |
| zspikedetector.Sensitivity |Velkých a malých písmen pro ZSpike detektor |3 |celé číslo |1-10 |3 až 5 (hodnoty menší význam citlivější) |
| postprocess.tailRows |Počet nejnovější datových bodů budou muset zůstat ve výsledcích výstup |0 |celé číslo |0 (uchovávejte všechny datové body), nebo zadejte počet bodů pro uchování v výsledky |neuvedeno |

### <a name="output"></a>Výstup
Rozhraní API spustí všechny detektory na datové řady čas a vrátí anomálií skóre a binární Špička indikátory pro každý bod v čase. Následující tabulka uvádí výstupy z rozhraní API. 

| Výstupy | Popis |
| --- | --- |
| Čas |Časová razítka z nezpracovaná data nebo data agregované (nebo) imputované Pokud agregace (nebo) chybí data imputace platí |
| Data |Hodnoty z nezpracovaná data nebo data agregované (nebo) imputované Pokud agregace (nebo) chybí data imputace platí |
| TSpike |Binární indikátor označující, zda se objeví se zjišťují pomocí TSpike detektor |
| ZSpike |Binární indikátor označující, zda se objeví se zjišťují pomocí ZSpike detektor |
| rpscore |Při změně úrovně obousměrného stanovení skóre plovoucí desetinné číslo představující anomálií |
| rpalert |Hodnota 1 nebo 0 oznamující, že je úroveň obousměrného změní anomálií podle vstupní velkých a malých písmen |
| tscore |Plovoucí desetinné číslo představující anomálií stanovení skóre na kladné trendu |
| talert |1 nebo 0 hodnotu udávající, že je kladné trend anomálií, podle vstupní velkých a malých písmen |

## <a name="scorewithseasonality-api"></a>ScoreWithSeasonality rozhraní API
Rozhraní API ScoreWithSeasonality se používá pro spouštění detekce anomálií na časové řady, která sezónní vzorce. Toto rozhraní API je užitečné k detekci odchylky v sezónní vzory.  
Následující obrázek znázorňuje příklad anomálie v sezónní časové řady. Časové řady má jeden Špička (1 černý bod), dvě vyhrazené IP adresy (2. černý bod a druhý na konci) a jeden Změna úrovně (červené tečky). Upozorňujeme, že dip uprostřed časové řady a změna úrovně jsou pouze discernable a po odebrání sezónní součásti z řady.
![Sezónnosti rozhraní API][2]

### <a name="detectors"></a>Detektory
Detektory v koncovém bodě sezónnosti jsou podobné těm, které jsou v jiných sezónnosti koncový bod, ale s názvy mírně odlišné parametrů (uvedené níže).

### <a name="parameters"></a>Parametry

Podrobnější informace o těchto vstupních parametrů jsou uvedeny v následující tabulce:

| Vstupní parametry | Popis | Výchozí nastavení | Typ | Platný rozsah | Navržená oblast |
| --- | --- | --- | --- | --- | --- |
| preprocess.aggregationInterval |Agregace interval v sekundách pro agregaci vstupní časové řady |0 (žádné agregace se provádí) |celé číslo |0: jinak přeskočit agregace > 0 |5 minut až 1 den, závislé na časové řady |
| preprocess.aggregationFunc |Funkce použitá pro agregaci dat do zadané AggregationInterval |střední |vytvořit její výčet. |Střední, sum, délka |neuvedeno |
| preprocess.replaceMissing |Hodnoty použité k dává chybějící data |lkv (poslední známá hodnota) |vytvořit její výčet. |nula, lkv, střední |neuvedeno |
| detectors.historyWindow |Historie (v počet datových bodů), které jsou používány pro výpočty anomálií skóre |500 |celé číslo |10-2000 |Závislé na časové řady |
| detectors.spikesdips | Jestli se má zjišťovat pouze špičky, pouze vyhrazené IP adresy, nebo obojí |Obojí |vytvořit její výčet. |Obě špičky, vyhrazené IP adresy |Obojí |
| bileveldetector.Sensitivity |Velkých a malých písmen pro úroveň obousměrného změnit detektor. |3.25 |double |Žádný |3,25-5 (hodnoty menší význam citlivější) |
| postrenddetector.Sensitivity |Velkých a malých písmen pro pozitivní trend detektor. |3.25 |double |Žádný |3,25-5 (hodnoty menší význam citlivější) |
| negtrenddetector.Sensitivity |Velkých a malých písmen pro detektor negativní trend. |3.25 |double |Žádný |3,25-5 (hodnoty menší význam citlivější) |
| tspikedetector.Sensitivity |Velkých a malých písmen pro TSpike detektor |3 |celé číslo |1-10 |3 až 5 (hodnoty menší význam citlivější) |
| zspikedetector.Sensitivity |Velkých a malých písmen pro ZSpike detektor |3 |celé číslo |1-10 |3 až 5 (hodnoty menší význam citlivější) |
| seasonality.enable |Zda je analýza sezónnosti provést |true (pravda) |Boolean |Hodnota TRUE, false |Závislé na časové řady |
| seasonality.numSeasonality |Maximální počet cyklů pravidelné rozpoznána |1 |celé číslo |1, 2 |1-2 |
| seasonality.Transform |Jestli sezónní (a) trend součásti se musí odebrat před použitím detekce anomálií |deseason |vytvořit její výčet. |NONE, deseason, deseasontrend |neuvedeno |
| postprocess.tailRows |Počet nejnovější datových bodů budou muset zůstat ve výsledcích výstup |0 |celé číslo |0 (uchovávejte všechny datové body), nebo zadejte počet bodů pro uchování v výsledky |neuvedeno |

### <a name="output"></a>Výstup
Rozhraní API spustí všechny detektory na datové řady čas a vrátí anomálií skóre a binární Špička indikátory pro každý bod v čase. Následující tabulka uvádí výstupy z rozhraní API. 

| Výstupy | Popis |
| --- | --- |
| Čas |Časová razítka z nezpracovaná data nebo data agregované (nebo) imputované Pokud agregace (nebo) chybí data imputace platí |
| OriginalData |Hodnoty z nezpracovaná data nebo data agregované (nebo) imputované Pokud agregace (nebo) chybí data imputace platí |
| ProcessedData |Některý z následujících: <ul><li>Pokud významné sezónnosti byl zjištěn a deseason možnost; ročních období upravena časové řady</li><li>ročních období upravit a detrendovaného časové řady zjištění významné sezónnosti a vybranou možností deseasontrend</li><li>jinak je to stejné jako OriginalData</li> |
| TSpike |Binární indikátor označující, zda se objeví se zjišťují pomocí TSpike detektor |
| ZSpike |Binární indikátor označující, zda se objeví se zjišťují pomocí ZSpike detektor |
| BiLevelChangeScore |Plovoucí desetinné číslo představující anomálií stanovení skóre při změně úrovně |
| BiLevelChangeAlert |1 nebo 0 hodnotu označující, že je změna úrovně anomálií, podle vstupní velkých a malých písmen |
| PosTrendScore |Plovoucí desetinné číslo představující anomálií stanovení skóre na kladné trendu |
| PosTrendAlert |1 nebo 0 hodnotu udávající, že je kladné trend anomálií, podle vstupní velkých a malých písmen |
| NegTrendScore |Plovoucí desetinné číslo představující anomálií stanovení skóre na záporné trendu |
| NegTrendAlert |1 nebo 0 hodnotu udávající, že je negativní trend anomálií, podle vstupní velkých a malých písmen |

[1]: ./media/apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/apps-anomaly-detection-api/anomaly-detection-seasonal.png

