---
title: 'Kurz: Výuka prvního modelu ML'
titleSuffix: Azure Machine Learning service
description: V tomto kurzu se naučíte základní vzory návrhu ve službě Azure Machine Learning Service a naučíte se jednoduchý model scikit-učení založený na diabetes sadě dat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 07/20/2019
ms.openlocfilehash: cee5801826c78bdee51ba5afb14d6776a1191702
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051633"
---
# <a name="tutorial-train-your-first-ml-model"></a>Kurz: Výuka prvního modelu ML

Tento kurz je **druhou částí z dvoudílné série kurzů**. V předchozím kurzu jste [vytvořili pracovní prostor a zvolili vývojové prostředí](tutorial-1st-experiment-sdk-setup.md). V tomto kurzu se naučíte základní vzory návrhu ve službě Azure Machine Learning Service a naučíte se jednoduchý model scikit-učení založený na diabetes sadě dat. Po dokončení tohoto kurzu budete mít praktické znalosti sady SDK pro horizontální navýšení kapacity a vývoje složitějších experimentů a pracovních postupů.

V tomto kurzu se seznámíte s následujícími úlohami:

> [!div class="checklist"]
> * Připojte svůj pracovní prostor a vytvořte experiment.
> * Načtěte data a Naučte scikit modely.
> * Zobrazení výsledků školení na portálu
> * Načíst tento nejlepší model

## <a name="prerequisites"></a>Požadavky

Jediným předpokladem je spuštění první části tohoto kurzu, [nastavení prostředí a pracovního prostoru](tutorial-1st-experiment-sdk-setup.md).

V této části kurzu spustíte kód v ukázkovém poznámkovém bloku `tutorials/tutorial-1st-experiment-sdk-train.ipynb` Jupyter otevřeném na konci části. Tento článek vás provede stejným kódem, který je v poznámkovém bloku.

## <a name="connect-workspace-and-create-experiment"></a>Připojit pracovní prostor a vytvořit experiment

Naimportujte `config.json`třídua načtěte informace o předplatném ze souboru pomocí `from_config().` funkce, která ve výchozím nastavení vyhledá soubor JSON v aktuálním adresáři, ale můžete zadat i parametr cesty, který bude odkazovat na soubor. `Workspace` pomocí `from_config(path="your/file/path")`. V případě serveru cloudového poznámkového bloku je soubor automaticky v kořenovém adresáři.

Pokud následující kód požádá o další ověřování, jednoduše vložte odkaz do prohlížeče a zadejte ověřovací token.

> [!TIP]
> Pokud s Jupyter poznámkami začínáte, spusťte kód tak, že kliknete na buňku kódu a zapnete **SHIFT + ENTER** , aby se buňky spouštěly po jednom. Pokud chcete spustit celý Poznámkový blok najednou, klikněte na **buňku** v horním řádku nabídky a pak klikněte na **Spustit vše**.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

Nyní vytvořte experiment v pracovním prostoru. Experiment je další základní cloudový prostředek, který představuje kolekci zkušebních verzí (jednotlivé spuštěné modely). V tomto kurzu použijete experiment k vytvoření spuštění a sledování školení modelu v Azure Portal. Parametry zahrnují odkaz na pracovní prostor a název řetězce pro experiment.


```python
from azureml.core import Experiment
experiment = Experiment(workspace=ws, name="diabetes-experiment")
```

## <a name="load-data-and-prepare-for-training"></a>Načtení dat a příprava na školení

V tomto kurzu použijete sadu dat diabetes, což je předem normalizovaná datová sada, která je součástí scikit – učení. Tato datová sada používá funkce, jako je věk, pohlaví a BMI, k předvídání diabetesí nemocí. Načtěte data ze `load_diabetes()` statické funkce a rozdělte je do školicích a testovacích sad `train_test_split()`pomocí. Tato funkce odděluje data, takže model obsahuje nepřesná data, která se mají použít pro testování po školení.


```python
from sklearn.datasets import load_diabetes
from sklearn.model_selection import train_test_split

X, y = load_diabetes(return_X_y = True)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=66)
```

## <a name="train-a-model"></a>Učení modelu

Výuku jednoduchého scikit modelu se dá snadno udělat místně pro účely malého měřítka, ale při školení mnoha iterací s desítkami různých funkcí a nastaveními parametrů je snadné sledovat, které modely jste si naučili a jak vám školení. Následující vzor návrhu ukazuje, jak pomocí sady SDK snadno sledovat vaše školení v cloudu.

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

1. Pro každou hodnotu parametru alfa parametr v `alphas` poli se vytvoří nový běh v rámci experimentu. Hodnota alfa je protokolována k odlišení jednotlivých spuštění.
1. V každém spuštění je vytvořen Ridge model, proškolený a používaný ke spuštění předpovědi. Hodnota root-střed_hodn-Square-Error se vypočítá pro skutečné versus předpovězené hodnoty a potom se do běhu přihlásí. V tomto okamžiku má spuštění metadata připojená jak pro hodnotu alfa, tak pro přesnost rmse.
1. V dalším kroku je model každého spuštění serializován a nahrán do běhu. To vám umožní stáhnout soubor modelu z běhu na portálu.
1. Na konci každé iterace je spuštění dokončeno voláním `run.complete()`.

Po dokončení školení volejte `experiment` proměnnou, která načte odkaz na experiment na portálu.

```python
experiment
```

<table style="width:100%"><tr><th>Name</th><th>Pracovní prostor</th><th>Stránka sestavy</th><th>Stránka docs</th></tr><tr><td>diabetes – experiment</td><td>vaše pracovní prostor – název</td><td>Odkaz na Azure Portal</td><td>Odkaz na dokumentaci</td></tr></table>

## <a name="view-training-results-in-portal"></a>Zobrazit výsledky školení na portálu

Po **odkazování na Azure Portal** přejdete na hlavní stránku experimentu. Tady vidíte všechna jednotlivá spuštění v experimentu. Všechny vlastní hodnoty protokolovaných hodnot`alpha_value` ( `rmse`a v tomto případě) se stanou poli pro každé spuštění a také jsou k dispozici pro grafy a dlaždice v horní části stránky experiment. Chcete-li přidat zaznamenanou metriku do grafu nebo dlaždice, najeďte myší na ni, klikněte na tlačítko Upravit a vyhledejte metriku s vlastním protokolem.

Když procházíte modely ve velkém množství přes stovky a tisíce samostatných spuštění, Tato stránka usnadňuje zobrazení všech vámi vyškolených modelů, konkrétně jejich školení a způsobu, jakým se vaše jedinečné metriky v průběhu času změnily.

![Stránka hlavní experiment na portálu](./media/tutorial-quickstart/experiment-main.png)

Kliknutím na odkaz číslo spuštění ve `RUN NUMBER` sloupci přejdete na stránku pro každé jednotlivé spuštění. Výchozí karta **Podrobnosti** Zobrazí podrobnější informace o každém spuštění. Přejděte na kartu **výstupy** a zobrazí se `.pkl` soubor pro model, který byl nahrán do běhu během každé školicí iterace. Tady si můžete stáhnout soubor modelu a nemusíte ho přesměrovat ručně.

![Stránka podrobností o spuštění na portálu](./media/tutorial-quickstart/model-download.png)

## <a name="get-the-best-model"></a>Získejte nejlepší model

Kromě toho, že je možné stahovat soubory modelu z experimentu na portálu, je můžete také stáhnout programově. Následující kód projde každým spuštěním v experimentu a přistupuje ke metrikám s protokolem a k podrobnostem o běhu (které obsahují run_id). Tím se sleduje nejlepší běh, v tomto případě se spustí s nejnižší hodnotou root-střed_hodn-Square-Error.

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

Tuto část neprovádějte, pokud máte v plánu spouštět jiné kurzy Azure Machine Learning služby.

### <a name="stop-the-notebook-vm"></a>Zastavení virtuálního počítače poznámkového bloku

Pokud jste použili server cloudového poznámkového bloku, zastavte virtuální počítač, pokud ho nepoužíváte ke snížení nákladů.

1. V pracovním prostoru vyberte **virtuální počítače poznámkového bloku**.

   ![Zastavení serveru VM](./media/tutorial-1st-experiment-sdk-setup/stop-server.png)

1. V seznamu vyberte virtuální počítač.

1. Vyberte **zastavit**.

1. Až budete chtít znovu použít server, vyberte **Spustit**.

### <a name="delete-everything"></a>Odstranit vše

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Můžete také zachovat skupinu prostředků, ale odstranit jeden pracovní prostor. Zobrazte vlastnosti pracovního prostoru a vyberte **Odstranit**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste provedli následující úlohy:

> [!div class="checklist"]
> * Připojili jsme váš pracovní prostor a vytvořili experiment.
> * Nahraná data a školené modely scikit-učení
> * Zobrazení výsledků školení na portálu a načítajících se modely

[Nasazení modelu](tutorial-deploy-models-with-aml.md) službou Azure Machine Learning.
Naučte se vyvíjet [automatizované](tutorial-auto-train-models.md) experimenty strojového učení.