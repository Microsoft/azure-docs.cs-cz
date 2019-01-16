---
title: Jak spravovat Azure Monitor pro agenta kontejnerů | Dokumentace Microsoftu
description: Tento článek popisuje správu většiny běžných úloh údržby s kontejnerizovaných agenta Log Analytics používá Azure Monitor pro kontejnery.
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
ms.date: 12/06/2018
ms.author: magoedte
ms.openlocfilehash: 6bfcb17d0a81c6e23c4bfa3dd4f4dd8cc3079379
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54331466"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>Jak spravovat Azure Monitor pro kontejnery agenta
Azure Monitor pro kontejnery používá kontejnerizovaných verzi agenta Log Analytics pro Linux. Po počátečním nasazení jsou rutiny nebo volitelné kroky, možná bude nutné provést během životního cyklu. Tento článek uvádí, o tom, jak ručně agenta upgradovat a zakázat kolekci proměnných prostředí od konkrétního kontejneru. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>Postup upgradu monitorování Azure pro kontejnery agenta
Azure Monitor pro kontejnery používá kontejnerizovaných verzi agenta Log Analytics pro Linux. Po vydání nové verze agenta, agent automaticky upgradovány na vaše spravované clustery Kubernetes hostované ve službě Azure Kubernetes Service (AKS).  

Pokud agent upgrade selže, tento článek popisuje postup ruční upgrade agenta. Postupujte podle vydané verze, najdete v článku [oznámení verzi agenta](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).   

### <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>Upgrade agenta na sledovaných clusteru Kubernetes
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

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Jak zakázat kolekci proměnných prostředí v kontejneru
Azure Monitor pro kontejnery shromažďuje z kontejnery spuštěné v podu proměnné prostředí a zobrazí v podokně vlastností vybraného kontejneru v **kontejnery** zobrazení. Toto chování můžete ovládat zakázáním kolekce pro konkrétní kontejner buď během nasazování clusteru AKS, nebo za nastavením proměnné prostředí *AZMON_COLLECT_ENV*. Tato funkce je k dispozici od verze agenta – ciprod11292018 a vyšší.  

Zakázání shromažďování systémových proměnných na nový nebo existující kontejner, nastavte proměnnou **AZMON_COLLECT_ENV** s hodnotou **False** v konfiguračním souboru yaml nasazení Kubernetes.   

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

Spusťte následující příkaz na použití změny do kontejneru AKS: `kubectl apply -f  <path to yaml file>`.

Pokud chcete ověřit, změna konfigurace vstoupily v platnost, vyberte kontejner ve službě **kontejnery** ve službě Azure Monitor pro kontejnery a na panelu Vlastnosti, rozbalte **proměnné prostředí**.  V části by se zobrazit pouze proměnné vytvořili - **AZMON_COLLECT_ENV = FALSE**. Pro všechny další kontejnery zveřejnit sekci proměnných prostředí všech proměnných prostředí zjistit.   

Opětovné povolení zjišťování proměnné prostředí, dříve použít stejný postup a změňte hodnotu z **False** k **True**a pak znovu spusťte `kubectl` příkaz k aktualizaci kontejneru.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Další postup
Pokud máte problémy při upgradu agenta, zkontrolujte [Průvodce odstraňováním potíží](container-insights-troubleshoot.md) pro podporu.