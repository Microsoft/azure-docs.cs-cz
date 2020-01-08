---
title: Jak spravovat Azure Monitor pro agenta kontejnerů | Dokumentace Microsoftu
description: Tento článek popisuje správu většiny běžných úloh údržby s kontejnerizovaných agenta Log Analytics používá Azure Monitor pro kontejnery.
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 5bd3af7787ee38011c52224f5830d8b719031db8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75404264"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>Jak spravovat Azure Monitor pro kontejnery agenta
Azure Monitor pro kontejnery používá kontejnerizovaných verzi agenta Log Analytics pro Linux. Po počátečním nasazení jsou rutiny nebo volitelné kroky, možná bude nutné provést během životního cyklu. Tento článek uvádí, o tom, jak ručně agenta upgradovat a zakázat kolekci proměnných prostředí od konkrétního kontejneru. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>Postup upgradu monitorování Azure pro kontejnery agenta
Azure Monitor pro kontejnery používá kontejnerizovaných verzi agenta Log Analytics pro Linux. Po vydání nové verze agenta, agent automaticky upgradovány na vaše spravované clustery Kubernetes hostované ve službě Azure Kubernetes Service (AKS).  

Pokud agent upgrade selže, tento článek popisuje postup ruční upgrade agenta. Postupujte podle vydané verze, najdete v článku [oznámení verzi agenta](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).   

### <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>Upgrade agenta na sledovaných clusteru Kubernetes
Postup upgradu agenta zahrnuje dva kroky přímo vpřed. Prvním krokem je vypnout monitorování pro kontejnery pomocí Azure CLI s Azure Monitor.  Postupujte podle kroků popsaných v [zakázat monitorování](container-insights-optout.md?#azure-cli) článku. Pomocí Azure CLI umožňuje odebrat agenta z uzlů v clusteru bez dopadu na řešení a odpovídajících dat, která je uložena v pracovním prostoru. 

>[!NOTE]
>Při provádění této aktivity údržby, uzly v clusteru nejsou předávání shromážděných dat a zobrazení výkonu nebudou zobrazovat data mezi časem odebrat agenta a nainstalujte novou verzi. 
>

Chcete-li nainstalovat novou verzi agenta, postupujte podle kroků popsaných v tématu [povolení monitorování pomocí rozhraní příkazového řádku Azure CLI](container-insights-enable-new-cluster.md#enable-using-azure-cli)k dokončení tohoto procesu.  

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
Azure Monitor pro kontejnery shromažďuje z kontejnery spuštěné v podu proměnné prostředí a zobrazí v podokně vlastností vybraného kontejneru v **kontejnery** zobrazení. Toto chování můžete řídit tak, že zakážete shromažďování pro konkrétní kontejner během nasazování clusteru AKS nebo po nastavení proměnné prostředí *AZMON_COLLECT_ENV*. Tato funkce je k dispozici od verze agenta – ciprod11292018 a vyšší.  

Chcete-li zakázat shromažďování proměnných prostředí v novém nebo existujícím kontejneru, nastavte proměnnou **AZMON_COLLECT_ENV** s hodnotou **false** v konfiguračním souboru YAML nasazení Kubernetes.   

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

Spusťte následující příkaz na použití změny do kontejneru AKS: `kubectl apply -f  <path to yaml file>`.

Chcete-li ověřit vliv změny konfigurace, vyberte kontejner v zobrazení **kontejnery** v části Azure monitor for Containers a na panelu vlastností rozbalte položku **proměnné prostředí**.  V části by se měla zobrazit pouze dříve vytvořená proměnná- **AZMON_COLLECT_ENV = false**. Pro všechny další kontejnery zveřejnit sekci proměnných prostředí všech proměnných prostředí zjistit.   

Chcete-li znovu povolit zjišťování proměnných prostředí, použijte stejný proces dříve a změňte hodnotu z **false** na **true**a pak znovu spusťte příkaz `kubectl` pro aktualizaci kontejneru.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Další kroky
Pokud máte problémy při upgradu agenta, zkontrolujte [Průvodce odstraňováním potíží](container-insights-troubleshoot.md) pro podporu.
