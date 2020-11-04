---
title: Azure Machine Learning rozhraní API pro detekci anomálií – vědecký proces týmového zpracování dat
description: Rozhraní API pro detekci anomálií je příklad sestavený s Microsoft Azure Machine Learning, který detekuje anomálie v datech časových řad s numerickými hodnotami, které jsou rovnoměrně rozmístěny v čase.
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
ms.openlocfilehash: e210c1683d5f14181bc0549e73a892eb91d2e746
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305702"
---
# <a name="machine-learning-anomaly-detection-api"></a>Machine Learning rozhraní API pro detekci anomálií

> [!NOTE]
> Probíhá údržba této položky. Doporučujeme vám používat [službu rozhraní API](https://azure.microsoft.com/services/cognitive-services/anomaly-detector/) pro detekci anomálií, která využívá galerii Machine Learning algoritmů v Azure Cognitive Services ke zjištění anomálií z metrik obchodních, provozních a IoT.

## <a name="overview"></a>Přehled
[Rozhraní API pro detekci anomálií](https://gallery.azure.ai/MachineLearningAPI/Anomaly-Detection-2) je příklad sestavený s Azure Machine Learning, který detekuje anomálie v datech časových řad s numerickými hodnotami, které jsou rovnoměrně rozmístěny v čase.

Toto rozhraní API dokáže detekovat následující typy neobvyklé vzorů v datech časových řad:

* **Pozitivní a negativní trendy** : například při monitorování využití paměti při výpočtu stoupajících trendů může být důležité, aby bylo možné vyhodnotit nevracení paměti.
* **Změny v dynamickém rozsahu hodnot** : například při monitorování výjimek vyvolaných cloudovou službou mohou všechny změny v dynamickém rozsahu hodnot indikovat nestabilitu ve stavu služby a
* **Špičky a** nedodržení: například při monitorování počtu selhání přihlášení ve službě nebo počtu rezervací na webu elektronického obchodování může špičky nebo DIP znamenat neobvyklé chování.

Tyto detektory strojového učení sledují takové změny v hodnotách v průběhu času a oznamují průběžné změny v jejich hodnotách jako skóre anomálií. Nevyžadují optimalizaci prahové hodnoty ad hoc a jejich skóre se dá použít k řízení kladné míry. Rozhraní API pro detekci anomálií je užitečné v několika scénářích, jako je monitorování služby, sledováním klíčových ukazatelů výkonu v průběhu času, monitorování využití prostřednictvím metrik, jako je třeba počet hledání, počet kliknutí, sledování výkonu prostřednictvím čítačů, jako je například paměť, procesor, čtení souborů atd. v průběhu času.

Nabídka detekce anomálií je dodávána s užitečnými nástroji, které vám pomohou začít.

* [Webová aplikace](https://anomalydetection-aml.azurewebsites.net/) vám pomůže vyhodnotit a vizualizovat výsledky rozhraní API pro detekci anomálií na vašich datech.

> [!NOTE]
> Vyzkoušejte **si řešení Anomáli Insights** založené na [tomto rozhraní API](https://gallery.azure.ai/MachineLearningAPI/Anomaly-Detection-2)
>
<!-- This Solution is no longer available
> To get this end to end solution deployed to your Azure subscription <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">**Start here >**</a>
-->

## <a name="api-deployment"></a>Nasazení rozhraní API
Aby bylo možné používat rozhraní API, je nutné ho nasadit do předplatného Azure, kde bude hostovat jako webová služba Azure Machine Learning.  Můžete to provést z [Azure AI Gallery](https://gallery.azure.ai/MachineLearningAPI/Anomaly-Detection-2).  Tím dojde k nasazení dvou Azure Machine Learning Studio (klasických) webových služeb (a jejich souvisejících prostředků) do předplatného Azure – jeden pro detekci anomálií s detekcí sezónnost a druhý bez detekce sezónnost.  Po dokončení nasazení budete moci spravovat vaše rozhraní API ze stránky [Azure Machine Learning Studio (klasické) webové služby](https://services.azureml.net/webservices/) .  Na této stránce budete moct najít umístění koncových bodů, klíče rozhraní API a ukázkový kód pro volání rozhraní API.  Podrobnější pokyny jsou k dispozici [zde](../classic/manage-new-webservice.md).

## <a name="scaling-the-api"></a>Škálování rozhraní API
Ve výchozím nastavení bude mít vaše nasazení bezplatný fakturační plán pro vývoj/testování, který zahrnuje 1 000 transakcí za měsíc a 2 výpočetní hodiny/měsíc.  V rámci svých potřeb můžete upgradovat na jiný plán.  Podrobnosti o cenách různých plánů [najdete v části](https://azure.microsoft.com/pricing/details/machine-learning/) "ceny produkčního webového rozhraní API".

## <a name="managing-aml-plans"></a>Správa plánů AML
Svůj fakturační plán můžete spravovat [tady](https://services.azureml.net/plans/).  Název plánu bude založen na názvu skupiny prostředků, který jste zvolili při nasazování rozhraní API, a navíc k řetězci, který je pro vaše předplatné jedinečný.  Pokyny, jak upgradovat plán, [najdete](../classic/manage-new-webservice.md) v části Správa fakturačních plánů.

## <a name="api-definition"></a>Definice rozhraní API
Webová služba poskytuje rozhraní API založené na REST prostřednictvím protokolu HTTPS, které lze spotřebovat různými způsoby, včetně webové nebo mobilní aplikace, R, Pythonu, Excelu atd.  Data časové řady odešlete do této služby prostřednictvím volání REST API a spustí se kombinace tří typů anomálií popsaných níže.

## <a name="calling-the-api"></a>Volání rozhraní API
Aby bylo možné volat rozhraní API, budete muset znát umístění koncového bodu a klíč rozhraní API.  Tyto dvě požadavky spolu s ukázkovým kódem pro volání rozhraní API jsou k dispozici na stránce [Azure Machine Learning Studio (klasické) webové služby](https://services.azureml.net/webservices/) .  Přejděte na požadované rozhraní API a potom klikněte na kartu spotřebovat a pak ji vyhledejte.  Rozhraní API můžete volat jako rozhraní Swagger API (to znamená s parametrem URL `format=swagger` ) nebo jako rozhraní API jiného typu než Swagger (tj `format` . bez parametru URL).  Vzorový kód používá formát Swagger.  Níže je příklad žádosti a odpovědi ve formátu mimo Swagger.  Tyto příklady jsou pro koncový bod sezónnost.  Koncový bod, který není sezónnost, je podobný.

### <a name="sample-request-body"></a>Ukázka textu žádosti
Požadavek obsahuje dva objekty: `Inputs` a `GlobalParameters` .  V níže uvedeném příkladu žádosti se některé parametry odesílají explicitně, zatímco jiné nejsou (posuňte se dolů na úplný seznam parametrů pro každý koncový bod).  Parametry, které se explicitně neodesílají v žádosti, budou používat výchozí hodnoty uvedené níže.

```json
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
```

### <a name="sample-response"></a>Ukázková odpověď
Chcete-li zobrazit `ColumnNames` pole, musíte `details=true` do žádosti zahrnout jako parametr adresy URL.  V níže uvedených tabulkách najdete význam každého z těchto polí.

```json
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
```


## <a name="score-api"></a>Rozhraní API skóre
Rozhraní API pro hodnocení se používá ke spuštění detekce anomálií pro data časových řad, která nejsou sezónní. Rozhraní API spustí pro data řadu detekcí anomálií a vrátí jejich výsledky anomálií.
Následující obrázek ukazuje příklad anomálií, které může rozhraní API skóre detekovat. Tato časová řada má dvě různé změny úrovně a tři špičky. Červené tečky ukazují čas, kdy se zjistí Změna úrovně, zatímco černé tečky ukazují zjištěné špičky.
![Rozhraní API skóre][1]

### <a name="detectors"></a>Detektory
Rozhraní API pro detekci anomálií podporuje detektory ve třech hlavních kategoriích. Podrobnosti o specifických vstupních parametrech a výstupech pro jednotlivé detektory najdete v následující tabulce.

| Kategorie detektoru | Detectoru | Popis | Vstupní parametry | Výstupy |
| --- | --- | --- | --- | --- |
| Detektory špičky |Detektor TSpike |Detekovat špičky a neshodné hodnoty na základě toho, co se týče hodnot od první a třetího Kvartily |*tspikedetector. Citlivost:* přebírá celočíselnou hodnotu v rozsahu 1-10, výchozí hodnota: 3; Vyšší hodnoty zachytí více extrémních hodnot tím, že budou méně citlivé. |TSpike: binární hodnoty – ' 1 ', pokud je detekována špička/DIP, ' 0 ' jinak |
| Detektory špičky | Detektor ZSpike |Detekovat špičky a nezávisle na tom, jak daleko jsou v databodech ze střední hodnoty |*zspikedetector. Citlivost:* přebírat celočíselnou hodnotu v rozsahu 1-10, výchozí hodnota: 3; Vyšší hodnoty zachytí více extrémních hodnot, takže jsou méně citlivé. |ZSpike: binární hodnoty – ' 1 ', pokud je detekována špička/DIP, ' 0 ' jinak |
| Detektor pomalého trendu |Detektor pomalého trendu |Detekovat pomalý kladný trend podle nastavené citlivosti |*trenddetector. Citlivost:* prahová hodnota skóre detektoru (výchozí: 3,25, 3,25 – 5 je přiměřený rozsah, ze kterého lze vybírat; Čím vyšší je méně citlivá) |tscore: číslo s plovoucí desetinnou známkou pro trend |
| Detektory změny úrovně | Detektor změny obousměrné úrovně |Detekovat změnu úrovně směrem nahoru i dolů podle nastavené citlivosti |*bileveldetector. Citlivost:* prahová hodnota skóre detektoru (výchozí: 3,25, 3,25 – 5 je přiměřený rozsah, ze kterého lze vybírat; Čím vyšší je méně citlivá) |rpscore: číslo s plovoucí desetinnou čárkou, které představuje skóre na úrovni vzhůru a dolů |

### <a name="parameters"></a>Parametry
Podrobnější informace o těchto vstupních parametrech jsou uvedeny v následující tabulce:

| Vstupní parametry | Popis | Výchozí nastavení | Typ | Platný rozsah | Navrhovaný rozsah |
| --- | --- | --- | --- | --- | --- |
| detektory. historywindow |Historie (v počtu datových bodů) použitá pro výpočet skóre anomálií |500 |integer |10-2000 |Závislá na časové řadě |
| detektory. spikesdips | Určuje, jestli se mají detekovat jenom špičky, jenom vyhrazené nebo obojí. |Obojí |Výčtový |Obě, špičky, DIP |Obojí |
| bileveldetector. Citlivost |Citlivost pro detektor se změnou úrovně obousměrné komunikace |3.25 |double |Žádné |3,25-5 (méně hodnoty znamená citlivější) |
| trenddetector. Citlivost |Citlivost pro pozitivní detektor trendu |3.25 |double |Žádné |3,25-5 (méně hodnoty znamená citlivější) |
| tspikedetector. Citlivost |Citlivost pro detektor TSpike |3 |integer |1-10 |3-5 (méně hodnot znamená citlivější) |
| zspikedetector. Citlivost |Citlivost pro detektor ZSpike |3 |integer |1-10 |3-5 (méně hodnot znamená citlivější) |
| postprocess.tailRows |Počet nejnovějších datových bodů, které mají být zachovány ve výstupních výsledcích |0 |integer |0 (zachovat všechny datové body) nebo zadat počet bodů, které mají být uchovávány ve výsledcích |– |

### <a name="output"></a>Výstup
Rozhraní API spustí všechny detektory dat časových řad a vrátí skóre anomálií a binární indikátory špičky pro každý bod v čase. V tabulce níže jsou uvedeny výstupy z rozhraní API.

| Výstupy | Popis |
| --- | --- |
| Čas |Časová razítka z nezpracovaných dat nebo agregovaná (a/nebo) imputované data v případě, že se používá agregace (a/nebo) chybějící imputace dat |
| Data |Hodnoty z nezpracovaných dat nebo agregovaná (a/nebo) imputované data v případě, že se používá agregace (a/nebo) chybějící imputace dat |
| TSpike |Binární indikátor, který označuje, jestli je špička detekována detektorem TSpike |
| ZSpike |Binární indikátor, který označuje, jestli je špička detekována detektorem ZSpike |
| rpscore |Plovoucí číslo představující skóre anomálií při změně obousměrné úrovně |
| rpalert |hodnota 1/0 označující, že se na základě citlivosti vstupu vyskytuje anomálie změny úrovně obousměrné úrovně. |
| tscore |Plovoucí číslo představující skóre anomálií na pozitivním trendu |
| talert |hodnota 1/0 označující, že je kladná anomálie trendu na základě citlivosti vstupu |

## <a name="scorewithseasonality-api"></a>Rozhraní API pro ScoreWithSeasonality
Rozhraní ScoreWithSeasonality API se používá ke spuštění detekce anomálií v časové řadě, která má sezónní vzory. Toto rozhraní API je užitečné ke zjišťování odchylek v sezónních vzorcích.
Následující obrázek ukazuje příklad anomálií zjištěných v sezónní časové řadě. Časová řada má jednu špičku (první černou tečka), dvě DIP (druhá černá tečka a jedna na konci) a jednu změnu úrovně (červenou tečkou). DIP uprostřed časové řady a změna úrovně se discernable jenom po odebrání sezónních komponent z řady.
![Rozhraní API pro sezónnost][2]

### <a name="detectors"></a>Detektory
Detektory v koncovém bodu sezónnost jsou podobné těm, které jsou v nesezónnostm koncovém bodu, ale s mírně odlišnými názvy parametrů (uvedené níže).

### <a name="parameters"></a>Parametry

Podrobnější informace o těchto vstupních parametrech jsou uvedeny v následující tabulce:

| Vstupní parametry | Popis | Výchozí nastavení | Typ | Platný rozsah | Navrhovaný rozsah |
| --- | --- | --- | --- | --- | --- |
| předzpracování. aggregationInterval |Interval agregace v sekundách pro agregaci vstupních časových řad |0 (není provedena žádná agregace) |integer |0: přeskočit agregaci > 0 jinak |5 minut až 1 den, časová řada závislá |
| předzpracování. aggregationFunc |Funkce používaná pro agregaci dat do zadaného AggregationInterval |mean |Výčtový |střední hodnota, suma, délka |– |
| předzpracování. replaceMissing |Hodnoty, které slouží k imputaci chybějících dat |LKV (Poslední známá hodnota) |Výčtový |nula, LKV, střední hodnota |– |
| detektory. historywindow |Historie (v počtu datových bodů) použitá pro výpočet skóre anomálií |500 |integer |10-2000 |Závislá na časové řadě |
| detektory. spikesdips | Určuje, jestli se mají detekovat jenom špičky, jenom vyhrazené nebo obojí. |Obojí |Výčtový |Obě, špičky, DIP |Obojí |
| bileveldetector. Citlivost |Citlivost pro detektor se změnou úrovně obousměrné komunikace |3.25 |double |Žádné |3,25-5 (méně hodnoty znamená citlivější) |
| postrenddetector. Citlivost |Citlivost pro pozitivní detektor trendu |3.25 |double |Žádné |3,25-5 (méně hodnoty znamená citlivější) |
| negtrenddetector. Citlivost |Citlivost pro záporný detektor trendu |3.25 |double |Žádné |3,25-5 (méně hodnoty znamená citlivější) |
| tspikedetector. Citlivost |Citlivost pro detektor TSpike |3 |integer |1-10 |3-5 (méně hodnot znamená citlivější) |
| zspikedetector. Citlivost |Citlivost pro detektor ZSpike |3 |integer |1-10 |3-5 (méně hodnot znamená citlivější) |
| sezónnost. Enable |Zda má být provedena analýza sezónnost |true |boolean |true, false |Závislá na časové řadě |
| sezónnost. numSeasonality |Maximální počet pravidelných cyklů, které se mají zjistit |1 |integer |1, 2 |1-2 |
| sezónnost. Transform |Zda mají být před použitím detekce anomálií odebrány sezónní (a) komponenty trendu |odsezóny |Výčtový |None, resezóny, deseasontrend |– |
| postprocess.tailRows |Počet nejnovějších datových bodů, které mají být zachovány ve výstupních výsledcích |0 |integer |0 (zachovat všechny datové body) nebo zadat počet bodů, které mají být uchovávány ve výsledcích |– |

### <a name="output"></a>Výstup
Rozhraní API spustí všechny detektory dat časových řad a vrátí skóre anomálií a binární indikátory špičky pro každý bod v čase. V tabulce níže jsou uvedeny výstupy z rozhraní API.

| Výstupy | Popis |
| --- | --- |
| Čas |Časová razítka z nezpracovaných dat nebo agregovaná (a/nebo) imputované data v případě, že se používá agregace (a/nebo) chybějící imputace dat |
| OriginalData |Hodnoty z nezpracovaných dat nebo agregovaná (a/nebo) imputované data v případě, že se používá agregace (a/nebo) chybějící imputace dat |
| ProcessedData |Jednu z následujících možností: <ul><li>Sestavila se řada časových řad, pokud byla zjištěna významná sezónnost a vybraná možnost pro odplánování;</li><li>seřízená a netrendovaná časová řada, pokud se zjistilo významné sezónnost a volba deseasontrend</li><li>v opačném případě je tato možnost stejná jako OriginalData</li> |
| TSpike |Binární indikátor, který označuje, jestli je špička detekována detektorem TSpike |
| ZSpike |Binární indikátor, který označuje, jestli je špička detekována detektorem ZSpike |
| BiLevelChangeScore |Plovoucí číslo představující skóre anomálií při změně úrovně |
| BiLevelChangeAlert |hodnota 1/0 označující, že na základě citlivosti vstupu se mění úroveň anomálií |
| PosTrendScore |Plovoucí číslo představující skóre anomálií na pozitivním trendu |
| PosTrendAlert |hodnota 1/0 označující, že je kladná anomálie trendu na základě citlivosti vstupu |
| NegTrendScore |Plovoucí číslo představující skóre anomálií na záporném trendu |
| NegTrendAlert |hodnota 1/0 označující, že je záporná anomálie trendu založená na citlivosti vstupu |

[1]: ./media/apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/apps-anomaly-detection-api/anomaly-detection-seasonal.png