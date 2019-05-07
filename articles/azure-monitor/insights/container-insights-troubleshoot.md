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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: magoedte
ms.openlocfilehash: 5f9fc128af4e89788e648fcfc238da300ff91724
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65068754"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Řešení potíží s Azure Monitor pro kontejnery

Při konfiguraci monitorování clusteru Azure Kubernetes Service (AKS) pomocí Azure monitoru pro kontejnery může dojít k potížím, brání ve sběru dat nebo hlásí stav. Tento článek podrobně popisuje některé běžné problémy a postup řešení potíží.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Chyba autorizace během operace registrace nebo aktualizace
Při povolování monitorování Azure pro kontejnery nebo aktualizaci clusteru pro podporu shromažďování metrik, může zobrazit chyba podobný tomuto - *klienta < identitu uživatele >' s objektem nemá id < uživatele objectId > oprávnění k provedení akce "Microsoft.Authorization/roleAssignments/write" rozsahu*

Během procesu registrace nebo aktualizace udělení **monitorování metrik vydavatele** dojde k pokusu o přiřazení role u prostředku clusteru. Uživatel zahajuje proces povolení monitorování Azure pro kontejnery nebo aktualizaci pro podporu shromažďování metrik musí mít přístup k **Microsoft.Authorization/roleAssignments/write** oprávnění v clusteru AKS prostředek oboru. Pouze členové **vlastníka** a **správce uživatelských přístupů** předdefinované role mají přístup k toto oprávnění. Pokud vaše zásady zabezpečení vyžadují přiřazení oprávnění na podrobné úrovni, doporučujeme, abyste si zobrazit [vlastní role](../../role-based-access-control/custom-roles.md) a přiřadit uživatelům, kteří ji potřebují. 

Tato role můžete udělit také ručně z portálu Azure portal provedením následujících kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 
2. Na webu Azure Portal klikněte v levém horním rohu na **Všechny služby**. V seznamu prostředků zadejte **Kubernetes**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Azure Kubernetes**.
3. V seznamu clustery Kubernetes vyberte ho ze seznamu.
2. Z nabídky na levé straně, klikněte na tlačítko **řízení přístupu (IAM)**.
3. Vyberte **+ přidat** přidat přiřazení role a vyberte **monitorování metrik vydavatele** role a v části **vyberte** zadejte **AKS** do filtrování výsledků na pouze clustery instanční definované v rámci předplatného. Vyberte ze seznamu, který je specifický pro daný cluster.
4. Vyberte **Uložit** k dokončení přiřazení role. 

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>Azure Monitor pro kontejnery je zapnutá, ale žádné informace o nevytvářejících sestavy
Pokud monitorování Azure pro kontejnery se úspěšně povolena a konfigurována, ale nelze zobrazit informace o stavu nebo žádné výsledky jsou vráceny z dotazů protokolu, Diagnostikujte problém pomocí následujících kroků: 

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

## <a name="error-messages"></a>Chybové zprávy

Následující tabulka shrnuje známých chyb, které můžete narazit při používání služby Azure Monitor pro kontejnery.

| Chybové zprávy  | Akce |  
| ---- | --- |  
| Chybová zpráva `No data for selected filters`  | Může trvat nějakou dobu vytvoření monitorování toku dat pro nově vytvořený clustery. Povolit alespoň 10 až 15 minut, než se data zobrazí pro váš cluster. |   
| Chybová zpráva `Error retrieving data` | Když je cluster Azure Kubenetes Service nastavení pro monitorování stavu a výkonu, se vytvoří připojení mezi clusterem a pracovního prostoru Azure Log Analytics. Pracovní prostor Log Analytics se používá k ukládání všech dat monitorování pro váš cluster. K této chybě může dojít, pokud byla odstraněna nebo ztráty pracovního prostoru Log Analytics. Zkontrolujte, zda je váš pracovní prostor dostupných kontrolou [spravovat přístup](../platform/manage-access.md#view-workspace-details). Pokud pracovní prostor chybí, je potřeba znovu povolte sledování vašeho clusteru pomocí Azure monitoru pro kontejnery. Pokud chcete znovu povolit, je potřeba [zakázat](container-insights-optout.md) monitorování pro cluster a [povolit](container-insights-enable-new-cluster.md) monitorování Azure pro kontejnery znovu. |  
| `Error retrieving data` Po přidání monitorování Azure pro kontejnery pomocí rozhraní cli az aks | Při povolení monitorování pomocí `az aks cli`, monitorování Azure pro kontejnery nemusí být správně připojili. Zkontrolujte, zda je řešení připojili. Chcete-li to provést, přejděte do pracovního prostoru Log Analytics a jestli řešení jsou dostupné tak, že vyberete **řešení** z podokna na levé straně. Chcete-li vyřešit tento problém, budete muset znovu nasadit řešení podle pokynů v [jak nasadit Azure Monitor pro kontejnery](container-insights-onboard.md) |  

Abychom mohli problém diagnostikovat, poskytujeme vám řešení potíží k dispozici skript [tady](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script).  

## <a name="next-steps"></a>Další postup
Pomocí monitorování povoleno zachycení stavu metriky pro uzly clusteru AKS a podů, jsou tyto metriky stavu k dispozici na webu Azure Portal. Naučte se používat Azure Monitor pro kontejnery, najdete v článku [zobrazení Azure Kubernetes Service health](container-insights-analyze.md).