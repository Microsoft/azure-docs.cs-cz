---
title: Doporučené postupy pro správu identity
titleSuffix: Azure Kubernetes Service
description: Seznamte se s doporučenými postupy operátora clusteru, jak spravovat ověřování a autorizaci clusterů ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 0e3569be769fcf70a65cbfee62a3b80a5abdc3b5
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668318"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Doporučené postupy pro ověřování a autorizaci ve službě Azure Kubernetes Service (AKS)

Při nasazování a údržbě clusterů ve službě Azure Kubernetes Service (AKS) je třeba implementovat způsoby správy přístupu k prostředkům a službám. Bez těchto ovládacích prvků mohou mít účty přístup k prostředkům a službám, které nepotřebují. Může být také obtížné sledovat, která sada pověření byla použita k provádění změn.

Tento článek osvědčených postupů se zaměřuje na to, jak může operátor clusteru spravovat přístup a identitu pro clustery AKS. V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Ověření uživatelů clusteru AKS pomocí služby Azure Active Directory
> * Řízení přístupu k prostředkům pomocí ovládacích prvků přístupu na základě rolí (RBAC)
> * Použití spravované identity k ověření pomocí jiných služeb

## <a name="use-azure-active-directory"></a>Použití Azure Active Directory

**Pokyny pro osvědčené postupy** – nasazení clusterů AKS s integrací Azure AD. Pomocí Azure AD centralizuje součást správy identit. Všechny změny stavu uživatelského účtu nebo skupiny jsou automaticky aktualizovány v přístupu ke clusteru AKS. Použití role nebo ClusterRoles a vazby, jak je popsáno v další části, k oboru uživatelů nebo skupin na nejmenší množství potřebných oprávnění.

Vývojáři a vlastníci aplikací clusteru Kubernetes potřebují přístup k různým prostředkům. Kubernetes neposkytuje řešení pro správu identit, které by řídilo, kteří uživatelé mohou pracovat s jakými prostředky. Místo toho obvykle integrujete cluster s existujícím řešením identity. Azure Active Directory (AD) poskytuje řešení pro správu identit připravená pro podniky a může se integrovat s clustery AKS.

S clustery integrované s Azure AD v AKS vytvoříte *role* nebo *role clusteru,* které definují přístupová oprávnění k prostředkům. Potom *svázat* role pro uživatele nebo skupiny z Azure AD. Tyto kubernetes ovládací prvky přístupu na základě rolí (RBAC) jsou popsány v další části. Integrace Azure AD a jak řídíte přístup k prostředkům lze zobrazit v následujícím diagramu:

![Ověřování na úrovni clusteru pro integraci služby Azure Active Directory pomocí Služby AKS](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. Vývojář se ověřuje pomocí Azure AD.
1. Koncový bod vystavování tokenu Azure AD vydává přístupový token.
1. Vývojář provede akci pomocí tokenu Azure AD, jako je například`kubectl create pod`
1. Kubernetes ověří token pomocí služby Azure Active Directory a načte členství vývojáře ve skupinách.
1. Kubernetes řízení přístupu na základě rolí (RBAC) a zásady clusteru jsou použity.
1. Žádost vývojáře je úspěšná nebo není založena na předchozím ověření členství ve skupině Azure AD a Kubernetes RBAC a zásad.

Pokud chcete vytvořit cluster AKS, který používá Azure AD, [přečtěte si článek Integrace Služby Azure Active Directory s AKS][aks-aad].

## <a name="use-role-based-access-controls-rbac"></a>Použití ovládacích prvků přístupu na základě rolí (RBAC)

**Pokyny pro osvědčené postupy** – pomocí Kubernetes RBAC definujte oprávnění, která mají uživatelé nebo skupiny k prostředkům v clusteru. Vytvořte role a vazby, které přiřazují nejmenší množství požadovaných oprávnění. Integrujte s Azure AD, aby se všechny změny stavu uživatele nebo členství ve skupině automaticky aktualizovaly a přístup k prostředkům clusteru byl aktuální.

V Kubernetes můžete poskytnout podrobné řízení přístupu k prostředkům v clusteru. Oprávnění lze definovat na úrovni clusteru nebo pro konkrétní obory názvů. Můžete definovat, jaké prostředky lze spravovat a s jakými oprávněními. Tyto role jsou pak použity pro uživatele nebo skupiny s vazbou. Další informace o *rolích*, *ClusterRoles*a *Vazby*najdete [v tématu možnosti přístupu a identity pro službu Azure Kubernetes Service (AKS).][aks-concepts-identity]

Jako příklad můžete vytvořit roli, která uděluje úplný přístup k prostředkům v oboru názvů s názvem *finance-app*, jak je znázorněno v následujícím příkladu manifestu YAML:

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

A RoleBinding je pak vytvořen, který váže vývojáře uživatele Azure *AD1\@contoso.com* roleBinding, jak je znázorněno v následujícím manifestu YAML:

```yaml
kind: RoleBinding
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

Když *\@developer1 contoso.com* je ověřena proti clusteru AKS, mají úplná oprávnění k prostředkům v oboru názvů finanční *aplikace.* Tímto způsobem logicky oddělit a řídit přístup k prostředkům. Kubernetes RBAC by se měl používat ve spojení s integrací Azure AD, jak je popsáno v předchozí části.

Postup použití skupin Azure AD k řízení přístupu k prostředkům Kubernetes pomocí RBAC najdete v [tématu Řízení přístupu k prostředkům clusteru pomocí ovládacích prvků přístupu založených na rolích a identit Azure Active Directory v AKS][azure-ad-rbac].

## <a name="use-pod-identities"></a>Použití identit podu

**Pokyny pro osvědčené postupy** – nepoužívejte pevná pověření v podech nebo iblizích kontejnerů, protože jsou ohroženy expozicí nebo zneužitím. Místo toho použijte pod identity automaticky požádat o přístup pomocí řešení centrální identity Azure AD. Pod identity je určen pro použití s linuxovými pody a image kontejnerů pouze.

Když pody potřebují přístup k jiným službám Azure, jako je Cosmos DB, Key Vault nebo Blob Storage, pod potřebuje přístupová pověření. Tato přístupová pověření mohou být definována pomocí bitové kopie kontejneru nebo vložena jako tajný klíč Kubernetes, ale je třeba je ručně vytvořit a přiřadit. Pověření jsou často znovu použity napříč pody a nejsou pravidelně otočeny.

Spravované identity pro prostředky Azure (aktuálně implementované jako přidružený projekt s otevřeným zdrojovým kódem AKS) umožňují automaticky žádat o přístup ke službám prostřednictvím Azure AD. Nedefinujete ručně pověření pro pody, místo toho požadují přístupový token v reálném čase a můžete jej použít pouze pro jejich přiřazené služby. V AKS jsou operátorem clusteru nasazeny dvě součásti, které umožňují podům používat spravované identity:

* **Server Identity správy uzlů (NMI)** je pod, který běží jako DaemonSet na každém uzlu v clusteru AKS. Server NMI naslouchá požadavkům podu na služby Azure.
* **Řadič spravované identity (MIC)** je centrální pod s oprávněními k dotazování na server rozhraní API Kubernetes a kontroluje mapování identit y Azure, které odpovídá podu.

Když pody požadují přístup ke službě Azure, pravidla sítě přesměrují provoz na server Identity správy uzlů (NMI). Server NMI identifikuje pody, které požadují přístup ke službám Azure na základě jejich vzdálené adresy, a dotazuje se spravovaného řadiče identity (MIC). MIC kontroluje mapování identit Azure v clusteru AKS a server NMI pak požaduje přístupový token z Azure Active Directory (AD) na základě mapování identit podu. Azure AD poskytuje přístup k serveru NMI, který se vrací do podu. Tento přístupový token může pod použít k vyžádání přístupu ke službám v Azure.

V následujícím příkladu vývojář vytvoří pod, který používá spravovanou identitu k vyžádání přístupu k instanci Azure SQL Server:

![Pod identity umožňují pod automaticky požádat o přístup k jiným službám](media/operator-best-practices-identity/pod-identities.png)

1. Operátor clusteru nejprve vytvoří účet služby, který lze použít k mapování identit, když pody požadují přístup ke službám.
1. Nmi server a MIC jsou nasazeny k přenosu všech pod požadavky na přístupové tokeny do Azure AD.
1. Vývojář nasadí pod se spravovanou identitou, která požaduje přístupový token prostřednictvím serveru NMI.
1. Token se vrátí do podu a slouží k přístupu k instanci Azure SQL Server.

> [!NOTE]
> Spravované pod identity je open source projekt a není podporován a technická podpora Azure.

Informace o identitách podu najdete [v tématu Identity služby Azure Active Directory pro aplikace Kubernetes][aad-pod-identity].

## <a name="next-steps"></a>Další kroky

Tento článek osvědčených postupů zaměřený na ověřování a autorizaci clusteru a prostředků. Chcete-li implementovat některé z těchto doporučených postupů, naleznete v následujících článcích:

* [Integrace služby Azure Active Directory s AKS][aks-aad]
* [Použití spravovaných identit pro prostředky Azure pomocí AKS][aad-pod-identity]

Další informace o operacích clusteru v AKS naleznete v následujících doporučených postupech:

* [Víceklientská architektura a izolace clusteru][aks-best-practices-cluster-isolation]
* [Základní funkce plánovače Kubernetes][aks-best-practices-scheduler]
* [Pokročilé funkce plánovače Kubernetes][aks-best-practices-advanced-scheduler]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: azure-ad-integration-cli.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[azure-ad-rbac]: azure-ad-rbac.md
