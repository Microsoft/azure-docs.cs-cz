---
title: Řešení potíží se službou Container Insights | Microsoft Docs
description: Tento článek popisuje, jak můžete řešit problémy a řešit problémy se službou Container Insights.
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: b7618e9073308da67a8e17c82375a0f05925a542
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627111"
---
# <a name="troubleshooting-container-insights"></a>Řešení potíží s kontejnerem Insights

Když konfigurujete monitorování clusteru Azure Kubernetes Service (AKS) s využitím služby Container Insights, může dojít k potížím s ochranou stavu shromažďování dat nebo vytváření sestav. Tento článek podrobně popisuje některé běžné problémy a postup řešení potíží.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Chyba autorizace během operace onboardingu nebo aktualizace

Když povolíte službu Container Insights nebo aktualizujete cluster tak, aby podporoval shromažďování metrik, může se zobrazit chybová zpráva podobná následujícímu: *> Identity <uživatele s ID objektu <uživatele objectId> nemá autorizaci k provedení akce "Microsoft. Authorization/roleAssignments/Write" nad oborem* .

Při připojování nebo aktualizaci se u prostředku clusteru pokusy o přiřazení role **vydavatele metrik monitorování** . Uživatel iniciující proces pro povolení služby Container Insights nebo aktualizace pro podporu kolekce metrik musí mít přístup k oprávněním **Microsoft. Authorization/roleAssignments/Write** v oboru prostředků clusteru AKS. Přístup k tomuto oprávnění mají udělit jenom členové předdefinovaných rolí **vlastník** a **Správce přístupu uživatelů** . Pokud vaše zásady zabezpečení vyžadují přiřazení podrobných oprávnění na úrovni, doporučujeme zobrazit [vlastní role](../../role-based-access-control/custom-roles.md) a přiřadit je uživatelům, kteří je potřebují.

Tuto roli můžete také ručně udělit z Azure Portal provedením následujících kroků:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Na webu Azure Portal klikněte v levém horním rohu na **Všechny služby**. V seznamu prostředků zadejte **Kubernetes**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Azure Kubernetes**.
3. V seznamu clusterů Kubernetes vyberte jeden ze seznamu.
2. V nabídce na levé straně klikněte na **řízení přístupu (IAM)**.
3. Vyberte **+ Přidat** , chcete-li přidat přiřazení role a vybrat roli **vydavatele metrik monitorování** a v poli **Vybrat** **AKS** zadejte příkaz pro filtrování výsledků jenom v clusterových objektech definovaných v předplatném. Vyberte jednu ze seznamu, která je specifická pro daný cluster.
4. Kliknutím na **Uložit** dokončete přiřazení role.

## <a name="container-insights-is-enabled-but-not-reporting-any-information"></a>Je povolený kontejner Insights, ale neoznamuje žádné informace.

Pokud je služba Container Insights úspěšně povolená a nakonfigurovaná, ale nemůžete zobrazit informace o stavu nebo nejsou vráceny žádné výsledky z dotazu protokolu, Diagnostikujte problém pomocí následujících kroků:

1. Ověřte stav agenta spuštěním příkazu:

    `kubectl get ds omsagent --namespace=kube-system`

    Výstup by měl vypadat podobně jako v následujícím příkladu, což znamená, že byl správně nasazen:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```
2. Pokud máte uzly Windows serveru, zkontrolujte stav agenta spuštěním příkazu:

    `kubectl get ds omsagent-win --namespace=kube-system`

    Výstup by měl vypadat podobně jako v následujícím příkladu, což znamená, že byl správně nasazen:

    ```
    User@aksuser:~$ kubectl get ds omsagent-win --namespace=kube-system
    NAME                   DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                   AGE
    omsagent-win           2         2         2         2            2           beta.kubernetes.io/os=windows   1d
    ```
3. Pomocí příkazu ověřte stav nasazení pomocí agenta verze *06072018* nebo novějšího:

    `kubectl get deployment omsagent-rs -n=kube-system`

    Výstup by měl vypadat podobně jako v následujícím příkladu, což znamená, že byl správně nasazen:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

4. Zkontrolujte stav pod, abyste ověřili, že je spuštěný, pomocí příkazu: `kubectl get pods --namespace=kube-system`

    Výstup by měl vypadat jako v následujícím příkladu se stavem *spuštěným* pro omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system
    NAME                                READY     STATUS    RESTARTS   AGE
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d
    omsagent-484hw                      1/1       Running   0          1d
    omsagent-fkq7g                      1/1       Running   0          1d
    omsagent-win-6drwq                  1/1       Running   0          1d
    ```

## <a name="error-messages"></a>Chybové zprávy

Následující tabulka shrnuje známé chyby, se kterými se můžete setkat při používání kontejneru Insights.

| Chybové zprávy  | Akce |
| ---- | --- |
| Chybová zpráva `No data for selected filters`  | Vytvoření toku dat monitorování pro nově vytvořené clustery může nějakou dobu trvat. Pro zobrazení dat pro váš cluster povolte aspoň 10 až 15 minut. |
| Chybová zpráva `Error retrieving data` | I když je cluster služby Azure Kubernetes nastaven pro monitorování stavu a výkonu, připojení mezi clusterem a pracovním prostorem Azure Log Analytics je navázáno. Pracovní prostor Log Analytics slouží k ukládání všech dat monitorování pro váš cluster. K této chybě může dojít, když byl pracovní prostor Log Analytics odstraněn. Ověřte, jestli se pracovní prostor odstranil, a pokud to bylo, budete muset znovu povolit monitorování clusteru pomocí služby Container Insights a zadat existující nebo vytvořit nový pracovní prostor. Pokud ho chcete znovu povolit, budete muset [Zakázat](container-insights-optout.md) monitorování clusteru a znovu [Povolit](container-insights-enable-new-cluster.md) službu Container Insights. |
| `Error retrieving data` Po přidání kontejneru Insights prostřednictvím AZ AKS CLI | Pokud povolíte monitorování pomocí `az aks cli` , nemusí být kontejnerové přehledy správně nasazené. Ověřte, zda je řešení nasazeno. Pokud to chcete ověřit, přejděte do pracovního prostoru Log Analytics a podívejte se, jestli je řešení dostupné, a to tak, že v podokně na levé straně vyberete **řešení** . Pokud chcete tento problém vyřešit, budete muset řešení znovu nasadit podle pokynů, [Jak nasadit službu Container Insights](container-insights-onboard.md) . |

Abychom vám pomohli diagnostikovat problém, máme k dispozici [skript pro odstraňování potíží](https://github.com/microsoft/Docker-Provider/tree/ci_dev/scripts/troubleshoot).

## <a name="container-insights-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>ReplicaSetí agenta kontejneru Insights se neplánují na Kubernetes cluster mimo Azure.

ReplicaSet lusků agenta kontejnerových informací má závislost na následujících selektorech uzlů v uzlech pracovního procesu (nebo agenta) pro plánování:

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

Pokud k vašim pracovním uzlům nejsou připojené popisky uzlů, pak se agent ReplicaSet neplánuje. Pokyny k připojení popisku najdete v tématu [Kubernetes Assign pro selektory Label](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) .

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>Grafy výkonu nezobrazují PROCESORy nebo paměť uzlů a kontejnerů v clusteru mimo Azure.

Lusky agentů Container Insights používají koncový bod cAdvisor na agentovi uzlu ke shromáždění metrik výkonu. Ověřte, že je agent s kontejnerem na uzlu nakonfigurovaný tak, aby umožňoval `cAdvisor port: 10255` jeho otevírání na všech uzlech v clusteru za účelem shromažďování metrik výkonu.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-container-insights"></a>V kontejneru Insights se nezobrazuje cluster s Kubernetes, který není Azure.

Pokud chcete zobrazit cluster Kubernetes mimo Azure ve službě Container Insights, vyžaduje se v pracovním prostoru Log Analytics přístup pro čtení, který podporuje tento přehled a v tématu ContainerInsights prostředků řešení Container Insights **(*pracovní prostor*)**.

## <a name="metrics-arent-being-collected"></a>Metriky se neshromažďují.

1. Ověřte, že cluster je v [podporované oblasti pro vlastní metriky](../essentials/metrics-custom-overview.md#supported-regions).

2. Pomocí následujícího příkazu CLI ověřte, zda existuje přiřazení role **vydavatele metrik monitorování** :

    ``` azurecli
    az role assignment list --assignee "SP/UserassignedMSI for omsagent" --scope "/subscriptions/<subid>/resourcegroups/<RG>/providers/Microsoft.ContainerService/managedClusters/<clustername>" --role "Monitoring Metrics Publisher"
    ```
    U clusterů s MSI se ID klienta přiřazené uživateli pro omsagent mění pokaždé, když je monitorování povolené a zakázané, takže přiřazení role by mělo existovat na aktuálním ID klienta MSI. 

3. Pro clustery s povolenou identitou Azure Active Directory pod a pomocí MSI:

   - Ověřte, zda je požadovaná jmenovka **Kubernetes.Azure.com/ManagedBy: AKS**  k dispozici v omsagent luskech pomocí následujícího příkazu:

        `kubectl get pods --show-labels -n kube-system | grep omsagent`

    - Ověřte, zda jsou výjimky povoleny, pokud je povolena identita pod pomocí jedné z podporovaných metod na adrese https://github.com/Azure/aad-pod-identity#1-deploy-aad-pod-identity .

        Spusťte následující příkaz, který ověří:

        `kubectl get AzurePodIdentityException -A -o yaml`

        Měl by se zobrazit výstup podobný následujícímu:

        ```
        apiVersion: "aadpodidentity.k8s.io/v1"
        kind: AzurePodIdentityException
        metadata:
        name: mic-exception
        namespace: default
        spec:
        podLabels:
        app: mic
        component: mic
        ---
        apiVersion: "aadpodidentity.k8s.io/v1"
        kind: AzurePodIdentityException
        metadata:
        name: aks-addon-exception
        namespace: kube-system
        spec:
        podLabels:
        kubernetes.azure.com/managedby: aks
        ```



## <a name="next-steps"></a>Další kroky

Díky monitorování, které povoluje zachycení metrik stavu pro uzly clusteru AKS a lusky, jsou tyto metriky stavu dostupné v Azure Portal. Informace o tom, jak používat službu Container Insights, najdete v tématu [zobrazení stavu služby Azure Kubernetes](container-insights-analyze.md).
