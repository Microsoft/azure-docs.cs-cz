---
title: 'Kurz: Trénování prvního modelu Azure ML v Pythonu'
titleSuffix: Azure Machine Learning
description: V tomto kurzu se naučíte základní návrhové vzory v Azure Machine Learning a trénovat jednoduchý model scikit-learn založený na datové sadě diabetes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 02/10/2020
ms.openlocfilehash: c8f259d2d4df46470a042c3f65ac1b8e1f66b1dd
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546018"
---
# <a name="tutorial-train-your-first-ml-model"></a>Výuka: Trénujte svůj první model ML

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Tento kurz je **druhou částí z dvoudílné série kurzů**. V předchozím kurzu jste [vytvořili pracovní prostor a zvolili vývojové prostředí](tutorial-1st-experiment-sdk-setup.md). V tomto kurzu se naučíte základní návrhové vzory v Azure Machine Learning a trénovat jednoduchý model scikit-learn založený na datové sadě diabetes. Po dokončení tohoto kurzu budete mít praktické znalosti sady SDK pro škálování až do vývoje složitějších experimentů a pracovních postupů.

V tomto kurzu se naučíte provádět následující úlohy:

> [!div class="checklist"]
> * Připojení pracovního prostoru a vytvoření experimentu
> * Načtení dat a trénování modelů scikit-learn
> * Zobrazit výsledky školení ve studiu
> * Načtení nejlepšího modelu

## <a name="prerequisites"></a>Požadavky

Jediným předpokladem je spuštění první části tohoto kurzu, [instalačního prostředí a pracovního prostoru](tutorial-1st-experiment-sdk-setup.md).

V této části kurzu spustíte kód v ukázkovém tutoriálu jupyterového *notebooku/create-first-ml-experiment/tutorial-1st-experiment-sdk-train.ipynb* otevřený na konci první části. Tento článek vás provede stejným kódem, který je v poznámkovém bloku.

## <a name="open-the-notebook"></a>Otevření poznámkového bloku

1. Přihlaste se do [studia Azure Machine Learning Studio](https://ml.azure.com/).

1. Otevřete **výukový program-1st-experiment-sdk-train.ipynb** ve složce, jak je znázorněno v [první části](tutorial-1st-experiment-sdk-setup.md#open).


> [!Warning]
> **Nevytvářejte** *nový* notebook v rozhraní Jupyter! Poznámkový blok *tutoriály/create-first-ml-experiment/tutorial-1st-experiment-sdk-train.ipynb* zahrnuje **veškerý kód a data potřebná** pro tento kurz.

## <a name="connect-workspace-and-create-experiment"></a>Připojení pracovního prostoru a vytvoření experimentu

> [!Important]
> Zbytek tohoto článku obsahuje stejný obsah, který vidíte v poznámkovém bloku.  
>
> Přepněte do poznámkového bloku Jupyter nyní, pokud si chcete přečíst spolu při spuštění kódu. 
> Pokud chcete v poznámkovém bloku spustit jednu buňku kódu, klikněte na buňku kódu a stiskněte **Shift+Enter**. Nebo spusťte celý poznámkový blok tak, že zvolíte **Spustit vše** z horního panelu nástrojů.

Importovat `Workspace` třídu a načíst informace `config.json` o `from_config().` předplatném ze souboru pomocí funkce Toto hledí pro soubor JSON v `from_config(path="your/file/path")`aktuálním adresáři ve výchozím nastavení, ale můžete také zadat parametr cesty, který přejde na soubor pomocí . Na cloudovém notebooku je soubor automaticky v kořenovém adresáři.

Pokud následující kód požádá o další ověřování, jednoduše vložte odkaz do prohlížeče a zadejte ověřovací token.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

Nyní vytvořte experiment ve svém pracovním prostoru. Experiment je další základní cloudový prostředek, který představuje kolekci zkušebních verzí (spuštění jednotlivých modelů). V tomto kurzu použijete experiment k vytvoření spuštění a sledování trénování modelu ve studiu Azure Machine Learning. Parametry zahrnují odkaz na pracovní prostor a název řetězce pro experiment.


```python
from azureml.core import Experiment
experiment = Experiment(workspace=ws, name="diabetes-experiment")
```

## <a name="load-data-and-prepare-for-training"></a>Načtení dat a příprava na školení

Pro účely tohoto kurzu použijete sadu dat diabetu, která používá funkce, jako je věk, pohlaví a BMI předpovědět progresi onemocnění diabetu. Načtěte data z třídy [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) a `train_test_split()`rozdělte je na trénovací a testovací sady pomocí . Tato funkce odděluje data tak, aby model má neviditelná data pro testování po trénování.


```python
from azureml.opendatasets import Diabetes
from sklearn.model_selection import train_test_split

x_df = Diabetes.get_tabular_dataset().to_pandas_dataframe().dropna()
y_df = x_df.pop("Y")

X_train, X_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=66)
```

## <a name="train-a-model"></a>Učení modelu

Školení jednoduchý scikit-learn model lze snadno provést místně pro malé-měřítku školení, ale při trénování mnoho iterací s desítkami různých funkcí permutace a nastavení hyperparametrů, je snadné ztratit přehled o tom, jaké modely jste trénovali a jak jste je trénoval. Následující návrhový vzor ukazuje, jak využít sdk snadno sledovat vaše školení v cloudu.

Vytvořte skript, který trénuje modely hřebenů ve smyčce prostřednictvím různých hodnot alfa hyperparametrů.


```python
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.externals import joblib
import math

alphas = [0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0]

for alpha in alphas:
    run = experiment.start_logging()
    run.log("alpha_value", alpha)

    model = Ridge(alpha=alpha)
    model.fit(X=X_train, y=y_train)
    y_pred = model.predict(X=X_test)
    rmse = math.sqrt(mean_squared_error(y_true=y_test, y_pred=y_pred))
    run.log("rmse", rmse)

    model_name = "model_alpha_" + str(alpha) + ".pkl"
    filename = "outputs/" + model_name

    joblib.dump(value=model, filename=filename)
    run.upload_file(name=model_name, path_or_stream=filename)
    run.complete()
```

Výše uvedený kód provádí následující:

1. Pro každou hodnotu alfa `alphas` hyperparametrv poli je v rámci experimentu vytvořen nový běh. Hodnota alfa je zaznamenána rozlišovat mezi každým spuštěním.
1. V každém spuštění ridge model je vytvořena instance, trénované a slouží ke spuštění předpovědi. Kořenová střední kvadratická chyba se vypočítá pro skutečné versus předpokládané hodnoty a pak se zaznamená do spuštění. V tomto okamžiku má spuštění připojená metadata pro hodnotu alfa i přesnost rmse.
1. Dále je model pro každý běh serializován a odeslán do spuštění. To vám umožní stáhnout soubor modelu ze spuštění ve studiu.
1. Na konci každé iterace je `run.complete()`spuštění dokončeno voláním .

Po dokončení školení zavolejte `experiment` proměnnou načíst odkaz na experiment ve studiu.

```python
experiment
```

<table style="width:100%"><tr><th>Name (Název)</th><th>Pracovní prostor</th><th>Stránka sestavy</th><th>Stránka Dokumenty</th></tr><tr><td>diabetes-experiment</td><td>název pracovního prostoru</td><td>Propojení se studiem Azure Machine Learning</td><td>Odkaz na dokumentaci</td></tr></table>

## <a name="view-training-results-in-studio"></a>Zobrazit výsledky školení ve studiu

Po **propojení s Azure Machine Learning studio** přejdete na hlavní stránku experimentu. Zde vidíte všechny jednotlivé běží v experimentu. Všechny vlastní protokolované`alpha_value` hodnoty `rmse`( a , v tomto případě) se stanou poli pro každé spuštění a také budou k dispozici pro grafy a dlaždice v horní části stránky experimentu. Pokud chcete do grafu nebo dlaždice přidat protokolovnou metriku, najeďte na ni tak, klikněte na tlačítko pro úpravy a najděte vlastní metriku.

Při trénování modelů ve velkém měřítku přes stovky a tisíce samostatných spuštění, tato stránka usnadňuje zobrazení každého modelu, který jste trénovali, konkrétně jak byly vyškoleny a jak se vaše jedinečné metriky v průběhu času změnily.

:::image type="content" source="./media/tutorial-1st-experiment-sdk-train/experiment-main.png" alt-text="Hlavní experiment ve studiu.":::


Vyberte odkaz na `RUN NUMBER` číslo spuštění ve sloupci, chcete-li zobrazit stránku pro jednotlivé spuštění. Výchozí karta **Podrobnosti** zobrazují podrobnější informace o každém spuštění. Přejděte na kartu **Výstupy + protokoly** a zobrazí se `.pkl` soubor pro model, který byl odeslán do spuštění během každé iterace školení. Zde si můžete stáhnout soubor modelu, spíše než muset přeškolit ručně.

:::image type="content" source="./media/tutorial-1st-experiment-sdk-train/model-download.png" alt-text="Spusťte stránku podrobností ve studiu.":::

## <a name="get-the-best-model"></a>Získejte nejlepší model

Kromě toho, že budete moci stahovat soubory modelů z experimentu ve studiu, můžete je také stáhnout programově. Následující kód iterates prostřednictvím každého spuštění v experimentu a přistupuje protokolované spuštění metriky a podrobnosti spuštění (který obsahuje run_id). To udržuje přehled o nejlepší spuštění, v tomto případě spustit s nejnižší kořen-střední kvadratická chyba.

```python
minimum_rmse_runid = None
minimum_rmse = None

for run in experiment.get_runs():
    run_metrics = run.get_metrics()
    run_details = run.get_details()
    # each logged metric becomes a key in this returned dict
    run_rmse = run_metrics["rmse"]
    run_id = run_details["runId"]

    if minimum_rmse is None:
        minimum_rmse = run_rmse
        minimum_rmse_runid = run_id
    else:
        if run_rmse < minimum_rmse:
            minimum_rmse = run_rmse
            minimum_rmse_runid = run_id

print("Best run_id: " + minimum_rmse_runid)
print("Best run_id rmse: " + str(minimum_rmse))
```

    Best run_id: 864f5ce7-6729-405d-b457-83250da99c80
    Best run_id rmse: 57.234760283951765

Použijte nejlepší id spuštění k načtení `Run` jednotlivého spuštění pomocí konstruktoru spolu s objektem experimentu. Pak `get_file_names()` volání zobrazíte všechny soubory, které jsou k dispozici ke stažení z tohoto běhu. V takovém případě jste pro každý běh během tréninku nahráli pouze jeden soubor.

```python
from azureml.core import Run
best_run = Run(experiment=experiment, run_id=minimum_rmse_runid)
print(best_run.get_file_names())
```

    ['model_alpha_0.1.pkl']

Volání `download()` na spustit objekt, zadání názvu souboru modelu ke stažení. Ve výchozím nastavení se tato funkce stáhne do aktuálního adresáře.

```python
best_run.download_file(name="model_alpha_0.1.pkl")
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nedokončete tuto část, pokud plánujete spustit další kurzy Azure Machine Learning.

### <a name="stop-the-compute-instance"></a>Zastavení výpočetní instance

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Odstranit vše

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Skupinu prostředků můžete také zachovat, ale odstranit jeden pracovní prostor. Zobrazte vlastnosti pracovního prostoru a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste provedli následující úkoly:

> [!div class="checklist"]
> * Propojení pracovního prostoru a vytvoření experimentu
> * Načtená data a trénované modely scikit-learn
> * Zobrazeny výsledky školení ve studiu a natržené modely

[Nasaďte svůj model](tutorial-deploy-models-with-aml.md) pomocí Azure Machine Learning.
Přečtěte si, jak vyvíjet automatizované experimenty [strojového učení.](tutorial-auto-train-models.md)
