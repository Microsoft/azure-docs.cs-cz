---
title: 'Kurz: výuka prvního modelu Azure ML v Pythonu'
titleSuffix: Azure Machine Learning
description: V tomto kurzu se naučíte základní vzory návrhu v Azure Machine Learning a naučíte se jednoduchý scikit model založený na datové sadě diabetes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 02/10/2020
ms.custom: tracking-python
ms.openlocfilehash: ec0ff6c5e53d33cf5c07171c2b678fe6857836e0
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84558371"
---
# <a name="tutorial-train-your-first-ml-model"></a>Kurz: analýza prvního modelu ML

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Tento kurz je **druhou částí z dvoudílné série kurzů**. V předchozím kurzu jste [vytvořili pracovní prostor a zvolili vývojové prostředí](tutorial-1st-experiment-sdk-setup.md). V tomto kurzu se naučíte základní vzory návrhu v Azure Machine Learning a naučíte se jednoduchý scikit model založený na datové sadě diabetes. Po dokončení tohoto kurzu budete mít praktické znalosti sady SDK pro horizontální navýšení kapacity a vývoje složitějších experimentů a pracovních postupů.

V tomto kurzu se naučíte provádět následující úlohy:

> [!div class="checklist"]
> * Připojte svůj pracovní prostor a vytvořte experiment.
> * Načtěte data a Naučte scikit modely.
> * Zobrazit výsledky školení v studiu
> * Načtení nejlepšího modelu

## <a name="prerequisites"></a>Požadavky

Jediným předpokladem je spuštění první části tohoto kurzu, [nastavení prostředí a pracovního prostoru](tutorial-1st-experiment-sdk-setup.md).

V této části kurzu spustíte kód v ukázce poznámkového bloku Jupyter */Create-First-ml-experiment/tutorial-1st-experiment-SDK-Train. ipynb* otevřený na konci části 1. Tento článek vás provede stejným kódem, který je v poznámkovém bloku.

## <a name="open-the-notebook"></a>Otevření poznámkového bloku

1. Přihlaste se k [Azure Machine Learning Studiu](https://ml.azure.com/).

1. Otevřete **kurz – 1st-experiment-SDK-vlak. ipynb** ve složce, jak je znázorněno v [části One](tutorial-1st-experiment-sdk-setup.md#open).


> [!Warning]
> Nevytvářejte *Nový* Poznámkový blok v rozhraní Jupyter. **not** Kurzy poznámkového bloku */Create-First-ml-experiment/tutorial-1st-experiment-SDK-Train. ipynb* jsou včetně **veškerého kódu a dat potřebných** pro účely tohoto kurzu.

## <a name="connect-workspace-and-create-experiment"></a>Připojit pracovní prostor a vytvořit experiment

> [!Important]
> Zbývající část tohoto článku obsahuje stejný obsah, jaký vidíte v poznámkovém bloku.  
>
> Pokud chcete při spuštění kódu číst společně, přepněte do poznámkového bloku Jupyter. 
> Pokud chcete na poznámkovém bloku spustit jednu buňku kódu, klikněte na buňku kódu a stiskněte **SHIFT + ENTER**. Případně spusťte celý Poznámkový blok výběrem možnosti **Spustit vše** na horním panelu nástrojů.

Naimportujte `Workspace` třídu a načtěte informace o předplatném ze souboru `config.json` pomocí `from_config().` této funkce, která ve výchozím nastavení vyhledá soubor JSON v aktuálním adresáři, ale můžete taky zadat parametr cesty, který bude odkazovat na soubor pomocí `from_config(path="your/file/path")` . V případě serveru cloudového poznámkového bloku je soubor automaticky v kořenovém adresáři.

Pokud následující kód požádá o další ověřování, jednoduše vložte odkaz do prohlížeče a zadejte ověřovací token.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

Nyní vytvořte experiment v pracovním prostoru. Experiment je další základní cloudový prostředek, který představuje kolekci zkušebních verzí (jednotlivé spuštěné modely). V tomto kurzu použijete experiment k vytvoření spuštění a sledování školení modelu v nástroji Azure Machine Learning Studio. Parametry zahrnují odkaz na pracovní prostor a název řetězce pro experiment.


```python
from azureml.core import Experiment
experiment = Experiment(workspace=ws, name="diabetes-experiment")
```

## <a name="load-data-and-prepare-for-training"></a>Načtení dat a příprava na školení

Pro účely tohoto kurzu použijete sadu dat diabetes, která používá funkce jako věk, pohlaví a BMI k předvídání pokroku diabetesch nemocí. Načtěte data z třídy [Azure Open DataSets](https://azure.microsoft.com/services/open-datasets/) a rozdělte je do školicích a testovacích sad pomocí `train_test_split()` . Tato funkce odděluje data, takže model obsahuje nepřesná data, která se mají použít pro testování po školení.


```python
from azureml.opendatasets import Diabetes
from sklearn.model_selection import train_test_split

x_df = Diabetes.get_tabular_dataset().to_pandas_dataframe().dropna()
y_df = x_df.pop("Y")

X_train, X_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=66)
```

## <a name="train-a-model"></a>Učení modelu

Výuku jednoduchého scikit modelu se dá snadno udělat místně pro účely malého rozsahu, ale při výuce mnoha iterací s desítkami různých permutací a nastaveními parametrů se snadno ztratí přehled o tom, které modely jste si naučili a jakým způsobem jste je naučili. Následující vzor návrhu ukazuje, jak pomocí sady SDK snadno sledovat vaše školení v cloudu.

Sestavte skript, který předávat modely Ridge ve smyčce prostřednictvím různých hodnot alfa parametrů.


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

Výše uvedený kód provede následující:

1. Pro každou hodnotu parametru alfa parametr v poli `alphas` se vytvoří nový běh v rámci experimentu. Hodnota alfa je protokolována k odlišení jednotlivých spuštění.
1. V každém spuštění je vytvořen Ridge model, proškolený a používaný ke spuštění předpovědi. Hodnota root-střed_hodn-Square-Error se vypočítá pro skutečné versus předpovězené hodnoty a potom se do běhu přihlásí. V tomto okamžiku má spuštění metadata připojená jak pro hodnotu alfa, tak pro přesnost rmse.
1. V dalším kroku je model každého spuštění serializován a nahrán do běhu. To vám umožní stáhnout soubor modelu z běhu v studiu.
1. Na konci každé iterace je spuštění dokončeno voláním `run.complete()` .

Po dokončení školení volejte `experiment` proměnnou, která načte odkaz na experiment v studiu.

```python
experiment
```

<table style="width:100%"><tr><th>Name</th><th>Pracovní prostor</th><th>Stránka sestavy</th><th>Stránka docs</th></tr><tr><td>diabetes – experiment</td><td>vaše pracovní prostor – název</td><td>Odkaz na Azure Machine Learning Studio</td><td>Odkaz na dokumentaci</td></tr></table>

## <a name="view-training-results-in-studio"></a>Zobrazit výsledky školení v studiu

Po **odkazu na Azure Machine Learning Studiu** přejdete na hlavní stránku experimentu. Tady vidíte všechna jednotlivá spuštění v experimentu. Všechny vlastní hodnoty protokolovaných hodnot ( `alpha_value` a `rmse` v tomto případě) se stanou poli pro každé spuštění a také jsou k dispozici pro grafy a dlaždice v horní části stránky experiment. Chcete-li přidat zaznamenanou metriku do grafu nebo dlaždice, najeďte myší na ni, klikněte na tlačítko Upravit a vyhledejte metriku s vlastním protokolem.

Když procházíte modely ve velkém množství přes stovky a tisíce samostatných spuštění, Tato stránka usnadňuje zobrazení všech vámi vyškolených modelů, konkrétně jejich školení a způsobu, jakým se vaše jedinečné metriky v průběhu času změnily.

:::image type="content" source="./media/tutorial-1st-experiment-sdk-train/experiment-main.png" alt-text="Hlavní stránka experimentu v studiu.":::


Vyberte odkaz číslo spuštění ve `RUN NUMBER` sloupci, aby se zobrazila stránka pro jednotlivé spuštění. Výchozí karta **Podrobnosti** Zobrazí podrobnější informace o každém spuštění. Přejděte na kartu **výstupy + protokoly** a zobrazí se `.pkl` soubor pro model, který byl nahrán do běhu během každé školicí iterace. Tady si můžete stáhnout soubor modelu a nemusíte ho přesměrovat ručně.

:::image type="content" source="./media/tutorial-1st-experiment-sdk-train/model-download.png" alt-text="Stránka podrobností o spuštění v studiu":::

## <a name="get-the-best-model"></a>Získejte nejlepší model

Kromě toho, že je možné stahovat soubory modelu z experimentu v studiu, je můžete také stáhnout programově. Následující kód projde každým spuštěním v experimentu a přistupuje ke metrikám s protokolem a k podrobnostem o běhu (které obsahují run_id). Tím se sleduje nejlepší běh, v tomto případě se spustí s nejnižší hodnotou root-střed_hodn-Square-Error.

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

Použijte nejlepší ID běhu k načtení jednotlivého spuštění pomocí `Run` konstruktoru společně s objektem experiment. Potom zavolejte `get_file_names()` na Zobrazit všechny soubory, které jsou k dispozici ke stažení z tohoto spuštění. V tomto případě jste během školení nahráli jenom jeden soubor pro každé spuštění.

```python
from azureml.core import Run
best_run = Run(experiment=experiment, run_id=minimum_rmse_runid)
print(best_run.get_file_names())
```

    ['model_alpha_0.1.pkl']

Zavolejte `download()` na objekt Run a určete název souboru modelu, který se má stáhnout. Ve výchozím nastavení tato funkce stahuje do aktuálního adresáře.

```python
best_run.download_file(name="model_alpha_0.1.pkl")
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tuto část neprovádějte, pokud máte v plánu spouštět jiné kurzy Azure Machine Learning.

### <a name="stop-the-compute-instance"></a>Zastavení výpočetní instance

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Odstranit vše

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Můžete také zachovat skupinu prostředků, ale odstranit jeden pracovní prostor. Zobrazte vlastnosti pracovního prostoru a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste provedli následující úlohy:

> [!div class="checklist"]
> * Připojili jsme váš pracovní prostor a vytvořili experiment.
> * Nahraná data a školené modely scikit-učení
> * Zobrazení výsledků školení v studiu a načtených modelů

[Nasaďte model](tutorial-deploy-models-with-aml.md) pomocí Azure Machine Learning.
Naučte se vyvíjet [automatizované experimenty strojového učení](tutorial-auto-train-models.md) .
