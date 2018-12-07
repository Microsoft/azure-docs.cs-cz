---
title: (NEPOUŽÍVANÉ) Monitorování clusteru Kubernetes v Azure – služby Sysdig
description: Monitorování clusteru Kubernetes ve službě Azure Container Service pomocí služby Sysdig
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 4aef241e2c86e4016c3c468fcdcfdfc620fc7aa9
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52998731"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-using-sysdig"></a>(NEPOUŽÍVANÉ) Monitorování clusteru služby Azure Container Service Kubernetes pomocí služby Sysdig

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Požadavky
Tento názorný průvodce předpokládá, že máte [vytvořit cluster Kubernetes pomocí služby Azure Container Service](container-service-kubernetes-walkthrough.md).

Dále předpokládá, že máte nainstalované azure nástroje pro rozhraní příkazového řádku a kubectl.

Můžete otestovat, pokud máte `az` nástroj pro instalaci spuštěním:

```console
$ az --version
```

Pokud nemáte k dispozici `az` nástroj nainstalovali, jsou k dispozici pokyny [tady](https://github.com/azure/azure-cli#installation).

Můžete otestovat, pokud máte `kubectl` nástroj pro instalaci spuštěním:

```console
$ kubectl version
```

Pokud nemáte `kubectl` nainstalované, můžete spustit:

```console
$ az acs kubernetes install-cli
```

## <a name="sysdig"></a>Sysdig
Sysdig je externí monitorování jako služba společnost, která může monitorovat kontejnery v clusteru Kubernetes v Azure. Pomocí služby Sysdig vyžaduje aktivní účet služby Sysdig.
Můžete se přihlásit k účtu jejich [lokality](https://app.sysdigcloud.com).

Po přihlášení na web cloudu Sysdig klikněte na uživatelské jméno. Zobrazí se stránka, na které byste měli najít svůj „přístupový klíč“. 

![Klíč rozhraní API služby Sysdig](./media/container-service-kubernetes-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>Instalace agentů služby Sysdig do Kubernetes
Pro monitorování kontejnerů služby Sysdig spustí proces na každém počítači s využitím Kubernetes `DaemonSet`.
DaemonSets jsou objekty rozhraní Kubernetes API spustit jednu instanci kontejneru na počítač.
Jsou ideální pro instalace nástrojů, jako je agenta monitorování služby Sysdig.

Chcete-li nainstalovat služby Sysdig daemonset, byste nejprve stáhnout [šablony](https://github.com/draios/sysdig-cloud-scripts/tree/master/agent_deploy/kubernetes) ze služby sysdig. Uložte tento soubor jako `sysdig-daemonset.yaml`.

V Linuxu a OS X můžete spustit:

```console
$ curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

V prostředí PowerShell:

```console
$ Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

Potom upravte tento soubor k vložení svůj přístupový klíč, který jste získali z vašeho účtu služby Sysdig.

Nakonec vytvořte DaemonSet:

```console
$ kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>Zobrazení monitorování
Jakmile nainstalovaná a spuštěná, by měl agenty pump data zpět do služby Sysdig.  Přejděte zpět [řídicího panelu služby sysdig](https://app.sysdigcloud.com) a zobrazí se informace o kontejnerech.

Můžete také nainstalovat řídicí panely specifické pro Kubernetes prostřednictvím [Průvodce novým řídicím panelem](https://app.sysdigcloud.com/#/dashboards/new).
