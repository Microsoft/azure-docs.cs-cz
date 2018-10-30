---
title: Kurz Azure Container Service – Monitorování Kubernetes
description: Kurz Azure Container Service – Monitorování Kubernetes pomocí služby Log Analytics
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 04/05/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 249e286b16b78643c33b567f705a4c92991f5553
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49404864"
---
# <a name="monitor-a-kubernetes-cluster-with-log-analytics"></a>Monitorování clusteru Kubernetes pomocí služby Log Analytics

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Monitorování clusteru a kontejnerů Kubernetes je důležité, zejména pokud spravujete produkční cluster ve velkém měřítku a s několika aplikacemi.

Můžete využít několik řešení pro monitorování Kubernetes od Microsoftu nebo jiných poskytovatelů. V tomto kurzu monitorujete cluster Kubernetes pomocí řešení kontejnerů ve službě [Log Analytics](../../operations-management-suite/operations-management-suite-overview.md) – cloudovém řešení pro správu IT od společnosti Microsoft. (Řešení kontejnerů je ve verzi Preview.)

V tomto kurzu, který je sedmou částí sedmidílné série, se probírají následující úlohy:

> [!div class="checklist"]
> * Získání nastavení pracovního prostoru Log Analytics
> * Nastavení agentů Log Analytics na uzlech Kubernetes
> * Přístup k informacím o monitorování na portálu Log Analytics nebo webu Azure Portal

## <a name="before-you-begin"></a>Než začnete

V předchozích kurzech se aplikace zabalila do imagí kontejneru, tyto image se odeslaly do Azure Container Registry a vytvořil se cluster Kubernetes.

Pokud jste tyto kroky neprovedli a chcete si je projít, vraťte se ke [kurzu 1 – Vytváření imagí kontejneru](./container-service-tutorial-kubernetes-prepare-app.md).

## <a name="get-workspace-settings"></a>Získání nastavení pracovního prostoru

Po získání přístupu k [portálu Log Analytics](https://mms.microsoft.com) přejděte do **Nastavení** > **Připojené zdroje** > **Servery s Linuxem**. Tam najdete *ID pracovního prostoru* a primární nebo sekundární *Klíč pracovního prostoru*. Tyto hodnoty si poznamenejte, protože je budete potřebovat k nastavení agentů Log Analytics v clusteru.

## <a name="create-kubernetes-secret"></a>Vytvoření tajného klíče Kubernetes

Pomocí příkazu [kubectl create secret][kubectl-create-secret]t uložte nastavení pracovního prostoru Log Analytics do tajného kódu Kubernetes s názvem `omsagent-secret`. Aktualizujte `WORKSPACE_ID` s použitím ID vašeho pracovního prostoru Log Analytics a `WORKSPACE_KEY` s použitím klíče pracovního prostoru.

```console
kubectl create secret generic omsagent-secret --from-literal=WSID=WORKSPACE_ID --from-literal=KEY=WORKSPACE_KEY
```

## <a name="set-up-log-analytics-agents"></a>Nastavení agentů Log Analytics

Ke konfiguraci agentů monitorování kontejnerů v clusteru Kubernetes můžete použít následující soubor manifestu Kubernetes. Ten vytvoří [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) Kubernetes, který na každém uzlu clusteru spustí jeden identický pod.

Uložte následující text do souboru `oms-daemonset.yaml`.

```YAML
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
 name: omsagent
spec:
 template:
  metadata:
   labels:
    app: omsagent
    agentVersion: 1.4.3-174
    dockerProviderVersion: 1.0.0-30
  spec:
   containers:
     - name: omsagent
       image: "microsoft/oms"
       imagePullPolicy: Always
       securityContext:
         privileged: true
       ports:
       - containerPort: 25225
         protocol: TCP
       - containerPort: 25224
         protocol: UDP
       volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /var/log
          name: host-log
        - mountPath: /etc/omsagent-secret
          name: omsagent-secret
          readOnly: true
        - mountPath: /var/lib/docker/containers
          name: containerlog-path
       livenessProbe:
        exec:
         command:
         - /bin/bash
         - -c
         - ps -ef | grep omsagent | grep -v "grep"
        initialDelaySeconds: 60
        periodSeconds: 60
   nodeSelector:
    beta.kubernetes.io/os: linux
   # Tolerate a NoSchedule taint on master that ACS Engine sets.
   tolerations:
    - key: "node-role.kubernetes.io/master"
      operator: "Equal"
      value: "true"
      effect: "NoSchedule"
   volumes:
    - name: docker-sock
      hostPath:
       path: /var/run/docker.sock
    - name: host-log
      hostPath:
       path: /var/log
    - name: omsagent-secret
      secret:
       secretName: omsagent-secret
    - name: containerlog-path
      hostPath:
       path: /var/lib/docker/containers
```

Pomocí následujícího příkazu vytvořte DaemonSet:

```azurecli-interactive
kubectl create -f oms-daemonset.yaml
```

Vytvoření DaemonSet můžete ověřit spuštěním následujícího příkazu:

```azurecli-interactive
kubectl get daemonset
```

Výstup je podobný tomuto:

```azurecli-interactive
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR   AGE
omsagent   3         3         3         0            3           <none>          5m
```

Po spuštění agentů trvá službě Log Analytics ingestování a zpracování dat několik minut.

## <a name="access-monitoring-data"></a>Přístup k datům monitorování

Data monitorování kontejnerů můžete zobrazit a analyzovat pomocí [Řešení kontejnerů](../../log-analytics/log-analytics-containers.md) na portálu Log Analytics nebo webu Azure Portal.

Pokud chcete řešení kontejnerů nainstalovat s použitím [portálu Log Analytics](https://mms.microsoft.com), přejděte do **Galerie řešení**. Pak přidejte **Řešení kontejnerů**. Případně můžete řešení kontejnerů přidat z [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.containersoms?tab=Overview).

Na portálu Log Analytics vyhledejte na řídicím panelu souhrnnou dlaždici **Kontejnery**. Kliknutím na dlaždici zobrazíte podrobnosti, včetně událostí kontejnerů, chyb, stavu, inventáře imagí a využití procesoru a paměti. Podrobnější informace zobrazíte kliknutím na řádek na dlaždici nebo [prohledáváním protokolu](../../log-analytics/log-analytics-log-searches.md).

![Řídicí panel Kontejnery na webu Azure Portal](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

Podobně na webu Azure Portal přejděte do části **Log Analytics** a vyberte název vašeho pracovního prostoru. Dlaždici se souhrnem **Kontejnery** zobrazíte kliknutím na **Řešení** > **Kontejnery**. Podrobnosti zobrazíte kliknutím na dlaždici.

Podrobné pokyny k dotazování a analýze dat monitorování najdete v [dokumentaci k Azure Log Analytics](../../log-analytics/log-analytics-queries.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste monitorovali svůj cluster Kubernetes pomocí služby Log Analytics. Mezi probírané úlohy patří:

> [!div class="checklist"]
> * Získání nastavení pracovního prostoru Log Analytics
> * Nastavení agentů Log Analytics na uzlech Kubernetes
> * Přístup k informacím o monitorování na portálu Log Analytics nebo webu Azure Portal


Na tomto odkazu najdete předem připravené ukázky skriptů pro službu Container Service.

> [!div class="nextstepaction"]
> [Ukázky skriptů pro službu Azure Container Service](cli-samples.md)
