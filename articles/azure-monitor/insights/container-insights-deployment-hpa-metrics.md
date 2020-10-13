---
title: Nasazení & metriky HPA s Azure Monitor pro kontejnery | Microsoft Docs
description: Tento článek popisuje, co nasazení & HPA (horizontálního automatického škálování) se shromažďují pomocí Azure Monitor pro kontejnery.
ms.topic: conceptual
ms.date: 08/09/2020
ms.openlocfilehash: ee1f0d4849a8382a898aaca84956ff78166e138f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89570471"
---
# <a name="deployment--hpa-metrics-with-azure-monitor-for-containers"></a>Nasazení & metriky HPA s Azure Monitor pro kontejnery

Počínaje agentem verze *ciprod08072020*Azure monitor pro kontejnery integrovaný agent teď shromažďuje metriky pro nasazení & HPAs.

## <a name="deployment-metrics"></a>Metriky nasazení

Azure Monitor for Containers automaticky spouští monitorování nasazení, a to shromažďováním následujících metrik v intervalech 60 sec a jejich uložením do tabulky **InsightMetrics** :

|Název metriky |Dimenze metriky (značky) |Description |
|------------|------------------------|------------|
|kube_deployment_status_replicas_ready |container.azm.ms/clusterId, container.azm.ms/clusterName, creationTime, Deployment, deploymentStrategy, k8sNamespace, spec_replicas, status_replicas_available status_replicas_updated (stav. updatedReplicas) | Celkový počet připravených lusků, které cílí na toto nasazení (status. readyReplicas). Níže jsou uvedeny rozměry této metriky. <ul> <li> nasazení – název nasazení </li> <li> k8sNamespace-Kubernetes obor názvů pro nasazení </li> <li> deploymentStrategy – strategii nasazení, která se použije k nahrazení lusků novými (spec. strategii. Type)</li><li> creationTime – časové razítko vytvoření nasazení </li> <li> spec_replicas – počet požadovaných lusků (spec. replik) </li> <li>status_replicas_available – celkový počet dostupných lusků (připraveno alespoň na minReadySeconds) cílený tímto nasazením (status. availableReplicas)</li><li>status_replicas_updated – celkový počet neukončených lusků, na které cílí toto nasazení, které mají požadovanou specifikaci šablony (status. updatedReplicas) </li></ul>|

## <a name="hpa-metrics"></a>HPA metriky

Azure Monitor for Containers automaticky spouští monitorování HPAs a shromažďuje následující metriky v intervalech 60 sec a ukládá je do tabulky **InsightMetrics** :

|Název metriky |Dimenze metriky (značky) |Description |
|------------|------------------------|------------|
|kube_hpa_status_current_replicas |container.azm.ms/clusterId, container.azm.ms/clusterName, creationTime, hPa, k8sNamespace, lastScaleTime, spec_max_replicas, spec_min_replicas, status_desired_replicas, targetKind, cílový_název | Aktuální počet replik lusků, které spravuje tento automatické škálování (status. currentReplicas). Níže jsou uvedeny rozměry této metriky. <ul> <li> hPa – název HPA </li> <li> k8sNamespace-Kubernetes obor názvů pro HPA </li> <li> lastScaleTime – čas, kdy HPA škáluje počet lusků (status. lastScaleTime)</li><li> časové razítko vytvoření creationTime-HPA </li> <li> spec_max_replicas – horní limit počtu lusků, které lze nastavit pomocí automatického škálování (spec. maxReplicas) </li> <li> spec_min_replicas – nižší limit počtu replik, na které může automatické škálování (spec. minReplicas) škálovat. </li><li>status_desired_replicas – požadovaný počet replik lusků spravovaných tímto nástrojem automatického škálování (status. desiredReplicas)</li><li>targetKind – druh cíle HPA (spec. scaleTargetRef. Kind) </li><li>cílový_název – název cíle HPA (spec.scaleTargetRef.name) </li></ul>|

## <a name="deployment--hpa-charts"></a>Nasazení & grafů HPA 

Azure Monitor for containers obsahuje předem nakonfigurované grafy pro metriky uvedené dříve v tabulce jako sešit pro každý cluster. Nasazení & sešitu HPA **nasazení & hPa** přímo z clusteru AKS můžete najít tak, že v levém podokně vyberete **sešity** a v rozevíracím seznamu **Zobrazit sešity** v přehledu.

## <a name="next-steps"></a>Další kroky

- Podívejte se na [Kube metriky v Kubernetes](https://github.com/kubernetes/kube-state-metrics/tree/master/docs) a zjistěte další informace o metrikách stavu Kube.