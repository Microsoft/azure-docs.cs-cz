---
title: Jak spravovat agenta Azure Monitor for Containers | Microsoft Docs
description: Tento článek popisuje, jak spravovat nejběžnější úlohy údržby pomocí kontejnerového Log Analyticsho agenta používaného Azure Monitor for Containers.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 1a1f8d690979a846dbf5041999180221752acc0b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79275318"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>Správa Azure Monitor pro agenta kontejnerů

Azure Monitor for Containers používá kontejnerové verze Log Analytics agenta pro Linux. Po počátečním nasazení jsou k dispozici rutinní nebo volitelné úkoly, které může být nutné provést během jejího životního cyklu. Tento článek podrobně popisuje, jak ručně upgradovat agenta a zakázat shromažďování proměnných prostředí z konkrétního kontejneru. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>Postup upgradu agenta Azure Monitor for Containers

Azure Monitor for Containers používá kontejnerové verze Log Analytics agenta pro Linux. Po vydání nové verze agenta se agent automaticky upgraduje na spravovaných clusterech Kubernetes hostovaných ve službě Azure Kubernetes Service (AKS) a Azure Red Hat OpenShift. Pro [hybridní cluster Kubernetes](container-insights-hybrid-setup.md) se agent nespravuje a potřebujete ručně upgradovat agenta.

Pokud se upgrade agenta pro cluster hostovaný v AKS nepovede, Tento článek popisuje také Postup ručního upgradu agenta. Pokud chcete postupovat podle vydaných verzí, přečtěte si téma [oznámení o vydáních](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)

### <a name="upgrade-agent-on-monitored-kubernetes-cluster"></a>Upgrade agenta na monitorovaném clusteru Kubernetes

Proces upgradu agenta v clusterech (kromě Azure Red Hat OpenShift) se skládá ze dvou přímých kroků. Prvním krokem je zakázat monitorování pomocí Azure Monitor pro kontejnery pomocí Azure CLI. Postupujte podle kroků popsaných v článku [zakázání monitorování](container-insights-optout.md?#azure-cli) . Použití rozhraní příkazového řádku Azure umožňuje odebrat agenta z uzlů v clusteru, aniž by to mělo dopad na řešení a odpovídající data uložená v pracovním prostoru. 

>[!NOTE]
>Při provádění této aktivity údržby nejsou uzly v clusteru shromážděnými daty a zobrazení výkonu nezobrazuje data mezi časem odebrání agenta a instalací nové verze. 
>

Chcete-li nainstalovat novou verzi agenta, postupujte podle kroků popsaných v tématu [povolení monitorování pomocí rozhraní příkazového řádku Azure CLI](container-insights-enable-new-cluster.md#enable-using-azure-cli)k dokončení tohoto procesu.  

Po opětovném povolení monitorování může trvat přibližně 15 minut, než budete moct zobrazit aktualizované metriky stavu pro cluster. Pokud chcete ověřit, jestli se agent úspěšně Upgradoval, spusťte příkaz:`kubectl logs omsagent-484hw --namespace=kube-system`

Stav by měl vypadat podobně jako v následujícím příkladu, kde hodnota pro *OMI* a *omsagent* by měla odpovídat nejnovější verzi zadané v [historii vydání verze agenta](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).  

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

## <a name="upgrade-agent-on-hybrid-kubernetes-cluster"></a>Upgrade agenta na hybridním clusteru Kubernetes

Proces upgradu agenta v hostovaném clusteru Kubernetes, AKS Engine v Azure a Azure Stack se dá dokončit spuštěním tohoto příkazu:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
```

Pokud je pracovní prostor Log Analytics v Azure Čína, spusťte následující příkaz:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

Pokud je pracovní prostor Log Analytics ve vládě Azure USA, spusťte následující příkaz:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Postup zakázání shromažďování proměnných prostředí v kontejneru

Azure Monitor for Containers shromažďuje proměnné prostředí z kontejnerů spuštěných v poli pod a prezentuje je v podokně vlastností vybraného kontejneru v zobrazení **kontejnerů** . Toto chování můžete řídit tak, že zakážete shromažďování pro konkrétní kontejner během nasazování clusteru Kubernetes nebo po nastavení proměnné prostředí *AZMON_COLLECT_ENV*. Tato funkce je k dispozici z verze agenta – ciprod11292018 a vyšší.  

Chcete-li zakázat shromažďování proměnných prostředí v novém nebo existujícím kontejneru, nastavte proměnnou **AZMON_COLLECT_ENV** s hodnotou **false** v konfiguračním souboru YAML nasazení Kubernetes. 

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

Spusťte následující příkaz, který aplikuje změnu na jiné clustery Kubernetes než Azure Red Hat OpenShift) `kubectl apply -f  <path to yaml file>`:. Pokud chcete upravit ConfigMap a použít tuto změnu pro clustery Azure Red Hat OpenShift, spusťte příkaz:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Tím se otevře výchozí textový editor. Po nastavení proměnné uložte soubor v editoru.

Chcete-li ověřit vliv změny konfigurace, vyberte kontejner v zobrazení **kontejnery** v části Azure monitor for Containers a na panelu vlastností rozbalte položku **proměnné prostředí**.  V části by se měla zobrazit pouze dříve vytvořená proměnná- **AZMON_COLLECT_ENV = false**. Pro všechny ostatní kontejnery by oddíl proměnných prostředí měl vypsat všechny zjištěné proměnné prostředí.

Chcete-li znovu povolit zjišťování proměnných prostředí, použijte stejný proces dříve a změňte hodnotu z **false** na **true**a pak znovu spusťte `kubectl` příkaz pro aktualizaci kontejneru.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Další kroky

Pokud při upgradu agenta dochází k potížím, přečtěte si [příručku pro řešení potíží](container-insights-troubleshoot.md) pro podporu.
