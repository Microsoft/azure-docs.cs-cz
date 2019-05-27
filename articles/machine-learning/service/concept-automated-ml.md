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
ms.date: 05/21/2019
ms.custom: seodec18
ms.openlocfilehash: 88e4e305e0f66c61ab4d73bcfef21319b4d02946
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65989796"
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

  ![Automatizované Machine learning](./media/how-to-automated-ml/automl-concept-diagram.png)

Během cvičení, vytvoří službu Azure Machine Learning celou řadou v paralelní kanály, které se pokoušejí různé algoritmy a parametry. Zastaví se po volání ukončit kritéria definovaná v experimentu.

Můžete také zkontrolovat zaznamenané spuštění informace, které obsahuje metriky shromážděné za běhu. Školení vznikly Python serializovat objekt (`.pkl` souboru), který obsahuje model a data předběžného zpracování.

Při vytváření modelů je automatické, můžete také [zjistěte, jak důležité nebo užitečné funkce jsou](how-to-configure-auto-train.md#explain) pro generované modely.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

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

## <a name="use-with-onnx-in-c-apps"></a>Použití s ONNX v C# aplikace

Pomocí služby Azure Machine Learning slouží k sestavení modelu Python a jeho převodu do formátu ONNX automatizované ML. Podporuje modul runtime ONNX C#, abyste mohli použít model sestavený automaticky ve vaší C# aplikace bez nutnosti pro nahrávání nebo některý z síťovou latenci, které zavádějí koncové body REST. Zkuste příkladem tento tok [v tento poznámkový blok Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb).

## <a name="automated-ml-across-microsoft"></a>Automatizované ML rámci společnosti Microsoft

Automatizované ML je také dostupná v dalších řešení Microsoftu, jako:

+ V aplikacích .NET pomocí sady Visual Studio a Visual Studio Code s [ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)
+ [V HDInsight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md), kde horizontální navýšení kapacity automatizované ML trénovací úlohy ve Sparku v clusterech HDInsight paralelně.
+ [V Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)

## <a name="next-steps"></a>Další postup

Podívejte se na příklady a další informace o vytváření modelů pomocí automatizované Machine Learning:

+ Postupujte podle [kurzu: Automaticky vyškolíme model klasifikace pomocí automatizované Azure Machine Learning](tutorial-auto-train-models.md)

+ Nakonfigurujte nastavení pro automatické výukového experimentu:
  + V rozhraní Azure portal [postupujte podle tohoto návodu](how-to-create-portal-experiments.md).
  + Pomocí sady Python SDK [postupujte podle tohoto návodu](how-to-configure-auto-train.md).
  
+ Zjistěte, jak automaticky pomocí dat časových řad, trénování [postupujte podle tohoto návodu](how-to-auto-train-forecast.md).

+ Vyzkoušejte si [ukázky Poznámkový blok Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
