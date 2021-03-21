---
title: Rozdělení dat a křížové ověřování v automatizovaném strojovém učení
titleSuffix: Azure Machine Learning
description: Naučte se konfigurovat rozdělení datových sad a křížové ověřování pro automatizované experimenty strojového učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, automl
ms.author: cesardl
author: CESARDELATORRE
ms.reviewer: nibaccam
ms.date: 02/23/2021
ms.openlocfilehash: 31d3dc2c2d8194541ba1fe7d0865e6c939d75f73
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501573"
---
# <a name="configure-data-splits-and-cross-validation-in-automated-machine-learning"></a>Konfigurace dělení dat a křížového ověřování v rámci automatizovaného strojového učení

V tomto článku se seznámíte s různými možnostmi konfigurace školicích dat a rozdělených dat spolu s nastavením křížového ověřování pro automatizované Machine Learning, automatizované ML a experimenty.

Při použití automatizovaného ML v Azure Machine Learning k sestavení více modelů ML musí každý podřízený běh ověřit související model výpočtem metrik kvality pro daný model, jako je přesnost nebo AUC vážená. Tyto metriky se vypočtou porovnáním předpovědi vytvořených s každým modelem a skutečnými popisky z minulých pozorování v datech ověřování. [Přečtěte si další informace o tom, jak se počítají metriky na základě typu ověřování](#metric-calculation-for-cross-validation-in-machine-learning). 

Automatizované experimenty ML provádí ověření modelu automaticky. Následující části popisují, jak můžete upravit nastavení ověřování pomocí [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/). 

Informace o prostředí s nízkým kódováním nebo bez kódu najdete [v tématu Vytvoření automatizovaných experimentů strojového učení v Azure Machine Learning Studiu](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment). 

> [!NOTE]
> Studio v současné době podporuje rozdělená a ověřovací data a také možnosti křížového ověřování, ale nepodporuje určení jednotlivých datových souborů pro sadu ověřování. 

## <a name="prerequisites"></a>Předpoklady

Pro tento článek potřebujete,

* Pracovní prostor služby Azure Machine Learning. Pokud chcete vytvořit pracovní prostor, přečtěte si téma [vytvoření Azure Machine Learningho pracovního prostoru](how-to-manage-workspace.md).

* Seznamte se s nastavením automatizovaného experimentu strojového učení s Azure Machine Learning SDK. Pokud si chcete prohlédnout základní modely návrhu experimentů pro strojové učení, postupujte podle [kurzu](tutorial-auto-train-models.md) nebo [postupu](how-to-configure-auto-train.md) .

* Porozumění datům vlaků a ověření a vzájemnému ověřování jako konceptů strojového učení. Vysvětlení vysoké úrovně

    * [Informace o školeních, ověřování a testování dat ve službě Machine Learning](https://towardsdatascience.com/train-validation-and-test-sets-72cb40cba9e7)

    * [Vysvětlení vzájemného ověřování ve strojovém učení](https://towardsdatascience.com/understanding-cross-validation-419dbd47e9bd) 

## <a name="default-data-splits-and-cross-validation-in-machine-learning"></a>Výchozí rozdělená data a křížové ověřování ve strojovém učení

Pomocí objektu [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) definujte nastavení experimentů a školení. V následujícím fragmentu kódu si všimněte, že jsou definovány pouze požadované parametry, které jsou parametry pro `n_cross_validation` nebo nejsou `validation_ data` zahrnuty  .

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'Class'
                            )
```

Pokud explicitně neurčíte `validation_data` `n_cross_validation` parametr nebo, použije automatizované ml výchozí techniky v závislosti na počtu řádků, které jsou k dispozici v jedné datové sadě `training_data` :

|&nbsp;Velikost dat &nbsp; školení| Technika ověřování |
|---|-----|
|**Větší &nbsp; než &nbsp; 20 000 &nbsp; řádků**| Je použito rozdělení dat pro vlak nebo ověření. Ve výchozím nastavení se jako sada ověření provede 10% počáteční sady dat školení. Pak se tato sada ověření používá pro výpočet metrik.
|**Menší &nbsp; než &nbsp; 20 000 &nbsp; řádků**| Je použit přístup pro křížové ověřování. Výchozí počet skládání závisí na počtu řádků. <br> **Pokud je datová sada menší než 1 000 řádků**, použije se 10 skládání. <br> **Pokud jsou řádky mezi 1 000 a 20 000**, budou použity tři skládání.

## <a name="provide-validation-data"></a>Zadat ověřovací data

V takovém případě můžete buď začít s jedním datovým souborem a rozdělit ho na data a sady dat pro ověření, nebo můžete zadat samostatný datový soubor pro sadu ověřování. V obou případech `validation_data` parametr v `AutoMLConfig` objektu přiřazuje ta data, která chcete použít jako sadu ověřování. Tento parametr přijímá pouze datové sady ve formě [Azure Machine Learning datové](how-to-create-register-datasets.md) sady nebo PANDAS dataframe.   

> [!NOTE]
> `validation_size`Parametr není podporován ve scénářích prognózy.

Následující příklad kódu explicitně definuje, kterou část poskytnutých dat v nástroji `dataset` můžete použít pro účely školení a ověření.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

training_data, validation_data = dataset.random_split(percentage=0.8, seed=1)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = training_data,
                             validation_data = validation_data,
                             label_column_name = 'Class'
                            )
```

## <a name="provide-validation-set-size"></a>Zadat velikost sady ověření

V tomto případě je pro experiment k dispozici pouze jedna datová sada. To znamená, že `validation_data` parametr není  zadán a poskytnutá datová sada je přiřazena k `training_data` parametru.  

V `AutoMLConfig` objektu můžete nastavit `validation_size` parametr tak, aby obsahoval část školicích dat k ověření. To znamená, že sada ověření bude rozdělená pomocí automatizovaného ML z počátečního dodaného `training_data` . Tato hodnota by měla být v rozmezí od 0,0 do 1,0 (například 0,2 znamená, že se pro data ověření uchovávají 20% dat).

> [!NOTE]
> `validation_size`Parametr není podporován ve scénářích prognózy. 

Podívejte se na následující příklad kódu:

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="k-fold-cross-validation"></a>K skládání křížového ověřování

Chcete-li provést křížové ověření k skládání, zahrňte `n_cross_validations` parametr a nastavte jej na hodnotu. Tento parametr určuje, kolik různých ověření má být provedeno, na základě stejného počtu skládání.

> [!NOTE]
> `n_cross_validations`Parametr není podporován v klasifikačních scénářích, které používají rozsáhlé sítě neuronové.
 
V následujícím kódu jsou definovány pět skládání pro křížové ověřování. Proto pět různých školení, každé školení pomocí 4/5 dat a každé ověření s použitím 1/5 dat s různými položením pokaždé.

V důsledku toho se metriky počítají s průměrem pěti metrik ověřování.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 5
                             label_column_name = 'Class'
                            )
```
## <a name="monte-carlo-cross-validation"></a>Křížové ověřování Monte Carlo

Chcete-li provést vzájemné ověřování Monte Carlo, zahrňte do `validation_size` `n_cross_validations` svého objektu parametry a `AutoMLConfig` . 

V případě vzájemného ověřování Monte Carlo se automatizovaná ML oddělí část školicích dat určených `validation_size` parametrem pro ověření a potom přiřadí zbývající data pro školení. Tento proces se pak opakuje na základě hodnoty zadané v `n_cross_validations` parametru. tím se v náhodných intervalech vygeneruje nová školení a rozdělení.

> [!NOTE]
> Křížové ověřování Monte Carlo není podporováno ve scénářích prognózování.

Kód následného kódu definuje 7 skládání pro křížové ověřování a 20% školicích dat, které by se měly použít k ověření. A proto 7 různých školení, každé školení využívá 80% dat a každé ověřování používá při každém každé ověření 20% dat s jiným přeložením.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 7
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="specify-custom-cross-validation-data-folds"></a>Zadat vlastní skládání dat křížového ověřování

Můžete také zadat vlastní skládání dat pro křížové ověřování (CV). To je považováno za pokročilejší scénář, protože určíte, které sloupce se mají rozdělit a použít k ověření.  Do svých školicích dat zahrnete vlastní rozdělené sloupce CV a určíte, které sloupce se naplní názvy sloupců v `cv_split_column_names` parametru. Každý sloupec představuje jedno rozdělení křížového ověření a je vyplněno celočíselnými hodnotami 1 nebo 0--, kde 1 znamená, že řádek by měl být použit pro školení a 0 označuje, že se má řádek použít k ověření.

Následující fragment kódu obsahuje data z bank marketingu se dvěma rozdělenými sloupci CV ' CV1 ' a ' CV2 '.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_with_cv.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'y',
                             cv_split_column_names = ['cv1', 'cv2']
                            )
```

> [!NOTE]
> Pokud chcete použít `cv_split_column_names` s `training_data` a `label_column_name` , upgradujte prosím Azure Machine Learning Python SDK verze 1.6.0 nebo novější. Pro předchozí verze sady SDK použijte prosím použití `cv_splits_indices` , ale Všimněte si, že se používá `X` pouze pro `y` vstup a datovou sadu. 


## <a name="metric-calculation-for-cross-validation-in-machine-learning"></a>Výpočet metriky pro vzájemné ověřování ve strojovém učení

Když se použije křížové ověření k přeložení nebo Monte Carlo, vypočítají se metriky pro každé přeložení pro ověření a pak se agreguje. Agregační operace je průměrem pro skalární metriky a součet pro grafy. Metriky vypočítané během vzájemného ověřování jsou založené na všech skládáních a proto všechny ukázky ze sady školení. [Přečtěte si další informace o metrikách v automatizovaném strojovém učení](how-to-understand-automated-ml.md).

Když se použije vlastní sada ověření nebo automaticky vybraná ověřovací sada, vypočítají se metriky vyhodnocení modelu jenom z této sady ověření, nikoli z dat školení.

## <a name="next-steps"></a>Další kroky

* [Zabránit nevyváženým datům a jejich přebudování](concept-manage-ml-pitfalls.md).
* [Kurz: Použití automatizovaného strojového učení k předpovědi taxislužbych tarifů a rozdělených dat do části](tutorial-auto-train-models.md#split-the-data-into-train-and-test-sets).
* Postup [automatického učení modelu prognózy časových řad](how-to-auto-train-forecast.md).
