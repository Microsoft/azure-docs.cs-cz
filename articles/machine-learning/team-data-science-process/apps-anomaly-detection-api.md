---
title: Azure Machine Learning rozhraní API detekce anomálií – proces vědecké vědy o týmových datech
description: Rozhraní API pro detekci anomálií je příklad vytvořený pomocí Microsoft Azure Machine Learning, který detekuje anomálie v datech časových řad s číselnými hodnotami, které jsou rovnoměrně rozmístěny v čase.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721876"
---
# <a name="machine-learning-anomaly-detection-api"></a>Rozhraní API pro detekci anomálií strojového učení

> [!NOTE]
> Tato položka je v údržbě. Doporučujeme vám používat [službu API detektoru anomálií](https://azure.microsoft.com/services/cognitive-services/anomaly-detector/) využívající galerii algoritmů strojového učení v rámci služby Azure Cognitive Services k detekci anomálií z obchodních, provozních a ioT metrik.

## <a name="overview"></a>Přehled
[Rozhraní API pro detekci anomálií](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2) je příklad vytvořený pomocí Azure Machine Learning, který detekuje anomálie v datech časových řad s číselnými hodnotami, které jsou rovnoměrně rozmístěny v čase.

Toto rozhraní API dokáže zjistit následující typy anomálních vzorů v datech časových řad:

* **Pozitivní a negativní trendy**: Například při sledování využití paměti při výpočtu může být zajímavý vzestupný trend, protože může svědčit o nevracení paměti,
* **Změny v dynamickém rozsahu hodnot**: Například při sledování výjimek vymrštěných cloudovou službou mohou jakékoli změny v dynamickém rozsahu hodnot naznačovat nestabilitu stavu služby a
* **Špičky a poklesy**: Například při sledování počtu selhání přihlášení ve službě nebo počtu ponechaných pokladny na webu elektronického obchodování může špičky nebo poklesy naznačovat abnormální chování.

Tyto detektory strojového učení sledují takové změny hodnot v průběhu času a hlásí probíhající změny svých hodnot jako skóre anomálií. Nevyžadují ladění adhoc prahových hodnot a jejich skóre lze použít ke kontrole míry falešně pozitivních výsledků. Rozhraní API pro detekci anomálií je užitečné v několika scénářích, jako je monitorování služeb sledováním klíčových ukazatelů výkonu v průběhu času, monitorování využití prostřednictvím metrik, jako je počet vyhledávání, počet kliknutí, sledování výkonu prostřednictvím čítačů, jako je paměť, procesor, čtení souborů atd. v průběhu času.

Nabídka detekce anomálií je dodávána s užitečnými nástroji, které vám pomohou začít.

* [Webová aplikace](https://anomalydetection-aml.azurewebsites.net/) vám pomůže vyhodnotit a vizualizovat výsledky api detekce anomálií na vašich datech.

> [!NOTE]
> Vyzkoušejte **řešení IT Anomaly Insights** využívající [toto rozhraní API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2)
>
<!-- This Solution is no longer available
> To get this end to end solution deployed to your Azure subscription <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">**Start here >**</a>
-->

## <a name="api-deployment"></a>Nasazení rozhraní API
Abyste mohli rozhraní API používat, musíte ho nasadit do předplatného Azure, kde bude hostované jako webová služba Azure Machine Learning.  Můžete to provést z [Galerie AI Azure](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2).  Tím se nasadí dvě webové služby Azure Machine Learning Studio (klasické) (a související prostředky) do vašeho předplatného Azure – jednu pro detekci anomálií s detekcí sezónnosti a jednu bez zjišťování sezónnosti.  Po dokončení nasazení budete moct spravovat vaše API ze stránky [webových služeb Azure Machine Learning Studio (klasické).](https://services.azureml.net/webservices/)  Na této stránce budete moci najít umístění koncovýbod, klíče rozhraní API, stejně jako ukázkový kód pro volání rozhraní API.  Podrobnější pokyny jsou k dispozici [zde](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice).

## <a name="scaling-the-api"></a>Změna velikosti rozhraní API
Ve výchozím nastavení bude mít vaše nasazení bezplatný fakturační plán pro vývoj a testování, který zahrnuje 1 000 transakcí za měsíc a 2 výpočetní hodiny za měsíc.  Můžete upgradovat na jiný plán podle vašich potřeb.  Podrobnosti o cenách různých plánů jsou k dispozici [zde v](https://azure.microsoft.com/pricing/details/machine-learning/) části "Ceny produkčního rozhraní API".

## <a name="managing-aml-plans"></a>Správa plánů AML
Fakturační plán můžete spravovat [zde](https://services.azureml.net/plans/).  Název plánu bude založen na názvu skupiny prostředků, který jste zvolili při nasazování rozhraní API, plus řetězec, který je jedinečný pro vaše předplatné.  Pokyny k upgradu plánu jsou k dispozici [zde v](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice) části Správa fakturačních plánů.

## <a name="api-definition"></a>Definice rozhraní API
Webová služba poskytuje rozhraní API založené na protokolu REST přes protokol HTTPS, které lze spotřebovávat různými způsoby, včetně webové nebo mobilní aplikace, R, Pythonu, Excelu atd.  Odesílat data časové řady do této služby prostřednictvím volání rozhraní REST API a spustí kombinaci tří typů anomálií popsaných níže.

## <a name="calling-the-api"></a>Volání rozhraní API
Chcete-li volat rozhraní API, budete potřebovat znát umístění koncového bodu a klíč rozhraní API.  Tyto dva požadavky spolu s ukázkovým kódem pro volání rozhraní API jsou k dispozici na stránce [webových služeb Azure Machine Learning Studio (klasické).](https://services.azureml.net/webservices/)  Přejděte na požadované rozhraní API a kliknutím na kartu "Konzumujte" je najděte.  Rozhraní API můžete volat jako rozhraní API Swagger (to znamená s parametrem `format=swagger`URL) nebo jako `format` rozhraní API bez swaggeru (to znamená bez parametru URL).  Ukázkový kód používá formát Swagger.  Níže je uveden příklad požadavku a odpovědi ve formátu, který není swagger.  Tyto příklady jsou ke koncovému bodu sezónnosti.  Koncový bod bez sezónnosti je podobný.

### <a name="sample-request-body"></a>Tělo požadavku vzorku
Požadavek obsahuje dva `Inputs` `GlobalParameters`objekty: a .  V příkladu požadavku níže jsou některé parametry odeslány explicitně, zatímco jiné nejsou (přejděte dolů pro úplný seznam parametrů pro každý koncový bod).  Parametry, které nejsou v požadavku explicitně odeslány, budou používat výchozí hodnoty uvedené níže.

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

### <a name="sample-response"></a>Ukázková odpověď
Chcete-li toto `ColumnNames` pole zobrazit, musíte do požadavku zahrnout `details=true` parametr adresy URL.  Význam každého z těchto polí naleznete v následujících tabulkách.

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


## <a name="score-api"></a>Skóre API
Rozhraní SCORE API se používá pro spuštění detekce anomálií na nesezónních datech časových řad. Rozhraní API spustí řadu detektorů anomálií na data a vrátí jejich skóre anomálií.
Na obrázku níže je uveden příklad anomálií, které skóre rozhraní API můžete zjistit. Tato časová řada má dvě odlišné změny úrovně a tři hroty. Červené tečky zobrazují čas, kdy je zjištěna změna úrovně, zatímco černé tečky zobrazují zjištěné špičky.
![Skóre API][1]

### <a name="detectors"></a>Detektory
Rozhraní API pro detekci anomálií podporuje detektory ve třech širokých kategoriích. Podrobnosti o specifických vstupních parametrech a výstupech pro každý detektor naleznete v následující tabulce.

| Kategorie detektoru | Detektor | Popis | Vstupní parametry | Výstupy |
| --- | --- | --- | --- | --- |
| Detektory spike |Detektor Tspike |Detekce hroty a poklesy na základě daleko hodnoty jsou z prvnía a třetího kvartilů |*tspikedetector.sensitivit:* bere celočíselnou hodnotu v rozsahu 1-10, výchozí: 3; Vyšší hodnoty zachytí více extrémních hodnot, což znějí méně citlivě |TSpike: binární hodnoty – '1', pokud je detekován špička / dip, '0' jinak |
| Detektory spike | Detektor ZSpike |Detekujte špičky a poklesy na základě toho, jak daleko jsou datové body od jejich střední |*zspikedetector.sensitivity:* vzít celočíselnou hodnotu v rozsahu 1-10, výchozí: 3; Vyšší hodnoty zachytí více extrémních hodnot, což je méně citlivé |ZSpike: binární hodnoty – '1', pokud je detekován hrot / dip, '0' jinak |
| Pomalý detektor trendů |Pomalý detektor trendů |Detekce pomalu pozitivního trendu podle nastavené citlivosti |*trenddetector.sensitivity:* práh na skóre detektoru (výchozí: 3.25, 3.25 - 5 je rozumný rozsah pro výběr; Čím vyšší, tím méně citlivé) |tscore: plovoucí číslo představující skóre anomálií na trendu |
| Detektory změny úrovně | Obousměrný detektor změny úrovně |Detekce změny hladiny směrem nahoru i dolů podle nastavené citlivosti |*bileveldetector.sensitivity:* práh na skóre detektoru (výchozí: 3.25, 3.25 - 5 je rozumný rozsah pro výběr; Čím vyšší, tím méně citlivé) |rpscore: plovoucí číslo představující skóre anomálií při změně hladiny směrem nahoru a dolů |

### <a name="parameters"></a>Parametry
Podrobnější informace o těchto vstupních parametrech jsou uvedeny v následující tabulce:

| Vstupní parametry | Popis | Výchozí nastavení | Typ | Platný rozsah | Navrhovaný rozsah |
| --- | --- | --- | --- | --- | --- |
| detectors.historywindow |Historie (v počtu datových bodů) používaná pro výpočty skóre anomálií |500 |celé číslo |10-2000 |Závislé na časových řadech |
| detectors.spikesdips | Zda zjistit pouze hroty, pouze poklesy, nebo obojí |Obojí |Výčtové |Oba, Hroty, Poklesy |Obojí |
| bileveldetector.citlivost |Citlivost pro obousměrný detektor změny úrovně. |3.25 |double |Žádný |3.25-5 (Menší hodnoty znamenají citlivější) |
| trenddetector.sensitivit |Citlivost pro pozitivní trendový detektor. |3.25 |double |Žádný |3.25-5 (Menší hodnoty znamenají citlivější) |
| tspikedetector.citlivost |Citlivost detektoru TSpike |3 |celé číslo |1-10 |3-5 (Menší hodnoty znamenají citlivější) |
| zspikedetector.citlivost |Citlivost detektoru ZSpike |3 |celé číslo |1-10 |3-5 (Menší hodnoty znamenají citlivější) |
| postprocess.tailRows |Počet nejnovějších datových bodů, které mají být uchovávány ve výsledcích výstupu |0 |celé číslo |0 (zachovat všechny datové body) nebo zadat počet bodů, které chcete zachovat ve výsledcích |Není dostupné. |

### <a name="output"></a>Výstup
Rozhraní API spustí všechny detektory na datech časové řady a vrátí skóre anomálií a binární indikátory špičky pro každý bod v čase. V následující tabulce jsou uvedeny výstupy z rozhraní API.

| Výstupy | Popis |
| --- | --- |
| Time |Časová razítka z nezpracovaných dat nebo agregovaná (a/nebo) imputovaná data, pokud je použita agregace (nebo) chybějící imputace dat |
| Data |Hodnoty z nezpracovaných dat nebo agregovaná (a/nebo) imputovaná data, pokud je použita agregace (nebo) chybějící imputace dat |
| Tspike |Binární indikátor označující, zda detektor TSpike detekuje špičku |
| ZSpike |Binární indikátor označující, zda detektor ZSpike detekuje špičku |
| rpscore |Plovoucí číslo představující skóre anomálií při změně obousměrné úrovně |
| rpalert |Hodnota 1/0 označující, že existuje anomálie změny obousměrné úrovně na základě vstupní citlivosti |
| tscore |Plovoucí číslo představující skóre anomálií na kladný trend |
| talert |Hodnota 1/0, která naznačuje pozitivní trendovou anomálii založenou na vstupní citlivosti |

## <a name="scorewithseasonality-api"></a>ScoreWithSeasonality API
Rozhraní ScoreWithSeasonality API se používá pro spuštění detekce anomálií na časové řady, které mají sezónní vzory. Toto rozhraní API je užitečné pro detekci odchylek v sezónnívzory.
Následující obrázek znázorňuje příklad anomálií zjištěných v sezónníčasové řadě. Časová řada má jeden hrot (první černá tečka), dva poklesy (druhá černá tečka a jedna na konci) a jednu změnu úrovně (červená tečka). Jak pokles uprostřed časové řady, tak změna úrovně jsou patrné až po odstranění sezónních složek ze série.
![Api sezónnosti][2]

### <a name="detectors"></a>Detektory
Detektory v koncovém bodě sezónnosti jsou podobné detektorům v koncovém bodu bez sezónnosti, ale s mírně odlišnými názvy parametrů (uvedené níže).

### <a name="parameters"></a>Parametry

Podrobnější informace o těchto vstupních parametrech jsou uvedeny v následující tabulce:

| Vstupní parametry | Popis | Výchozí nastavení | Typ | Platný rozsah | Navrhovaný rozsah |
| --- | --- | --- | --- | --- | --- |
| preprocess.aggregationInterval |Interval agregace v sekundách pro agregaci vstupních časových řad |0 (neprovádí se žádná agregace) |celé číslo |0: přeskočit agregaci, > 0 jinak |5 minut až 1 den, závisí na časových řadách |
| preprocess.aggregationFunc |Funkce použitá pro agregaci dat do zadaného intervalu agregace |mean |Výčtové |průměr, součet, délka |Není dostupné. |
| preprocess.replaceMissing |Hodnoty použité k přiznamují chybějící data |lkv (poslední známá hodnota) |Výčtové |nula, lkv, průměr |Není dostupné. |
| detectors.historywindow |Historie (v počtu datových bodů) používaná pro výpočty skóre anomálií |500 |celé číslo |10-2000 |Závislé na časových řadech |
| detectors.spikesdips | Zda zjistit pouze hroty, pouze poklesy, nebo obojí |Obojí |Výčtové |Oba, Hroty, Poklesy |Obojí |
| bileveldetector.citlivost |Citlivost pro obousměrný detektor změny úrovně. |3.25 |double |Žádný |3.25-5 (Menší hodnoty znamenají citlivější) |
| postrenddetector.citlivost |Citlivost pro pozitivní trendový detektor. |3.25 |double |Žádný |3.25-5 (Menší hodnoty znamenají citlivější) |
| negtrenddetector.citlivost |Citlivost pro negativní trendový detektor. |3.25 |double |Žádný |3.25-5 (Menší hodnoty znamenají citlivější) |
| tspikedetector.citlivost |Citlivost detektoru TSpike |3 |celé číslo |1-10 |3-5 (Menší hodnoty znamenají citlivější) |
| zspikedetector.citlivost |Citlivost detektoru ZSpike |3 |celé číslo |1-10 |3-5 (Menší hodnoty znamenají citlivější) |
| sezónality.enable |Zda má být provedena analýza sezónnosti |true |Boolean |pravda, nepravdivé |Závislé na časových řadech |
| sezónality.numSezóna |Maximální počet periodických cyklů, které mají být detekovány |1 |celé číslo |1, 2 |1-2 |
| sezónality.transformace |Zda musí být před použitím detekce anomálií odstraněny sezónní (a) trendové složky |mimo sezónu |Výčtové |žádný, deseason, deseasontrend |Není dostupné. |
| postprocess.tailRows |Počet nejnovějších datových bodů, které mají být uchovávány ve výsledcích výstupu |0 |celé číslo |0 (zachovat všechny datové body) nebo zadat počet bodů, které chcete zachovat ve výsledcích |Není dostupné. |

### <a name="output"></a>Výstup
Rozhraní API spustí všechny detektory na datech časové řady a vrátí skóre anomálií a binární indikátory špičky pro každý bod v čase. V následující tabulce jsou uvedeny výstupy z rozhraní API.

| Výstupy | Popis |
| --- | --- |
| Time |Časová razítka z nezpracovaných dat nebo agregovaná (a/nebo) imputovaná data, pokud je použita agregace (nebo) chybějící imputace dat |
| OriginalData |Hodnoty z nezpracovaných dat nebo agregovaná (a/nebo) imputovaná data, pokud je použita agregace (nebo) chybějící imputace dat |
| Zpracovaná data |Některá z následujících možností: <ul><li>Sezónně očištěné časové řady, pokud byla zjištěna významná sezónnost a byla vybrána možnost odsezónyní;</li><li>sezónně očištěné a detrendované časové řady, pokud byla zjištěna významná sezónnost a zvolena možnost deseasontrend</li><li>v opačném případě je tato možnost stejná jako</li> |
| Tspike |Binární indikátor označující, zda detektor TSpike detekuje špičku |
| ZSpike |Binární indikátor označující, zda detektor ZSpike detekuje špičku |
| BiLevelChangeScore |Plovoucí číslo představující skóre anomálií při změně úrovně |
| BiLevelChangeAlert |Hodnota 1/0 označující anomálii změny hladiny na základě vstupní citlivosti |
| PosTrendScore |Plovoucí číslo představující skóre anomálií na kladný trend |
| PosTrendAlert |Hodnota 1/0, která naznačuje pozitivní trendovou anomálii založenou na vstupní citlivosti |
| NegTrendScore |Plovoucí číslo představující skóre anomálií na negativní mj. |
| NegTrendAlert |Hodnota 1/0, která naznačuje, že existuje záporná trendová anomálie založená na vstupní citlivosti |

[1]: ./media/apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/apps-anomaly-detection-api/anomaly-detection-seasonal.png

