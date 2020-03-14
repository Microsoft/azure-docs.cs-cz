---
title: ZASTARALÉ Monitorování clusteru Azure Kubernetes – služby Sysdig
description: Monitorování clusteru Kubernetes v Azure Container Service pomocí služby Sysdig
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 68136d5b9ec16c822cb62e4fee85b8ace9b1899a
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371095"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-using-sysdig"></a>ZASTARALÉ Monitorování clusteru Azure Container Service Kubernetes pomocí služby Sysdig

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Požadavky
Tento návod předpokládá, že jste [vytvořili cluster Kubernetes pomocí Azure Container Service](container-service-kubernetes-walkthrough.md).

Také předpokládá, že máte nainstalované nástroje Azure CLI a kubectl Tools.

Můžete otestovat, jestli máte nainstalovaný nástroj `az`, a to spuštěním:

```azurecli
az --version
```

Pokud nemáte nainstalovaný nástroj `az`, [tady](https://github.com/azure/azure-cli#installation)najdete pokyny.

Můžete otestovat, jestli máte nainstalovaný nástroj `kubectl`, a to spuštěním:

```console
kubectl version
```

Pokud nemáte nainstalované `kubectl`, můžete spustit:

```azurecli
az acs kubernetes install-cli
```

## <a name="sysdig"></a>Sysdig
Služby Sysdig je externí monitorování jako společnost služby, která může monitorovat kontejnery v clusteru Kubernetes běžícím v Azure. Použití služby Sysdig vyžaduje aktivní účet služby Sysdig.
Můžete si zaregistrovat účet na svém [webu](https://app.sysdigcloud.com).

Po přihlášení na web cloudu Sysdig klikněte na uživatelské jméno. Zobrazí se stránka, na které byste měli najít svůj „přístupový klíč“. 

![Klíč rozhraní API služby Sysdig](./media/container-service-kubernetes-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>Instalace agentů služby Sysdig do Kubernetes
Pro monitorování vašich kontejnerů služby Sysdig spustí proces na každém počítači, který používá Kubernetes `DaemonSet`.
DaemonSets jsou objekty rozhraní API Kubernetes, které spouštějí jednu instanci kontejneru v jednom počítači.
Jsou ideální pro instalaci nástrojů, jako je služby Sysdig Monitoring Agent.

Pokud chcete nainstalovat služby Sysdig daemonset, měli byste nejdřív stáhnout [šablonu](https://github.com/draios/sysdig-cloud-scripts/tree/master/agent_deploy/kubernetes) z služby Sysdig. Uložte tento soubor jako `sysdig-daemonset.yaml`.

V systémech Linux a OS X můžete spustit:

```console
curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

V PowerShellu:

```powershell
Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

Dalším úpravou tohoto souboru vložte přístupový klíč, který jste získali z účtu služby Sysdig.

Nakonec vytvořte DaemonSet:

```console
kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>Zobrazení monitorování
Po nainstalování a spuštění by agenti měli data pumpa zpět do služby Sysdig.  Vraťte se na [řídicí panel služby Sysdig](https://app.sysdigcloud.com) a měli byste vidět informace o kontejnerech.

Řídicí panely specifické pro Kubernetes můžete nainstalovat také pomocí [Průvodce novým řídicím panelem](https://app.sysdigcloud.com/#/dashboards/new).
