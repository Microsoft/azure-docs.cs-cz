---
title: ZASTARALÉ Monitorování clusteru Azure Kubernetes pomocí služby Datadog
description: Monitorování clusteru Kubernetes v Azure Container Service pomocí služby Datadog
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 1f3f70c30ab397bd549a2f3305a738274ee4f64f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79371167"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-datadog"></a>ZASTARALÉ Monitorování clusteru Azure Container Service pomocí služby Datadog

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Požadavky
Tento návod předpokládá, že jste [vytvořili cluster Kubernetes pomocí Azure Container Service](container-service-kubernetes-walkthrough.md).

Také předpokládá, že máte nainstalované rozhraní `az` příkazového řádku `kubectl` Azure a nástroje.

V případě, že máte `az` nástroj nainstalovaný, můžete otestovat spuštěním:

```azurecli
az --version
```

Pokud `az` nástroj nemáte nainstalovaný, [tady](https://github.com/azure/azure-cli#installation)najdete pokyny.

V případě, že máte `kubectl` nástroj nainstalovaný, můžete otestovat spuštěním:

```console
kubectl version
```

Pokud nemáte `kubectl` nainstalované, můžete spustit:

```azurecli
az acs kubernetes install-cli
```

## <a name="datadog"></a>Služby Datadog
Služby Datadog je monitorovací služba, která shromažďuje data monitorování z vašich kontejnerů v rámci vašeho clusteru Azure Container Service. Služby Datadog má řídicí panel integrace Docker, kde vidíte konkrétní metriky v rámci kontejnerů. Metriky shromážděné z vašich kontejnerů jsou uspořádané podle procesoru, paměti, sítě a vstupně-výstupních operací. Služby Datadog rozdělí metriky do kontejnerů a imagí.

Nejdřív je potřeba [vytvořit účet](https://www.datadoghq.com/lpg/) .

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>Instalace agenta služby Datadog s DaemonSet
DaemonSets používají Kubernetes ke spuštění jedné instance kontejneru na každém hostiteli v clusteru.
Jsou ideální pro spouštění agentů monitorování.

Jakmile se přihlásíte k služby Datadog, můžete postupovat podle [pokynů služby Datadog](https://app.datadoghq.com/account/settings#agent/kubernetes) pro instalaci agentů služby Datadog v clusteru pomocí DaemonSet.

## <a name="conclusion"></a>Závěr
A to je vše! Až budou agenti v provozu a běží, měli byste v konzole zobrazovat data za několik minut. Můžete navštívit integrovaný [řídicí panel Kubernetes](https://app.datadoghq.com/screen/integration/kubernetes) a zobrazit souhrn vašeho clusteru.
