---
title: Konfigurace křížového ověřování a rozdělení dat při experimentech automatizovaného strojového učení
titleSuffix: Azure Machine Learning
description: Přečtěte si, jak nakonfigurovat křížové ověřování a rozdělení datových sad pro automatizované experimenty strojového učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, automl
ms.author: cesardl
author: CESARDELATORRE
ms.reviewer: nibaccam
ms.date: 06/16/2020
ms.openlocfilehash: c29c8ab31507c0ec904a7534e50ef6523e1aab96
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360101"
---
# <a name="configure-data-splits-and-cross-validation-in-automated-machine-learning"></a>Konfigurace dělení dat a křížového ověřování v rámci automatizovaného strojového učení

V tomto článku se dozvíte, jaké jsou různé možnosti konfigurace rozdělených a ověřovacích dat a křížového ověřování pro automatizované Machine Learning, AutoML a experimenty.

Když v Azure Machine Learning k sestavování více modelů ML použijete AutoML, musí každý podřízený běh ověřit související model výpočtem metrik kvality pro daný model, jako je přesnost nebo AUC vážená. Tyto metriky se vypočtou porovnáním předpovědi vytvořených s každým modelem a skutečnými popisky z minulých pozorování v datech ověřování. 

AutoML experimenty provádí ověření modelu automaticky. Následující části popisují, jak můžete upravit nastavení ověřování pomocí [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py). 

Informace o prostředí s nízkým kódováním nebo bez kódu najdete [v tématu Vytvoření automatizovaných experimentů strojového učení v Azure Machine Learning Studiu](how-to-use-automated-ml-for-ml-models.md). 

> [!NOTE]
> Studio v současné době podporuje rozdělení dat pro školení a ověřování a možnosti křížového ověřování, ale nepodporuje zadání jednotlivých datových souborů pro sadu ověřování. 

## <a name="prerequisites"></a>Požadavky

Pro tento článek potřebujete,

* Pracovní prostor služby Azure Machine Learning. Pokud chcete vytvořit pracovní prostor, přečtěte si téma [vytvoření Azure Machine Learningho pracovního prostoru](how-to-manage-workspace.md).

* Seznamte se s nastavením automatizovaného experimentu strojového učení s Azure Machine Learning SDK. Pokud si chcete prohlédnout základní modely návrhu experimentů pro strojové učení, postupujte podle [kurzu](tutorial-auto-train-models.md) nebo [postupu](how-to-configure-auto-train.md) .

* Porozumění datům pro křížové ověřování a analýzu/ověření se rozdělí jako koncepty. Vysvětlení vysoké úrovně

    * [O výukových, ověřovacích a testovacích sadách v Machine Learning](https://towardsdatascience.com/train-validation-and-test-sets-72cb40cba9e7)

    * [Porozumění vzájemnému ověřování](https://towardsdatascience.com/understanding-cross-validation-419dbd47e9bd)

## <a name="default--data-splits-and-cross-validation"></a>Výchozí rozdělená a křížová ověřování dat

Pomocí objektu [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?preserve-view=true&view=azure-ml-py) definujte nastavení experimentů a školení. V následujícím fragmentu kódu si všimněte, že jsou definovány pouze požadované parametry, které jsou parametry pro `n_cross_validation` nebo nejsou `validation_ data` zahrnuty **not** .

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

Pokud explicitně neurčíte `validation_data` `n_cross_validation` parametr nebo, použije AutoML výchozí techniky v závislosti na počtu řádků v jedné zadané datové sadě `training_data` :

|&nbsp;Velikost dat &nbsp; školení| Technika ověřování |
|---|-----|
|**Větší &nbsp; než &nbsp; 20 000 &nbsp; řádků**| Je použito rozdělení dat pro vlak nebo ověření. Ve výchozím nastavení se jako sada ověření provede 10% počáteční sady dat školení. Pak se tato sada ověření používá pro výpočet metrik.
|**Menší &nbsp; než &nbsp; 20 000 &nbsp; řádků**| Je použit přístup pro křížové ověřování. Výchozí počet skládání závisí na počtu řádků. <br> **Pokud je datová sada menší než 1 000 řádků** , použije se 10 skládání. <br> **Pokud jsou řádky mezi 1 000 a 20 000** , budou použity tři skládání.

## <a name="provide-validation-data"></a>Zadat ověřovací data

V takovém případě můžete buď začít s jedním datovým souborem a rozdělit ho na sady pro školení a ověřování, nebo můžete pro sadu ověřování zadat samostatný datový soubor. V obou případech `validation_data` parametr v `AutoMLConfig` objektu přiřazuje ta data, která chcete použít jako sadu ověřování. Tento parametr přijímá pouze datové sady ve formě [Azure Machine Learning datové](how-to-create-register-datasets.md) sady nebo PANDAS dataframe.   

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

V tomto případě je pro experiment k dispozici pouze jedna datová sada. To znamená, že `validation_data` parametr není **not** zadán a poskytnutá datová sada je přiřazena k `training_data` parametru.  V `AutoMLConfig` objektu můžete nastavit `validation_size` parametr tak, aby obsahoval část školicích dat k ověření. To znamená, že sada ověření bude rozdělena podle AutoML z počátečního `training_data` poskytnutého. Tato hodnota by měla být v rozmezí od 0,0 do 1,0 (například 0,2 znamená, že se pro data ověření uchovávají 20% dat).

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

## <a name="set-the-number-of-cross-validations"></a>Nastavení počtu křížových ověření

Chcete-li provést křížové ověření, zahrňte `n_cross_validations` parametr a nastavte jej na hodnotu. Tento parametr určuje, kolik různých ověření má být provedeno, na základě stejného počtu skládání.

V následujícím kódu jsou definovány pět skládání pro křížové ověřování. Proto pět různých školení, každé školení pomocí 4/5 dat a každé ověření s použitím 1/5 dat s různými položením pokaždé.

V důsledku toho se metriky počítají s průměrem 5 metrik ověřování.

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

## <a name="next-steps"></a>Další kroky

* [Zabránit nevyváženým datům a jejich přebudování](concept-manage-ml-pitfalls.md).
* [Kurz: Použití automatizovaného strojového učení k předpovědi taxislužbych tarifů a rozdělených dat do části](tutorial-auto-train-models.md#split-the-data-into-train-and-test-sets).
* Postup [automatického učení modelu prognózy časových řad](how-to-auto-train-forecast.md).