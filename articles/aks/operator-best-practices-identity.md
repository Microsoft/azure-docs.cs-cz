---
title: Osvědčené postupy – operátor - Identity ve službě Azure Kubernetes služby (AKS)
description: Přečtěte si osvědčené postupy clusteru operátor jak spravovat ověřování a autorizace pro clustery ve službě Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: iainfou
ms.openlocfilehash: 478034d1c9f99f40a4827515433357c76235e9ee
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52428939"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro ověřování a autorizace ve službě Azure Kubernetes Service (AKS)

Jak nasadit a spravovat clustery ve službě Azure Kubernetes Service (AKS), budete muset implementovat způsoby, jak spravovat přístup k prostředkům a službám. Bez těchto ovládacích prvků účty mohou mít přístup k prostředkům a službám, které nepotřebují. Také může být obtížné sledovat, které sadu přihlašovacích údajů, které byly použity provádět změny.

Tento článek o osvědčených postupech, zaměřuje tom, jak můžete spravovat cluster operátor přístupu a identit pro AKS clustery. V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Ověřování uživatelů cluster AKS pomocí Azure Active Directory
> * Řízení přístupu k prostředkům pomocí řízení přístupu na základě rolí (RBAC)
> * Použití spravované identity pro ověřování s jinými službami

## <a name="use-azure-active-directory"></a>Použití Azure Active Directory

**Osvědčené postupy pro moduly** – AKS nasadit clustery pomocí integrace služby Azure AD. Používání služby Azure AD centralizuje správu komponentu identita. Všechny změny ve stavu účet nebo skupinu uživatelů se automaticky aktualizuje v přístup ke clusteru AKS. Pomocí rolí nebo ClusterRoles a vazby, jak je popsáno v další části, a oboru uživatele nebo skupiny na minimální množství potřebná oprávnění.

Vývojáři a vlastníci aplikace vašeho clusteru Kubernetes potřebují přístup k různým prostředkům. Kubernetes neposkytuje řešení pro správu identit na ovládací prvek, který mohou uživatelé komunikovat s prostředky. Místo toho je obvykle integrovat váš cluster s existující řešení identit. Azure Active Directory (AD) poskytuje řešení pro správu identit připraveno pro podniky a můžete integrovat s clustery AKS.

S Azure integrované s Active Directory clustery ve službě AKS, vytvoříte *role* nebo *ClusterRoles* , které definují oprávnění k přístupu k prostředkům. Potom *svázat* role pro uživatele nebo skupiny z Azure AD. Tyto prvky Kubernetes přístupu na základě role (RBAC) jsou popsány v následující části. Integrace služby Azure AD a jak řídit přístup k prostředkům můžete vidět v následujícím diagramu:

![Ověřování na úrovni clusteru integrace služby Azure Active Directory s AKS](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. Pro vývojáře se ověřuje pomocí Azure AD.
1. Koncový bod vystavení tokenu Azure AD vydá token přístupu.
1. Vývojář provede akci, například pomocí tokenu Azure AD `kubectl create pod`
1. Kubernetes ověří token se službou Azure Active Directory a načte členství ve skupinách pro vývojáře.
1. Kubernetes na základě rolí řízení přístupu (RBAC) a clusteru se použijí zásady.
1. Požadavek pro vývojáře je úspěšné, nebo není podle předchozího ověření členství ve skupině Azure AD a Kubernetes RBAC a zásad.

Vytvoření clusteru AKS, který používá Azure AD najdete v tématu [integrace Azure Active Directory s AKS][aks-aad].

## <a name="use-role-based-access-controls-rbac"></a>Použití řízení přístupu na základě rolí (RBAC)

**Osvědčené postupy pro moduly** – použití Kubernetes RBAC k definování, které uživatele nebo skupiny mají oprávnění k prostředkům v clusteru. Vytvoření role a vazby, které přiřazují nejmenší rozsah oprávnění nutných. Integrace s Azure AD tak, aby změnu stavu uživatele nebo členství ve skupině se automaticky aktualizuje a přístup k prostředkům clusteru je aktuální.

V systému Kubernetes můžete zadat podrobné řízení přístupu k prostředkům v clusteru. Oprávnění lze definovat na úrovni clusteru nebo na konkrétní obory názvů. Můžete definovat, jaké prostředky je možné spravovat a s jakým oprávněním. Tyto role jsou použitého k uživatelům nebo skupinám s vazbou. Další informace o *role*, *ClusterRoles*, a *vazby*, naleznete v tématu [možnosti přístupu a identit pro službu Azure Kubernetes Service (AKS)] [aks-concepts-identity].

Například můžete vytvořit Role, která uděluje plný přístup k prostředkům v oboru názvů s názvem *finanční aplikace*, jak je znázorněno v následujícím příkladu YAML manifestu:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role
  namespace: finance-app
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["*"]
```

RoleBinding se pak vytvoří, která vytvoří vazbu uživatele Azure AD *developer1@contoso.com* k RoleBinding, jak je znázorněno v následujícím YAML manifestu:

```yaml
ind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role-binding
  namespace: finance-app
subjects:
- kind: User
  name: developer1@contoso.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: finance-app-full-access-role
  apiGroup: rbac.authorization.k8s.io
```

Když *developer1@contoso.com* ověření pro cluster AKS, mají úplná oprávnění k prostředkům v *finanční aplikace* oboru názvů. V tomto, které logicky oddělená a ovládací prvek přístup k prostředkům. Kubernetes RBAC byste měli použít ve spojení s Azure AD – integrace, jak je popsáno v předchozí části.

## <a name="use-pod-identities"></a>Používat pod identity

**Osvědčené postupy pro moduly** – nepoužívejte pevně nastavené přihlašovací údaje v rámci podů nebo imagí kontejnerů, protože se riziku ohrožení nebo zneužití. Místo toho použijte identity podů automaticky zajistí vyžádání přístupu pomocí centrální řešení identit Azure AD.

Pokud podů potřebují mít přístup k jiným službám Azure, jako je například Cosmos DB, služby Key Vault nebo úložiště objektů Blob, pod potřebuje přihlašovací údaje pro přístup. Tyto přihlašovací údaje pro přístup může být definované s image kontejneru nebo vložený jako tajného kódu Kubernetes, ale je potřeba ručně vytvořit a přiřadit. Často přihlašovací údaje se využívají opakovaně v podů a nejsou pravidelně otočen.

Spravované identity pro prostředky Azure umožňují automaticky žádost o přístup ke službám prostřednictvím služby Azure AD. Nebudete ručně definovat údaje pro podů, místo toho se žádost o přístupový token v reálném čase a můžete použít pro přístup k jejich přiřazené služby. Ve službě AKS dvě součásti nasazené v clusteru operátor umožňující podů použití spravované identity:

* **Uzel správy identit (NMI) server** je pod, na kterém běží jako DaemonSet na každém uzlu v clusteru AKS. NMI server naslouchá požadavkům pod ke službám Azure.
* **Kontroler spravované Identity (MIC)** je centrální pod s oprávnění k dotazování na serveru Kubernetes API a kontroluje Azure identity mapování, která odpovídá pod.

Když podů žádat o přístup do služby Azure, pravidel sítě přesměrovat provoz na server uzlu správy identit (NMI). NMI server identifikuje podů, které vyžadují přístup ke službám Azure na základě jejich vzdálené adresy a dotazuje řadiče spravované Identity (MIC). MIC vyhledává mapování identit Azure v clusteru AKS a NMI server pak požadavky že na mapování identit podu na základě přístupového tokenu z Azure Active Directory (AD). Azure AD poskytuje přístup k serveru NMI, který je vrácen pod. Tento přístupový token lze pod pak požádat o přístup ke službám v Azure.

Vývojář v následujícím příkladu se vytvoří pod, který používá spravovanou identitu požádáte o přístup k instanci serveru SQL Azure:

![Pod identit umožňují podů automaticky požádáte o přístup k dalším službám](media/operator-best-practices-identity/pod-identities.png)

1. Operátor clusteru nejprve vytvoří účet služby, který můžete použít k mapování identit, když podů žádat o přístup ke službám.
1. NMI server a povinná kontrola úrovně Důvěryhodnosti jsou nasazené předat libovolný pod žádosti o tokeny přístupu ke službě Azure AD.
1. Vývojář nasadí pod s spravovanou identitu, která požaduje token přístupu přes NMI server.
1. Token, který je vrácen pod a používá pro přístup k instanci serveru SQL Azure.

Používat pod identity, najdete v článku [identit Azure Active Directory pro Kubernetes aplikace][aad-pod-identity].

## <a name="next-steps"></a>Další postup

Tento článek o osvědčených postupech, zaměřuje na ověřování a autorizace pro cluster a prostředky. K provedení některých těchto osvědčených postupů, naleznete v následujících článcích:

* [Integrace služby Azure Active Directory s AKS][aks-aad]
* [Použití spravované identity pro prostředky Azure s AKS][aad-pod-identity]

Další informace o operacích clusteru ve službě AKS najdete v následující osvědčené postupy:

* [Izolace více tenantů a clusteru][aks-best-practices-scheduler]
* [Základní funkce plánovače Kubernetes][aks-best-practices-scheduler]
* [Pokročilé funkce plánovače Kubernetes][aks-best-practices-advanced-scheduler]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: aad-integration.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
