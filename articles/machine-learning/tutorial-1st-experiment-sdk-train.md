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
ms.openlocfilehash: f5c2690ea97136c2b7887a8450c2788e3902d4e3
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91369956"
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
* Úvodní znalost jazyka Pythonu a pracovních postupů strojového učení.
* Místní vývojové prostředí, například Visual Studio Code, Jupyter nebo PyCharm.
* Python (verze 3,5 až 3,7).

## <a name="create-training-scripts"></a>Vytváření školicích skriptů

Nejdřív v souboru definujte architekturu sítě neuronové `model.py` . Veškerý kód školení přejde do `src` podadresáře, včetně `model.py` .

Následující kód je pořízen z [tohoto úvodního příkladu](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html) z PyTorch. Všimněte si, že Azure Machine Learning koncepty se vztahují na jakýkoliv kód strojového učení, ne jen na PyTorch.

```python
# tutorial/src/model.py
import torch.nn as nn
import torch.nn.functional as F


class Net(nn.Module):
    def __init__(self):
        super(Net, self).__init__()
        self.conv1 = nn.Conv2d(3, 6, 5)
        self.pool = nn.MaxPool2d(2, 2)
        self.conv2 = nn.Conv2d(6, 16, 5)
        self.fc1 = nn.Linear(16 * 5 * 5, 120)
        self.fc2 = nn.Linear(120, 84)
        self.fc3 = nn.Linear(84, 10)

    def forward(self, x):
        x = self.pool(F.relu(self.conv1(x)))
        x = self.pool(F.relu(self.conv2(x)))
        x = x.view(-1, 16 * 5 * 5)
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = self.fc3(x)
        return x
```

Dále definujte školicí skript. Tento skript stáhne datovou sadu CIFAR10 pomocí `torchvision.dataset` rozhraní API PyTorch, nastaví síť definovanou v a nahlásí `model.py` ji pro dva epochsy pomocí standardní SGD a ztráty mezi entropiemi.

Vytvořte `train.py` skript v `src` podadresáři:

```python
# tutorial/src/train.py
import torch
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms

from model import Net

# download CIFAR10 data
trainset = torchvision.datasets.CIFAR10(
    root="./data",
    train=True,
    download=True,
    transform=torchvision.transforms.ToTensor(),
)
trainloader = torch.utils.data.DataLoader(
    trainset, batch_size=4, shuffle=True, num_workers=2
)

if __name__ == "__main__":

    # define convolutional network
    net = Net()

    # set up pytorch loss /  optimizer
    criterion = torch.nn.CrossEntropyLoss()
    optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)

    # train the network
    for epoch in range(2):

        running_loss = 0.0
        for i, data in enumerate(trainloader, 0):
            # unpack the data
            inputs, labels = data

            # zero the parameter gradients
            optimizer.zero_grad()

            # forward + backward + optimize
            outputs = net(inputs)
            loss = criterion(outputs, labels)
            loss.backward()
            optimizer.step()

            # print statistics
            running_loss += loss.item()
            if i % 2000 == 1999:
                loss = running_loss / 2000
                print(f"epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}")
                running_loss = 0.0

    print("Finished Training")

```

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

## <a name="create-a-python-environment"></a>Vytvoření prostředí Pythonu

Pro demonstrační účely budeme používat prostředí conda. (Kroky pro virtuální prostředí PIP jsou téměř identické.)

`pytorch-env.yml`Ve skrytém adresáři vytvořte soubor s názvem `.azureml` :

```yml
# tutorial/.azureml/pytorch-env.yml
name: pytorch-env
channels:
    - defaults
    - pytorch
dependencies:
    - python=3.6.2
    - pytorch
    - torchvision
```

Toto prostředí má všechny závislosti, které model a školicí skript vyžaduje. Všimněte si, že Azure Machine Learning SDK pro Python neexistuje žádná závislost.

## <a name="test-locally"></a>Test lokálně

Pomocí následujícího kódu otestujte skript spouštěný místně v tomto prostředí:

```bash
conda env create -f .azureml/pytorch-env.yml    # create conda environment
conda activate pytorch-env                      # activate conda environment
python src/train.py                             # train model
```

Po spuštění tohoto skriptu uvidíte data stažená do adresáře s názvem `tutorial/data` .

## <a name="create-the-control-script"></a>Vytvoření řídicího skriptu

Rozdíl mezi následujícím skriptem řízení a ten, který jste použili k odeslání "Hello World!" je přidání několika dalších řádků pro nastavení prostředí.

Vytvořte nový soubor Python v adresáři s `tutorial` názvem `04-run-pytorch.py` :

```python
# tutorial/04-run-pytorch.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig

if __name__ == "__main__":
    ws = Workspace.from_config()
    experiment = Experiment(workspace=ws, name='day1-experiment-train')
    config = ScriptRunConfig(source_directory='src', script='train.py', compute_target='cpu-cluster')

    # set up pytorch environment
    env = Environment.from_conda_specification(name='pytorch-env', file_path='.azureml/pytorch-env.yml')
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
      Azure Machine Learning poskytuje koncept [prostředí](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true) , které představuje reprodukovatelné prostředí Pythonu ve verzi pro spouštění experimentů. Je snadné vytvořit prostředí z místního prostředí conda nebo PIP.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config.run_config.environment = env`
   :::column-end:::
   :::column span="2":::
      Přidá prostředí do [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true).
   :::column-end:::
:::row-end:::

## <a name="submit-the-run-to-azure-machine-learning"></a>Odeslat běh do Azure Machine Learning

Pokud jste přepnuli místní prostředí, nezapomeňte přepnout zpátky do prostředí, ve kterém je nainstalovaná sada Azure Machine Learning SDK pro Python. 

Potom následujícím příkazem:

```bash
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

## <a name="log-training-metrics"></a>Metriky školení protokolu

Teď, když máte školení modelu v Azure Machine Learning, začněte sledovat některé metriky výkonu.

Aktuální školicí skript vytiskne metriky do terminálu. Azure Machine Learning poskytuje mechanismus protokolování metrik s více funkcemi. Přidáním několika řádků kódu získáte možnost vizualizovat metriky v nástroji Studio a porovnat metriky mezi několika spuštěními.

### <a name="modify-trainpy-to-include-logging"></a>Upravit `train.py` a zahrnout protokolování

Upravte `train.py` skript tak, aby zahrnoval dva další řádky kódu:

```python
# train.py
import torch
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms

from model import Net
from azureml.core import Run


# ADDITIONAL CODE: get Azure Machine Learning run from the current context
run = Run.get_context()

# download CIFAR10 data
trainset = torchvision.datasets.CIFAR10(
    root="./data",
    train=True,
    download=True,
    transform=torchvision.transforms.ToTensor(),
)
trainloader = torch.utils.data.DataLoader(
    trainset, batch_size=4, shuffle=True, num_workers=2
)

if __name__ == "__main__":

    # define convolutional network
    net = Net()

    # set up pytorch loss /  optimizer
    criterion = torch.nn.CrossEntropyLoss()
    optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)

    # train the network
    for epoch in range(2):

        running_loss = 0.0
        for i, data in enumerate(trainloader, 0):
            # unpack the data
            inputs, labels = data

            # zero the parameter gradients
            optimizer.zero_grad()

            # forward + backward + optimize
            outputs = net(inputs)
            loss = criterion(outputs, labels)
            loss.backward()
            optimizer.step()

            # print statistics
            running_loss += loss.item()
            if i % 2000 == 1999:
                loss = running_loss / 2000
                run.log('loss', loss) # ADDITIONAL CODE: log loss metric to Azure Machine Learning
                print(f'epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}')
                running_loss = 0.0

    print('Finished Training')
```

#### <a name="understand-the-additional-two-lines-of-code"></a>Pochopení dalších dvou řádků kódu

V aplikaci `train.py` získáte přístup k objektu Run z _within_ školicího skriptu samotného pomocí `Run.get_context()` metody a použijete ho k protokolování metrik:

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

### <a name="update-the-conda-environment-file"></a>Aktualizace souboru prostředí conda

`train.py`Skript právě zabral novou závislost na `azureml.core` . Aktualizace `pytorch-env.yml` , aby odrážela tuto změnu:

```yaml
# tutorial/.azureml/pytorch-env.yml
name: pytorch-env
channels:
    - defaults
    - pytorch
dependencies:
    - python=3.6.2
    - pytorch
    - torchvision
    - pip
    - pip:
        - azureml-sdk
```

### <a name="submit-the-run-to-azure-machine-learning"></a>Odeslat běh do Azure Machine Learning
Odeslat tento skript jednou za další:

```bash
python 04-run-pytorch.py
```

Tentokrát, když navštívíte Studio, přejděte na kartu **metriky** , kde se teď můžete podívat na průběžné aktualizace modelu na základě ztráty školení.

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/logging-metrics.png" alt-text="Graf ztrát školení na kartě metriky.":::

## <a name="next-steps"></a>Další kroky

V této relaci jste upgradovali ze základního "Hello World!" skript do realističtějšího školicího skriptu, který vyžadoval spuštění konkrétního prostředí Pythonu. Zjistili jste, jak převzít místní prostředí conda do cloudu pomocí Azure Machine Learningch prostředí. Nakonec jste viděli, jak v několika řádcích kódu můžete protokolovat metriky pro Azure Machine Learning.

Existují i jiné způsoby vytváření Azure Machine Learning prostředí, včetně souboru [pip requirements.txt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#from-pip-requirements-name--file-path-) nebo [ze stávajícího místního prostředí conda](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#from-existing-conda-environment-name--conda-environment-name-).

V další relaci uvidíte, jak pracovat s daty v Azure Machine Learning nahráním datové sady CIFAR10 do Azure.

> [!div class="nextstepaction"]
> [Kurz: Převeďte vlastní data](tutorial-1st-experiment-bring-data.md)

>[!NOTE] 
> Pokud chcete dokončit řadu kurzů zde a nepostupovat k dalšímu kroku, nezapomeňte [vyčistit své prostředky](tutorial-1st-experiment-bring-data.md#clean-up-resources).
