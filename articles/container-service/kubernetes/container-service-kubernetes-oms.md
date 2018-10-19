---
title: Monitorování clusteru Kubernetes v Azure – řízení provozu
description: Monitorování clusteru Kubernetes ve službě Azure Container Service pomocí Log Analytics
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: a353fe3803b2d93c151559076960df06eb260bfe
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426409"
---
# <a name="monitor-an-azure-container-service-cluster-with-log-analytics"></a>Monitorování clusteru služby Azure Container Service pomocí služby Log Analytics

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>Požadavky
Tento názorný průvodce předpokládá, že máte [vytvořit cluster Kubernetes pomocí služby Azure Container Service](container-service-kubernetes-walkthrough.md).

Dále předpokládá, že máte `az` rozhraní příkazového řádku Azure a `kubectl` nainstalované nástroje.

Můžete otestovat, pokud máte `az` nástroj pro instalaci spuštěním:

```console
$ az --version
```

Pokud nemáte k dispozici `az` nástroj nainstalovali, jsou k dispozici pokyny [tady](https://github.com/azure/azure-cli#installation).
Alternativně můžete použít [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), který má `az` rozhraní příkazového řádku Azure a `kubectl` nástroje už nainstalovaný za vás.

Můžete otestovat, pokud máte `kubectl` nástroj pro instalaci spuštěním:

```console
$ kubectl version
```

Pokud nemáte `kubectl` nainstalované, můžete spustit:
```console
$ az acs kubernetes install-cli
```

K otestování, pokud máte kubernetes klíče nainstalována v kubectl nástroji, které můžete spustit:
```console
$ kubectl get nodes
```

Pokud výše uvedené chyby příkazu si budete muset nainstalovat klíče clusteru kubernetes do vašeho nástroje pro kubectl. Můžete to udělat pomocí následujícího příkazu:
```console
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-log-analytics"></a>Monitorování kontejnerů pomocí Log Analytics

Log Analytics je od Microsoftu cloudové řešení pro správu IT, která pomáhá spravovat a chránit místní a cloudovou infrastrukturu. Řešení kontejnerů je řešení ve službě Log Analytics, který umožňuje zobrazit inventář kontejneru, výkonu a protokolů na jednom místě. Můžete auditovat, řešení potíží s kontejnery zobrazením protokolů v centrálním umístění a najít hlučného využívání nadbytečné kontejneru na hostiteli.

![](media/container-service-monitoring-oms/image1.png)

Další informace o řešení kontejnerů najdete [kontejneru řešení Log Analytics](../../log-analytics/log-analytics-containers.md).

## <a name="installing-log-analytics-on-kubernetes"></a>Instalace Log Analytics v Kubernetes

### <a name="obtain-your-workspace-id-and-key"></a>Získání ID pracovního prostoru a klíč
Ke službě Log Analytics ke komunikaci služby je potřeba nakonfigurovat s ID pracovního prostoru a klíč pracovního prostoru. Chcete-li získat ID pracovního prostoru a klíč, budete muset vytvořit účet na webu <https://mms.microsoft.com>.
Postupujte podle kroků k vytvoření účtu. Po dokončení vytváření účtu, můžete získat vaše ID a klíče po kliknutí na **Log Analytics** okno a potom název pracovního prostoru. Potom v části **Upřesnit nastavení**, **připojené zdroje**a potom **servery s Linuxem**, najdete informace, které potřebujete, jak je znázorněno níže.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-log-analytics-agent-using-a-daemonset"></a>Instalace agenta Log Analytics pomocí DaemonSet
DaemonSets používají ke spuštění jedné instance kontejneru na každého hostitele v clusteru Kubernetes.
Jsou ideální pro provoz agentů monitorování.

Tady je [soubor DaemonSet YAML](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Uložit do souboru s názvem `oms-daemonset.yaml` a nahraďte zástupný text hodnoty `WSID` a `KEY` s ID pracovního prostoru a klíč v souboru.

Po přidání ID pracovního prostoru a klíč v konfiguraci DaemonSet můžete nainstalovat agenta Log Analytics v clusteru s `kubectl` nástroj příkazového řádku:

```console
$ kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-log-analytics-agent-using-a-kubernetes-secret"></a>Instalace agenta Log Analytics, pomocí tajného klíče Kubernetes
K ochraně ID pracovního prostoru Log Analytics a klíč slouží jako součást soubor DaemonSet YAML tajného kódu Kubernetes.

 - Zkopírujte skript, soubor tajného kódu šablony a soubor DaemonSet YAML (z [úložiště](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)) a ujistěte se, že jsou ve stejném adresáři.
      - Generování skriptu - secret gen.sh tajný klíč
      - Šablona tajné – template.yaml tajný klíč
   - Soubor DaemonSet YAML - omsagent-ds-secrets.yaml
 - Spusťte skript. Skript vyzve pro ID pracovního prostoru Log Analytics a primární klíč. Vložit, a skript se vytvoří soubor yaml tajného kódu, můžete ji spustit.
   ```
   #> sudo bash ./secret-gen.sh
   ```

   - Vytvořte pod tajných kódů spuštěním následujícího: ``` kubectl create -f omsagentsecret.yaml ```

   - Pokud chcete zkontrolovat, spusťte následující příkaz:

   ```
   root@ubuntu16-13db:~# kubectl get secrets
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

  - Vytvoření vašeho omsagent démon sady spuštěním ``` kubectl create -f omsagent-ds-secrets.yaml ```

### <a name="conclusion"></a>Závěr
A to je vše! Po několika minutách měli vidět dat odesílaných do řídicího panelu Log Analytics.
