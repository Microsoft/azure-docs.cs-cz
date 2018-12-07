---
title: (NEPOUŽÍVANÉ) Monitorování clusteru služby Azure Kubernetes pomocí služby CoScale
description: Monitorování clusteru Kubernetes ve službě Azure Container Service pomocí služby CoScale
services: container-service
author: fryckbos
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 895346447e33926dcaa5ca09302f35c9d6636ed9
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "53000221"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-with-coscale"></a>(NEPOUŽÍVANÉ) Monitorování clusteru služby Azure Container Service Kubernetes pomocí služby CoScale

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

V tomto článku ukážeme, jak nasadit [služby CoScale](https://web.archive.org/web/20180317071550/ https://www.coscale.com/) agenta monitorování všechny uzly tak kontejnery v clusteru Kubernetes ve službě Azure Container Service. Pro tuto konfiguraci potřebujete účet pomocí služby CoScale. 


## <a name="about-coscale"></a>O služby CoScale 

Služby coScale je platforma pro monitorování, která shromažďuje metriky a události ze všech kontejnerů na několika platformách Orchestrace. Služby coScale nabízí kompletní monitorování prostředí Kubernetes. Poskytuje vizualizace a analýzy pro všechny vrstvy v zásobníku: OS, Kubernetes, Dockerem a aplikace běžící uvnitř kontejnerů. Služby coScale nabízí několik řídicích panelů integrované monitorování a má integrovanou detekci anomálií umožňující Operátoři a vývojáři a rychle najít problémy, infrastruktury a aplikací.

![Uživatelské rozhraní služby coScale](./media/container-service-kubernetes-coscale/coscale.png)

Jak je znázorněno v tomto článku, můžete nainstalovat agenty na cluster Kubernetes pro spuštění služby CoScale jako řešení SaaS. Pokud chcete zachovat data na místě, je také k dispozici pro instalaci místní služby CoScale.


## <a name="prerequisites"></a>Požadavky

Je nutné nejprve [vytvořit účet služby CoScale](https://web.archive.org/web/20170507123133/ https://www.coscale.com/free-trial).

Tento názorný průvodce předpokládá, že máte [vytvořit cluster Kubernetes pomocí služby Azure Container Service](container-service-kubernetes-walkthrough.md).

Dále předpokládá, že máte `az` rozhraní příkazového řádku Azure a `kubectl` nainstalované nástroje.

Můžete otestovat, pokud máte `az` nástroj pro instalaci spuštěním:

```azurecli
az --version
```

Pokud nemáte k dispozici `az` nástroj nainstalovali, jsou k dispozici pokyny [tady](/cli/azure/install-azure-cli).

Můžete otestovat, pokud máte `kubectl` nástroj pro instalaci spuštěním:

```bash
kubectl version
```

Pokud nemáte `kubectl` nainstalované, můžete spustit:

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>Instalace agenta služby CoScale s DaemonSet
[DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) Kubernetes používají ke spuštění jedné instance kontejneru na každého hostitele v clusteru.
Jsou ideální pro provoz agentů monitorování, jako jsou služby CoScale agenta.

Po přihlášení do služby CoScale přejděte [agent stránka](https://app.coscale.com/) nainstalovat agenty služby CoScale ve vašem clusteru pomocí DaemonSet. Uživatelské rozhraní služby CoScale poskytuje s asistencí konfigurační kroky k vytvoření agenta a začít monitorovat kompletní cluster Kubernetes.

![Konfigurace agenta služby coScale](./media/container-service-kubernetes-coscale/installation.png)

Pokud chcete spustit agenta v clusteru, spusťte zadaný příkaz:

![Spustit agenta služby CoScale](./media/container-service-kubernetes-coscale/agent_script.png)

A to je vše! Po zprovoznění jsou agenti, měli byste vidět data v konzole za pár minut. Navštivte [agent stránka](https://app.coscale.com/) prohlédnout souhrnné informace o clusteru, provést další kroky konfigurace a zobrazit řídicí panely, jako **přehled clusteru Kubernetes**.

![Přehled clusteru Kubernetes](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

Agent služby CoScale se automaticky nasadí na nové počítače v clusteru. Aktualizace agenta automaticky, když je vydána nová verze.


## <a name="next-steps"></a>Další postup

Najdete v článku [služby CoScale dokumentaci](https://web.archive.org/web/20180415164304/ http://docs.coscale.com:80/) a [blogu](https://web.archive.org/web/20170501021344/ http://www.coscale.com:80/blog) Další informace o služby CoScale řešení monitorování. 

