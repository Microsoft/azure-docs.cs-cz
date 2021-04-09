---
title: Jak spravovat agenta pro Container Insights | Microsoft Docs
description: Tento článek popisuje, jak spravovat nejběžnější úlohy údržby s kontejnerem Log Analytics agentem používaným službou Container Insights.
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: 2a0c32ef797a953eca794e16fe0ace5e967f339f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101713792"
---
# <a name="how-to-manage-the-container-insights-agent"></a>Jak spravovat agenta pro Container Insights

V kontejneru Insights se používá kontejnerové verze Log Analytics agenta pro Linux. Po počátečním nasazení jsou k dispozici rutinní nebo volitelné úkoly, které může být nutné provést během jejího životního cyklu. Tento článek podrobně popisuje, jak ručně upgradovat agenta a zakázat shromažďování proměnných prostředí z konkrétního kontejneru. 

## <a name="how-to-upgrade-the-container-insights-agent"></a>Postup upgradu agenta Container Insights

V kontejneru Insights se používá kontejnerové verze Log Analytics agenta pro Linux. Po vydání nové verze agenta se agent automaticky upgraduje na spravovaných clusterech Kubernetes hostovaných ve službě Azure Kubernetes Service (AKS) a v Azure Red Hat OpenShift verze 3. x. Pro [hybridní cluster Kubernetes](container-insights-hybrid-setup.md) a Azure Red Hat OpenShift verze 4. x není Agent spravovaný a vy budete muset agenta upgradovat ručně.

Pokud se upgrade agenta pro cluster hostovaný v AKS nebo Azure Red Hat OpenShift verze 3. x nepovede, Tento článek popisuje také Postup ručního upgradu agenta. Pokud chcete postupovat podle vydaných verzí, přečtěte si téma [oznámení o vydáních](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)

### <a name="upgrade-agent-on-aks-cluster"></a>Upgrade agenta v clusteru AKS

Proces upgradu agenta v clusterech AKS se skládá ze dvou přímých kroků. Prvním krokem je zakázat monitorování pomocí služby Container Insights pomocí Azure CLI. Postupujte podle kroků popsaných v článku [zakázání monitorování](container-insights-optout.md?#azure-cli) . Použití rozhraní příkazového řádku Azure umožňuje odebrat agenta z uzlů v clusteru, aniž by to mělo dopad na řešení a odpovídající data uložená v pracovním prostoru. 

>[!NOTE]
>Při provádění této aktivity údržby nejsou uzly v clusteru shromážděnými daty a zobrazení výkonu nezobrazuje data mezi časem odebrání agenta a instalací nové verze. 
>

Chcete-li nainstalovat novou verzi agenta, postupujte podle kroků popsaných v tématu [povolení monitorování pomocí rozhraní příkazového řádku Azure CLI](container-insights-enable-new-cluster.md#enable-using-azure-cli)k dokončení tohoto procesu.  

Po opětovném povolení monitorování může trvat přibližně 15 minut, než budete moct zobrazit aktualizované metriky stavu pro cluster. Pokud chcete ověřit, jestli se agent úspěšně Upgradoval, můžete:

* Spusťte příkaz: `kubectl get pod <omsagent-pod-name> -n kube-system -o=jsonpath='{.spec.containers[0].image}'` . Ve vráceném stavu si všimněte hodnoty v části **Image** pro omsagent v oddílu *Containers* výstupu.
* Na kartě **uzly** vyberte uzel clusteru a v podokně **vlastnosti** napravo si poznamenejte hodnotu v části **značka image agenta**.

Zobrazená verze agenta by měla odpovídat nejnovější verzi uvedené na stránce [Historie verzí](https://github.com/microsoft/docker-provider/tree/ci_feature_prod) .

### <a name="upgrade-agent-on-hybrid-kubernetes-cluster"></a>Upgrade agenta na hybridním clusteru Kubernetes

Proveďte následující kroky k upgradu agenta v clusteru Kubernetes se systémem:

* Samostatné spravované clustery Kubernetes hostované v Azure pomocí stroje AKS.
* Samostatné spravované clustery Kubernetes hostované na Azure Stack nebo místně pomocí AKS Engine.
* Red Hat OpenShift verze 4. x.

Pokud je pracovní prostor Log Analytics v komerčním Azure, spusťte následující příkaz:

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
```

Pokud je pracovní prostor Log Analytics v Azure Čína 21Vianet, spusťte následující příkaz:

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

Pokud je pracovní prostor Log Analytics ve vládě Azure USA, spusťte následující příkaz:

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

### <a name="upgrade-agent-on-azure-red-hat-openshift-v4"></a>Upgrade agenta na Azure Red Hat OpenShift v4

Proveďte následující kroky k upgradu agenta v clusteru Kubernetes běžícím na Azure Red Hat OpenShift verze 4. x. 

>[!NOTE]
>Azure Red Hat OpenShift verze 4. x podporuje spouštění jenom v komerčním cloudu Azure.
>

```console
curl -o upgrade-monitoring.sh -L https://aka.ms/upgrade-monitoring-bash-script
export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
bash upgrade-monitoring.sh --resource-id $ azureAroV4ClusterResourceId
```

Podrobné informace o použití instančního objektu s tímto příkazem najdete v tématu **použití instančního objektu** v tématu [povolení monitorování clusteru Kubernetes s povoleným](container-insights-enable-arc-enabled-clusters.md#enable-using-bash-script) vydaným Azure ARC.

### <a name="upgrade-agent-on-azure-arc-enabled-kubernetes"></a>Upgrade agenta na Kubernetes s povoleným rozšířením Azure ARC

Provedením následujícího příkazu Upgradujte agenta v clusteru Kubernetes s povoleným ARC Azure.

```console
curl -o upgrade-monitoring.sh -L https://aka.ms/upgrade-monitoring-bash-script
export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
bash upgrade-monitoring.sh --resource-id $azureArcClusterResourceId
```

Podrobné informace o použití instančního objektu s tímto příkazem najdete v tématu **použití instančního objektu** v tématu [povolení monitorování clusteru Kubernetes s povoleným](container-insights-enable-arc-enabled-clusters.md#enable-using-bash-script) vydaným Azure ARC.


## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Postup zakázání shromažďování proměnných prostředí v kontejneru

Container Insights shromažďuje proměnné prostředí z kontejnerů spuštěných v poli pod a prezentuje je v podokně vlastností vybraného kontejneru v zobrazení **kontejnerů** . Toto chování můžete řídit tak, že zakážete shromažďování pro konkrétní kontejner během nasazování clusteru Kubernetes nebo po nastavení proměnné prostředí *AZMON_COLLECT_ENV*. Tato funkce je k dispozici z verze agenta – ciprod11292018 a vyšší.  

Chcete-li zakázat shromažďování proměnných prostředí v novém nebo existujícím kontejneru, nastavte proměnnou **AZMON_COLLECT_ENV** s hodnotou **false** v konfiguračním souboru YAML nasazení Kubernetes. 

```yaml
- name: AZMON_COLLECT_ENV  
  value: "False"  
```

Spusťte následující příkaz, který aplikuje změnu na jiné clustery Kubernetes než Azure Red Hat OpenShift): `kubectl apply -f  <path to yaml file>` . Pokud chcete upravit ConfigMap a použít tuto změnu pro clustery Azure Red Hat OpenShift, spusťte příkaz:

```bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Tím se otevře výchozí textový editor. Po nastavení proměnné uložte soubor v editoru.

Chcete-li ověřit vliv změny konfigurace, vyberte kontejner v zobrazení **kontejnery** v části Container Insights a na panelu vlastností rozbalte položku **proměnné prostředí**.  V části by se měla zobrazit pouze dříve vytvořená proměnná- **AZMON_COLLECT_ENV = false**. Pro všechny ostatní kontejnery by oddíl proměnných prostředí měl vypsat všechny zjištěné proměnné prostředí.

Chcete-li znovu povolit zjišťování proměnných prostředí, použijte stejný proces dříve a změňte hodnotu z **false** na **true** a pak znovu spusťte `kubectl` příkaz pro aktualizaci kontejneru.  

```yaml
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Další kroky

Pokud při upgradu agenta dochází k potížím, přečtěte si [příručku pro řešení potíží](container-insights-troubleshoot.md) pro podporu.
