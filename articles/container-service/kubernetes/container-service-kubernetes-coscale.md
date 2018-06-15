---
title: Monitorování clusteru služby Azure Kubernetes s CoScale
description: Monitorování Kubernetes cluster Azure Container Service pomocí CoScale
services: container-service
author: fryckbos
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 16580307193bbb7eb9b401eb1b14356e8589d6e2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32162785"
---
# <a name="monitor-an-azure-container-service-kubernetes-cluster-with-coscale"></a>Monitorování clusteru Azure Container Service Kubernetes s CoScale

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

V tomto článku jsme ukazují, jak nasadit [CoScale](https://www.coscale.com/) agenta ke sledování všech uzlů a kontejnery v clusteru Kubernetes v Azure Container Service. Účet s CoScale musíte pro tuto konfiguraci. 


## <a name="about-coscale"></a>O CoScale 

CoScale je monitorování platforma, která shromažďuje metriky a události z všechny kontejnery v několik platforem orchestration. CoScale nabízí úplné zásobníku monitorování pro Kubernetes prostředí. Poskytuje vizualizace a analýzy pro všechny vrstvy v zásobníku: operačního systému, Kubernetes, Docker a aplikacemi spuštěnými ve kontejnerů. CoScale nabízí několik předdefinovaných monitorování řídicí panely a má detekce anomálií předdefinované umožňující operátory a vývojářům rychlého hledání problémy infrastruktury a aplikace.

![CoScale uživatelského rozhraní](./media/container-service-kubernetes-coscale/coscale.png)

Jak je znázorněno v tomto článku, můžete nainstalovat agenty na clusteru s podporou Kubernetes spuštění CoScale jako řešení SaaS. Pokud chcete, aby vaše data na místě, CoScale je také k dispozici pro místní instalaci.


## <a name="prerequisites"></a>Požadavky

Je nutné nejprve [vytvořit účet CoScale](https://www.coscale.com/free-trial).

Tento návod předpokládá, že máte [vytvořit Kubernetes clusteru Azure Container Service pomocí](container-service-kubernetes-walkthrough.md).

Předpokládá také, abyste měli `az` rozhraní příkazového řádku Azure a `kubectl` nástroje nainstalované.

Můžete otestovat, pokud máte `az` nainstalovaná, spuštěním nástroje:

```azurecli
az --version
```

Pokud nemáte `az` nástroj nainstalovali, jsou k dispozici pokyny [zde](/cli/azure/install-azure-cli).

Můžete otestovat, pokud máte `kubectl` nainstalovaná, spuštěním nástroje:

```bash
kubectl version
```

Pokud nemáte `kubectl` nainstalován, můžete spustit:

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>Instalace agenta CoScale s DaemonSet
[DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) používají Kubernetes spustit jednu instanci kontejner na jednotlivých hostitelích v clusteru.
Jsou ideální pro spuštění monitorování agentů, jako je například CoScale agenta.

Po přihlášení do CoScale, přejděte na [agent stránka](https://app.coscale.com/) k instalaci agentů CoScale v clusteru pomocí DaemonSet. Rozhraní CoScale poskytuje informací konfigurace kroky k vytvoření agenta a začít monitorovat kompletní Kubernetes clusteru.

![Konfigurace agenta coScale](./media/container-service-kubernetes-coscale/installation.png)

Spuštění agenta v clusteru, spusťte zadaný příkaz:

![Spuštění agenta CoScale](./media/container-service-kubernetes-coscale/agent_script.png)

A to je vše! Jakmile agenti jsou spuštěná, měli byste vidět data v konzole za pár minut. Navštivte [agent stránka](https://app.coscale.com/) souhrn clusteru najdete provést další kroky konfigurace a zobrazit řídicí panely, jako **Kubernetes clusteru přehled**.

![Přehled Kubernetes clusteru](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

CoScale agenta je automaticky nasadit na nové počítače v clusteru. Aktualizace agenta automaticky, když je vydána nová verze.


## <a name="next-steps"></a>Další postup

Najdete v článku [CoScale dokumentace](http://docs.coscale.com/) a [blog](https://www.coscale.com/blog) Další informace o CoScale sledování řešení. 

