---
title: (ZASTARALÉ) Monitor clusteru Azure Kubernetes - Sysdig
description: Monitorování clusteru Kubernetes ve službě Azure Container Service pomocí sysdigu
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 68136d5b9ec16c822cb62e4fee85b8ace9b1899a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371095"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-using-sysdig"></a>(ZASTARALÉ) Monitorování clusteru Kubernetes služby Azure Container Service pomocí sysdigu

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Požadavky
Tento návod předpokládá, že jste [vytvořili cluster Kubernetes pomocí služby Azure Container Service](container-service-kubernetes-walkthrough.md).

Také předpokládá, že máte nainstalované nástroje azure cli a kubectl.

Můžete otestovat, zda `az` máte nástroj nainstalován spuštěním:

```azurecli
az --version
```

Pokud nástroj nemáte nainstalovaný, `az` jsou [zde](https://github.com/azure/azure-cli#installation)pokyny .

Můžete otestovat, zda `kubectl` máte nástroj nainstalován spuštěním:

```console
kubectl version
```

Pokud nemáte `kubectl` nainstalovaný, můžete spustit:

```azurecli
az acs kubernetes install-cli
```

## <a name="sysdig"></a>Sysdig
Sysdig je externí monitorování jako servisní společnost, která může monitorovat kontejnery ve vašem clusteru Kubernetes běžícím v Azure. Použití Sysdig vyžaduje aktivní účet Sysdig.
Můžete si zaregistrovat účet na svých [stránkách](https://app.sysdigcloud.com).

Po přihlášení na web cloudu Sysdig klikněte na uživatelské jméno. Zobrazí se stránka, na které byste měli najít svůj „přístupový klíč“. 

![Klíč rozhraní API služby Sysdig](./media/container-service-kubernetes-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>Instalace agentů Sysdig do Kubernetes
Pro sledování kontejnerů, Sysdig běží proces na každém stroji pomocí Kubernetes `DaemonSet`.
DaemonSets jsou Objekty rozhraní API Kubernetes, které spouštějí jednu instanci kontejneru na počítač.
Jsou ideální pro instalaci nástrojů, jako je monitorovací agent Sysdigu.

Chcete-li nainstalovat daemonset Sysdig, měli byste nejprve stáhnout [šablonu](https://github.com/draios/sysdig-cloud-scripts/tree/master/agent_deploy/kubernetes) z sysdig. Uložte tento `sysdig-daemonset.yaml`soubor jako .

Na Linuxu a OS X můžete spustit:

```console
curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

V PowerShellu:

```powershell
Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

Další upravit tento soubor vložit přístupový klíč, který jste získali ze svého účtu Sysdig.

Nakonec vytvořte sadu daemonset:

```console
kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>Zobrazení monitorování
Po instalaci a spuštění by agenti měli pumpovat data zpět do Sysdigu.  Vraťte se na [palubní desku sysdig](https://app.sysdigcloud.com) a měli byste vidět informace o vašich kontejnerech.

Řídicí panely specifické pro Kubernetes můžete také nainstalovat pomocí [nového průvodce řídicím panelem](https://app.sysdigcloud.com/#/dashboards/new).
