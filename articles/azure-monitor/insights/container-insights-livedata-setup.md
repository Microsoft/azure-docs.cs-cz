---
title: Nastavení Azure Monitoru pro živá data kontejnerů (preview) | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak nastavit zobrazení kontejnerů v reálném čase (stdout/stderr) a události bez použití kubectl s Azure Monitor pro kontejnery.
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: f19071ca642cd229cbd7d49b4eab90c970672eee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275370"
---
# <a name="how-to-set-up-the-live-data-preview-feature"></a>Jak nastavit funkci Živá data (náhled)

Chcete-li zobrazit živá data (preview) pomocí Služby Azure Monitor pro kontejnery z clusterů Služby Azure Kubernetes (AKS), je třeba nakonfigurovat ověřování, aby udělovalo oprávnění k přístupu k vašim datům Kubernetes. Tato konfigurace zabezpečení umožňuje přístup k vašim datům v reálném čase prostřednictvím rozhraní API Kubernetes přímo na webu Azure Portal.

Tato funkce podporuje následující metody řízení přístupu k protokolům, událostem a metrikám:

- AKS bez povolení Kubernetes RBAC povoleno
- AKS povolena s autorizací Kubernetes RBAC
    - AKS nakonfigurovaný s ** [clusterem vazby role clusteruMonitoringUser](https://docs.microsoft.com/rest/api/aks/managedclusters/listclustermonitoringusercredentials?view=azurermps-5.2.0)**
- AKS povoleno s jedním přihlášením na základě služby Azure Active Directory (AD) SAML

Tyto pokyny vyžadují jak přístup pro správu ke clusteru Kubernetes, tak i konfiguraci použití služby Azure Active Directory (AD) pro ověřování uživatelů, přístup pro správu do Azure AD.  

Tento článek vysvětluje, jak nakonfigurovat ověřování pro řízení přístupu k funkci Živých dat (náhled) z clusteru:

- Řízení přístupu založené na rolích (RBAC) povolil cluster AKS
- Integrovaný cluster AKS azure active directory. 

>[!NOTE]
>Clustery AKS povolené jako [privátní clustery](https://azure.microsoft.com/updates/aks-private-cluster/) nejsou touto funkcí podporovány. Tato funkce závisí na přímém přístupu k rozhraní API Kubernetes prostřednictvím proxy serveru z vašeho prohlížeče. Povolení zabezpečení sítě blokovat Rozhraní API Kubernetes z tohoto proxy serveru bude blokovat tento provoz. 

>[!NOTE]
>Tato funkce je dostupná ve všech oblastech Azure, včetně Azure China. V současné době není k dispozici v Azure US Government.

## <a name="authentication-model"></a>Režim ověřování

Funkce Živých dat (preview) využívá rozhraní API Kubernetes, které je totožné s nástrojem `kubectl` příkazového řádku. Koncové body rozhraní API Kubernetes využívají certifikát podepsaný svým držitelem, který váš prohlížeč nebude moci ověřit. Tato funkce využívá interní proxy server k ověření certifikátu pomocí služby AKS a zajišťuje důvěryhodnost provozu.

Portál Azure vás vyzve k ověření přihlašovacích údajů pro cluster Azure Active Directory a přesměruje vás na nastavení registrace klienta během vytváření clusteru (a překonfigurovat v tomto článku). Toto chování je podobné procesu ověřování vyžadovanému společností `kubectl`. 

>[!NOTE]
>Autorizaci clusteru spravuje společnost Kubernetes a model zabezpečení, se kterým je nakonfigurován. Uživatelé, kteří přistupují k této funkci, vyžadují oprávnění ke `az aks get-credentials -n {your cluster name} -g {your resource group}`stažení konfigurace Kubernetes (*kubeconfig*), podobně jako běží . Tento konfigurační soubor obsahuje token autorizace a ověřování pro **roli uživatele clusteru služby Azure Kubernetes**, v případě clusterů s podporou Azure RBAC a AKS bez povolení autorizace RBAC. Obsahuje informace o Azure AD a podrobnosti registrace klienta při AKS je povolena s Azure Active Directory (AD) SAML založené jednotné přihlášení.

>[!IMPORTANT]
>Uživatelé těchto funkcí vyžaduje [Azure Kubernetes role uživatele clusteru](../../azure/role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role permissions) do clusteru za účelem `kubeconfig` stažení a použití této funkce. Uživatelé **nevyžadují** přístup přispěvatele ke clusteru k využití této funkce. 

## <a name="using-clustermonitoringuser-with-rbac-enabled-clusters"></a>Použití clusteruMonitoringUser s clustery s podporou RBAC

Chcete-li vyloučit potřebu použít další změny konfigurace, aby kubernetes role role **clusterUUživatel** přístup k funkci Živá data (náhled) po povolení autorizace [RBAC,](#configure-kubernetes-rbac-authorization) AKS přidal novou vazbu role clusteru Kubernetes s názvem **clusterMonitoringUser**. Tato vazba role clusteru má všechna potřebná oprávnění out-of-the-box pro přístup k rozhraní API Kubernetes a koncové body pro využití funkce živá data (náhled).

Chcete-li s tímto novým uživatelem využívat funkci Živých dat (preview), musíte být členem role [přispěvatele](../../role-based-access-control/built-in-roles.md#contributor) v prostředku clusteru AKS. Azure Monitor pro kontejnery, pokud je povolena, je nakonfigurovaný k ověřování pomocí tohoto uživatele ve výchozím nastavení. Pokud vazba role clustermonitoringuser v clusteru neexistuje, **clusterUser** se místo toho použije k ověřování.

AKS vydala tuto novou vazbu role v lednu 2020, takže clustery vytvořené před lednem 2020 ji nemají. Pokud máte cluster, který byl vytvořen před lednem 2020, nový **clusterMonitoringUser** lze přidat do existujícího clusteru provedením operace PUT v clusteru nebo provedením jakékoli jiné operace v clusteru tha provádí operaci PUT v clusteru, jako je například aktualizace verze clusteru.

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Cluster Kubernetes bez povoleného RBAC

Pokud máte cluster Kubernetes, který není nakonfigurovaný s autorizací Kubernetes RBAC nebo integrovaný s jedním přihlášením služby Azure AD, nemusíte postupovat podle těchto kroků. Důvodem je, že máte oprávnění správce ve výchozím nastavení v konfiguraci bez RBAC.

## <a name="configure-kubernetes-rbac-authorization"></a>Konfigurace autorizace RBAC Kubernetes

Když povolíte autorizaci Kubernetes RBAC, jsou využity dva uživatelé: **clusterUser** a **clusterAdmin** pro přístup k rozhraní API Kubernetes. To je podobné `az aks get-credentials -n {cluster_name} -g {rg_name}` jako běh bez možnosti správy. To znamená, že **clusterUser** musí být udělen přístup ke koncovým bodům v rozhraní API Kubernetes.

Následující příklad kroky ukazují, jak nakonfigurovat vazbu role clusteru z této šablony konfigurace yaml.

1. Zkopírujte a vložte soubor yaml a uložte jej jako LogReaderRBAC.yaml.  

    ```
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRole 
    metadata: 
       name: containerHealth-log-reader 
    rules: 
        - apiGroups: ["", "metrics.k8s.io", "extensions", "apps"] 
          resources: 
             - "pods/log" 
             - "events" 
             - "nodes" 
             - "pods" 
             - "deployments" 
             - "replicasets" 
          verbs: ["get", "list"] 
    --- 
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRoleBinding 
    metadata: 
       name: containerHealth-read-logs-global 
    roleRef: 
       kind: ClusterRole 
       name: containerHealth-log-reader 
       apiGroup: rbac.authorization.k8s.io 
    subjects: 
    - kind: User 
      name: clusterUser 
      apiGroup: rbac.authorization.k8s.io 
    ```

2. Chcete-li aktualizovat konfiguraci, `kubectl apply -f LogReaderRBAC.yaml`spusťte následující příkaz: .

>[!NOTE] 
> Pokud jste v clusteru `LogReaderRBAC.yaml` použili předchozí verzi souboru, aktualizujte ji zkopírováním a vložením nového kódu uvedeného v kroku 1 výše a pak spusťte příkaz uvedený v kroku 2, který jej použije te pro svůj cluster.

## <a name="configure-ad-integrated-authentication"></a>Konfigurace ověřování integrovaného ve službě AD 

Cluster AKS nakonfigurovaný pro použití služby Azure Active Directory (AD) pro ověřování uživatelů využívá přihlašovací údaje osoby, která k této funkci přistupuje. V této konfiguraci se můžete přihlásit ke clusteru AKS pomocí ověřovacího tokenu Azure AD.

Registrace klienta Azure AD musí být překonfigurována tak, aby portál Azure přesměroval stránky autorizace jako důvěryhodnou adresu URL přesměrování. Uživatelům z Azure AD je pak udělen přístup přímo ke stejným koncovým bodům rozhraní API Kubernetes prostřednictvím **clusterroles** a **clusterrolebindings**. 

Další informace o pokročilém nastavení zabezpečení v Kubernetes naleznete v [dokumentaci kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/). 

>[!NOTE]
>Pokud vytváříte nový cluster s podporou RBAC, [přečtěte si článek Integrace služby Azure Active Directory se službou Azure Kubernetes](../../aks/azure-ad-integration.md) a podle pokynů nakonfigurujte ověřování Azure AD. Během kroků k vytvoření klientské aplikace poznámka v této části zvýrazní dvě adresy URL přesměrování, které potřebujete vytvořit pro Azure Monitor pro kontejnery odpovídající těm, které jsou uvedeny v kroku 3 níže.

### <a name="client-registration-reconfiguration"></a>Změna konfigurace registrace klienta

1. Vyhledejte registraci klienta pro váš cluster Kubernetes ve službě Azure AD v části **Registrace > aplikací Azure na** webu Azure Portal.

2. V levém podokně vyberte **Ověřování.** 

3. Přidejte do tohoto seznamu dvě adresy URL přesměrování jako typy **webových** aplikací. První základní hodnota adresy `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` URL by měla být `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`a druhá základní hodnota adresy URL by měla být .

    >[!NOTE]
    >Pokud používáte tuto funkci v Azure China, první `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` základní hodnota adresy URL `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`by měla být a druhá základní hodnota adresy URL by měla být . 
    
4. Po registraci adres URL přesměrování vyberte v části **Implicitní udělení**možnosti **přístupových tokenů** a **tokenů ID** a uložte změny.

>[!NOTE]
>Konfigurace ověřování pomocí služby Azure Active Directory pro jednotné přihlašování lze provést pouze během počátečního nasazení nového clusteru AKS. Pro již nasazený cluster AKS nelze konfigurovat jednotné přihlašování.
  
>[!IMPORTANT]
>Pokud jste překonfigurovali Azure AD pro ověřování uživatelů pomocí aktualizovaného identifikátoru URI, zrušte mezipaměť prohlížeče, abyste zajistili, že se aktualizovaný ověřovací token stáhne a použije.

## <a name="grant-permission"></a>Udělit oprávnění

Každý účet Azure AD musí být uděleno oprávnění k příslušným api v Kubernetes pro přístup k funkci živá data (preview). Kroky k udělení účtu Služby Azure Active Directory jsou podobné krokům popsaným v části [ověřování Kubernetes RBAC.](#configure-kubernetes-rbac-authorization) Před použitím šablony konfigurace yaml do clusteru nahraďte **clusterUser** v části **ClusterRoleBinding** požadovaným uživatelem. 

>[!IMPORTANT]
>Pokud uživatel, který udělíte vazbu RBAC pro je ve stejném tenantovi Azure AD, přiřaďte oprávnění na základě userPrincipalName. Pokud je uživatel v jiném tenantovi Azure AD, dotaz na a použít vlastnost objectId.

Další nápovědu ke konfiguraci **clusteru ClusterRoleBinding**služby AKS naleznete v [tématu Vytvoření vazby RBAC](../../aks/azure-ad-integration-cli.md#create-rbac-binding).

## <a name="next-steps"></a>Další kroky

Nyní, když máte ověřování nastavení, můžete zobrazit [metriky](container-insights-livedata-metrics.md), [nasazení](container-insights-livedata-deployments.md)a [události a protokoly](container-insights-livedata-overview.md) v reálném čase z clusteru.
