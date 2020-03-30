---
title: Jak spravovat Agent Azure Monitor pro kontejnery | Dokumenty společnosti Microsoft
description: Tento článek popisuje správu nejběžnějších úloh údržby s agentem analýzy protokolů kontejnerizovaného používaného službou Azure Monitor pro kontejnery.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 1a1f8d690979a846dbf5041999180221752acc0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275318"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>Jak spravovat Azure Monitor pro kontejnery agenta

Azure Monitor pro kontejnery používá kontejnerizovanou verzi agenta Log Analytics pro Linux. Po počátečním nasazení jsou rutinní nebo volitelné úkoly, které budete muset provést během jeho životního cyklu. Tento článek popisuje, jak ručně upgradovat agenta a zakázat kolekci proměnných prostředí z určitého kontejneru. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>Jak upgradovat Azure Monitor pro kontejnery agenta

Azure Monitor pro kontejnery používá kontejnerizovanou verzi agenta Log Analytics pro Linux. Když je vydána nová verze agenta, agent se automaticky upgraduje na spravované clustery Kubernetes hostované ve službě Azure Kubernetes Service (AKS) a Azure Red Hat OpenShift. Pro [hybridní cluster Kubernetes](container-insights-hybrid-setup.md) agent není spravován a je třeba ručně upgradovat agenta.

Pokud se upgrade agenta nezdaří pro cluster hostovaný v AKS, tento článek také popisuje proces ručníinovace agenta. Chcete-li sledovat vydané verze, [přečtěte si informace o vydání agenta](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

### <a name="upgrade-agent-on-monitored-kubernetes-cluster"></a>Agent upgradu na monitorovaném clusteru Kubernetes

Proces upgradu agenta v clusterech, než je Azure Red Hat OpenShift, se skládá ze dvou přímých kroků vpřed. Prvním krokem je zakázat monitorování pomocí Azure Monitor pro kontejnery pomocí Azure CLI. Postupujte podle kroků popsaných v článku [Zakázat monitorování.](container-insights-optout.md?#azure-cli) Pomocí Azure CLI nám umožňuje odebrat agenta z uzlů v clusteru bez dopadu na řešení a odpovídající data uložená v pracovním prostoru. 

>[!NOTE]
>Při provádění této činnosti údržby uzly v clusteru nejsou předávání shromážděná data a zobrazení výkonu nezobrazí data mezi čas odebráníagenta a instalaci nové verze. 
>

Chcete-li nainstalovat novou verzi agenta, dokončete tento proces podle kroků popsaných v [povolení monitorování pomocí příkazového příkazu Kontu Azure](container-insights-enable-new-cluster.md#enable-using-azure-cli).  

Po opětovném povolení monitorování může trvat přibližně 15 minut, než budete moci zobrazit aktualizované metriky stavu pro cluster. Chcete-li úspěšně ověřit úspěšně upgradovaný agent, spusťte příkaz:`kubectl logs omsagent-484hw --namespace=kube-system`

Stav by se měl podobat následujícímu příkladu, kde by hodnota pro *omi* a *omsagent* měla odpovídat nejnovější verzi zadané v [historii vydání agenta](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).  

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

## <a name="upgrade-agent-on-hybrid-kubernetes-cluster"></a>Upgrade agenta v hybridním clusteru Kubernetes

Proces upgradu agenta v místním clusteru Kubernetes, AKS Engine v Azure a Azure Stacku lze dokončit spuštěním následujícího příkazu:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
```

Pokud je pracovní prostor Log Analytics v Azure V Číně, spusťte následující příkaz:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

Pokud je pracovní prostor Log Analytics v Azure US Government, spusťte následující příkaz:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Jak zakázat kolekci proměnných prostředí v kontejneru

Azure Monitor pro kontejnery shromažďuje proměnné prostředí z kontejnerů spuštěných v podu a prezentuje je v podokně vlastností vybraného kontejneru v zobrazení **Kontejnery.** Toto chování můžete řídit zakázáním kolekce pro konkrétní kontejner buď během nasazení clusteru Kubernetes, nebo po nastavení proměnné prostředí *AZMON_COLLECT_ENV*. Tato funkce je k dispozici ve verzi agenta – ciprod11292018 a vyšší.  

Chcete-li zakázat kolekci proměnných prostředí v novém nebo existujícím kontejneru, nastavte proměnnou **AZMON_COLLECT_ENV** s hodnotou **False** v konfiguračním souboru yaml nasazení Kubernetes. 

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

Spuštěním následujícího příkazu použijte změnu v jiných clusterech Kubernetes než Azure Red Hat OpenShift): `kubectl apply -f  <path to yaml file>`. Chcete-li upravit ConfigMap a použít tuto změnu pro clustery Azure Red Hat OpenShift, spusťte příkaz:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Tím otevřete výchozí textový editor. Po nastavení proměnné uložte soubor do editoru.

Chcete-li ověřit, že změna konfigurace se projevila, vyberte kontejner v zobrazení **Kontejnery** v nástroji Azure Monitor pro kontejnery a v panelu vlastností rozbalte **proměnné prostředí**.  V oddíle by měla být zobrazována pouze proměnná vytvořená dříve - **AZMON_COLLECT_ENV=FALSE**. Pro všechny ostatní kontejnery proměnné prostředí části by měl seznam všech proměnných prostředí zjištěné.

Chcete-li znovu povolit zjišťování proměnných prostředí, použijte stejný proces dříve a změňte `kubectl` hodnotu z **False** na **True**a potom znovu spusťte příkaz pro aktualizaci kontejneru.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Další kroky

Pokud při upgradu agenta narazíte na problémy, přečtěte si příručku pro [řešení potíží,](container-insights-troubleshoot.md) která vám poskytne podporu.
