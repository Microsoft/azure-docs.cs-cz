---
title: Postup řešení potíží s Azure Monitor pro kontejnery | Dokumentace Microsoftu
description: Tento článek popisuje, jak můžete odstraňovat potíže a řešit problémy s monitorováním Azure pro kontejnery.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2018
ms.author: magoedte
ms.openlocfilehash: de7ae5788224b83105e4dc9a24aea35c8b841c88
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986723"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Řešení potíží s Azure Monitor pro kontejnery

Při konfiguraci monitorování clusteru Azure Kubernetes Service (AKS) pomocí Azure monitoru pro kontejnery může dojít k potížím, brání ve sběru dat nebo hlásí stav. Tento článek podrobně popisuje některé běžné problémy a postup řešení potíží.

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>Azure Monitor pro kontejnery je zapnutá, ale žádné informace o nevytvářejících sestavy
Pokud monitorování Azure pro kontejnery se úspěšně povolena a konfigurována, ale nelze zobrazit informace o stavu nebo žádné výsledky jsou vráceny z dotazů protokolu Log Analytics, Diagnostikujte problém pomocí následujících kroků: 

1. Zkontrolujte stav agenta spuštěním příkazu: 

    `kubectl get ds omsagent --namespace=kube-system`

    Výstup by měl vypadat podobně jako následující text, který označuje, že byla správně nasazena:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Zkontrolujte stav nasazení verze agenta *06072018* nebo později pomocí příkazu:

    `kubectl get deployment omsagent-rs -n=kube-system`

    Výstup by měl vypadat podobně jako v následujícím příkladu, který označuje, že byla správně nasazena:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Postup kontroly stavu pod celému ověřte, zda je spuštěna pomocí příkazu: `kubectl get pods --namespace=kube-system`

    Výstup by měl vypadat podobně jako v následujícím příkladu se stavem *systémem* pro omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. V protokolech agenta. Když se nasadí kontejnerizovanou agenta, spustí rychlou kontrolu spuštěním příkazů (OMI) a zobrazí verzi agenta a poskytovatele. 

5. Pokud chcete ověřit, že agent byl úspěšně připojit, spusťte příkaz: `kubectl logs omsagent-484hw --namespace=kube-system`

    Stav by měl vypadat podobně jako v následujícím příkladu:

    ```
    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="next-steps"></a>Další postup
Pomocí monitorování povoleno zachycení stavu metriky pro uzly clusteru AKS a podů, jsou tyto metriky stavu k dispozici na webu Azure Portal. Naučte se používat Azure Monitor pro kontejnery, najdete v článku [zobrazení Azure Kubernetes Service health](monitoring-container-insights-analyze.md).