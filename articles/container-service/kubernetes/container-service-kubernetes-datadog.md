---
title: (ZASTARALÉ) Monitorování clusteru Azure Kubernetes pomocí datadogu
description: Monitorování clusteru Kubernetes ve službě Azure Container Service pomocí Datadogu
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 1f3f70c30ab397bd549a2f3305a738274ee4f64f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371167"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-datadog"></a>(ZASTARALÉ) Monitorování clusteru služby Azure Container Service pomocí datového psa

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Požadavky
Tento návod předpokládá, že jste [vytvořili cluster Kubernetes pomocí služby Azure Container Service](container-service-kubernetes-walkthrough.md).

Také předpokládá, že máte `az` nainstalované `kubectl` k dispozici azure cli a nástroje.

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

## <a name="datadog"></a>Datový pes
Datadog je monitorovací služba, která shromažďuje data monitorování z vašich kontejnerů v rámci clusteru služby Azure Container Service. Datadog má řídicí panel integrace Dockeru, kde můžete vidět konkrétní metriky v rámci kontejnerů. Metriky získané z kontejnerů jsou uspořádány podle procesoru, paměti, sítě a vstupně-výkon. Datadog rozděluje metriky do kontejnerů a obrázků.

Nejprve je třeba [vytvořit účet](https://www.datadoghq.com/lpg/)

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>Instalace agenta Datadogu s daemonsetem
DaemonSets jsou používány Kubernetes spustit jednu instanci kontejneru na každém hostiteli v clusteru.
Jsou perfektní pro vedení monitorovacích agentů.

Jakmile se přihlásíte do Datadog, můžete postupovat [podle pokynů Datadog](https://app.datadoghq.com/account/settings#agent/kubernetes) nainstalovat Datadog agenti na vašem clusteru pomocí DaemonSet.

## <a name="conclusion"></a>Závěr
A to je vše! Jakmile jsou agenti v provozu, měli byste vidět data v konzoli během několika minut. Můžete navštívit integrovaný [řídicí panel kubernetes](https://app.datadoghq.com/screen/integration/kubernetes) a zobrazit souhrn vašeho clusteru.
