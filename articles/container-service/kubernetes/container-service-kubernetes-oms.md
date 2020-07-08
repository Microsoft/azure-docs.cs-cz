---
title: ZASTARALÉ Monitorování clusteru Azure Kubernetes – Operations Management
description: Monitorování clusteru Kubernetes v Azure Container Service pomocí Log Analytics
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 02d04076ccc41d243a493838667f5e8cc6bfa5ac
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "79371150"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-log-analytics"></a>ZASTARALÉ Monitorování clusteru Azure Container Service s využitím Log Analytics

> [!TIP]
> Aktualizovanou verzi tohoto článku, který používá službu Azure Kubernetes, najdete v tématu [Azure monitor for Containers](../../azure-monitor/insights/container-insights-overview.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Požadavky
Tento návod předpokládá, že jste [vytvořili cluster Kubernetes pomocí Azure Container Service](container-service-kubernetes-walkthrough.md).

Také předpokládá, že máte nainstalované rozhraní příkazového `az` řádku Azure a `kubectl` nástroje.

V případě, že máte nástroj nainstalovaný, můžete otestovat `az` spuštěním:

```azurecli
az --version
```

Pokud `az` Nástroj nemáte nainstalovaný, [tady](https://github.com/azure/azure-cli#installation)najdete pokyny.
Alternativně můžete použít [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), které mají `az` `kubectl` již nainstalované nástroje Azure CLI a Tools.

V případě, že máte nástroj nainstalovaný, můžete otestovat `kubectl` spuštěním:

```console
kubectl version
```

Pokud nemáte `kubectl` nainstalované, můžete spustit:

```azurecli
az acs kubernetes install-cli
```

Pokud chcete otestovat, jestli máte v nástroji kubectl nainstalované klíče Kubernetes, můžete spustit:

```console
kubectl get nodes
```

Pokud výše uvedené příkazy vyprší, musíte do nástroje kubectl nainstalovat klíče clusteru Kubernetes. Můžete to udělat pomocí následujícího příkazu:

```azurecli
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-log-analytics"></a>Monitorování kontejnerů pomocí Log Analytics

Log Analytics je cloudové řešení pro správu IT od Microsoftu, které pomáhá spravovat a chránit místní i cloudovou infrastrukturu.Řešení kontejneru je řešení v Log Analytics, které vám pomůže zobrazit inventář kontejnerů, výkon a protokoly v jednom umístění. Můžete auditovat, řešit potíže s kontejnery zobrazením protokolů v centralizovaném umístění a zjistit vysokou úroveň nenáročného kontejneru na hostiteli.

![](media/container-service-monitoring-oms/image1.png)

Další informace o řešení kontejnerů najdete v [Log Analytics řešení kontejneru](../../azure-monitor/insights/containers.md).

## <a name="installing-log-analytics-on-kubernetes"></a>Instalace Log Analytics v Kubernetes

### <a name="obtain-your-workspace-id-and-key"></a>Získání ID a klíče pracovního prostoru
Aby mohl agent Log Analytics komunikovat se službou, je nutné nakonfigurovat ID pracovního prostoru a klíč pracovního prostoru. Chcete-li získat ID a klíč pracovního prostoru, je třeba vytvořit účet na adrese <https://mms.microsoft.com> .
Pokud chcete vytvořit účet, postupujte podle pokynů. Jakmile dokončíte vytváření účtu, můžete získat ID a klíč kliknutím na okno **Log Analytics** a pak na název pracovního prostoru. V části **Upřesnit nastavení**, **připojené zdroje**a pak **servery Linux**najdete informace, které potřebujete, jak vidíte níže.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-log-analytics-agent-using-a-daemonset"></a>Instalace agenta Log Analytics pomocí DaemonSet
DaemonSets používají Kubernetes ke spuštění jedné instance kontejneru na každém hostiteli v clusteru.
Jsou ideální pro spouštění agentů monitorování.

Zde je [soubor DAEMONSET YAML](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Uložte ho do souboru s názvem `oms-daemonset.yaml` a nahraďte hodnoty držitele místa pro `WSID` a `KEY` ID a klíč pracovního prostoru v souboru.

Jakmile přidáte ID a klíč pracovního prostoru do konfigurace DaemonSet, můžete do clusteru nainstalovat agenta Log Analytics pomocí `kubectl` nástroje příkazového řádku:

```console
kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-log-analytics-agent-using-a-kubernetes-secret"></a>Instalace agenta Log Analytics pomocí tajného klíče Kubernetes
Pokud chcete chránit své ID a klíč pracovního prostoru Log Analytics můžete použít tajný klíč Kubernetes jako součást souboru DaemonSet YAML.

- Zkopírujte skript, soubor tajné šablony a soubor DaemonSet YAML (z [úložiště](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)) a ujistěte se, že jsou ve stejném adresáři.
  - tajný kód generování skriptu – secret-gen.sh
  - Šablona tajného kódu-tajné šablony. yaml
    - DaemonSet YAML File-omsagent-DS-tajná. yaml
- Spusťte skript. Skript zobrazí výzvu k zadání ID a primárního klíče Log Analytics pracovního prostoru. Vložením skriptu a skriptu se vytvoří tajný soubor YAML, abyste ho mohli spustit.

  ```console
  sudo bash ./secret-gen.sh
  ```

  - Tajné klíče vytvoříte spuštěním následujícího:

     ```console
     kubectl create -f omsagentsecret.yaml
     ```

  - Chcete-li ověřit, spusťte následující příkaz:

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

  - Vytvořte svůj omsagent démon-set spuštěním následujícího:
  
  ```console
  kubectl create -f omsagent-ds-secrets.yaml
  ```

### <a name="conclusion"></a>Závěr
A to je vše! Po několika minutách byste si měli být schopni zobrazit tok dat na řídicím panelu Log Analytics.
