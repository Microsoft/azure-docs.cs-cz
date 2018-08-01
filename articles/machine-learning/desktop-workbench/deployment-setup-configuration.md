---
title: Nastavení správy modelů Azure Machine Learning a konfigurace | Dokumentace Microsoftu
description: Tento dokument popisuje kroky a koncepty součástí nastavení a konfigurace správy modelů ve službě Azure Machine Learning.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 12/6/2017
ms.openlocfilehash: 150114184f6f04f22aa9da409758daa6a0d175b5
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39369048"
---
# <a name="model-management-setup"></a>Nastavení správy modelů

Tento dokument vám pomůže začít s pomocí správy modelů ve službě Azure ML k nasazení a správě vašich modelů jako webové služby machine learning. 

Pomocí správy modelů ve službě Azure ML, můžete efektivně nasadit a spravovat modely Machine Learning, které se vytvářejí pomocí několika architektur, včetně ve SparkML, Keras, TensorFlow, Microsoft Cognitive Toolkit nebo Python. 

Na konci tohoto dokumentu vám měl mít svému prostředí pro správu modelu nastavené a připravené k nasazení modelů strojového učení.

## <a name="what-you-need-to-get-started"></a>Co potřebujete, abyste mohli začít
Pokud chcete naplno využít této příručce, byste měli mít přístup přispěvatele k předplatnému Azure nebo skupinu prostředků, kterou můžete nasadit vlastní modely a.
Rozhraní příkazového řádku je předem nainstalovaný na aplikaci Azure Machine Learning Workbench a v [Azure datové](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview).

## <a name="using-the-cli"></a>Pomocí rozhraní příkazového řádku
Pokud chcete používat rozhraní příkazového řádku (rozhraní příkazového řádku) z aplikace Workbench, klikněte na tlačítko **souboru** -> **otevřít příkazový řádek**. 

Na virtuální počítač pro datové vědy, připojení a otevřete okno příkazového řádku. Typ `az ml -h` zobrazíte možnosti. Podrobné informace o příkazů použít příznak--help.

V ostatních systémech je třeba nainstalovat rozhraní příkazového řádku.

>[!NOTE]
> V poznámkovém bloku Jupyter na Linux DSVM můžete přistupovat pomocí níže uvedeným formátem příkaz rozhraní příkazového řádku Azure a Azure ML CLI.  **Toto je pro aplikace Jupyter notebook v systému Linux DSVM, konkrétně**.  Tyto příkazy přístup k aktuální Python jádra v poznámkovém bloku (třeba conda `py35` prostředí)
>```
>import sys
>! {sys.executable} -m azure.cli login
>! {sys.executable} -m azure.cli ml -h
>```

### <a name="installing-or-updating-on-windows"></a>Instalaci (nebo aktualizaci) ve Windows

Instalace Pythonu z https://www.python.org/. Ujistěte se, že jste vybrali k instalaci pip.

Otevřete příkazový řádek pomocí spustit jako správce a spusťte následující příkazy:

```cmd
pip install -r https://aka.ms/az-ml-o16n-cli-requirements-file
```

### <a name="installing-or-updating-on-linux"></a>Instalace (nebo aktualizace) v Linuxu
Spuštěním následujícího příkazu z příkazového řádku a postupujte podle pokynů:

```bash
sudo -i
pip install -r https://aka.ms/az-ml-o16n-cli-requirements-file
```

### <a name="configuring-docker-on-linux"></a>Konfigurace Dockeru v Linuxu
Aby bylo možné konfigurovat Dockeru v Linuxu používají uživatelé nekořenovými, postupujte podle zde uvedených pokynů: [po instalaci kroky pro Linux](https://docs.docker.com/engine/installation/linux/linux-postinstall/)

>[!NOTE]
> V systému Linux DSVM můžete spustit skript uvedený níže, aby věci správně nakonfiguroval Dockeru. **Nezapomeňte se odhlásit a znovu přihlásit a po spuštění skriptu.**
>```
>sudo /opt/microsoft/azureml/initial_setup.sh
>```

## <a name="deploying-your-model"></a>Nasazení modelu
Nasazení modelů jako webové služby pomocí rozhraní příkazového řádku. Webové služby je možné nasadit místně nebo do clusteru.

Začněte s místní nasazení, ověřte, že váš model a kód fungovat, potom nasadit do clusteru pro použití v produkčním prostředí škálování.

Pokud chcete začít, musíte nastavit prostředí pro nasazení. Nastavení prostředí je čas úkolu. Po dokončení instalace můžete znovu použít prostředí pro další nasazení. Následující části Další podrobnosti.

Po dokončení instalace prostředí:
- Zobrazí se výzva k přihlášení do Azure. Pro přihlášení, použijte webový prohlížeč a otevřete stránku https://aka.ms/devicelogin a zadání poskytnutého kódu k ověření.
- Během procesu ověřování budete vyzváni k ověření pomocí účtu. Důležité: Vyberte účet, který má platné předplatné Azure a dostatečná oprávnění k vytváření prostředků v účtu. Po protokolu v dokončení se zobrazí informace o vašem předplatném a zobrazí se výzva, jestli chcete pokračovat s vybraného účtu.

### <a name="environment-setup"></a>Nastavení prostředí
K zahájení procesu instalace, budete muset zaregistrovat několik poskytovatelů prostředí tak, že zadáte následující příkazy:

```azurecli
az provider register -n Microsoft.MachineLearningCompute
az provider register -n Microsoft.ContainerRegistry
az provider register -n Microsoft.ContainerService
```
#### <a name="local-deployment"></a>Místní nasazení
K nasazení a testování webové služby na místním počítači, nastavte místní prostředí pomocí následujícího příkazu. Název skupiny prostředků je volitelný.

```azurecli
az ml env setup -l [Azure Region, e.g. eastus2] -n [your environment name] [-g [existing resource group]]
```
>[!NOTE] 
>Nasazení místní webové služby je potřeba nainstalovat Docker v místním počítači. 
>

Příkaz místní prostředí instalační program vytvoří následující prostředky ve vašem předplatném:
- Prostředek skupiny (Pokud se nezadá, nebo pokud neexistuje zadaný název)
- Účet úložiště
- Služby Azure Container Registry (ACR)
- Účet Application insights

Po úspěšném dokončení instalace, nastavte prostředí k použití pomocí následujícího příkazu:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

#### <a name="cluster-deployment"></a>Nasazení clusteru
Nasazení clusteru použijte pro zajištění vysoce škálovatelné produkční scénáře. Nastavuje clusteru ACS s Kubernetes jako orchestrátor. Cluster služby ACS lze škálovat na zpracovávat větší propustnost pro vaše volání webové služby.

Nasazení webové služby do produkčního prostředí, nejprve nastavit prostředí pomocí následujícího příkazu:

```azurecli
az ml env setup --cluster -n [your environment name] -l [Azure region e.g. eastus2] [-g [resource group]]
```

Příkaz clusteru prostředí instalační program vytvoří následující prostředky ve vašem předplatném:
- Prostředek skupiny (Pokud se nezadá, nebo pokud neexistuje zadaný název)
- Účet úložiště
- Služby Azure Container Registry (ACR)
- Nasazení Kubernetes v clusteru Azure Container Service (ACS)
- Účet Application insights

>[!IMPORTANT]
> Chcete-li úspěšně vytvořit prostředí clusteru, musíte mít přístup přispěvatele na předplatné Azure nebo skupinu prostředků.

Rychle se vytvoří skupina prostředků, účet úložiště a služby ACR. Nasazení služby ACS může trvat až 20 minut. 

Chcete-li zkontrolovat stav zřizování probíhající clusteru, použijte následující příkaz:

```azurecli
az ml env show -n [environment name] -g [resource group]
```

Po dokončení instalace je nutné nastavit prostředí pro toto nasazení. Použijte následující příkaz:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

>[!NOTE] 
> Po vytvoření prostředí pro další nasazení, stačí ji znovu použít pomocí výše uvedeného příkazu set.
>

### <a name="create-a-model-management-account"></a>Vytvořit účet správy modelů
Účet správy modelů je vyžadován pro nasazení modelů. Je potřeba provést jednou na jedno předplatné a můžete znovu použít stejný účet ve více nasazeních.

Chcete-li vytvořit nový účet, použijte následující příkaz:

```azurecli
az ml account modelmanagement create -l [Azure region, e.g. eastus2] -n [your account name] -g [resource group name] --sku-instances [number of instances, e.g. 1] --sku-name [Pricing tier for example S1]
```

Pokud chcete použít existující účet, použijte následující příkaz:
```azurecli
az ml account modelmanagement set -n [your account name] -g [resource group it was created in]
```

V důsledku tohoto procesu je prostředí připravené a vytvoření účtu správy modelů a zajistit tak funkce museli spravovat a nasazovat modely Machine Learning (viz [Správa modelů Azure Machine Learning](model-management-overview.md) pro Přehled).

## <a name="next-steps"></a>Další postup

* Pro pokyny o tom, jak nasadit webové služby ke spuštění na místním počítači nebo clusteru pokračovat na [nasazení modelu strojového učení jako webovou službu](model-management-service-deploy.md).
* Zkuste použijte jeden z mnoha ukázek v galerii.
