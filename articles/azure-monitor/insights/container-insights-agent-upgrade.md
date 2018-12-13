---
title: Postup upgradu Azure Monitor pro agenta kontejnery (Preview) | Dokumentace Microsoftu
description: Tento článek popisuje, jak upgradovat agenta Log Analytics používá Azure Monitor pro kontejnery.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: magoedte
ms.openlocfilehash: 08206b9de4fca3b422c5b076d7e0c1c180f82fbd
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184079"
---
# <a name="how-to-upgrade-the-azure-monitor-for-containers-preview-agent"></a>Postup upgradu Azure Monitor pro agenta kontejnery (Preview)
Azure Monitor pro kontejnery používá kontejnerizovaných verzi agenta Log Analytics pro Linux. Po vydání nové verze agenta, agent automaticky upgradovány na vaše spravované clustery Kubernetes hostované ve službě Azure Kubernetes Service (AKS).  

Pokud agent upgrade selže, tento článek popisuje postup ruční upgrade agenta. Postupujte podle vydané verze, najdete v článku [oznámení verzi agenta](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).   

## <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>Upgrade agenta na sledovaných clusteru Kubernetes
Postup upgradu agenta zahrnuje dva kroky přímo vpřed. Prvním krokem je vypnout monitorování pro kontejnery pomocí Azure CLI s Azure Monitor.  Postupujte podle kroků popsaných v [zakázat monitorování](container-insights-optout.md?toc=%2fazure%2fmonitoring%2ftoc.json#azure-cli) článku. Pomocí Azure CLI umožňuje odebrat agenta z uzlů v clusteru bez dopadu na řešení a odpovídajících dat, která je uložena v pracovním prostoru. 

>[!NOTE]
>Při provádění této aktivity údržby, uzly v clusteru nejsou předávání shromážděných dat a zobrazení výkonu nebudou zobrazovat data mezi časem odebrat agenta a nainstalujte novou verzi. 
>

Pokud chcete nainstalovat novou verzi agenta, postupujte podle kroků popsaných v [připojení monitorování](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-using-azure-cli) článku pomocí Azure CLI pro ukončení tohoto procesu.  

Po opětovné povolení monitorování, může trvat přibližně 15 minut, než uvidíte aktualizovaný stav metriky pro cluster. K ověření agenta úspěšně upgradován, spusťte příkaz: `kubectl logs omsagent-484hw --namespace=kube-system`

Stav by měl vypadat následovně, kde hodnota *omi* a *omsagent* by měl odpovídat zadaná v nejnovější verzi [historie verzí agenta](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).  

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
    omi 1.4.2.5
    omsagent 1.6.0-163
    docker-cimprov 1.0.0.31

## <a name="next-steps"></a>Další postup
Pokud máte problémy při upgradu agenta, zkontrolujte [Průvodce odstraňováním potíží](container-insights-troubleshoot.md) pro podporu.