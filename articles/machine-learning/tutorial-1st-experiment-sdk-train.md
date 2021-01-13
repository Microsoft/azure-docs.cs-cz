---
title: 'Kurz: výuka prvního modelu Machine Learning – Python'
titleSuffix: Azure Machine Learning
description: Třetí část Azure Machine Learning série Začínáme ukazuje, jak naučit model strojového učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: d2c0003058c2271e46a352567a14e1b01dfabdbf
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/13/2021
ms.locfileid: "98071098"
---
# <a name="tutorial-train-your-first-machine-learning-model-part-3-of-4"></a>Kurz: výuka prvního modelu strojového učení (část 3 ze 4)

V tomto kurzu se dozvíte, jak ve Azure Machine Learning naučit model strojového učení.

Tento kurz je *třetí částí série kurzů* , ve které se seznámíte se základy Azure Machine Learning a dokončení úloh strojového učení na základě úloh v Azure. Tento kurz sestaví na práci, kterou jste dokončili v [části 1: nastavení](tutorial-1st-experiment-sdk-setup-local.md) a [část 2: spustit "Hello World!"](tutorial-1st-experiment-hello-world.md) řady.

V tomto kurzu provedete další krok odesláním skriptu, který bude přebírat model strojového učení. Tento příklad vám pomůže pochopit, jak Azure Machine Learning usnadňuje konzistentní chování mezi místním laděním a vzdáleným spuštěním.

V tomto kurzu jste:

> [!div class="checklist"]
> * Vytvořte školicí skript.
> * Pomocí conda definujte prostředí Azure Machine Learning.
> * Vytvořte skript ovládacího prvku.
> * Pochopení Azure Machine Learning tříd ( `Environment` , `Run` , `Metrics` ).
> * Odešlete a spusťte školicí skript.
> * Zobrazte si výstup kódu v cloudu.
> * Protokoluje metriky pro Azure Machine Learning.
> * Podívejte se na vaše metriky v cloudu.

## <a name="prerequisites"></a>Požadavky

* Dokončení [části 2](tutorial-1st-experiment-hello-world.md) série.

## <a name="create-training-scripts"></a>Vytváření školicích skriptů

Nejdřív v souboru definujte architekturu sítě neuronové `model.py` . Veškerý kód školení přejde do `src` podadresáře, včetně `model.py` .

Následující kód je pořízen z [tohoto úvodního příkladu](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html) z PyTorch. Všimněte si, že Azure Machine Learning koncepty se vztahují na jakýkoliv kód strojového učení, ne jen na PyTorch.

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/src/model.py":::

Dále definujte školicí skript. Tento skript stáhne datovou sadu CIFAR10 pomocí `torchvision.dataset` rozhraní API PyTorch, nastaví síť definovanou v a nahlásí `model.py` ji pro dva epochsy pomocí standardní SGD a ztráty mezi entropiemi.

Vytvořte `train.py` skript v `src` podadresáři:

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/src/train.py":::

Nyní máte následující adresářovou strukturu:

```txt
tutorial
└──.azureml
|  └──config.json
└──src
|  └──hello.py
|  └──model.py
|  └──train.py
└──01-create-workspace.py
└──02-create-compute.py
└──03-run-hello.py
```

> [!div class="nextstepaction"]
> [Vytvořili jste školicí skripty,](?success=create-scripts#environment) u kterých došlo [k problému](https://www.research.net/r/7CTJQQN?issue=create-scripts)

## <a name="create-a-new-python-environment"></a><a name="environment"></a> Vytvořit nové prostředí Pythonu

`pytorch-env.yml`Ve skrytém adresáři vytvořte soubor s názvem `.azureml` :

:::code language="yml" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/environments/pytorch-env.yml":::

Toto prostředí má všechny závislosti, které model a školicí skript vyžaduje. Všimněte si, že Azure Machine Learning SDK pro Python neexistuje žádná závislost.

> [!div class="nextstepaction"]
> [Vytvořili jsem soubor prostředí,](?success=create-env-file#test-local) [který jsem narazil na problém](https://www.research.net/r/7CTJQQN?issue=create-env-file)

## <a name="test-locally"></a><a name="test-local"></a> Test lokálně

Použijte následující kód k otestování skriptu lokálně v novém prostředí.  

```bash
conda deactivate                                # If you are still using the tutorial environment, exit it
conda env create -f .azureml/pytorch-env.yml    # create the new Conda environment
conda activate pytorch-env                      # activate new Conda environment
python src/train.py                             # train model
```

Po spuštění tohoto skriptu uvidíte data stažená do adresáře s názvem `tutorial/data` .

> [!div class="nextstepaction"]
> [Kód jsem v místním](?success=test-local#create-local) [prostředí](https://www.research.net/r/7CTJQQN?issue=test-local) jsem narazil na problém

## <a name="create-the-control-script"></a><a name="create-local"></a> Vytvoření řídicího skriptu

Rozdíl mezi následujícím skriptem řízení a ten, který jste použili k odeslání "Hello World!" je přidání několika dalších řádků pro nastavení prostředí.

Vytvořte nový soubor Python v adresáři s `tutorial` názvem `04-run-pytorch.py` :

```python
# 04-run-pytorch.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig

if __name__ == "__main__":
    ws = Workspace.from_config()
    experiment = Experiment(workspace=ws, name='day1-experiment-train')
    config = ScriptRunConfig(source_directory='./src',
                             script='train.py',
                             compute_target='cpu-cluster')

    # set up pytorch environment
    env = Environment.from_conda_specification(
        name='pytorch-env',
        file_path='./.azureml/pytorch-env.yml'
    )
    config.run_config.environment = env

    run = experiment.submit(config)

    aml_url = run.get_portal_url()
    print(aml_url)
```    
    
### <a name="understand-the-code-changes"></a>Pochopení změn kódu

:::row:::
   :::column span="":::
      `env = ...`
   :::column-end:::
   :::column span="2":::
      Azure Machine Learning poskytuje koncept [prostředí](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py) , které představuje reprodukovatelné prostředí Pythonu ve verzi pro spouštění experimentů. Je snadné vytvořit prostředí z místního prostředí conda nebo PIP.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config.run_config.environment = env`
   :::column-end:::
   :::column span="2":::
      Přidá prostředí do [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py).
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [Vytvořili jste řídicí skript,](?success=control-script#submit) který [jsem narazil na problém](https://www.research.net/r/7CTJQQN?issue=control-script)


## <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit"></a> Odeslat běh do Azure Machine Learning

Přepněte zpátky do prostředí *kurzu* , ve kterém je nainstalovaná sada Azure Machine Learning SDK for Python. Vzhledem k tomu, že kód školení není v počítači spuštěný, není nutné mít nainstalované PyTorch.  Ale potřebujete `azureml-sdk` , který je v prostředí *kurzu* .

```bash
conda deactivate
conda activate tutorial
python 04-run-pytorch.py
```

>[!NOTE] 
> Při prvním spuštění tohoto skriptu Azure Machine Learning vytvoří novou image Docker z prostředí PyTorch. Dokončení celého spuštění může trvat 5 až 10 minut. 
>
> V Azure Machine Learning Studiu vidíte protokoly sestavení Docker. Použijte odkaz na Studio, vyberte kartu **výstupy + protokoly** a pak vyberte `20_image_build_log.txt` .
>
> Tato image se znovu použije v budoucích spuštěních, aby se spouštěla mnohem rychleji.

Po vytvoření image vyberte, aby se `70_driver_log.txt` zobrazil výstup školicího skriptu.

```txt
Downloading https://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz to ./data/cifar-10-python.tar.gz
...
Files already downloaded and verified
epoch=1, batch= 2000: loss 2.19
epoch=1, batch= 4000: loss 1.82
epoch=1, batch= 6000: loss 1.66
epoch=1, batch= 8000: loss 1.58
epoch=1, batch=10000: loss 1.52
epoch=1, batch=12000: loss 1.47
epoch=2, batch= 2000: loss 1.39
epoch=2, batch= 4000: loss 1.38
epoch=2, batch= 6000: loss 1.37
epoch=2, batch= 8000: loss 1.33
epoch=2, batch=10000: loss 1.31
epoch=2, batch=12000: loss 1.27
Finished Training
```

> [!WARNING]
> Pokud se zobrazí chyba `Your total snapshot size exceeds the limit` , `data` adresář se nachází v `source_directory` hodnotě použité v `ScriptRunConfig` .
>
> Přesunout `data` mimo `src` .

Prostředí je možné zaregistrovat v pracovním prostoru pomocí `env.register(ws)` . Pak je lze snadno sdílet, znovu použít a se správou verzí. Prostředí usnadňují reprodukování předchozích výsledků a spolupráci se svým týmem.

Azure Machine Learning také udržuje kolekci podmnožinových prostředí. Tato prostředí se týkají běžných scénářů strojového učení a jsou zálohována imagemi Docker uložených v mezipaměti. Image Docker v mezipaměti usnadňují první vzdálené spuštění rychleji.

V krátké době vám použití registrovaných prostředí může ušetřit čas. Další informace najdete v tématu [použití prostředí](./how-to-use-environments.md) .

> [!div class="nextstepaction"]
> [Jsem mi poslal (a](?success=test-w-environment#log) ) [jsem to](https://www.research.net/r/7CTJQQN?issue=test-w-environment) , že mi běžela chyba

## <a name="log-training-metrics"></a><a name="log"></a> Metriky školení protokolu

Teď, když máte školení modelu v Azure Machine Learning, začněte sledovat některé metriky výkonu.

Aktuální školicí skript vytiskne metriky do terminálu. Azure Machine Learning poskytuje mechanismus protokolování metrik s více funkcemi. Přidáním několika řádků kódu získáte možnost vizualizovat metriky v nástroji Studio a porovnat metriky mezi několika spuštěními.

### <a name="modify-trainpy-to-include-logging"></a>Upravit `train.py` a zahrnout protokolování

Upravte `train.py` skript tak, aby zahrnoval dva další řádky kódu:

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/code/pytorch-cifar10-train-with-logging/train.py":::


#### <a name="understand-the-additional-two-lines-of-code"></a>Pochopení dalších dvou řádků kódu

V aplikaci `train.py` získáte přístup k objektu Run z  školicího skriptu samotného pomocí `Run.get_context()` metody a použijete ho k protokolování metrik:

```python
# in train.py
run = Run.get_context()

...

run.log('loss', loss)
```

Metriky v Azure Machine Learning jsou:

- Uspořádáno podle experimentů a spouštění, takže je snadné sledovat metriky a porovnávat je.
- Vybavené uživatelským rozhraním, abyste mohli vizualizovat školicí výkon v studiu.
- Tato výhoda je navržena pro škálování, takže tyto výhody udržujete i při spouštění stovek experimentů.

> [!div class="nextstepaction"]
> [Změnil ](?success=modify-train#log) (a) jsem Train.py [jsem narazili na problém](https://www.research.net/r/7CTJQQN?issue=modify-train)

### <a name="update-the-conda-environment-file"></a>Aktualizace souboru prostředí conda

`train.py`Skript právě zabral novou závislost na `azureml.core` . Aktualizace `pytorch-env.yml` , aby odrážela tuto změnu:

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/configuration/pytorch-aml-env.yml":::

> [!div class="nextstepaction"]
> [Aktualizoval (a) jsem soubor prostředí, u kterého](?success=update-environment#submit-again) [došlo k problému](https://www.research.net/r/7CTJQQN?issue=update-environment)

### <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit-again"></a> Odeslat běh do Azure Machine Learning
Odeslat tento skript jednou za další:

```bash
python 04-run-pytorch.py
```

Tentokrát, když navštívíte Studio, přejděte na kartu **metriky** , kde se teď můžete podívat na průběžné aktualizace modelu na základě ztráty školení.

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/logging-metrics.png" alt-text="Graf ztrát školení na kartě metriky.":::

> [!div class="nextstepaction"]
> Znovu [jsem odeslal spuštění, které](?success=resubmit-with-logging#next-steps) [jsem narazil na problém](https://www.research.net/r/7CTJQQN?issue=resubmit-with-logging) .

## <a name="next-steps"></a>Další kroky

V této relaci jste upgradovali ze základního "Hello World!" skript do realističtějšího školicího skriptu, který vyžadoval spuštění konkrétního prostředí Pythonu. Zjistili jste, jak převzít místní prostředí conda do cloudu pomocí Azure Machine Learningch prostředí. Nakonec jste viděli, jak v několika řádcích kódu můžete protokolovat metriky pro Azure Machine Learning.

Existují i jiné způsoby vytváření Azure Machine Learning prostředí, včetně souboru [pip requirements.txt](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#from-pip-requirements-name--file-path-) nebo [ze stávajícího místního prostředí conda](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-).

V další relaci uvidíte, jak pracovat s daty v Azure Machine Learning nahráním datové sady CIFAR10 do Azure.

> [!div class="nextstepaction"]
> [Kurz: Převeďte vlastní data](tutorial-1st-experiment-bring-data.md)

>[!NOTE] 
> Pokud chcete dokončit řadu kurzů zde a nepostupovat k dalšímu kroku, nezapomeňte [vyčistit své prostředky](tutorial-1st-experiment-bring-data.md#clean-up-resources).
