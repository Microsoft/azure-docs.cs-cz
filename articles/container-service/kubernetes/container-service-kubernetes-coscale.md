---
title: (ZASTARALÉ) Monitorování clusteru Azure Kubernetes pomocí CoScale
description: Monitorování clusteru Kubernetes ve službě Azure Container Service pomocí funkce CoScale
author: fryckbos
ms.service: container-service
ms.topic: conceptual
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: f1d0ca1ffc2e7a3d645ac5acbaafdf45f85550be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76271098"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-with-coscale"></a>(ZASTARALÉ) Monitorování clusteru Kubernetes služby Azure Container Service pomocí coscale

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

V tomto článku vám ukážeme, jak nasadit [agenta CoScale](https://web.archive.org/web/20180317071550/https://www.coscale.com/) pro monitorování všech uzlů a kontejnerů v clusteru Kubernetes ve službě Azure Container Service. Pro tuto konfiguraci potřebujete účet s CoScale. 


## <a name="about-coscale"></a>O společnosti Coscale 

CoScale je monitorovací platforma, která shromažďuje metriky a události ze všech kontejnerů v několika platformách orchestrace. CoScale nabízí monitorování celého zásobníku pro prostředí Kubernetes. Poskytuje vizualizace a analýzy pro všechny vrstvy v zásobníku: operační ho, Kubernetes, Docker a aplikace spuštěné uvnitř kontejnerů. CoScale nabízí několik integrovaných řídicích panelů monitorování a má vestavěnou detekci anomálií, která umožňuje operátorům a vývojářům rychle najít problémy s infrastrukturou a aplikacemi.

![CoScale UI](./media/container-service-kubernetes-coscale/coscale.png)

Jak je znázorněno v tomto článku, můžete nainstalovat agenty v clusteru Kubernetes a spustit CoScale jako řešení SaaS. Pokud chcete zachovat data na místě, CoScale je také k dispozici pro místní instalaci.


## <a name="prerequisites"></a>Požadavky

Nejprve je třeba [vytvořit účet CoScale](https://web.archive.org/web/20170507123133/https://www.coscale.com/free-trial).

Tento návod předpokládá, že jste [vytvořili cluster Kubernetes pomocí služby Azure Container Service](container-service-kubernetes-walkthrough.md).

Také předpokládá, že máte `az` nainstalované `kubectl` azure cli a nástroje.

Můžete otestovat, zda `az` máte nástroj nainstalován spuštěním:

```azurecli
az --version
```

Pokud nástroj nemáte nainstalovaný, `az` jsou [zde](/cli/azure/install-azure-cli)pokyny .

Můžete otestovat, zda `kubectl` máte nástroj nainstalován spuštěním:

```bash
kubectl version
```

Pokud nemáte `kubectl` nainstalovaný, můžete spustit:

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>Instalace agenta CoScale s daemonsetem
[DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) jsou používány Kubernetes spustit jednu instanci kontejneru na každém hostiteli v clusteru.
Jsou ideální pro spuštění agentů monitorování, jako je například agent CoScale.

Po přihlášení do CoScale přejděte na [stránku agenta](https://app.coscale.com/) a nainstalujte agenty CoScale do clusteru pomocí sady DaemonSet. CoScale UI poskytuje řízené kroky konfigurace k vytvoření agenta a začít sledovat celý cluster Kubernetes.

![Konfigurace agenta CoScale](./media/container-service-kubernetes-coscale/installation.png)

Chcete-li spustit agenta v clusteru, spusťte dodaný příkaz:

![Spuštění agenta CoScale](./media/container-service-kubernetes-coscale/agent_script.png)

A to je vše! Jakmile jsou agenti v provozu, měli byste vidět data v konzole během několika minut. Navštivte [stránku agenta,](https://app.coscale.com/) kde najdete souhrn clusteru, proveďte další kroky konfigurace a řídicí panely, jako je **přehled clusteru Kubernetes**.

![Přehled clusteru Kubernetes](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

Agent CoScale se automaticky nasazuje na nové počítače v clusteru. Agent se aktualizuje automaticky při vydání nové verze.


## <a name="next-steps"></a>Další kroky

Další informace o monitorovacích řešeních CoScale najdete v dokumentaci a [blogu](https://web.archive.org/web/20170501021344/http://www.coscale.com:80/blog) [CoScale.](https://web.archive.org/web/20180415164304/http://docs.coscale.com:80/) 

