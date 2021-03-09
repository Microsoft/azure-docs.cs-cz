---
title: 'Kurz: spuštění "Hello World!" Skript jazyka Python'
titleSuffix: Azure Machine Learning
description: Část 2 Azure Machine Learning série Začínáme ukazuje, jak odeslat triviální "Hello World!" Skript Pythonu do cloudu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 02/11/2021
ms.custom: devx-track-python
ms.openlocfilehash: 4f2b01b7a04958c4bd1f97332b54a1ff4fc32356
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102522321"
---
# <a name="tutorial-run-a-hello-world-python-script-part-2-of-4"></a>Kurz: spuštění "Hello World!" Skript Pythonu (část 2 ze 4)

V tomto kurzu se naučíte, jak pomocí sady Azure Machine Learning SDK pro Python Odeslat a spustit Python "Hello World!". pravidel.

Tento kurz je třetí *částí série kurzů* , ve které se seznámíte se základy Azure Machine Learning a dokončení úloh strojového učení na základě úloh v Azure. Tento kurz sestaví na práci, kterou jste dokončili v [části 1: nastavení místního počítače pro Azure Machine Learning](tutorial-1st-experiment-sdk-setup-local.md).

V tomto kurzu:

> [!div class="checklist"]
> * Vytvoří a spustí "Hello World!" Skript v jazyce Python lokálně.
> * Vytvoření skriptu ovládacího prvku Python pro odeslání "Hello World!" Azure Machine Learning.
> * Pochopení Azure Machine Learning konceptů v řídicím skriptu.
> * Odeslat a spustit "Hello World!" pravidel.
> * Zobrazte si výstup kódu v cloudu.

## <a name="prerequisites"></a>Požadavky

- Dokončení [části 1](tutorial-1st-experiment-sdk-setup-local.md) , pokud ještě nemáte Azure Machine Learning pracovní prostor.

## <a name="create-and-run-a-python-script-locally"></a>Místní vytvoření a spuštění skriptu v jazyce Python

Vytvořte nový podadresář s názvem `src` v adresáři, `tutorial` do kterého chcete uložit kód, který chcete spustit ve výpočetním clusteru Azure Machine Learning. V `src` podadresáři vytvořte `hello.py` skript Pythonu:

```python
# src/hello.py
print("Hello world!")
```

Vaše adresářová struktura projektu teď bude vypadat takto:

:::image type="content" source="media/tutorial-1st-experiment-hello-world/directory-structure.png" alt-text="Struktura adresáře zobrazuje hello.py v podadresáři src":::


### <a name="test-your-script-locally"></a><a name="test"></a>Místní otestování skriptu

Svůj kód můžete spustit lokálně pomocí svého oblíbeného integrovaného vývojového prostředí (IDE) nebo terminálu. Místní spuštění kódu má výhodu interaktivního ladění kódu.  V okně, které obsahuje aktivované prostředí *tutorial1* Conda, spusťte soubor Python:

```bash
cd <path/to/tutorial>
python ./src/hello.py
```

> [!div class="nextstepaction"]
> [Spustil (a) jsem skript místně](?success=run-local#control-script) [jsem narazil na problém](https://www.research.net/r/7C2NTH7?issue=run-local)

## <a name="create-a-control-script"></a><a name="control-script"></a> Vytvoření skriptu ovládacího prvku

*Řídicí skript* umožňuje spuštění `hello.py` skriptu v cloudu. Řídicí skript slouží k řízení, jak a kde se spouští kód strojového učení.  

V adresáři kurzu vytvořte nový soubor Python s názvem `03-run-hello.py` a zkopírujte nebo vložte do tohoto souboru následující kód:

```python
# tutorial/03-run-hello.py
from azureml.core import Workspace, Experiment, Environment, ScriptRunConfig

ws = Workspace.from_config()
experiment = Experiment(workspace=ws, name='day1-experiment-hello')

config = ScriptRunConfig(source_directory='./src', script='hello.py', compute_target='cpu-cluster')

run = experiment.submit(config)
aml_url = run.get_portal_url()
print(aml_url)
```

### <a name="understand-the-code"></a>Vysvětlení kódu

Tady je popis toho, jak skript ovládacího prvku funguje:

:::row:::
   :::column span="":::
      `ws = Workspace.from_config()`
   :::column-end:::
   :::column span="2":::
      [Pracovní prostor](/python/api/azureml-core/azureml.core.workspace.workspace) se připojuje k vašemu pracovnímu prostoru Azure Machine Learning, abyste mohli komunikovat s prostředky Azure Machine Learning.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `experiment =  Experiment( ... )`
   :::column-end:::
   :::column span="2":::
      [Experimentování](/python/api/azureml-core/azureml.core.experiment.experiment) nabízí jednoduchý způsob, jak uspořádat více spuštění pod jedním názvem. Později uvidíte, jak experimenty usnadňují porovnávání metrik mezi spoustami spuštění.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig( ... )` 
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) zabalí váš `hello.py` kód a předá ho do vašeho pracovního prostoru. Jak název navrhuje, můžete tuto třídu použít ke _konfiguraci_ způsobu, jakým se má _skript_ _spouštět_ v Azure Machine Learning. Také určuje, na jaký cíl výpočtů se skript spustí. V tomto kódu je cílem výpočetní cluster, který jste vytvořili v [kurzu instalace](tutorial-1st-experiment-sdk-setup-local.md).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `run = experiment.submit(config)`
   :::column-end:::
   :::column span="2":::
       Odešle váš skript. Toto odeslání se nazývá [spuštění](/python/api/azureml-core/azureml.core.run%28class%29). Spuštění zapouzdřuje jedno spuštění kódu. Pomocí spuštění můžete monitorovat průběh skriptu, zachytit výstup, analyzovat výsledky, vizualizovat metriky a další.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `aml_url = run.get_portal_url()` 
   :::column-end:::
   :::column span="2":::
        `run`Objekt poskytuje popisovač pro spuštění kódu. Sledujte jeho průběh z Azure Machine Learning studia s adresou URL, která se vytiskla ze skriptu Pythonu.  
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [Vytvořili jste řídicí skript,](?success=create-control-script#submit) který [jsem narazil na problém](https://www.research.net/r/7C2NTH7?issue=create-control-script)

## <a name="submit-and-run-your-code-in-the-cloud"></a><a name="submit"></a> Odeslání a spuštění kódu v cloudu

Spusťte skript ovládacího prvku, který zase běží `hello.py` na výpočetním clusteru, který jste vytvořili v [kurzu instalace](tutorial-1st-experiment-sdk-setup-local.md).


```bash
python 03-run-hello.py
```

> [!TIP]
> Pokud spuštění tohoto kódu vám poskytne chybu, ke které nemáte přístup k předplatnému, přečtěte si téma [připojení k pracovnímu prostoru](how-to-manage-workspace.md?tab=python#connect-multi-tenant) , kde najdete informace o možnostech ověřování.

> [!div class="nextstepaction"]
> Jsem poslal (a) jsem [kód v cloudu](?success=submit-to-cloud#monitor) , u kterého došlo k [problému](https://www.research.net/r/7C2NTH7?issue=submit-to-cloud) .

## <a name="monitor-your-code-in-the-cloud-by-using-the-studio"></a><a name="monitor"></a>Monitorování kódu v cloudu pomocí studia

Výstup z vašeho skriptu bude obsahovat odkaz na Studio, který vypadá nějak takto: `https://ml.azure.com/experiments/hello-world/runs/<run-id>?wsid=/subscriptions/<subscription-id>/resourcegroups/<resource-group>/workspaces/<workspace-name>` .

Použijte odkaz.  Nejprve se zobrazí stav **Příprava**.  První spuštění bude trvat 5-10 minut. Důvodem je, že dojde k následujícímu:

* Image Docker je vestavěná v cloudu.
* Velikost výpočetního clusteru je změněná z 0 na 1 uzel.
* Image Docker se stáhne do výpočetního prostředí. 

Další spuštění jsou mnohem rychlejší (přibližně 15 sekund), protože se image Docker ukládá do mezipaměti ve výpočetním prostředí. Tuto možnost můžete otestovat opětovným odesláním kódu uvedeného níže po dokončení prvního spuštění.

Po dokončení úlohy přejdete na kartu **výstupy a protokoly** . Tady vidíte `70_driver_log.txt` soubor, který vypadá takto:

```txt
 1: [2020-08-04T22:15:44.407305] Entering context manager injector.
 2: [context_manager_injector.py] Command line Options: Namespace(inject=['ProjectPythonPath:context_managers.ProjectPythonPath', 'RunHistory:context_managers.RunHistory', 'TrackUserError:context_managers.TrackUserError', 'UserExceptions:context_managers.UserExceptions'], invocation=['hello.py'])
 3: Starting the daemon thread to refresh tokens in background for process with pid = 31263
 4: Entering Run History Context Manager.
 5: Preparing to call script [ hello.py ] with arguments: []
 6: After variable expansion, calling script [ hello.py ] with arguments: []
 7:
 8: Hello world!
 9: Starting the daemon thread to refresh tokens in background for process with pid = 31263
10:
11:
12: The experiment completed successfully. Finalizing run...
13: Logging experiment finalizing status in history service.
14: [2020-08-04T22:15:46.541334] TimeoutHandler __init__
15: [2020-08-04T22:15:46.541396] TimeoutHandler __enter__
16: Cleaning up all outstanding Run operations, waiting 300.0 seconds
17: 1 items cleaning up...
18: Cleanup took 0.1812913417816162 seconds
19: [2020-08-04T22:15:47.040203] TimeoutHandler __exit__
```

Na řádku 8 se zobrazí zpráva "Hello World!" výkonem.

`70_driver_log.txt`Soubor obsahuje standardní výstup z běhu. Tento soubor může být užitečný při ladění vzdálených běhů v cloudu.

> [!div class="nextstepaction"]
> [Zjistili jsem, že v studiu](?success=monitor-in-studio#next-steps) [jsem narazil na problém](https://www.research.net/r/7C2NTH7?issue=monitor-in-studio)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste provedli jednoduché "Hello World!" skripty a běžely v Azure. Zjistili jste, jak se připojit ke svému pracovnímu prostoru Azure Machine Learning, vytvořit experiment a odeslat svůj `hello.py` kód do cloudu.

V dalším kurzu sestavíte pomocí těchto výukových kurzů s něčím zajímavější než `print("Hello world!")` .

> [!div class="nextstepaction"]
> [Kurz: Trénování modelu](tutorial-1st-experiment-sdk-train.md)

>[!NOTE] 
> Pokud chcete dokončit řadu kurzů zde a nepostupovat k dalšímu kroku, nezapomeňte [vyčistit své prostředky](tutorial-1st-experiment-bring-data.md#clean-up-resources).
