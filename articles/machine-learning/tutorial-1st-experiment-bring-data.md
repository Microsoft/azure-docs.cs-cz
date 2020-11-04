---
title: 'Kurz: použití vlastních dat'
titleSuffix: Azure Machine Learning
description: Část 4 Azure Machine Learning série Začínáme ukazuje, jak používat vlastní data ve vzdáleném školicím běhu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: tracking-python
ms.openlocfilehash: 123e55202de8a33bca88afcfd1f0dc0c7edeae77
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93320090"
---
# <a name="tutorial-use-your-own-data-part-4-of-4"></a>Kurz: použití vlastních dat (část 4 ze 4)

V tomto kurzu se dozvíte, jak nahrát a používat vlastní data pro učení modelů strojového učení v Azure Machine Learning.

Tento kurz je čtvrtou *částí série kurzů* , ve které se seznámíte se základy Azure Machine Learning a dokončení úloh strojového učení na základě úloh v Azure. Tento kurz sestaví na práci, kterou jste dokončili v [části 1: nastavení](tutorial-1st-experiment-sdk-setup-local.md), [2. část: Run "Hello World!"](tutorial-1st-experiment-hello-world.md)a [část 3: výuka modelu](tutorial-1st-experiment-sdk-train.md).

V [části 3: výuka modelu](tutorial-1st-experiment-sdk-train.md), data byla stažena prostřednictvím sestavené `torchvision.datasets.CIFAR10` metody v rozhraní PyTorch API. V mnoha případech ale budete chtít používat vlastní data ve vzdáleném školicím běhu. Tento článek ukazuje pracovní postup, který můžete použít pro práci s vlastními daty v Azure Machine Learning.

V tomto kurzu jste:

> [!div class="checklist"]
> * Nakonfigurujte školicí skript, který bude používat data v místním adresáři.
> * Otestujte školicí skript místně.
> * Nahrajte data do Azure.
> * Vytvořte skript ovládacího prvku.
> * Pochopení nových konceptů Azure Machine Learning (předávání parametrů, datových sad, úložišť dat).
> * Odešlete a spusťte školicí skript.
> * Zobrazte si výstup kódu v cloudu.

## <a name="prerequisites"></a>Předpoklady

* Dokončení [třetí části](tutorial-1st-experiment-sdk-train.md) série.
* Úvodní znalost jazyka Pythonu a pracovních postupů strojového učení.
* Místní vývojové prostředí, například Visual Studio Code, Jupyter nebo PyCharm.
* Python (verze 3,5 až 3,7).

## <a name="adjust-the-training-script"></a>Úprava školicího skriptu
Teď máte školicí skript (kurz/src/vlak. py) běžící v Azure Machine Learning a můžete monitorovat výkon modelu. Řekněme, že skript školicího skriptu zavádíme argumenty. Použití argumentů vám umožní snadno porovnat různé parametry.

Náš školicí skript je teď nastavený tak, aby při každém spuštění stahoval datovou sadu CIFAR10. Následující kód Pythonu byl upraven tak, aby četl data z adresáře.

>[!NOTE] 
> Použití `argparse` parameterizes skriptu.

```python
# tutorial/src/train.py
import os
import argparse
import torch
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms

from model import Net
from azureml.core import Run

run = Run.get_context()

if __name__ == "__main__":
    parser = argparse.ArgumentParser()
    parser.add_argument('--data_path', type=str, help='Path to the training data')
    parser.add_argument('--learning_rate', type=float, default=0.001, help='Learning rate for SGD')
    parser.add_argument('--momentum', type=float, default=0.9, help='Momentum for SGD')
    args = parser.parse_args()
    
    print("===== DATA =====")
    print("DATA PATH: " + args.data_path)
    print("LIST FILES IN DATA PATH...")
    print(os.listdir(args.data_path))
    print("================")
    
    # prepare DataLoader for CIFAR10 data
    transform = transforms.Compose([transforms.ToTensor(), transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))])
    trainset = torchvision.datasets.CIFAR10(
        root=args.data_path,
        train=True,
        download=False,
        transform=transform,
    )
    trainloader = torch.utils.data.DataLoader(trainset, batch_size=4, shuffle=True, num_workers=2)

    # define convolutional network
    net = Net()

    # set up pytorch loss /  optimizer
    criterion = torch.nn.CrossEntropyLoss()
    optimizer = optim.SGD(
        net.parameters(),
        lr=args.learning_rate,
        momentum=args.momentum,
    )

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
                run.log('loss', loss) # log loss metric to AML
                print(f'epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}')
                running_loss = 0.0

    print('Finished Training')
```

### <a name="understanding-the-code-changes"></a>Porozumění změnám kódu

Kód v nástroji `train.py` použil `argparse` knihovnu k nastavení `data_path` , `learning_rate` a `momentum` .

```python
# .... other code
parser = argparse.ArgumentParser()
parser.add_argument('--data_path', type=str, help='Path to the training data')
parser.add_argument('--learning_rate', type=float, default=0.001, help='Learning rate for SGD')
parser.add_argument('--momentum', type=float, default=0.9, help='Momentum for SGD')
args = parser.parse_args()
# ... other code
```

`train.py`Skript byl také přizpůsoben pro aktualizaci Optimalizátoru tak, aby používal uživatelsky definované parametry:

```python
optimizer = optim.SGD(
    net.parameters(),
    lr=args.learning_rate,     # get learning rate from command-line argument
    momentum=args.momentum,    # get momentum from command-line argument
)
```

## <a name="test-the-script-locally"></a>Místní otestování skriptu

Váš skript teď jako argument přijímá _cestu k datům_ . Začněte tím, že ho otestujete místně. Do struktury adresáře kurzu přidejte složku s názvem `data` . Vaše adresářová struktura by měla vypadat takto:

```txt
tutorial
└──.azureml
|  └──config.json
|  └──pytorch-env.yml
└──data
└──src
|  └──hello.py
|  └──model.py
|  └──train.py
└──01-create-workspace.py
└──02-create-compute.py
└──03-run-hello.py
└──04-run-pytorch.py
```

Pokud jste v předchozím kurzu nespouštěli místně, nebudete `train.py` mít `data/` adresář. V takovém případě spusťte `torchvision.datasets.CIFAR10` metodu místně `download=True` ve vašem `train.py` skriptu.

Pokud chcete upravený školicí skript spustit místně, zavolejte:

```bash
python src/train.py --data_path ./data --learning_rate 0.003 --momentum 0.92
```

Nemusíte si stahovat datovou sadu CIFAR10 předáním místní cesty k datům. Můžete také experimentovat s různými hodnotami pro _studijní_ kurzy a _potenciál_ parametry, aniž byste je museli v školicím skriptu zakódovat.

## <a name="upload-the-data-to-azure"></a>Nahrajte data do Azure.

Pokud chcete tento skript spustit v Azure Machine Learning, je potřeba, abyste v Azure mohli zpřístupnit vaše školicí údaje. Váš pracovní prostor Azure Machine Learning vybaven _výchozím_ úložištěm dat. Toto je účet Azure Blob Storage, do kterého můžete ukládat školicí data.

>[!NOTE] 
> Azure Machine Learning umožňuje propojit další cloudová úložiště dat, která ukládají vaše data. Další podrobnosti najdete v dokumentaci k [úložiště dat](./concept-data.md).  

Vytvořte nový skript řízení Pythonu s názvem `05-upload-data.py` v `tutorial` adresáři:

```python
# tutorial/05-upload-data.py
from azureml.core import Workspace
ws = Workspace.from_config()
datastore = ws.get_default_datastore()
datastore.upload(src_dir='./data', target_path='datasets/cifar10', overwrite=True)
```

`target_path`Hodnota určuje cestu k úložišti dat, kam budou odeslána data CIFAR10.

>[!TIP] 
> Když používáte Azure Machine Learning k nahrávání dat, můžete k nahrání souborů ad hoc použít [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) . Pokud potřebujete nástroj ETL, můžete k ingestování dat do Azure použít [Azure Data Factory](../data-factory/introduction.md) .

Spusťte soubor Pythonu pro nahrání dat. (Nahrávání by mělo být rychlé, méně než 60 sekund.)

```bash
python 05-upload-data.py
```
Měl by se zobrazit následující standardní výstup:
```txt
Uploading ./data\cifar-10-batches-py\data_batch_2
Uploaded ./data\cifar-10-batches-py\data_batch_2, 4 files out of an estimated total of 9
.
.
Uploading ./data\cifar-10-batches-py\data_batch_5
Uploaded ./data\cifar-10-batches-py\data_batch_5, 9 files out of an estimated total of 9
Uploaded 9 files
```


## <a name="create-a-control-script"></a>Vytvoření skriptu ovládacího prvku

Jak už jste hotovi, vytvořte nový skript ovládacího prvku Pythonu s názvem `06-run-pytorch-data.py` :

```python
# tutorial/06-run-pytorch-data.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig
from azureml.core import Dataset

if __name__ == "__main__":
    ws = Workspace.from_config()
    
    datastore = ws.get_default_datastore()
    dataset = Dataset.File.from_files(path=(datastore, 'datasets/cifar10'))

    experiment = Experiment(workspace=ws, name='day1-experiment-data')

    config = ScriptRunConfig(
        source_directory='./src',
        script='train.py',
        compute_target='cpu-cluster',
        arguments=[
            '--data_path', dataset.as_named_input('input').as_mount(),
            '--learning_rate', 0.003,
            '--momentum', 0.92],
        )
    
    # set up pytorch environment
    env = Environment.from_conda_specification(name='pytorch-env',file_path='.azureml/pytorch-env.yml')
    config.run_config.environment = env

    run = experiment.submit(config)
    aml_url = run.get_portal_url()
    print("Submitted to an Azure Machine Learning compute cluster. Click on the link below")
    print("")
    print(aml_url)
```

### <a name="understand-the-code-changes"></a>Pochopení změn kódu

Řídicí skript je podobný jako z [třetí části této série](tutorial-1st-experiment-sdk-train.md)s následujícími novými řádky:

:::row:::
   :::column span="":::
      `dataset = Dataset.File.from_files( ... )`
   :::column-end:::
   :::column span="2":::
      [Datová sada](/python/api/azureml-core/azureml.core.dataset.dataset?preserve-view=true&view=azure-ml-py) se používá k odkazování na data, která jste nahráli do Azure Blob Storage. Datové sady jsou abstraktní vrstva nad daty, která jsou navržena pro zlepšení spolehlivosti a věrohodnosti.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig(...)`
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) se upraví tak, aby zahrnoval seznam argumentů, které se budou předávat do `train.py` . `dataset.as_named_input('input').as_mount()`Argument znamená, že zadaný adresář bude _připojen_ k cíli služby Compute.
   :::column-end:::
:::row-end:::

## <a name="submit-the-run-to-azure-machine-learning"></a>Odeslat běh do Azure Machine Learning

Nyní spusťte znovu spuštění, aby se použila nová konfigurace:

```bash
python 06-run-pytorch-data.py
```

Tento kód vytiskne adresu URL k experimentu v Azure Machine Learning Studiu. Pokud přejdete na tento odkaz, uvidíte, že váš kód běží.

### <a name="inspect-the-log-file"></a>Kontrola souboru protokolu

V studiu přejdete na experimentový běh (výběrem předchozího výstupu adresy URL) následovaných **výstupy + protokoly**. Vyberte `70_driver_log.txt` soubor. Měl by se zobrazit následující výstup:

```txt
Processing 'input'.
Processing dataset FileDataset
{
  "source": [
    "('workspaceblobstore', 'datasets/cifar10')"
  ],
  "definition": [
    "GetDatastoreFiles"
  ],
  "registration": {
    "id": "XXXXX",
    "name": null,
    "version": null,
    "workspace": "Workspace.create(name='XXXX', subscription_id='XXXX', resource_group='X')"
  }
}
Mounting input to /tmp/tmp9kituvp3.
Mounted input to /tmp/tmp9kituvp3 as folder.
Exit __enter__ of DatasetContextManager
Entering Run History Context Manager.
Current directory:  /mnt/batch/tasks/shared/LS_root/jobs/dsvm-aml/azureml/tutorial-session-3_1600171983_763c5381/mounts/workspaceblobstore/azureml/tutorial-session-3_1600171983_763c5381
Preparing to call script [ train.py ] with arguments: ['--data_path', '$input', '--learning_rate', '0.003', '--momentum', '0.92']
After variable expansion, calling script [ train.py ] with arguments: ['--data_path', '/tmp/tmp9kituvp3', '--learning_rate', '0.003', '--momentum', '0.92']

Script type = None
===== DATA =====
DATA PATH: /tmp/tmp9kituvp3
LIST FILES IN DATA PATH...
['cifar-10-batches-py', 'cifar-10-python.tar.gz']
```

Šestiměsíční

- Azure Machine Learning k výpočetnímu clusteru automaticky připojen Blob Storage.
- ``dataset.as_named_input('input').as_mount()``Použití v řídicím skriptu se překládá na přípojný bod.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Můžete také zachovat skupinu prostředků, ale odstranit jeden pracovní prostor. Zobrazte vlastnosti pracovního prostoru a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jsme viděli, jak nahrát data do Azure pomocí `Datastore` . Úložiště dat poskytované jako cloudové úložiště pro váš pracovní prostor a poskytuje trvalé a flexibilní místo pro uchování vašich dat.

Zjistili jste, jak upravit školicí skript, aby přijímal cestu k datům prostřednictvím příkazového řádku. Pomocí nástroje `Dataset` jste mohli připojit adresář ke vzdálenému spuštění. 

Teď, když máte model, Naučte se:

* Postup [nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md).