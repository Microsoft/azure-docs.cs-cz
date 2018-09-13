---
title: Nastavení správy modelů Azure Machine Learning a konfigurace | Dokumentace Microsoftu
description: Tento dokument popisuje kroky a koncepty součástí nastavení a konfigurace správy modelů ve službě Azure Machine Learning.
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 08/29/2017
ms.openlocfilehash: 883e3d2c5945a38c8fbca5c9f0f5e8a1e4093be1
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35643568"
---
# <a name="model-management-setup"></a>Nastavení správy modelů

## <a name="overview"></a>Přehled
Tento dokument vám pomůže začít s pomocí správy modelů ve službě Azure ML k nasazení a správě vašich modelů jako webové služby machine learning. 

Pomocí správy modelů ve službě Azure ML, můžete efektivně nasadit a spravovat modely Machine Learning, které se vytvářejí pomocí několika architektur, včetně ve SparkML, Keras, TensorFlow, Microsoft Cognitive Toolkit nebo Python. 

Na konci tohoto dokumentu vám měl mít svému prostředí pro správu modelu nastavené a připravené k nasazení modelů strojového učení.

## <a name="what-you-need-to-get-started"></a>Co potřebujete, abyste mohli začít
Abyste využili na maximum Tato příručka, byste měli mít přístup vlastníka k předplatnému Azure, kterou můžete nasadit vlastní modely a.
Rozhraní příkazového řádku je předem nainstalovaný na aplikaci Azure Machine Learning Workbench a v [Azure datové](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview).

## <a name="using-the-cli"></a>Pomocí rozhraní příkazového řádku
Pokud chcete používat rozhraní příkazového řádku (rozhraní příkazového řádku) z aplikace Workbench, klikněte na tlačítko **souboru** -] **otevřete rozhraní příkazového řádku**. 

Na virtuální počítač pro datové vědy, připojení a otevřete okno příkazového řádku. Typ `az ml -h` zobrazíte možnosti. Podrobné informace o příkazů použít příznak--help.

V ostatních systémech je třeba nainstalovat rozhraní příkazového řádku.

### <a name="installing-or-updating-on-windows"></a>Instalaci (nebo aktualizaci) ve Windows

Instalace Pythonu z https://www.python.org/. Ujistěte se, že jste vybrali k instalaci pip.

Otevřete příkazový řádek pomocí spustit jako správce a spusťte následující příkazy:

```cmd
pip install azure-cli
pip install azure-cli-ml
```
 
>[!NOTE]
>Pokud máte starší verzi, odinstalujte ji nejdřív pomocí následujícího příkazu:
>

```cmd
pip uninstall azure-cli-ml
```

### <a name="installing-or-updating-on-linux"></a>Instalace (nebo aktualizace) v Linuxu
Spuštěním následujícího příkazu z příkazového řádku a postupujte podle pokynů:

```bash
sudo -i
pip install azure-cli
pip install azure-cli-ml
```

Po dokončení websites, spusťte následující příkaz:

```bash
sudo /opt/microsoft/azureml/initial_setup.sh
```

>[!NOTE]
>Odhlaste se a přihlaste se zpět k relaci SSH, aby se změny projevily.
>Předchozí příkazy můžete použít k aktualizaci starší verze rozhraní v datové VĚDY.
>

## <a name="deploying-your-model"></a>Nasazení modelu
Rozhraní příkazového řádku použijte k nasazení modelů jako webové služby. Webové služby je možné nasadit místně nebo do clusteru.

Začněte s místní nasazení, ověřte, že váš model a kód fungovat, potom nasadit do clusteru pro použití v produkčním prostředí škálování.

Pokud chcete začít, musíte nastavit prostředí pro nasazení. Nastavení prostředí je čas úkolu. Po dokončení instalace můžete znovu použít prostředí pro další nasazení. Následující části Další podrobnosti.

Po dokončení instalace prostředí:
- Zobrazí se výzva k přihlášení do Azure. Pro přihlášení, použijte webový prohlížeč a otevřete stránku https://aka.ms/devicelogin a zadání poskytnutého kódu k ověření.
- Během procesu ověřování budete vyzváni k ověření pomocí účtu. Vyberte účet, který má platné předplatné Azure a dostatečná oprávnění k vytváření prostředků v účtu. důležité: - Po li přihlásit dokončení se zobrazí informace o vašem předplatném a zobrazí se výzva, jestli chcete pokračovat vybraný účet.

### <a name="environment-setup"></a>Nastavení prostředí
K zahájení procesu instalace, budete muset zaregistrovat poskytovatele prostředí tak, že zadáte následující příkaz:

```azurecli
az provider register -n Microsoft.MachineLearningCompute
```

#### <a name="local-deployment"></a>Místní nasazení
K nasazení a testování webové služby na místním počítači, nastavte místní prostředí pomocí následujícího příkazu:

```azurecli
az ml env setup -l [location of Azure Region, e.g. eastus2] -n [your environment name] [-g [existing resource group]]
```
>[!NOTE] 
>Vyžaduje nasazení místní webové služby vaší k instalaci Dockeru na místním počítači. 
>

Příkaz místní prostředí instalační program vytvoří následující prostředky ve vašem předplatném:
- Skupinu prostředků (Pokud není zadáno)
- Účet úložiště
- Služby Azure Container Registry (ACR)
- Application Insights

Po úspěšném dokončení instalace, nastavte prostředí k použití pomocí následujícího příkazu:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

#### <a name="cluster-deployment"></a>Nasazení clusteru
Nasazení clusteru použijte pro zajištění vysoce škálovatelné produkční scénáře. Nastavuje clusteru ACS s Kubernetes jako orchestrátor. Cluster služby ACS lze škálovat na zpracovávat větší propustnost pro vaše volání webové služby.

Nasazení webové služby do produkčního prostředí, nejprve nastavit prostředí pomocí následujícího příkazu:

```azurecli
az ml env setup -c --name [your environment name] --location [Azure region e.g. eastus2] [-g [resource group]]
```

Příkaz clusteru prostředí instalační program vytvoří následující prostředky ve vašem předplatném:
- Skupinu prostředků (Pokud není zadáno)
- Účet úložiště
- Služby Azure Container Registry (ACR)
- Nasazení Kubernetes v clusteru Azure Container Service (ACS)
- Application Insights

Rychle se vytvoří skupina prostředků, účet úložiště a služby ACR. Nasazení služby ACS může trvat až 20 minut. 

Po dokončení instalace je nutné nastavit prostředí pro toto nasazení. Použijte následující příkaz:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

>[!NOTE] 
> Po vytvoření prostředí pro další nasazení, stačí ji znovu použít pomocí výše uvedeného příkazu set.
>

>[!NOTE] 
>Pokud chcete vytvořit koncový bod HTTPS, zadejte certifikát SSL při vytváření clusteru pomocí možnosti--název certifikátu a--pem certifikátu v instalačním programu env az ml. Tím se nastaví clusteru pro požadavků na https zabezpečená pomocí zadaného certifikátu. Po dokončení instalace, vytvoření záznamu CNAME DNS, který odkazuje na plně kvalifikovaný název domény clusteru.

### <a name="create-an-account"></a>Vytvořit účet
Účet je vyžadován pro nasazení modelů. Je potřeba provést jednou na jeden účet a můžete znovu použít stejný účet ve více nasazeních.

Chcete-li vytvořit nový účet, použijte následující příkaz:

```azurecli
az ml account modelmanagement create -l [Azure region, e.g. eastus2] -n [your account name] -g [resource group name] --sku-instances [number of instances, e.g. 1] --sku-name [Pricing tier for example S1]
```

Pokud chcete použít existující účet, použijte následující příkaz:
```azurecli
az ml account modelmanagement set -n [your account name] -g [resource group it was created in]
```

### <a name="deploy-your-model"></a>Nasazení modelu
Nyní jste připraveni k uložené model nasadit jako webovou službu. 

```azurecli
az ml service create realtime --model-file [model file/folder path] -f [scoring file e.g. score.py] -n [your service name] -s [schema file e.g. service_schema.json] -r [runtime for the Docker container e.g. spark-py or python] -c [conda dependencies file for additional python packages]
```

### <a name="next-steps"></a>Další kroky
Zkuste použijte jeden z mnoha ukázek v galerii.
