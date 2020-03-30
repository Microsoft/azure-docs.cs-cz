---
title: (ZASTARALÉ) Monitorování clusteru Azure Kubernetes – správa operací
description: Monitorování clusteru Kubernetes ve službě Azure Container Service pomocí log analytics
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 02d04076ccc41d243a493838667f5e8cc6bfa5ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371150"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-log-analytics"></a>(ZASTARALÉ) Monitorování clusteru služby Azure Container Service pomocí analýzy protokolů

> [!TIP]
> Aktualizovanou verzi tohoto článku, který používá službu Azure Kubernetes, najdete v článku [Azure Monitor pro kontejnery](../../azure-monitor/insights/container-insights-overview.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Požadavky
Tento návod předpokládá, že jste [vytvořili cluster Kubernetes pomocí služby Azure Container Service](container-service-kubernetes-walkthrough.md).

Také předpokládá, že máte `az` nainstalované `kubectl` k dispozici azure cli a nástroje.

Můžete otestovat, zda `az` máte nástroj nainstalován spuštěním:

```azurecli
az --version
```

Pokud nástroj nemáte nainstalovaný, `az` jsou [zde](https://github.com/azure/azure-cli#installation)pokyny .
Případně můžete použít [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), `az` který má `kubectl` Azure cli a nástroje již nainstalované pro vás.

Můžete otestovat, zda `kubectl` máte nástroj nainstalován spuštěním:

```console
kubectl version
```

Pokud nemáte `kubectl` nainstalovaný, můžete spustit:

```azurecli
az acs kubernetes install-cli
```

Chcete-li otestovat, zda máte v nástroji kubectl nainstalované klíče kubernetes, můžete spustit:

```console
kubectl get nodes
```

Pokud se výše uvedený příkaz chybí, musíte do nástroje kubectl nainstalovat klíče clusteru kubernetes. Můžete to udělat pomocí následujícího příkazu:

```azurecli
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-log-analytics"></a>Monitorování kontejnerů pomocí analýzy protokolů

Log Analytics je cloudové řešení pro správu IT od microsoftu, které vám pomůže spravovat a chránit místní a cloudovou infrastrukturu.Kontejnerové řešení je řešení v Log Analytics, které vám pomůže zobrazit inventář kontejnerů, výkon a protokoly v jednom umístění. Můžete auditovat, řešit problémy s kontejnery zobrazením protokolů v centralizovaném umístění a najít hlučné náročné přebytečný kontejner na hostiteli.

![](media/container-service-monitoring-oms/image1.png)

Další informace o řešení kontejneru naleznete v [analýze protokolu řešení kontejneru](../../azure-monitor/insights/containers.md).

## <a name="installing-log-analytics-on-kubernetes"></a>Instalace log analytics na Kubernetes

### <a name="obtain-your-workspace-id-and-key"></a>Získání ID a klíče pracovního prostoru
Pro agenta Log Analytics mluvit se službou, kterou potřebuje nakonfigurovat s ID pracovního prostoru a klíč pracovního prostoru. Chcete-li získat ID a klíč pracovního <https://mms.microsoft.com>prostoru, musíte si vytvořit účet na adrese .
Chcete-li vytvořit účet, postupujte podle pokynů. Po dokončení vytváření účtu můžete získat své ID a klíč kliknutím na okno **Log Analytics** a potom název pracovního prostoru. Potom v části **Upřesnit nastavení**, **připojené zdroje**a pak **linuxové servery**najdete informace, které potřebujete, jak je znázorněno níže.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-log-analytics-agent-using-a-daemonset"></a>Instalace agenta Log Analytics pomocí sady DaemonSet
DaemonSets jsou používány Kubernetes spustit jednu instanci kontejneru na každém hostiteli v clusteru.
Jsou perfektní pro vedení monitorovacích agentů.

Zde je [DaemonSet YAML soubor](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Uložte jej do `oms-daemonset.yaml` souboru s názvem `WSID` a `KEY` nahraďte hodnoty zástupného symbolu pro iD pracovního prostoru a jeho klíčem.

Po přidání ID pracovního prostoru a klíče ke konfiguraci DaemonSet můžete do clusteru `kubectl` nainstalovat agenta Log Analytics pomocí nástroje příkazového řádku:

```console
kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-log-analytics-agent-using-a-kubernetes-secret"></a>Instalace agenta Log Analytics pomocí tajného klíče Kubernetes
Chcete-li chránit ID a klíč pracovního prostoru Analýzy protokolů, můžete použít Kubernetes Secret jako součást souboru DaemonSet YAML.

- Zkopírujte skript, soubor tajné šablony a soubor DaemonSet YAML (z [úložiště)](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)a ujistěte se, že jsou ve stejném adresáři.
  - tajný generující skript - secret-gen.sh
  - tajná šablona - secret-template.yaml
    - DaemonSet YAML soubor - omsagent-ds-secrets.yaml
- Spusťte skript. Skript požádá o ID pracovního prostoru analýzy protokolu a primární klíč. Vložte to a skript vytvoří tajný soubor yaml, abyste jej mohli spustit.

  ```console
  sudo bash ./secret-gen.sh
  ```

  - Vytvořte pod tajných klíčů spuštěním následující:

     ```console
     kubectl create -f omsagentsecret.yaml
     ```

  - Chcete-li zkontrolovat, spusťte následující:

  ```console
  kubectl get secrets
  ```

  ```output
  NAME                  TYPE                                  DATA      AGE
  default-token-gvl91   kubernetes.io/service-account-token   3         50d
  omsagent-secret       Opaque                                2         1d
  root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
  Name:           omsagent-secret
  Namespace:      default
  Labels:         <none>
  Annotations:    <none>

  Type:   Opaque

  Data
  ====
  WSID:   36 bytes
  KEY:    88 bytes
  ```

  - Vytvořte si omsagent daemon-set spuštěním následující:
  
  ```console
  kubectl create -f omsagent-ds-secrets.yaml
  ```

### <a name="conclusion"></a>Závěr
A to je vše! Po několika minutách byste měli vidět data přitékající na řídicí panel Log Analytics.
