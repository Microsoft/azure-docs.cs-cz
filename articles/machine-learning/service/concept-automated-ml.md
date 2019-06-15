---
title: Automatizované výběr algoritmů ML a laděním
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak služba Azure Machine Learning můžete automaticky vyberte algoritmus pro vás a generovat model z něj šetřit čas pomocí parametrů a kritéria, které poskytnete vybrat nejlepší algoritmus pro model.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 06/10/2019
ms.custom: seodec18
ms.openlocfilehash: 1dcdbbf0a2a71fa38b6eacd6a8d179cdad979937
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67059292"
---
# <a name="what-is-automated-machine-learning"></a>Co je automatické machine learning?

Automatizované strojového učení, také označuje jako automatické ML je proces automatizovat časově náročné, iterativní úkoly strojového učení vývoje modelů. Umožňuje datovým vědcům, analytiky a vývojáři k sestavení modelů ML s vysokou škálovatelností, efektivity a produktivity vše při udržování kvality modelu.

Tradiční strojového učení vývoje modelů je náročné vyžadující významné znalosti a čas vytvoření a porovnat desítky modely. Automatizované ML použijte, pokud chcete Azure Machine Learning k trénování a vyladění modelu pomocí cílové metriku, kterou zadáte. Služba poté iteruje skrz algoritmů ML párována s výběry funkcí, kde každé iteraci vytváří model se skóre školení. Čím vyšší skóre, tím lepší považuje model za "podle" vaše data.

Automatizované machine Learning, budete zrychlení čas potřebný k získání skvělé snadnou a efektivní modelů ML připravené pro produkční prostředí.

## <a name="when-to-use-automated-ml"></a>Kdy použít automatizované ML

Automatizované ML demokratizuje strojového učení proces vývoje modelu a umožňuje jejich uživatele, bez ohledu na to své znalosti vědy data k identifikaci kanálu začátku do konce strojového učení pro jakýkoli problém s.

Odborníci přes data, analytiky a vývojáři napříč odvětvími můžete použít automatické ML do:

+ Implementovat řešení strojového učení bez rozsáhlé znalosti programování
+ Šetřete čas a prostředky
+ Využít osvědčené postupy pro datové vědy
+ Poskytují agilní řešení problémů

## <a name="how-automated-ml-works"></a>Funguje jak automatizované ML

Pomocí **služby Azure Machine Learning**, můžete navrhnout a spustit vaše automatizované školení experimenty ML tímto postupem:

1. **Zjistěte, co problém ML** mají být vyřešeny: klasifikace, Prognózování nebo regrese

1. **Zadat zdroj a formát s popiskem trénovacích dat**: Pole Numpy nebo Pandas dataframe

1. **Konfigurace cílové výpočetní prostředí k tréninku modelu**, jako například vaše [místního počítače, Azure Machine Learning vypočítá, vzdálených virtuálních počítačích nebo Azure Databricks](how-to-set-up-training-targets.md).  Další informace o automatické školení [vzdáleného prostředku](how-to-auto-train-remote.md).

1. **Konfigurace automatizovaného strojového učení parametry** , které definují, kolik iterací prostřednictvím různých modelů, nastavení hyperparameter pokročilé předběžného zpracování a snadné a které metriky se podívat na při určování nejlepší model.  Můžete nakonfigurovat nastavení pro automatické výukového experimentu [na webu Azure portal](how-to-create-portal-experiments.md) nebo [pomocí sady SDK](how-to-configure-auto-train.md).

1. **Odešlete školení spustit.**

  ![Automatizované Machine learning](./media/how-to-automated-ml/automl-concept-diagram2.png)

Během cvičení, vytvoří službu Azure Machine Learning celou řadou v paralelní kanály, které se pokoušejí různé algoritmy a parametry. Zastaví se po volání ukončit kritéria definovaná v experimentu.

Můžete také zkontrolovat zaznamenané spuštění informace, které obsahuje metriky shromážděné za běhu. Školení vznikly Python serializovat objekt (`.pkl` souboru), který obsahuje model a data předběžného zpracování.

Při vytváření modelů je automatické, můžete také [zjistěte, jak důležité nebo užitečné funkce jsou](how-to-configure-auto-train.md#explain) pro generované modely.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="time-series-forecasting"></a>Vytváření prognóz časových řad
Vytváření prognóz je nedílnou součástí jakoukoli firmu, ať už jde výnosy, inventáře, sales nebo zákazníků. Automatizované ml používá řadu kombinované techniky a přístupy k doporučujeme prognózy vysoce kvalitní časovou řadu. Časových řad experimentů v ml automatizovaná jsou považovány za problém regrese s množstvím proměnných. Poslední časové řady hodnoty jsou "neseskupené" se další dimenze pro regresor společně s další prediktory. 

Tento přístup, na rozdíl od klasického čas řady metod, má výhodu přirozeně začlenění více kontextových proměnných a jejich vzájemných vztazích během cvičení. Ve skutečných aplikacích prognóz několika faktory mohou mít vliv na prognózy. Například při Prognózování prodeje, interakce historické trendy, exchange sazba a cena všechny společně jednotka prodejní výsledky. Další výhodou je, všechny nejnovější inovace v regresních modelů ihned k předpovědi.

Jak daleko dopředu prognózy by měl rozšířit (prognózy horizontu) je součástí základní specifikace prognózy. Nastavení požadovaný parametr `max_horizon` v experimentu definuje kolik jednotek období (podle časového intervalu cvičných dat, např. měsíčně, každý týden uživatel by měl předpovědět navýšení kapacity. 

Automatizované ML zjistí model jednu, ale často interně větvenou pro všechny položky v datové sady a predikcí možností. Další data jsou tedy k dispozici k odhadu parametry modelu a je možné generalizace nezobrazený řady. 

Funkce z trénovací data extrahovat hrát důležitou roli. Automatizované ML provede standardní předběžného zpracování kroky a vygeneruje další funkce časových řad (např. rok, měsíc, den v týdnu atd.) pro zachycení sezónní efekty a maximalizovat prediktivní přesnost. 

Pokud se hodí pro váš scénář, můžete nastavit automatizované ML k vytvoření zjevně demonstruje výskyt pomalé (`target_lags`) nebo se zajištěním provozu okno agregaci dat (`target_rolling_window_size`) z vaší cílovou (`y_value`) za hodnoty. 

## <a name="preprocessing"></a>Předzpracování

V každé automatizované experimentu strojového učení je Předzpracovaný vašich dat pomocí výchozí metody a volitelně prostřednictvím pokročilého předběžného zpracování.

### <a name="automatic-preprocessing-standard"></a>Automatické předběžného zpracování (standard)

V každé automatizované experimentu strojového učení vaše data automaticky škálovat nebo normalizovány na Nápověda algoritmy dobře fungují.  Při tréninku modelu, jeden z následujících postupů škálování nebo normalizace se použijí pro každý model.

|Škálování&nbsp;&&nbsp;normalizace| Popis |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Standardizovat funkce odebráním střední a škáluje se na odchylky jednotky  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Funkce transformuje Škálováním každé funkce tak, že minimum a maximum tento sloupec  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Můžete škálovat jednotlivé funkce maximální absolutní hodnota |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Tato funkce Scaler podle jejich rozsah quantile |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Lineární snížení projekt k nižší rozměrného prostoru pomocí singulární rozložené hodnota dat. |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Tato transformace provádí lineární snížení prostřednictvím zkrácená hodnota singulární rozložené (SVD). Rozporu PCA tento odhad není datacentra data před computingu rozložené singulární hodnotu. To znamená, že ho můžete efektivně spolupracovat s scipy.sparse matice |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Každá ukázka (to znamená, že každý řádek v matici dat) se alespoň jedna součást nenulové je znovu škálovaný nezávisle na ostatních vzorků tak, aby jeho norm (l1 nebo l2) má jednu |

### <a name="advanced-preprocessing-optional-featurization"></a>Pokročilé předběžného zpracování: volitelné snadné

K dispozici, například chybějící hodnoty imputace, kódování a transformace jsou taky další pokročilé předběžného zpracování a snadné. [Další informace o tom, jaké snadné je součástí](how-to-create-portal-experiments.md#preprocess). Povolením tohoto nastavení se:

+ Azure portal: Výběr **předzpracování** zaškrtávací políčko ve **upřesňující nastavení** [tímto postupem](how-to-create-portal-experiments.md).

+ Python SDK: Určení `"preprocess": True` pro [ `AutoMLConfig` třídy](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

## <a name="ensemble-models"></a>Modely skupiny stromů

Trénovat modely komplet pomocí automatizovaných machine learning s [algoritmus výběru skupiny stromů Caruana s inicializací seřazený komplet](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf). Komplet learning zlepšuje výsledky machine learning a prediktivní výkon kombinace mnoha modelů na rozdíl od použití jedné modelů. Komplet iterace se zobrazí jako poslední iteraci spuštění.

## <a name="training-metric-output"></a>Výstup trénovacích metriky

Pokud chcete zobrazit metriky přesnosti školení pro každou iteraci spuštění několika způsoby.

* Pomocí widgetu Jupyter.
* Použití `get_metrics()` funkce na všech `Run` objektu.
* Zobrazte metriky na webu Azure Portal v experimentu.

### <a name="classification-metrics"></a>Klasifikace metriky

Tyto metriky jsou uloženy v každé iteraci spuštění úlohy klasifikace.

|Metrika|Popis|Výpočet|Další parametry
--|--|--|--|
AUC_Macro| AUC je oblasti pod křivkou charakteristiku provozní příjemce. Makro je aritmetický průměr AUC pro každou třídu.  | [Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Průměrná = "makro"|
AUC_Micro| AUC je oblasti pod křivkou charakteristiku provozní příjemce. Micro je vypočítán globálně kombinací pravdivě pozitivní i falešně pozitivních výsledků z každé třídě| [Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Průměrná = "micro"|
AUC_Weighted  | AUC je oblasti pod křivkou charakteristiku provozní příjemce. Váha je aritmetický průměr skóre pro každou třídu váženo podle počtu true instancí každé třídy| [Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|Průměrná = "váha"
accuracy|Přesnost je procento předpokládané popisky, které přesně odpovídají true popisky. |[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Žádný|
average_precision_score_macro|Průměrná přesnost shrnuje křivky přesnosti a úplnosti jako vážený průměr přesnosti dosáhnout při každé prahové hodnotě, zvýšení spojené s vracením z předchozí prahové hodnoty použít jako váhu. Aritmetický průměr přesnost skóre každé třídy je – makro|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Průměrná = "makro"|
average_precision_score_micro|Průměrná přesnost shrnuje křivky přesnosti a úplnosti jako vážený průměr přesnosti dosáhnout při každé prahové hodnotě, zvýšení spojené s vracením z předchozí prahové hodnoty použít jako váhu. Micro je vypočítán globálně pomocí kombinace pravdivě pozitivní i falešně pozitivních výsledků při každé odříznutí|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Průměrná = "micro"|
average_precision_score_weighted|Průměrná přesnost shrnuje křivky přesnosti a úplnosti jako vážený průměr přesnosti dosáhnout při každé prahové hodnotě, zvýšení spojené s vracením z předchozí prahové hodnoty použít jako váhu. Váha je aritmetický průměr přesnost průměrné skóre pro každou třídu váženo podle počtu true instancí každé třídy|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Průměrná = "váha"|
balanced_accuracy|Vyvážený přesnost je aritmetický průměr o odvolání pro každou třídu.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Průměrná = "makro"|
f1_score_macro|F1 skóre je průměr přesnosti a odvolání. Aritmetický průměr skóre F1 pro každou třídu je – makro|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Průměrná = "makro"|
f1_score_micro|F1 skóre je průměr přesnosti a odvolání. Micro je vypočítán globálně výpočtem celkového pravdivě pozitivní, falešně negativní a počet falešně pozitivních výsledků|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Průměrná = "micro"|
f1_score_weighted|F1 skóre je průměr přesnosti a odvolání. Vážený průměr třídy frekvence skóre F1 pro každou třídu|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Průměrná = "váha"|
log_loss|Toto je ztráta funkce použitá v logistické regrese (multinomial) a rozšíření jako jsou neuronové sítě definované jako záporné pravděpodobnost protokolu true popisků zadaný pravděpodobnostní třídění předpovědi. Jeden vzorek hodnotou true, pokud popisek yt v {0,1} a odhad pravděpodobnosti yp této yt = 1, ztráta protokolu je – protokolování P (yt&#124;yp) =-(yt log(yp) + (1 - yt) protokolu (1 - yp))|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Žádný|
norm_macro_recall|Normalizovaná si možná Vzpomínáte – makro je makro si možná Vzpomínáte, normalizovaná tak, aby náhodná výkonu má skóre 0 a ideální výkon má skóre 1. Toho dosáhnete pomocí norm_macro_recall: = (recall_score_macro - R) /(1-R), kde R je očekávané hodnotě recall_score_macro pro náhodné predikcí (tj, R = 0,5 pro binární klasifikaci) a R=(1/C) problémů klasifikace C – třída|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Průměrná = "makro" a potom (recall_score_macro - R) /(1-R), kde R je očekávané hodnotě recall_score_macro pro náhodné predikcí (tj, R = 0,5 pro binární klasifikaci) a R=(1/C) problémů klasifikace C – třída|
precision_score_macro|Přesnost je procento prvky označeny jako určité třídy, které jsou ve skutečnosti v dané třídě. Aritmetický průměr přesnosti pro každou třídu je – makro|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Průměrná = "makro"|
precision_score_micro|Přesnost je procento prvky označeny jako určité třídy, které jsou ve skutečnosti v dané třídě. Micro je vypočítán globálně výpočtem celkového pravdivě pozitivní a počet falešně pozitivních výsledků|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Průměrná = "micro"|
precision_score_weighted|Přesnost je procento prvky označeny jako určité třídy, které jsou ve skutečnosti v dané třídě. Váha je aritmetický průměr přesnosti pro každou třídu váženo podle počtu true instancí každé třídy|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Průměrná = "váha"|
recall_score_macro|Odvolání je procento prvky ve skutečnosti určité třídy, které jsou správně označené. Aritmetický průměr o odvolání pro každou třídu je – makro|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Průměrná = "makro"|
recall_score_micro|Odvolání je procento prvky ve skutečnosti určité třídy, které jsou správně označené. Micro je vypočítán globálně výpočtem celkového pravdivě pozitivní, falešně negativní|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Průměrná = "micro"|
recall_score_weighted|Odvolání je procento prvky ve skutečnosti určité třídy, které jsou správně označené. Váha je aritmetický průměr o odvolání pro každou třídu váženo podle počtu true instancí každé třídy|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Průměrná = "váha"|
weighted_accuracy|Vážený přesnost je přesnost, kde je rovna podíl true instancí v tomto příkladu true třídě váhou na každý příklad|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight je rovna poměr této třídy pro každý prvek v cílovém vektor|

### <a name="regression-and-forecasting-metrics"></a>Regrese a Prognózování metriky

Tyto metriky jsou uloženy v každé iteraci spuštění pro regresní nebo Prognózování úloh.

|Metrika|Popis|Výpočet|Další parametry
--|--|--|--|
explained_variance|Bylo vysvětleno odchylky je poměr, ke kterému matematické modelu účty pro variantu dané datové sady. Je, že procento snížení odchylku původní data, která mají odchylku chyby. Pokud chyby je 0, je roven bylo vysvětleno variance.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Žádný|
r2_score|R2 je koeficient stanovení nebo snížení procenta v porovnání s modelem směrný plán, který zobrazí průměr kvadratických chyb. Pokud chyby je 0, je roven bylo vysvětleno variance.|[Výpočet](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Žádný|
spearman_correlation|Korelace spearman je nonparametric míra monotonicity vztah mezi dvěma datovými sadami. Na rozdíl od korelace Pearson korelace Spearman nepředpokládá, že oba datové sady mají obvykle distribuovat. Stejně jako ostatní koeficienty korelace ten se pohybuje mezi -1 a + 1 hodnotou 0 zdání žádnou souvislost. Korelací mezi -1 nebo + 1 implikují přesné monotónní relace. Pozitivní korelace neznamená, že jako x zvýšení zločinců se stejně y. Negativní korelace znamenají, že jako x zvyšují v pořadí, y snižuje.|[Výpočet](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Žádný|
mean_absolute_error|To znamenat, že absolutní chyba je očekávaná hodnota absolutní hodnota rozdílu mezi cílem a do predikce.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Žádný|
normalized_mean_absolute_error|Normalizovaná střední absolutní chyba je střední absolutní chyba rozdělené podle rozsahu dat|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Rozdělit podle rozsahu dat|
median_absolute_error|Střední absolutní chyba je Medián všech absolutních rozdílů mezi cílem a do predikce. To je robustní odlehlé hodnoty.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Žádný|
normalized_median_absolute_error|Normalizovaná střední absolutní chyba je střední absolutní chyba rozdělené podle rozsahu dat|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Rozdělit podle rozsahu dat|
root_mean_squared_error|Kořenové bude kvadratická chyba odmocninu očekávané kvadratickému rozdílu mezi cílem a do predikce.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Žádný|
normalized_root_mean_squared_error|Normalizovaná kořenové bude kvadratická chyba kořenové střední kvadratické chyby rozdělené podle rozsahu dat|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Rozdělit podle rozsahu dat|
root_mean_squared_log_error|Kořenové znamenat protokolu kvadratických chyb je odmocninu očekávané kvadratická chyba logaritmické|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Žádný|
normalized_root_mean_squared_log_error|Normalizované kořenové mean ve čtverci protokolu chyba je chyba mean ve čtverci protokolu kořenové rozdělené podle rozsahu dat|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Rozdělit podle rozsahu dat|


## <a name="use-with-onnx-in-c-apps"></a>Použití s ONNX v C# aplikace

Pomocí služby Azure Machine Learning slouží k sestavení modelu Python a jeho převodu do formátu ONNX automatizované ML. Podporuje modul runtime ONNX C#, abyste mohli použít model sestavený automaticky ve vaší C# aplikace bez nutnosti pro nahrávání nebo některý z síťovou latenci, které zavádějí koncové body REST. Zkuste příkladem tento tok [v tento poznámkový blok Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb).

## <a name="automated-ml-across-microsoft"></a>Automatizované ML rámci společnosti Microsoft

Automatizované ML je také dostupná v dalších řešení Microsoftu, jako:

|Integrace|Popis|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|Výběr automatického modelu a školení v aplikacích .NET pomocí sady Visual Studio a Visual Studio Code s ML.NET automatizované ML (Preview).|
|[HDIsnight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|Horizontální navýšení kapacity vašeho automatizované úlohy trénování ML ve Sparku v HDInsight clustery paralelně.|
|[PowerBI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|Vyvolání modelů strojového učení přímo v Power BI (Preview).|
|[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)|Vytvořte nové modely strojového učení nad vašimi daty v clusterech velké objemy dat SQL serveru 2019.|

## <a name="next-steps"></a>Další postup

Podívejte se na příklady a další informace o vytváření modelů pomocí automatizované Machine Learning:

+ Postupujte podle [kurzu: Automaticky vyškolíme model klasifikace pomocí automatizované Azure Machine Learning](tutorial-auto-train-models.md)

+ Nakonfigurujte nastavení pro automatické výukového experimentu:
  + V rozhraní Azure portal [postupujte podle tohoto návodu](how-to-create-portal-experiments.md).
  + Pomocí sady Python SDK [postupujte podle tohoto návodu](how-to-configure-auto-train.md).

+ Zjistěte, jak automaticky pomocí dat časových řad, trénování [postupujte podle tohoto návodu](how-to-auto-train-forecast.md).

+ Vyzkoušejte si [ukázky Poznámkový blok Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
