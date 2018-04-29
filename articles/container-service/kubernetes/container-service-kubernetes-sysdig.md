---
title: Monitorování Azure Kubernetes cluster - Sysdig
description: Monitorování Kubernetes clusteru v Azure Container Service pomocí Sysdig
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 275e71ce054b83c16b9f9cbfe621c6a7e31f79c6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="monitor-an-azure-container-service-kubernetes-cluster-using-sysdig"></a>Monitorování clusteru Azure Container Service Kubernetes pomocí Sysdig

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>Požadavky
Tento návod předpokládá, že máte [vytvořit Kubernetes clusteru Azure Container Service pomocí](container-service-kubernetes-walkthrough.md).

Předpokládá také, že máte azure cli a kubectl nástroje nainstalované.

Můžete otestovat, pokud máte `az` nainstalovaná, spuštěním nástroje:

```console
$ az --version
```

Pokud nemáte `az` nástroj nainstalovali, jsou k dispozici pokyny [zde](https://github.com/azure/azure-cli#installation).

Můžete otestovat, pokud máte `kubectl` nainstalovaná, spuštěním nástroje:

```console
$ kubectl version
```

Pokud nemáte `kubectl` nainstalován, můžete spustit:

```console
$ az acs kubernetes install-cli
```

## <a name="sysdig"></a>Sysdig
Sysdig je, že externí monitorování jako služba společnosti, které můžete sledovat kontejnery v clusteru Kubernetes běžící v Azure. Použití Sysdig vyžaduje aktivní Sysdig účet.
Můžete si zaregistrovat účet jejich [lokality](https://app.sysdigcloud.com).

Po přihlášení na web cloudu Sysdig klikněte na uživatelské jméno. Zobrazí se stránka, na které byste měli najít svůj „přístupový klíč“. 

![Klíč rozhraní API služby Sysdig](./media/container-service-kubernetes-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>Instalace agentů Sysdig k Kubernetes
Ke sledování kontejnerů, Sysdig spustí nějaký proces na každém počítači pomocí Kubernetes `DaemonSet`.
DaemonSets jsou objekty Kubernetes rozhraní API, které spustit jednu instanci kontejner na počítač.
Jsou ideální pro instalaci nástroje, například Sysdig agent monitorování.

Pokud chcete nainstalovat Sysdig daemonset, musí nejdřív Stáhnout [šablony](https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml) z sysdig. Uložte tento soubor jako `sysdig-daemonset.yaml`.

Na Linuxu a OS X můžete spustit:

```console
$ curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

V prostředí PowerShell:

```console
$ Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

Tento soubor vložit přístupový klíč, který jste získali z vašeho účtu Sysdig dále upravte.

Nakonec vytvořte DaemonSet:

```console
$ kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>Zobrazení monitorování
Jakmile nainstalovaná a spuštěná, by měl agenty čerpadla data zpět do Sysdig.  Přejděte zpět [sysdig řídicí panel](https://app.sysdigcloud.com) a měli byste vidět informace o kontejnerů.

Můžete taky nainstalovat specifické Kubernetes řídicí panely prostřednictvím [Průvodce novým řídicím panelu](https://app.sysdigcloud.com/#/dashboards/new).
