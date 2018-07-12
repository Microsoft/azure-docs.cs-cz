---
title: Monitorování clusteru Kubernetes v Azure pomocí služby Datadog
description: Monitorování clusteru Kubernetes ve službě Azure Container Service pomocí služby Datadog
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 0a3f0baa4998dbc594023935575d659f7d45bbb9
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38629065"
---
# <a name="monitor-an-azure-container-service-cluster-with-datadog"></a>Monitorování clusteru služby Azure Container Service pomocí služby DataDog

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>Požadavky
Tento názorný průvodce předpokládá, že máte [vytvořit cluster Kubernetes pomocí služby Azure Container Service](container-service-kubernetes-walkthrough.md).

Dále předpokládá, že máte `az` rozhraní příkazového řádku Azure a `kubectl` nainstalované nástroje.

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

## <a name="datadog"></a>Služby DataDog
Služby Datadog je monitorovací služba, která shromažďuje data monitorování z vašeho kontejnerů v clusteru Azure Container Service. Služby Datadog má řídicí panel integrace Dockeru ve kterém uvidíte konkrétní metriky v rámci kontejnerů. Metrik shromážděných z kontejnerů jsou uspořádané podle procesoru, paměti, síťové a vstupně-výstupních operací. Služby Datadog rozdělí metriky do kontejnerů a obrázky.

Je nutné nejprve [vytvořit účet](https://www.datadoghq.com/lpg/)

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>Instalace agenta služby Datadog s DaemonSet
DaemonSets používají ke spuštění jedné instance kontejneru na každého hostitele v clusteru Kubernetes.
Jsou ideální pro provoz agentů monitorování.

Po přihlášení do služby Datadog, můžete postupovat podle [služby Datadog pokyny](https://app.datadoghq.com/account/settings#agent/kubernetes) nainstalovat agenty služby Datadog ve vašem clusteru pomocí DaemonSet.

## <a name="conclusion"></a>Závěr
A to je vše! Po zprovoznění agenty byste měli vidět data v konzole za pár minut. Můžete navštívit integrované [řídicí panel kubernetes](https://app.datadoghq.com/screen/integration/kubernetes) zobrazíte přehled vašeho clusteru.
