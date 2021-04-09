---
title: Nastavte živá data služby Container Insights (Preview) | Microsoft Docs
description: Tento článek popisuje, jak nastavit zobrazení protokolů kontejnerů v reálném čase (stdout/stderr) a událostí bez použití kubectl se službou Container Insights.
ms.topic: conceptual
ms.date: 01/08/2020
ms.custom: references_regions
ms.openlocfilehash: 4302bdbb3d71c890f7fb0cfb82ab5f8d5aecbd43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101713775"
---
# <a name="how-to-set-up-the-live-data-preview-feature"></a>Jak nastavit funkci živých dat (Preview)

Chcete-li zobrazit živá data (Preview) se službou Container Insights z clusterů Azure Kubernetes Service (AKS), je nutné nakonfigurovat ověřování pro udělení oprávnění k přístupu k datům Kubernetes. Tato konfigurace zabezpečení umožňuje přístup k datům v reálném čase prostřednictvím rozhraní API Kubernetes přímo v Azure Portal.

Tato funkce podporuje následující metody řízení přístupu k protokolům, událostem a metrikám:

- AKS bez povoleného ověřování RBAC Kubernetes
- AKS povolený s autorizací Kubernetes RBAC
    - AKS nakonfigurovaný s ClusterMonitoringUser vazby role clusteru **[](/rest/api/aks/managedclusters/listclustermonitoringusercredentials)**
- AKS povolený pomocí jednotného přihlašování založené na Azure Active Directory (AD) založeného na SAML

Tyto pokyny vyžadují přístup pro správu ke clusteru Kubernetes a pokud se konfigurace používá Azure Active Directory (AD) pro ověřování uživatelů, přístup pro správu k Azure AD.

Tento článek vysvětluje, jak nakonfigurovat ověřování pro řízení přístupu k funkci živá data (Preview) z clusteru:

- Kubernetes cluster AKS s povoleným řízením přístupu na základě role (Kubernetes RBAC)
- Azure Active Directory integrovaný cluster AKS.


## <a name="authentication-model"></a>Režim ověřování

Funkce Live data (Preview) využívá rozhraní Kubernetes API, které se shoduje s `kubectl` nástrojem příkazového řádku. Koncové body rozhraní API Kubernetes využívají certifikát podepsaný svým držitelem, který se nedá ověřit v prohlížeči. Tato funkce využívá interní proxy server k ověření certifikátu se službou AKS, která zajišťuje důvěryhodnost provozu.

Azure Portal vás vyzve k ověření přihlašovacích údajů pro cluster Azure Active Directory a přesměrování vás na nastavení registrace klienta během vytváření clusteru (a v tomto článku znovu nakonfigurované). Toto chování je podobné procesu ověřování, který vyžaduje `kubectl` .

>[!NOTE]
>Autorizaci ke clusteru spravuje Kubernetes a model zabezpečení, se kterým se konfiguruje. Uživatelé, kteří mají přístup k této funkci, vyžadují oprávnění ke stažení konfigurace Kubernetes (*kubeconfig*), podobně jako je spuštěná `az aks get-credentials -n {your cluster name} -g {your resource group}` . Tento konfigurační soubor obsahuje token pro autorizaci a ověření pro **roli uživatele clusteru služby Azure Kubernetes** v případě, že jsou clustery Azure RBAC-Enabled a AKS bez povoleného ověřování RBAC. Obsahuje informace o Azure AD a registrační registraci klienta, pokud je AKS povoleno pomocí jednotného přihlašování založeného na SAML Azure Active Directory (AD).

>[!IMPORTANT]
>Uživatelé této funkce vyžadují ke clusteru [roli uživatele clusteru Azure Kubernetes](../../role-based-access-control/built-in-roles.md) , aby mohli stáhnout `kubeconfig` a používat tuto funkci. K využití této funkce uživatelé **nevyžadují přístup** Přispěvatel ke clusteru.

## <a name="using-clustermonitoringuser-with-kubernetes-rbac-enabled-clusters"></a>Použití clusterMonitoringUser s clustery s povolenými Kubernetes RBAC

Aby se odstranila nutnost použít další změny konfigurace, aby Kubernetes role uživatele **clusterUser** přístup k funkci živá data (Preview) po [Povolení autorizace KUBERNETES RBAC](#configure-kubernetes-rbac-authorization) , AKS se přidala nová vazba role clusteru Kubernetes s názvem **clusterMonitoringUser**. Pro přístup k rozhraní Kubernetes API a koncovým bodům pro použití funkce živá data (Preview) má tato vazba role clusteru všechna potřebná oprávnění.

Aby bylo možné používat funkci živá data (Preview) s tímto novým uživatelem, musíte být členem role uživatele nebo [přispěvatele](../../role-based-access-control/built-in-roles.md#contributor) [clusteru služby Azure Kubernetes](../../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) v prostředku clusteru AKS. Služba Container Insights, pokud je povolená, je nakonfigurovaná k ověřování pomocí clusterMonitoringUser ve výchozím nastavení. Pokud vazba role clusterMonitoringUser neexistuje v clusteru, místo toho se použije **clusterUser** pro ověřování. Přispěvatel vám poskytne přístup k clusterMonitoringUser (pokud existuje) a uživatel clusteru služby Azure Kuberenetes vám poskytne přístup k clusterUser. Kterákoli z těchto dvou rolí poskytuje dostatečný přístup k použití této funkce.

AKS vydal tuto novou vazbu role v lednu 2020, takže clustery vytvořené před lednem 2020 je neobsahují. Pokud máte cluster vytvořený před lednem 2020, můžete nový **clusterMonitoringUser** přidat do existujícího clusteru provedením operace Put v clusteru nebo provedením jakékoli jiné operace v clusteru, který provádí operaci Put v clusteru, jako je například aktualizace verze clusteru.

## <a name="kubernetes-cluster-without-kubernetes-rbac-enabled"></a>Cluster Kubernetes bez povoleného Kubernetes RBAC

Pokud máte cluster Kubernetes, který není nakonfigurovaný s autorizací Kubernetes RBAC nebo se integruje s jednotným přihlašováním k Azure AD, nemusíte postupovat podle těchto kroků. Důvodem je to, že ve výchozím nastavení máte v konfiguraci jiného typu než RBAC oprávnění správce.

## <a name="configure-kubernetes-rbac-authorization"></a>Konfigurace autorizace RBAC Kubernetes

Když povolíte autorizaci Kubernetes RBAC, používají se dva uživatelé: **clusterUser** a **clusterAdmin** pro přístup k rozhraní Kubernetes API. To je podobné jako při spuštění `az aks get-credentials -n {cluster_name} -g {rg_name}` bez možnosti správy. To znamená, že **clusterUser** musí mít udělen přístup k koncovým bodům v rozhraní Kubernetes API.

Následující příklady kroků ukazují, jak nakonfigurovat vazbu role clusteru z této šablony konfigurace YAML.

1. Zkopírujte a vložte soubor YAML a uložte ho jako LogReaderRBAC. yaml.

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

2. Pokud chcete aktualizovat konfiguraci, spusťte následující příkaz: `kubectl apply -f LogReaderRBAC.yaml` .

>[!NOTE]
> Pokud jste v clusteru použili předchozí verzi `LogReaderRBAC.yaml` souboru, aktualizujte ji zkopírováním a vložením nového kódu uvedeného v kroku 1 výše a potom spuštěním příkazu zobrazeného v kroku 2 ho použijte pro svůj cluster.

## <a name="configure-ad-integrated-authentication"></a>Konfigurace ověřování integrovaného se službou AD

Cluster AKS nakonfigurovaný k použití Azure Active Directory (AD) pro ověřování uživatelů využívá přihlašovací údaje osoby, která přistupuje k této funkci. V této konfiguraci se můžete přihlásit ke clusteru AKS pomocí ověřovacího tokenu Azure AD.

Registrace klienta Azure AD musí být znovu nakonfigurovaná, aby Azure Portal mohl přesměrovat autorizační stránky jako důvěryhodnou adresu URL pro přesměrování. Uživatelům z Azure AD se pak udělí přímý přístup ke stejným koncovým bodům rozhraní Kubernetes API prostřednictvím **ClusterRoles** a **ClusterRoleBindings**.

Další informace o pokročilém nastavení zabezpečení v Kubernetes najdete v [dokumentaci k Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/).

>[!NOTE]
>Pokud vytváříte nový cluster s podporou RBAC Kubernetes, přečtěte si téma věnované [integraci Azure Active Directory se službou Azure Kubernetes](../../aks/azure-ad-integration-cli.md) a postupujte podle pokynů ke konfiguraci ověřování Azure AD. Všimněte si, že během postupu vytvoření klientské aplikace se v této části zvýrazní dvě adresy URL pro přesměrování, které je potřeba vytvořit pro službu Container Insights, která se shoduje s hodnotami uvedenými v kroku 3 níže.

### <a name="client-registration-reconfiguration"></a>Konfigurace registrace klienta

1. V části **Azure Active Directory > registrace aplikací** v Azure Portal vyhledejte registraci klienta pro váš cluster Kubernetes ve službě Azure AD.

2. V levém podokně vyberte **ověřování** .

3. Do tohoto seznamu přidejte dvě adresy URL pro přesměrování jako typy **webových** aplikací. První základní hodnota URL by měla být `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` a druhá základní hodnota URL by měla být `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` .

    >[!NOTE]
    >Pokud tuto funkci používáte v Azure Čína, měla by první základní hodnota URL být `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` a druhá základní hodnota URL by měla být `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` .

4. Po registraci adres URL pro přesměrování v části **implicitní udělení** vyberte možnost **přístupové tokeny** a **tokeny ID** a uložte provedené změny.

>[!NOTE]
>Konfigurace ověřování pomocí Azure Active Directory pro jednotné přihlašování se dá provést jenom při počátečním nasazení nového clusteru AKS. Nemůžete nakonfigurovat jednotné přihlašování pro cluster AKS, který je už nasazený.

>[!IMPORTANT]
>Pokud jste překonfigurovali službu Azure AD pro ověřování uživatelů pomocí aktualizovaného identifikátoru URI, vymažte mezipaměť prohlížeče, aby se zajistilo stažení a použití aktualizovaného ověřovacího tokenu.

## <a name="grant-permission"></a>Udělit oprávnění

Aby bylo možné získat přístup k funkci živá data (Preview), musí mít každý účet Azure AD udělené oprávnění k příslušným rozhraním API v Kubernetes. Postup pro udělení Azure Active Directory účtu se podobá postupu popsanému v části [ověřování RBAC Kubernetes](#configure-kubernetes-rbac-authorization) . Než použijete šablonu konfigurace YAML pro váš cluster, nahraďte **clusterUser** v rámci **ClusterRoleBinding** požadovaným uživatelem.

>[!IMPORTANT]
>Pokud je uživateli, kterému udělíte vazbu Kubernetes RBAC, ve stejném tenantovi Azure AD, přiřaďte oprávnění na základě třídy userPrincipalName. Pokud je uživatel v jiném tenantovi služby Azure AD, dotaz na a použijte vlastnost objectId.

Další nápovědu ke konfiguraci **ClusterRoleBinding** clusteru AKS najdete v tématu [vytvoření vazby Kubernetes RBAC](../../aks/azure-ad-integration-cli.md#create-kubernetes-rbac-binding).

## <a name="next-steps"></a>Další kroky

Teď, když máte nastavené ověřování, můžete z clusteru zobrazit [metriky](container-insights-livedata-metrics.md), [nasazení](container-insights-livedata-deployments.md)a [události a protokoly](container-insights-livedata-overview.md) v reálném čase.
