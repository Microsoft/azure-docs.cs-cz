---
title: Řešení potíží s Azure Monitorem pro kontejnery | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak můžete řešit a řešit problémy s Azure Monitor pro kontejnery.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 17a2817b320599b2aa2c331c354d316b9d864a32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75403377"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Poradce při potížích s Azure Monitorem pro kontejnery

Když nakonfigurujete monitorování clusteru služby Azure Kubernetes Service (AKS) s Azure Monitor pro kontejnery, může dojít k problému, který brání shromažďování dat nebo stavu vykazování. Tento článek podrobně popisuje některé běžné problémy a kroky řešení potíží.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Chyba autorizace během registrace nebo aktualizace
Při povolení Azure Monitor pro kontejnery nebo aktualizaci clusteru pro podporu shromažďování metrik, může se zobrazit chyba připomínající následující – *Klient <identity uživatele>' s id objektu '<uživatele objectId>' nemá oprávnění k provedení akce 'Microsoft.AuthorizationAssignments/write' přes obor*

Během procesu registrace nebo aktualizace se v prostředku clusteru pokusí o udělení přiřazení role **vydavatele metrik monitorování.** Uživatel, který iniciuje proces povolení azure monitoru pro kontejnery nebo aktualizaci pro podporu shromažďování metrik, musí mít přístup k oprávnění **Microsoft.Authorization/roleAssignments/write** v oboru prostředků clusteru AKS. K tomuto oprávnění mají přístup pouze členové předdefinované role **Vlastník** a **Správce přístupu uživatelů.** Pokud zásady zabezpečení vyžadují přiřazení oprávnění na úrovni podrobné úrovně, doporučujeme zobrazit [vlastní role](../../role-based-access-control/custom-roles.md) a přiřadit je uživatelům, kteří je potřebují. 

Tuto roli můžete také ručně udělit z portálu Azure provedením následujících kroků:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). 
2. Na webu Azure Portal klikněte v levém horním rohu na **Všechny služby**. V seznamu zdrojů zadejte **Příkaz Kubernetes**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Azure Kubernetes**.
3. V seznamu Kubernetesových klastrů vyberte jednu ze seznamu.
2. V levé nabídce klikněte na **Ovládací prvek přístupu (IAM).**
3. Výběrem **možnosti + Přidat** přidáte přiřazení role a vyberte roli **Vydavatel metrik monitorování metrik** a v poli **Vybrat** typ **AKS** filtrovat výsledky pouze na objekty služby clusterů definované v předplatném. Vyberte ten ze seznamu, který je specifický pro daný cluster.
4. Chcete-li dokončit přiřazení role, vyberte **uložit.** 

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>Azure Monitor pro kontejnery je povolený, ale nehlásí žádné informace
Pokud azure monitorování pro kontejnery je úspěšně povolena a nakonfigurována, ale nelze zobrazit informace o stavu nebo žádné výsledky jsou vráceny z dotazu protokolu, diagnostikovat problém pomocí následujících kroků: 

1. Zkontrolujte stav agenta spuštěním příkazu: 

    `kubectl get ds omsagent --namespace=kube-system`

    Výstup by se měl podobat následujícímu, což znamená, že byl správně nasazen:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Zkontrolujte stav nasazení pomocí verze agenta *06072018* nebo novějšípomocí příkazu:

    `kubectl get deployment omsagent-rs -n=kube-system`

    Výstup by se měl podobat následujícímu příkladu, což znamená, že byl správně nasazen:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Zkontrolujte stav podu a ověřte, zda je spuštěn pomocí příkazu:`kubectl get pods --namespace=kube-system`

    Výstup by se měl podobat následujícímu příkladu se stavem *Running* pro omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. Zkontrolujte protokoly agenta. Když kontejnerizovaného agenta nasadí, spustí rychlou kontrolu spuštěním příkazů OMI a zobrazí verzi agenta a zprostředkovatele. 

5. Chcete-li ověřit, zda byl agent úspěšně nasazen, spusťte příkaz:`kubectl logs omsagent-484hw --namespace=kube-system`

    Stav by se měl podobat následujícímu příkladu:

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

## <a name="error-messages"></a>Chybové zprávy

Níže uvedená tabulka shrnuje známé chyby, se kterými se můžete setkat při používání Azure Monitoru pro kontejnery.

| Chybové zprávy  | Akce |  
| ---- | --- |  
| Chybová zpráva`No data for selected filters`  | Vytvoření toku dat monitorování pro nově vytvořené clustery může nějakou dobu trvat. Vyčkejte alespoň 10 až 15 minut, než se data zobrazí ve vašem clusteru. |   
| Chybová zpráva`Error retrieving data` | Zatímco cluster služby Azure Kubernetes se nastavuje pro monitorování stavu a výkonu, nastaví se připojení mezi clusterem a pracovním prostorem Azure Log Analytics. Pracovní prostor Log Analytics se používá k ukládání všech dat monitorování pro váš cluster. K této chybě může dojít, pokud byl odstraněn pracovní prostor Analýzy protokolů. Zkontrolujte, jestli byl pracovní prostor odstraněn a pokud byl, budete muset znovu povolit monitorování clusteru pomocí Azure Monitor u kontejnerů a určit existující nebo vytvořit nový pracovní prostor. Chcete-li znovu povolit, budete muset [zakázat](container-insights-optout.md) monitorování pro cluster a [znovu povolit](container-insights-enable-new-cluster.md) Azure Monitor pro kontejnery. |  
| `Error retrieving data`po přidání Azure Monitor pro kontejnery prostřednictvím az aks cli | Když povolíte `az aks cli`monitorování pomocí , Azure Monitor pro kontejnery nemusí být správně nasazené. Zkontrolujte, zda je řešení nasazeno. Chcete-li to provést, přejděte do pracovního prostoru Log Analytics a zjistěte, zda je řešení k dispozici výběrem **řešení** z podokna na levé straně. Chcete-li tento problém vyřešit, budete muset znovu nasadit řešení podle [pokynů, jak nasadit Azure Monitor pro kontejnery](container-insights-onboard.md) |  

Chcete-li problém diagnostikovat, poskytli jsme skript pro řešení potíží, který je k dispozici [zde](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script).

## <a name="azure-monitor-for-containers-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>Azure Monitor pro kontejnery agent ReplicaSet pods nejsou naplánované v clusteru kubernetes jiného než Azure

Azure Monitor pro kontejnery agent ReplicaSet Pods má závislost na následující chod selektory na pracovní pracovník (nebo agent) uzly pro plánování:

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

Pokud vaše pracovní uzly nemají připojené popisky uzlů, agent ReplicaSet Pods nebude naplánovat. Pokyny k připojení štítku naleznete v části [Kubernetes, která přiřazují voliče štítků.](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/)

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>Grafy výkonu nezobrazují procesor nebo paměť uzlů a kontejnerů v clusteru mimo Azure

Azure Monitor pro kontejnery agent pods používá koncový bod cAdvisor na agenta uzlu ke shromažďování metriky výkonu. Ověřte, zda je kontejnerizovaný agent `cAdvisor port: 10255` v uzlu nakonfigurován tak, aby mohl být otevřen ve všech uzlech v clusteru za účelem shromažďování metrik výkonu.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-azure-monitor-for-containers"></a>Cluster Kubernetes, který není Azure, se v Azure Monitoru pro kontejnery nezobrazuje.

Chcete-li zobrazit cluster u jiného než Azure Kubernetes v Azure Monitor pro kontejnery, přístup pro čtení je vyžadováno v pracovním prostoru Log Analytics podporující tento přehled a na kontejnerinsights řešení prostředku **ContainerInsights *(pracovní prostor)***.

## <a name="next-steps"></a>Další kroky

Díky monitorování povoleno pro zachycení metriky stavu pro uzly clusteru AKS a pody, tyto metriky stavu jsou k dispozici na webu Azure Portal. Informace o tom, jak používat Azure Monitor pro kontejnery, najdete [v tématu Zobrazení stavu služby Azure Kubernetes](container-insights-analyze.md).
