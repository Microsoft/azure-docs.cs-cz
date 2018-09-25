---
title: Nainstalovat a používat rozhraní příkazového řádku pro hlavní úlohy – Azure Machine Learning
description: Zjistěte, jak nainstalovat a používat rozhraní příkazového řádku pro nejběžnější strojového učení úlohy v Azure Machine Learning.
services: machine-learning
author: haining
ms.author: haining
manager: cgronlun
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/10/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 06e85845d41b240638a5b5b4d75d64fd460a99bf
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953323"
---
# <a name="install-and-use-the-machine-learning-cli-for-top-tasks-in-azure-machine-learning"></a>Instalace a používání strojového učení rozhraní příkazového řádku pro nejdůležitější úkoly v Azure Machine Learning

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

Služba Azure Machine Learning je začátku do konce, integrované datové vědy a pokročilé řešení pro analýzu. Profesionální datové vědce, můžete použít službu Azure Machine Learning k přípravě dat, vývoji experimentů a nasazování modelů na úrovni cloudu. 

Azure Machine Learning nabízí rozhraní příkazového řádku (CLI), pomocí kterých lze:
+ Správa pracovních prostorů a projektů
+ Nastavení cílových výpočetních prostředí
+ Spouštět experimenty školení
+ Zobrazení historie a metriky pro poslední spuštění
+ Nasadit modely do produkce jako webové služby
+ Správa modelů v produkčním prostředí a služeb

Tento článek představuje několik nejužitečnější příkazů rozhraní příkazového řádku pro vaše pohodlí. 

![Azure Machine Learning rozhraní příkazového řádku](media/cli-for-azure-machine-learning/flow.png)

## <a name="what-you-need-to-get-started"></a>Co potřebujete, abyste mohli začít

Potřebujete přístup přispěvatele k předplatnému Azure nebo skupinu prostředků, kde můžete nasadit modely. Potřebujete také nainstalovat aplikaci Azure Machine Learning Workbench, aby spusťte rozhraní příkazového řádku. 

>[!IMPORTANT]
>Rozhraní příkazového řádku se dodávají s se liší od služby Azure Machine Learning [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), který se používá pro správu prostředků Azure.

## <a name="get-and-start-cli"></a>Získání a spuštění rozhraní příkazového řádku

Toto rozhraní příkazového řádku získáte nainstalujte aplikaci Azure Machine Learning Workbench, můžete stáhnout tady:
    + Windows – https://aka.ms/azureml-wb-msi 
    + MacOS - https://aka.ms/azureml-wb-dmg 

Spuštění rozhraní příkazového řádku:
+ V aplikaci Azure Machine Learning Workbench, spusťte rozhraní příkazového řádku z nabídky **soubor -> otevřít příkazový řádek.**

## <a name="get-command-help"></a>Získání nápovědy k příkazům 

Můžete získat další informace o příkazech rozhraní příkazového řádku pomocí `--debug` nebo `--help` po příkazech, jako například `az ml <xyz> --debug` kde `<xyz>` je název příkazu. Příklad:
```azurecli
az ml computetarget --debug 

az ml experiment --help
```

## <a name="common-cli-tasks-for-azure-machine-learning"></a>Běžné úlohy rozhraní příkazového řádku pro službu Azure Machine Learning 

Další informace o nejběžnějších úlohách můžete provádět pomocí rozhraní příkazového řádku v této části, včetně:
+ [Nastavení cílových výpočetních prostředí](#target)
+ [Odesílání úloh pro vzdálené spuštění](#jobs)
+ [Práce s poznámkovými bloky Jupyter](#jupyter)
+ [Interakce s historie spuštění](#history)
+ [Konfigurace prostředí na zprovoznění](#o16n)

<a name="target"></a>

### <a name="set-up-a-compute-target"></a>Nastavení cílové výpočetní prostředí

Můžete vypočítat váš model strojového učení v různých cílů, včetně:
+ v místním režimu
+ ve virtuálním počítači (DSVM) pro datové vědy
+ v clusteru služby HDInsight

Připojit cílové virtuální počítač pro datové vědy:
```azurecli
az ml computetarget attach remotedocker -n <target name> -a <ip address or FQDN> -u <username> -w <password>
``` 

Připojit cílové HDInsight:
```azurecli
az ml computetarget attach cluster -n <target name> -a <cluster name, e.g. myhdicluster-ssh.azurehdinsight.net> -u <ssh username> -w <ssh password>
```

V rámci **aml_config** složky, můžete změnit závislosti systému conda. 

Navíc můžete pracovat s PySpark, Python nebo Python v GPU DSVM. 

Chcete-li definovat režim operace Pythonu:
+ Pro Python, přidejte `Framework:Python` v `<target name>.runconfig` 

+ PySpark, přidejte `Framework:PySpark` v `<target name>.runconfig` 

+ Pro jazyk Python v GPU DSVM,
    1. Přidat `Framework:Python` v `<target name>.runconfig` 

    1. Přidejte také `baseDockerImage: microsoft/mmlspark:plus-gpu-0.9.9 and nvidiaDocker:true` v `<target name>.compute`

Příprava cílového výpočetního prostředí:
```azurecli
az ml experiment prepare -c <target name>
```

>[!TIP]
>Chcete-li zobrazit vaše předplatné:<br/>
>`az account show`<br/>
>
>Nastavení předplatného:<br/>
>`az account set –s "my subscription name" `

<a name="jobs"></a>

### <a name="submit-remote-jobs"></a>Odesílat vzdálené úlohy

Odeslání úlohy pro vzdálený cíl:
```azurecli
az ml experiment submit -c <target name> myscript.py
```

<a name="jupyter"></a>

### <a name="work-with-jupyter-notebooks"></a>Práce s poznámkovými bloky Jupyter

Chcete-li spustit Poznámkový blok Jupyter:
```azurecli
az ml notebook start
```

Tento příkaz spustí Poznámkový blok Jupyter v místním hostiteli. Můžete pracovat v místní tak, že vyberete jádra Python 3, nebo ve vzdáleném virtuálním počítači fungovat tak, že vyberete jádra `<target name>`.

<a name="history"></a>

### <a name="interact-with-and-explore-the-run-history"></a>Pracovat a prozkoumejte historii spuštění

Do seznamu historie spuštění:
```azurecli
az ml history list -o table
```

Chcete-li vypsat všechny dokončené běhy:
```azurecli
az ml history list --query "[?status=='Completed']" -o table
```

K vyhledání spuštění s největší přesností:
```azurecli
az ml history list --query "@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy"
```

Můžete také stáhnout soubory generované záznamem pro každé spuštění. 

Podporovat model, který je uložen ve složce výstupy:
```azurecli
az ml history promote -r <run id> -ap outputs/model.pkl -n <model name>
```

Chcete-li stáhnout tento model:
```azurecli
az ml asset download -l assets/model.pkl.link -d <model folder path>
```

<a name="o16n"></a>

### <a name="configure-your-environment-to-operationalize"></a>Nakonfigurujte své prostředí na zprovoznění

Chcete-li nastavení prostředí operacionalizace, musíte vytvořit:

> [!div class="checklist"]
> * Skupinu prostředků 
> * Účet úložiště
> * Služby Azure Container Registry (ACR)
> * Účet Application insight
> * Nasazení Kubernetes v clusteru Azure Container Service (ACS)


Nastavení místního nasazení pro testování v kontejneru Dockeru:
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name>
```

Nastavení clusteru ACS s Kubernetes:
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name> --cluster
```

K monitorování stavu nasazení:
```azurecli
az ml env show -n <environment name> -g <resource group name>
```

Nastavení prostředí, který se má použít:
```azurecli
az ml env set -n <environment name> -g <resource group name>
```

## <a name="next-steps"></a>Další postup

Začněte s některou z těchto článků: 
+ [Nainstalovat a začít používat Azure Machine Learning](quickstart-installation.md)
+ [Kurz klasifikace Iris dat: Část 1](tutorial-classifying-iris-part-1.md)

Podívejte se podrobněji s jedním z těchto článků:
+ [Příkazy rozhraní příkazového řádku pro správu modelů](model-management-cli-reference.md)
