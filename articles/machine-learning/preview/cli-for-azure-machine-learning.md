---
title: "Nainstalovat a používat strojového učení rozhraní příkazového řádku pro nejdůležitější úkoly Azure Machine Learning"
description: "Zjistěte, jak nainstalovat a používat rozhraní příkazového řádku pro nejběžnější strojového učení úlohy v Azure Machine Learning."
services: machine-learning
author: haining
ms.author: haining
manager: mwinkler
ms.reviewer: mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 03/10/2018
ms.openlocfilehash: f34c247728c854c47f486925d440eee0dc5b1945
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="install-and-use-the-machine-learning-cli-for-top-tasks-in-azure-machine-learning"></a>Nainstalovat a používat strojového učení rozhraní příkazového řádku pro nejdůležitější úkoly v Azure Machine Learning

Služby Azure Machine Learning se vědecké zpracování dat integrované, začátku do konce a pokročilé analýzy řešení. Professional datových vědců slouží služby Azure Machine Learning k přípravě dat, vývoj experimenty a nasazovat modely v cloudovém měřítku. 

Azure Machine Learning nabízí rozhraní příkazového řádku (CLI), pomocí které můžete:
+ Správa prostoru a projektů
+ Nastavit výpočetní cíle
+ Spustit experimenty školení
+ Zobrazení historie a metriky pro posledních běží
+ Modely nasazení do provozu jako webové služby
+ Správa modelů produkční a služby

Tento článek představuje některé velmi užitečné příkazy rozhraní příkazového řádku pro usnadnění vaší práce. 

![Azure Machine Learning rozhraní příkazového řádku](media/cli-for-azure-machine-learning/flow.png)

>[!NOTE]
>Rozhraní příkazového řádku doručit službou Azure Machine Learning se liší od [rozhraní příkazového řádku Azure](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest), který se používá ke správě prostředků Azure.

## <a name="get-and-start-cli"></a>Získat a spustit rozhraní příkazového řádku

Toto rozhraní příkazového řádku získáte nainstalujte Azure Machine Learning Workbench, který lze stáhnout z tohoto umístění:
    + Windows – https://aka.ms/azureml-wb-msi 
    + MacOS - https://aka.ms/azureml-wb-dmg 

Spuštění rozhraní příkazového řádku:
+ V nástroji Azure Machine Learning Workbench, spusťte rozhraní příkazového řádku z nabídky **souboru -> spusťte příkazový řádek.**

## <a name="get-command-help"></a>Získat nápovědu k příkazu 

Můžete získat další informace o rozhraní příkazového řádku pomocí `--debug` nebo `--help` po příkazech, jako třeba `az ml <xyz> --debug` kde `<xyz>` je název příkazu. Příklad:
```azurecli
az ml computetarget --debug 

az ml experiment --help
```

## <a name="common-cli-tasks-for-azure-machine-learning"></a>Běžné úlohy rozhraní příkazového řádku pro Azure Machine Learning 

Další informace o běžné úkoly, je možné provádět pomocí rozhraní příkazového řádku v této části, včetně:
+ [Nastavení výpočetní cíle](#target)
+ [Odeslání úlohy pro vzdálené spuštění](#jobs)
+ [Práce s poznámkovými bloky Jupyter](#jupyter)
+ [Interakci s historií spuštění](#history)
+ [Konfigurace prostředí pro zprovoznění](#o16n)

<a name="target"></a>

### <a name="set-up-a-compute-target"></a>Nastavit výpočetní cíl

Můžete vypočítat vaší strojového učení modelu v různé cíle, včetně:
+ v místním režimu
+ v vědecké zpracování dat virtuálních počítačů (DSVM)
+ v clusteru HDInsight

Připojit cílové vědecké účely dat virtuálních počítačů:
```azurecli
az ml computetarget attach remotedocker -n <target name> -a <ip address or FQDN> -u <username> -w <password>
``` 

Pro připojení cílové HDInsight:
```azurecli
az ml computetarget attach cluster -n <target name> -a <cluster name, e.g. myhdicluster-ssh.azurehdinsight.net> -u <ssh username> -w <ssh password>
```

V rámci **aml_config** složky, můžete změnit conda závislosti. 

Navíc můžete pracovat s PySpark, Python nebo Python v GPU DSVM. 

Chcete-li definovat Python operační režim:
+ Pro Python, přidejte `Framework:Python` v `<target name>.runconfig` 

+ PySpark, přidejte `Framework:PySpark` v `<target name>.runconfig` 

+ Pro jazyk Python v DSVM GPU
    1. Přidat `Framework:Python` v `<target name>.runconfig` 

    1. Navíc přidat `baseDockerImage: microsoft/mmlspark:plus-gpu-0.9.9 and nvidiaDocker:true` v `<target name>.compute`

Příprava cílového výpočetní:
```azurecli
az ml experiment prepare -c <target name>
```

>[!TIP]
>Chcete-li zobrazit vaše předplatné:<br/>
>`az account show`<br/>
>
>Chcete-li nastavit vaše předplatné:<br/>
>`az account set –s "my subscription name" `

<a name="jobs"></a>

### <a name="submit-remote-jobs"></a>Odesílání vzdálené úloh

Se odeslat úlohu na vzdálený cíl:
```azurecli
az ml experiment submit -c <target name> myscript.py
```

<a name="jupyter"></a>

### <a name="work-with-jupyter-notebooks"></a>Práce s poznámkovými bloky Jupyter

Spuštění poznámkového bloku Jupyter:
```azurecli
az ml notebook start
```

Tento příkaz spustí poznámkového bloku Jupyter v localhost. Můžete pracovat na místní výběrem jádra Python 3, nebo ve vzdálené virtuálního počítače fungovat tak, že vyberete jádra `<target name>`.

<a name="history"></a>

### <a name="interact-with-and-explore-the-run-history"></a>Komunikovat s a seznamte se s historie spouštění

Do seznamu historie spouštění:
```azurecli
az ml history list -o table
```

Seznam všech dokončil spustí:
```azurecli
az ml history list --query "[?status=='Completed']" -o table
```

Najít spustí s nejsprávnější výsledky:
```azurecli
az ml history list --query "@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy"
```

Můžete také stáhnout soubory generované každé spuštění. 

Podporovat model, který je uložen v výstupy složky:
```azurecli
az ml history promote -r <run id> -ap outputs/model.pkl -n <model name>
```

Stažení tohoto modelu:
```azurecli
az ml asset download -l assets/model.pkl.link -d <model folder path>
```

<a name="o16n"></a>

### <a name="configure-your-environment-to-operationalize"></a>Konfigurace prostředí, aby zprovoznění

Chcete-li nastavit svoje prostředí operationalization, musíte vytvořit:

> [!div class="checklist"]
> * Skupinu prostředků. 
> * účet úložiště
> * Azure kontejneru registru (ACR)
> * Přehled účet aplikace
> * Kubernetes nasazení v clusteru Azure Container Service (ACS)


Nastavení pro testování v kontejner Docker místní nasazení:
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name>
```

Nastavení clusteru s Kubernetes služby ACS:
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name> --cluster
```

Pokud chcete monitorovat stav nasazení:
```azurecli
az ml env show -n <environment name> -g <resource group name>
```

Nastavení prostředí, který se má použít:
```azurecli
az ml env set -n <environment name> -g <resource group name>
```

## <a name="next-steps"></a>Další kroky

Začínáme s jedním z těchto článků: 
+ [Instalace a spuštění pomocí Azure Machine Learning](quickstart-installation.md)
+ [Klasifikace dat Iris kurz: Část 1](tutorial-classifying-iris-part-1.md)

Prozkoumat podrobněji s jedním z těchto článků:
+ [Rozhraní příkazového řádku pro správu modelů](model-management-cli-reference.md)