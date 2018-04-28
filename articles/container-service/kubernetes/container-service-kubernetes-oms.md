---
title: Monitorování Azure Kubernetes cluster - Operations Management
description: Monitorování Kubernetes clusteru v Azure Container Service pomocí analýzy protokolů
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 3b014ce4c91d1dc9fae744ef4b528c98f9f787b3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="monitor-an-azure-container-service-cluster-with-log-analytics"></a>Monitorování clusteru Azure Container Service pomocí analýzy protokolů

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>Požadavky
Tento návod předpokládá, že máte [vytvořit Kubernetes clusteru Azure Container Service pomocí](container-service-kubernetes-walkthrough.md).

Předpokládá také, abyste měli `az` rozhraní příkazového řádku Azure a `kubectl` nástroje nainstalované.

Můžete otestovat, pokud máte `az` nainstalovaná, spuštěním nástroje:

```console
$ az --version
```

Pokud nemáte `az` nástroj nainstalovali, jsou k dispozici pokyny [zde](https://github.com/azure/azure-cli#installation).
Alternativně můžete použít [prostředí cloudu Azure](https://docs.microsoft.com/azure/cloud-shell/overview), který má `az` rozhraní příkazového řádku Azure a `kubectl` nástroje pro je již nainstalována.

Můžete otestovat, pokud máte `kubectl` nainstalovaná, spuštěním nástroje:

```console
$ kubectl version
```

Pokud nemáte `kubectl` nainstalován, můžete spustit:
```console
$ az acs kubernetes install-cli
```

K testování, pokud máte nainstalovaný ve vaší kubectl nástroj, který můžete spustit klíče kubernetes:
```console
$ kubectl get nodes
```

Pokud výše uvedené chyby příkazu si musíte nainstalovat kubernetes clusteru klíče do vaší kubectl nástroje. Můžete to udělat pomocí následujícího příkazu:
```console
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-log-analytics"></a>Monitorování kontejnery s analýzy protokolů

Analýzy protokolů je společnosti Microsoft založená na cloudu IT řešení správy, které pomáhá spravovat a chránit místní a cloudové infrastruktury. Kontejner řešení je řešení v analýzy protokolů, který umožňuje zobrazit inventář kontejneru, výkonu a protokoly na jednom místě. Můžete auditovat, řešení potíží s kontejnery zobrazením protokoly v centrálním umístění a najít aktivní využívání nadbytečné kontejneru na hostiteli.

![](media/container-service-monitoring-oms/image1.png)

Další informace o kontejneru řešení, naleznete [analýzy protokolů řešení kontejneru](../../log-analytics/log-analytics-containers.md).

## <a name="installing-log-analytics-on-kubernetes"></a>Instalace na Kubernetes analýzy protokolů

### <a name="obtain-your-workspace-id-and-key"></a>Získání ID a klíč
Pro analýzu protokolu agenta ke komunikaci služby je potřeba nakonfigurovat s ID pracovního prostoru a klíč pracovního prostoru. ID pracovního prostoru a klíč musíte vytvořit účet na webu <https://mms.microsoft.com>.
Postupujte podle kroků pro vytvoření účtu. Po dokončení vytváření účtu, je nutné získat ID a klíč kliknutím **nastavení**, pak **připojené zdroje**a potom **servery se systémem Linux**, jak je uvedeno níže.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-log-analytics-agent-using-a-daemonset"></a>Instalace agenta analýzy protokolů pomocí DaemonSet
DaemonSets Kubernetes používá ke spuštění jednu instanci kontejner na jednotlivých hostitelích v clusteru.
Jsou ideální pro spuštění monitorovací agenty.

Tady je [DaemonSet YAML soubor](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Uložit do souboru s názvem `oms-daemonset.yaml` a nahraďte zástupný symbol hodnoty pro `WSID` a `KEY` s vaše ID a klíč v souboru.

Po přidání vaše ID a klíč v konfiguraci DaemonSet, analýzy protokolů agenta můžete nainstalovat na cluster s `kubectl` nástroj příkazového řádku:

```console
$ kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-log-analytics-agent-using-a-kubernetes-secret"></a>Instalace agenta analýzy protokolů pomocí Kubernetes tajný klíč
K ochraně ID pracovního prostoru analýzy protokolů a klíč můžete jako součást souboru DaemonSet YAML Kubernetes tajný klíč.

 - Zkopírujte skript, soubor tajný šablonu a soubor DaemonSet YAML (z [úložiště](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)) a ujistěte se, že jsou na stejném adresáři.
      - Generování skriptu - tajný klíč gen.sh tajný klíč
      - Šablona tajné – template.yaml tajný klíč
   - Soubor DaemonSet YAML - omsagent – ds-secrets.yaml
 - Spusťte skript. Skript vyzve pro ID pracovního prostoru analýzy protokolů a primární klíč. Vložit, a tento skript vytvoří soubor tajný yaml, můžete ji spustit.
   ```
   #> sudo bash ./secret-gen.sh
   ```

   - Vytvoření tajných klíčů pod spuštěním následujícího: ``` kubectl create -f omsagentsecret.yaml ```

   - Pokud chcete zkontrolovat, spusťte následující:

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

  - Vytvoření vaší omsagent démon set spuštěním ``` kubectl create -f omsagent-ds-secrets.yaml ```

### <a name="conclusion"></a>Závěr
A to je vše! Po několika minutách byste měli vidět dat odesílaných do řídicího panelu analýzy protokolů.
