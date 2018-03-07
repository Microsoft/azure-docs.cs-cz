---
title: "Kurz Azure Container Service – Monitorování Kubernetes"
description: "Kurz Azure Container Service – Monitorování Kubernetes pomocí Microsoft Operations Management Suite (OMS)"
services: container-service
author: dlepow
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 965ce4b7e154684fc1d171c90f17498afc828a66
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2018
---
# <a name="monitor-a-kubernetes-cluster-with-operations-management-suite"></a>Monitorování clusteru Kubernetes pomocí Operations Management Suite

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Monitorování clusteru a kontejnerů Kubernetes je důležité, zejména pokud spravujete produkční cluster ve velkém měřítku a s několika aplikacemi. 

Můžete využít několik řešení pro monitorování Kubernetes od Microsoftu nebo jiných poskytovatelů. V tomto kurzu monitorujete cluster Kubernetes pomocí řešení kontejnerů v cloudovém řešení pro správu IT Microsoftu [Operations Management Suite](../../operations-management-suite/operations-management-suite-overview.md). (Řešení kontejnerů v OMS je ve verzi Preview.)

V tomto kurzu, který je sedmou částí sedmidílné série, se probírají následující úlohy:

> [!div class="checklist"]
> * Získání nastavení pracovního prostoru OMS
> * Nastavení agentů OMS na uzlech Kubernetes
> * Přístup k informacím o monitorování na portálu OMS nebo webu Azure Portal

## <a name="before-you-begin"></a>Než začnete

V předchozích kurzech se aplikace zabalila do imagí kontejneru, tyto image se odeslaly do Azure Container Registry a vytvořil se cluster Kubernetes. 

Pokud jste tyto kroky neprovedli a chcete si je projít, vraťte se ke [kurzu 1 – Vytváření imagí kontejneru](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="get-workspace-settings"></a>Získání nastavení pracovního prostoru

Po získání přístupu k [portálu OMS](https://mms.microsoft.com) přejděte do **Nastavení** > **Připojené zdroje** > **Servery s Linuxem**. Tam najdete *ID pracovního prostoru* a primární nebo sekundární *Klíč pracovního prostoru*. Tyto hodnoty si poznamenejte, protože je budete potřebovat k nastavení agentů OMS v clusteru.

## <a name="set-up-oms-agents"></a>Nastavení agentů OMS

Tady je soubor YAML pro nastavení agentů OMS na uzlech clusteru s Linuxem. Ten vytvoří [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) Kubernetes, který na každém uzlu clusteru spustí jeden identický pod. Prostředek DaemonSet je ideální pro nasazení agenta monitorování. 

Uložte následující text do souboru `oms-daemonset.yaml` a nahraďte zástupné hodnoty *myWorkspaceID* a *myWorkspaceKey* svým ID a klíčem pracovního prostoru OMS. (V produkčním prostředí můžete tyto hodnoty zakódovat jako tajné kódy.)

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
    agentVersion: v1.3.4-127
    dockerProviderVersion: 10.0.0-25
  spec:
   containers:
     - name: omsagent 
       image: "microsoft/oms"
       imagePullPolicy: Always
       env:
       - name: WSID
         value: myWorkspaceID
       - name: KEY 
         value: myWorkspaceKey
       - name: DOMAIN
         value: opinsights.azure.com
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
       livenessProbe:
        exec:
         command:
         - /bin/bash
         - -c
         - ps -ef | grep omsagent | grep -v "grep"
        initialDelaySeconds: 60
        periodSeconds: 60
   volumes:
    - name: docker-sock 
      hostPath:
       path: /var/run/docker.sock
    - name: host-log
      hostPath:
       path: /var/log
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

Po spuštění agentů trvá OMS ingestování a zpracování dat několik minut.

## <a name="access-monitoring-data"></a>Přístup k datům monitorování

Data monitorování kontejnerů OMS můžete zobrazit a analyzovat pomocí [Řešení kontejnerů](../../log-analytics/log-analytics-containers.md) na portálu OMS nebo webu Azure Portal. 

Pokud chcete řešení kontejnerů nainstalovat s použitím [portálu OMS](https://mms.microsoft.com), přejděte do **Galerie řešení**. Pak přidejte **Řešení kontejnerů**. Případně můžete řešení kontejnerů přidat z [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.containersoms?tab=Overview).

Na portálu OMS vyhledejte na řídicím panelu OMS dlaždici se souhrnem **Kontejnery**. Kliknutím na dlaždici zobrazíte podrobnosti, včetně událostí kontejnerů, chyb, stavu, inventáře imagí a využití procesoru a paměti. Podrobnější informace zobrazíte kliknutím na řádek na dlaždici nebo [prohledáváním protokolu](../../log-analytics/log-analytics-log-searches.md).

![Řídicí panel Kontejnery na portálu OMS](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

Podobně na webu Azure Portal přejděte do části **Log Analytics** a vyberte název vašeho pracovního prostoru. Dlaždici se souhrnem **Kontejnery** zobrazíte kliknutím na **Řešení** > **Kontejnery**. Podrobnosti zobrazíte kliknutím na dlaždici.

Podrobné pokyny k dotazování a analýze dat monitorování najdete v [dokumentaci k Azure Log Analytics](../../log-analytics/index.yml).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste monitorovali svůj cluster Kubernetes pomocí OMS. Mezi probírané úlohy patří:

> [!div class="checklist"]
> * Získání nastavení pracovního prostoru OMS
> * Nastavení agentů OMS na uzlech Kubernetes
> * Přístup k informacím o monitorování na portálu OMS nebo webu Azure Portal


Na tomto odkazu najdete předem připravené ukázky skriptů pro službu Container Service.

> [!div class="nextstepaction"]
> [Ukázky skriptů pro službu Azure Container Service](cli-samples.md)
