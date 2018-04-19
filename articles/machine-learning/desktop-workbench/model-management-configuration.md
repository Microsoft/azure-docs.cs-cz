---
title: "Azure Machine Learning Model správy nastavení a konfigurace | Microsoft Docs"
description: "Tento dokument popisuje kroky a koncepty součástí nastavení a konfiguraci modelu správy v Azure Machine Learning."
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 08/29/2017
ms.openlocfilehash: 45ddd4dc6fb5559c020706e2784158b1319f9b52
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2018
---
# <a name="model-management-setup"></a>Nastavení modelu správce

## <a name="overview"></a>Přehled
Tento dokument vám pomůže začít s pomocí správy modelu Azure ML nasazovat a spravovat vaše strojového učení modely jako webové služby. 

Pomocí správy modelu Azure ML můžete efektivně nasadit a spravovat modelů Machine Learning, které jsou vytvořeny s počtem rozhraní, včetně SparkML, Keras, TensorFlow, kognitivní nástrojů Microsoft nebo Python. 

Na konci tohoto dokumentu musí mít možnost získat přehledné prostředí správy modelu nastavené a připravené k nasazení vaší modelů strojového učení.

## <a name="what-you-need-to-get-started"></a>Co potřebujete, abyste mohli začít
K Získejte maximum z této příručky, byste měli mít vlastníka přístup k předplatnému Azure, které vaše modely, které můžete nasadit.
Rozhraní příkazového řádku obsahuje předem nainstalovaná na Azure Machine Learning Workbench a na [Azure DSVMs](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview).

## <a name="using-the-cli"></a>Pomocí rozhraní příkazového řádku
Chcete-li používat rozhraní příkazového řádku (CLIs) z nástroje Workbench, klikněte na tlačítko **soubor** -] **otevřete rozhraní příkazového řádku**. 

Na datové vědě virtuální počítač, připojte a otevřete okno příkazového řádku. Typ `az ml -h` zobrazíte možnosti. Další informace o příkazech, použijte příznak – Nápověda.

Na všechny ostatní systémy je třeba nainstalovat CLIs.

### <a name="installing-or-updating-on-windows"></a>Instalace (nebo aktualizace) v systému Windows

Nainstalujte Python z https://www.python.org/. Ujistěte se, že jste vybrali k instalaci pip.

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

### <a name="installing-or-updating-on-linux"></a>Instalace (nebo aktualizace) v systému Linux
Spusťte následující příkaz z příkazového řádku a postupujte podle pokynů:

```bash
sudo -i
pip install azure-cli
pip install azure-cli-ml
```

Po dokončení instalační spusťte následující příkaz:

```bash
sudo /opt/microsoft/azureml/initial_setup.sh
```

>[!NOTE]
>Odhlaste se a přihlaste se zpět do relace SSH změny se projeví.
>Předchozích příkazů můžete aktualizovat dřívější verzi CLIs na DSVM.
>

## <a name="deploying-your-model"></a>Nasazení modelu
Pomocí CLIs nasaďte modely jako webové služby. Webové služby se dá nasadit místně nebo do clusteru.

Spustit s místní nasazení, ověřte, že model a kód fungovat, potom nasadit do clusteru s podporou pro produkční škálování použití.

Pokud chcete spustit, musíte nastavit prostředí pro nasazení. Nastavení prostředí je čas úlohy. Po dokončení instalace můžete opakovaně použít prostředí pro následné nasazení. Následující části Další podrobnosti.

Po dokončení instalace prostředí:
- Zobrazí se výzva k přihlášení k Azure. K přihlášení použijte webový prohlížeč a otevřete stránku https://aka.ms/devicelogin zadejte poskytnutý kód pro ověření.
- Během procesu ověřování budete vyzváni k účtu k ověření. Důležité: Vyberte účet, který má platné předplatné Azure a dostatečná oprávnění k vytváření prostředků v účtu. - po protokolu v dokončení se zobrazí informace o vašem předplatném a zobrazí se výzva, jestli chcete pokračovat vybraný účet.

### <a name="environment-setup"></a>Nastavení prostředí
K zahájení procesu instalace, budete muset registraci poskytovatele prostředí tak, že zadáte následující příkaz:

```azurecli
az provider register -n Microsoft.MachineLearningCompute
```

#### <a name="local-deployment"></a>Místní nasazení
Nasaďte a otestujte webovou službu v místním počítači, nastavte místní prostředí pomocí následujícího příkazu:

```azurecli
az ml env setup -l [location of Azure Region, e.g. eastus2] -n [your environment name] [-g [existing resource group]]
```
>[!NOTE] 
>Místní web nasazení služby vyžaduje, aby vaše instalace Docker v místním počítači. 
>

Příkaz místní prostředí instalační program vytvoří následující prostředky ve vašem předplatném:
- Skupiny prostředků (Pokud není zadáno)
- účet úložiště
- Azure kontejneru registru (ACR)
- Application Insights

Po úspěšném dokončení instalace nastavte prostředí, který se má použít, pomocí následujícího příkazu:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

#### <a name="cluster-deployment"></a>Nasazení clusteru
Použijte pro špičkové produkčních scénářích nasazení clusteru. Nastavuje clusteru služby ACS s Kubernetes jako orchestrator. Cluster služby ACS lze škálovat pro zpracování větší propustnost pro vaše volání webové služby.

Chcete-li nasadit webové služby v produkčním prostředí, nejprve nastavte prostředí pomocí následujícího příkazu:

```azurecli
az ml env setup -c --name [your environment name] --location [Azure region e.g. eastus2] [-g [resource group]]
```

Příkaz clusteru prostředí instalační program vytvoří následující prostředky ve vašem předplatném:
- Skupiny prostředků (Pokud není zadáno)
- účet úložiště
- Azure kontejneru registru (ACR)
- Kubernetes nasazení v clusteru Azure Container Service (ACS)
- Application Insights

Rychle se vytvoří skupinu prostředků, účet úložiště a ACR. Nasazení služby ACS může trvat až 20 minut. 

Po dokončení instalace budete muset nastavit prostředí, který se má použít pro toto nasazení. Použijte následující příkaz:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

>[!NOTE] 
> Po vytvoření prostředí pro následné nasazení, stačí použít příkaz set výše pro další použití.
>

>[!NOTE] 
>Pokud chcete vytvořit koncový bod HTTPS, zadejte certifikát SSL při vytváření clusteru pomocí--název certifikátu a--cert pem možnosti v instalačním programu env ml az. Toto nastaví clusteru tak, aby obsluhovat požadavky na protokol https, zabezpečené pomocí k zadanému certifikátu. Po dokončení instalace vytvořte záznam CNAME DNS, který odkazuje na plně kvalifikovaný název domény clusteru.

### <a name="create-an-account"></a>Vytvoření účtu
Účet je vyžadována pro nasazení modelů. Je potřeba udělat to jednou každý účet a můžete znovu použít stejný účet ve více nasazení.

Pokud chcete vytvořit nový účet, použijte následující příkaz:

```azurecli
az ml account modelmanagement create -l [Azure region, e.g. eastus2] -n [your account name] -g [resource group name] --sku-instances [number of instances, e.g. 1] --sku-name [Pricing tier for example S1]
```

Chcete-li použít existující účet, použijte následující příkaz:
```azurecli
az ml account modelmanagement set -n [your account name] -g [resource group it was created in]
```

### <a name="deploy-your-model"></a>Nasazení modelu
Nyní jste připraveni k nasazení uložené model jako webovou službu. 

```azurecli
az ml service create realtime --model-file [model file/folder path] -f [scoring file e.g. score.py] -n [your service name] -s [schema file e.g. service_schema.json] -r [runtime for the Docker container e.g. spark-py or python] -c [conda dependencies file for additional python packages]
```

### <a name="next-steps"></a>Další kroky
Použijte jeden z mnoha ukázek v galerii.
