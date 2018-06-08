---
title: Azure Machine Learning Model správy nastavení a konfigurace | Microsoft Docs
description: Tento dokument popisuje kroky a koncepty součástí nastavení a konfiguraci modelu správy v Azure Machine Learning.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 12/6/2017
ms.openlocfilehash: bf96a6986abe858074decaaecfe38b3807822aab
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830237"
---
# <a name="model-management-setup"></a>Nastavení modelu správce

Tento dokument vám pomůže začít s pomocí správy modelu Azure ML nasazovat a spravovat vaše strojového učení modely jako webové služby. 

Pomocí správy modelu Azure ML můžete efektivně nasadit a spravovat modelů Machine Learning, které jsou vytvořeny s počtem rozhraní, včetně SparkML, Keras, TensorFlow, kognitivní nástrojů Microsoft nebo Python. 

Na konci tohoto dokumentu musí mít možnost získat přehledné prostředí správy modelu nastavené a připravené k nasazení vaší modelů strojového učení.

## <a name="what-you-need-to-get-started"></a>Co potřebujete, abyste mohli začít
K plnému využití z této příručky, byste měli mít přispěvatele přístup k předplatné Azure nebo skupinu prostředků, které vaše modely, které můžete nasadit.
Rozhraní příkazového řádku obsahuje předem nainstalovaná na Azure Machine Learning Workbench a na [Azure DSVMs](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview).

## <a name="using-the-cli"></a>Pomocí rozhraní příkazového řádku
Chcete-li používat rozhraní příkazového řádku (CLIs) z nástroje Workbench, klikněte na tlačítko **soubor** -> **spusťte příkazový řádek**. 

Na datové vědě virtuální počítač, připojte a otevřete okno příkazového řádku. Typ `az ml -h` zobrazíte možnosti. Další informace o příkazech, použijte příznak – Nápověda.

Na všechny ostatní systémy je třeba nainstalovat CLIs.

### <a name="installing-or-updating-on-windows"></a>Instalace (nebo aktualizace) v systému Windows

Nainstalujte Python z https://www.python.org/. Ujistěte se, že jste vybrali k instalaci pip.

Otevřete příkazový řádek pomocí spustit jako správce a spusťte následující příkazy:

```cmd
pip install -r https://aka.ms/az-ml-o16n-cli-requirements-file
```

### <a name="installing-or-updating-on-linux"></a>Instalace (nebo aktualizace) v systému Linux
Spusťte následující příkaz z příkazového řádku a postupujte podle pokynů:

```bash
sudo -i
pip install -r https://aka.ms/az-ml-o16n-cli-requirements-file
```

### <a name="configuring-docker-on-linux"></a>Konfigurace Docker v systému Linux
Chcete-li nakonfigurovat Docker používají jiné kořenové uživatelé v systému Linux, postupujte podle pokynů tady: [po instalaci kroky pro Linux](https://docs.docker.com/engine/installation/linux/linux-postinstall/)

>[!NOTE]
> Na DSVM Linux můžete spustit skript níže nakonfigurujte Docker správně. **Mějte na paměti, odhlaste se a přihlaste se zpět po spuštění skriptu.**
>```
>sudo /opt/microsoft/azureml/initial_setup.sh
>```

## <a name="deploying-your-model"></a>Nasazení modelu
Nasadit modely jako webové služby pomocí rozhraní příkazového řádku. Webové služby se dá nasadit místně nebo do clusteru.

Spustit s místní nasazení, ověřte, že model a kód fungovat, potom nasadit do clusteru s podporou pro produkční škálování použití.

Pokud chcete spustit, musíte nastavit prostředí pro nasazení. Nastavení prostředí je čas úlohy. Po dokončení instalace můžete opakovaně použít prostředí pro následné nasazení. Následující části Další podrobnosti.

Po dokončení instalace prostředí:
- Zobrazí se výzva k přihlášení k Azure. K přihlášení, použijte webový prohlížeč otevře se stránka https://aka.ms/devicelogin a zadejte poskytnutý kód pro ověření.
- Během procesu ověřování budete vyzváni k účtu k ověření. Důležité: Vyberte účet, který má platné předplatné Azure a dostatečná oprávnění k vytváření prostředků v účtu. Po protokolu v dokončení se zobrazí informace o vašem předplatném a zobrazí se výzva, jestli chcete pokračovat s vybraný účet.

### <a name="environment-setup"></a>Nastavení prostředí
K zahájení procesu instalace, musíte zaregistrovat několik poskytovatelů prostředí zadáním následujících příkazů:

```azurecli
az provider register -n Microsoft.MachineLearningCompute
az provider register -n Microsoft.ContainerRegistry
az provider register -n Microsoft.ContainerService
```
#### <a name="local-deployment"></a>Místní nasazení
Nasaďte a otestujte webovou službu v místním počítači, nastavte místní prostředí pomocí následujícího příkazu. Název skupiny prostředků je volitelné.

```azurecli
az ml env setup -l [Azure Region, e.g. eastus2] -n [your environment name] [-g [existing resource group]]
```
>[!NOTE] 
>Místní web nasazení služby vyžaduje, abyste instalaci Docker v místním počítači. 
>

Příkaz místní prostředí instalační program vytvoří následující prostředky ve vašem předplatném:
- Prostředek skupiny (Pokud není zadaná nebo pokud se zadaným názvem neexistuje.)
- účet úložiště
- Azure kontejneru registru (ACR)
- Účet statistiky aplikace

Po úspěšném dokončení instalace nastavte prostředí, který se má použít, pomocí následujícího příkazu:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

#### <a name="cluster-deployment"></a>Nasazení clusteru
Použijte pro špičkové produkčních scénářích nasazení clusteru. Nastavuje clusteru služby ACS s Kubernetes jako orchestrator. Cluster služby ACS lze škálovat pro zpracování větší propustnost pro vaše volání webové služby.

Chcete-li nasadit webové služby v produkčním prostředí, nejprve nastavte prostředí pomocí následujícího příkazu:

```azurecli
az ml env setup --cluster -n [your environment name] -l [Azure region e.g. eastus2] [-g [resource group]]
```

Příkaz clusteru prostředí instalační program vytvoří následující prostředky ve vašem předplatném:
- Prostředek skupiny (Pokud není zadaná nebo pokud se zadaným názvem neexistuje.)
- účet úložiště
- Azure kontejneru registru (ACR)
- Kubernetes nasazení v clusteru Azure Container Service (ACS)
- Účet statistiky aplikace

>[!IMPORTANT]
> Aby bylo možné úspěšně vytvořit prostředí clusteru, musíte mít přístup Přispěvatel na předplatné Azure nebo skupinu prostředků.

Rychle se vytvoří skupinu prostředků, účet úložiště a ACR. Nasazení služby ACS může trvat až 20 minut. 

Pokud chcete zkontrolovat stav zřizování probíhající clusteru, použijte následující příkaz:

```azurecli
az ml env show -n [environment name] -g [resource group]
```

Po dokončení instalace budete muset nastavit prostředí, který se má použít pro toto nasazení. Použijte následující příkaz:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

>[!NOTE] 
> Po vytvoření prostředí pro následné nasazení, stačí použít příkaz set výše pro další použití.
>

### <a name="create-a-model-management-account"></a>Vytvořit účet Model správy
Účet pro správu modelu je vyžadována pro nasazení modelů. Je potřeba udělat to jednou za předplatné a můžete znovu použít stejný účet ve více nasazení.

Pokud chcete vytvořit nový účet, použijte následující příkaz:

```azurecli
az ml account modelmanagement create -l [Azure region, e.g. eastus2] -n [your account name] -g [resource group name] --sku-instances [number of instances, e.g. 1] --sku-name [Pricing tier for example S1]
```

Chcete-li použít existující účet, použijte následující příkaz:
```azurecli
az ml account modelmanagement set -n [your account name] -g [resource group it was created in]
```

V důsledku tohoto procesu je prostředí připravené a účet správy model byl vytvořen a zajistit tak funkce potřebné ke správě a nasazení modelů Machine Learning (najdete v části [Azure Machine Learning modelu Management](model-management-overview.md) pro Přehled).

## <a name="next-steps"></a>Další postup

* Pro pokyny o tom, jak nasadit webové služby ke spuštění v místním počítači nebo clusteru pokračovat na [nasazení modelu strojového učení jako webovou službu](model-management-service-deploy.md).
* Použijte jeden z mnoha ukázek v galerii.
