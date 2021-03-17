---
title: Osvědčené postupy pro správu identity
titleSuffix: Azure Kubernetes Service
description: Seznamte se s osvědčenými postupy pro postupy, jak spravovat ověřování a autorizaci pro clustery ve službě Azure Kubernetes (AKS).
services: container-service
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 8e0c7324f5b73b3a2ac5e5fd6fa256202035077a
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790965"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro ověřování a autorizaci ve službě Azure Kubernetes (AKS)

Při nasazení a údržbě clusterů ve službě Azure Kubernetes Service (AKS) je nutné implementovat způsoby, jak spravovat přístup k prostředkům a službám. Bez těchto ovládacích prvků mohou mít účty přístup k prostředkům a službám, které nepotřebují. Může také být obtížné sledovat, kterou sadu přihlašovacích údajů byly použity k provedení změn.

Tento článek o osvědčených postupech se zaměřuje na to, jak operátor clusteru může spravovat přístup a identitu pro clustery AKS. V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Ověřování uživatelů clusteru AKS pomocí Azure Active Directory
> * Řízení přístupu k prostředkům pomocí Kubernetes řízení přístupu založeného na rolích (Kubernetes RBAC)
> * Využijte Azure RBAC k podrobnému řízení přístupu k AKS prostředku a škálování Kubernetes API a také ke kubeconfig.
> * Použití spravované identity k ověřování lusků s ostatními službami

## <a name="use-azure-active-directory"></a>Použití Azure Active Directory

**Doprovodné materiály k osvědčeným postupům** – nasazení clusterů AKS s integrací služby Azure AD. Pomocí Azure AD se soustřeďuje na součást správy identit. Všechny změny v uživatelském účtu nebo skupině se automaticky aktualizují v rámci přístupu ke clusteru AKS. Použijte role nebo ClusterRoles a vazby, jak je popsáno v další části, k určení oboru uživatelů nebo skupin na nejnižší množství potřebných oprávnění.

Vývojáři a vlastníci aplikací Kubernetes clusteru potřebují přístup k různým prostředkům. Kubernetes neposkytuje řešení pro správu identit, které řídí, kteří uživatelé můžou pracovat s prostředky. Místo toho se cluster obvykle integruje s existujícím řešením identity. Azure Active Directory (AD) poskytuje řešení pro správu identit připravené na podnik a může se integrovat s clustery AKS.

Clustery s integrovanými službami Azure AD v AKS vám umožní vytvořit *role* nebo *ClusterRoles* , které definují přístupová oprávnění k prostředkům. Pak budete role *navazovat* na uživatele nebo skupiny z Azure AD. Tyto Kubernetes řízení přístupu na základě role (Kubernetes RBAC) jsou popsány v následující části. Integrace služby Azure AD a způsobu řízení přístupu k prostředkům lze zobrazit v následujícím diagramu:

![Ověřování na úrovni clusteru pro integraci Azure Active Directory s AKS](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. Vývojář se ověřuje pomocí Azure AD.
1. Koncový bod pro vystavení tokenu Azure AD vydá přístupový token.
1. Vývojář provede akci, která používá token Azure AD, například `kubectl create pod`
1. Kubernetes ověří token pomocí Azure Active Directory a načte členství ve skupině vývojářů.
1. Kubernetes řízení přístupu na základě role (Kubernetes RBAC) a zásady clusteru se aplikují.
1. Žádost vývojáře je úspěšná nebo není založená na předchozím ověření členství ve skupině Azure AD a Kubernetes RBAC a zásady.

Pokud chcete vytvořit cluster AKS, který používá Azure AD, přečtěte si téma věnované [integraci Azure Active Directory s AKS][aks-aad].

## <a name="use-kubernetes-role-based-access-control-kubernetes-rbac"></a>Použití řízení přístupu založeného na rolích Kubernetes (Kubernetes RBAC)

**Doprovodné materiály k osvědčeným postupům** – pomocí Kubernetes RBAC Definujte oprávnění, která uživatelé nebo skupiny mají k prostředkům v clusteru. Vytvořte role a vazby, které přiřadí minimální počet požadovaných oprávnění. Integrace se službou Azure AD, takže jakákoli změna stavu uživatele nebo členství ve skupině se automaticky aktualizuje a přístup k prostředkům clusteru je aktuální.

V Kubernetes můžete poskytovat podrobnou kontrolu nad přístupem k prostředkům v clusteru. Oprávnění jsou definována na úrovni clusteru nebo konkrétní obory názvů. Můžete definovat, které prostředky se dají spravovat, a s jakými oprávněními. Tyto role se pak aplikují na uživatele nebo skupiny s vazbou. Další informace o *rolích*, *ClusterRoles* a *vazbách* najdete v tématu [Možnosti přístupu a identit pro Azure Kubernetes Service (AKS)][aks-concepts-identity].

Jako příklad můžete vytvořit roli, která udělí úplný přístup k prostředkům v oboru názvů s názvem *finance-App*, jak je znázorněno v následujícím příkladu manifestu YAML:

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

Pak se vytvoří RoleBinding, který váže uživatele Azure AD *developer1 \@ contoso.com* na RoleBinding, jak je znázorněno v následujícím manifestu YAML:

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

Pokud se *developer1 \@ contoso.com* ověřuje v rámci clusteru AKS, mají úplná oprávnění k prostředkům v oboru názvů *finance-aplikace* . Tímto způsobem můžete logicky oddělit a řídit přístup k prostředkům. Kubernetes RBAC by se měla používat ve spojení s integrací Azure AD, jak je popsáno v předchozí části.

Pokud chcete zjistit, jak používat skupiny Azure AD k řízení přístupu k prostředkům Kubernetes pomocí Kubernetes RBAC, přečtěte si téma [řízení přístupu k prostředkům clusteru pomocí řízení přístupu na základě rolí a Azure Active Directory identit v AKS][azure-ad-rbac].

## <a name="use-azure-rbac"></a>Použití Azure RBAC 
**Doprovodné materiály k osvědčeným postupům** – pomocí Azure RBAC definujte minimální požadovaná oprávnění, která uživatelé nebo skupiny musí AKS k prostředkům v jednom nebo několika předplatných.

Existují dvě úrovně přístupu, které jsou potřeba k plnému provozu clusteru AKS: 
1. Přístup k prostředku AKS ve vašem předplatném Azure. Tato úroveň přístupu vám umožní řídit, jak škálovat nebo upgradovat cluster pomocí rozhraní AKS API, a také načíst vaše kubeconfig.
Pokud chcete zjistit, jak řídit přístup k AKS prostředku a kubeconfig, přečtěte si téma [omezení přístupu ke konfiguračnímu souboru clusteru](control-kubeconfig-access.md).

2. Přístup k rozhraní Kubernetes API. Tato úroveň přístupu se řídí buď [KUBERNETES RBAC](#use-kubernetes-role-based-access-control-kubernetes-rbac) (tradičně), nebo integrací Azure RBAC s AKS pro autorizaci Kubernetes.
Postup při podrobném udělení oprávnění k rozhraní Kubernetes API pomocí Azure RBAC najdete v tématu [použití Azure RBAC pro autorizaci Kubernetes](manage-azure-rbac.md).

## <a name="use-pod-managed-identities"></a>Použití identit pod správou

**Doprovodné materiály k osvědčeným postupům** – nepoužívejte pevná pověření v rámci lusků ani imagí kontejnerů, protože jsou ohroženy ozářením nebo zneužitím. Místo toho použijte identity pod, pokud chcete automaticky požádat o přístup pomocí centrálního řešení identit Azure AD. Identity pod jsou určené pro použití jenom pro systémy Linux a image kontejnerů.

> [!NOTE]
> Už brzy bude dostupná podpora identit pod správou pro kontejnery Windows.

Když lusky potřebují přístup k jiným službám Azure, jako jsou Cosmos DB, Key Vault nebo Blob Storage, potřebuje přístup k přihlašovacím údajům. Tyto přístupové přihlašovací údaje by se daly definovat s imagí kontejneru nebo vložené jako Kubernetes tajný klíč, ale je potřeba je ručně vytvořit a přiřadit. Přihlašovací údaje se často použijí v různých luskech a nejsou pravidelně otočené.

Pod-spravované identity pro prostředky Azure vám umožní automaticky požádat o přístup ke službám přes Azure AD. Identity pod správou jsou teď teď ve verzi Preview pro službu Azure Kubernetes. Informace o tom, jak začít, najdete v dokumentaci k [používání Azure Active Directory pod pod správou v dokumentaci ke službě Azure Kubernetes (Preview)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity) . S využitím identit pod správou nedefinujete ručně přihlašovací údaje pro lusky, ale vyžádají přístup k přístupovému tokenu v reálném čase a může je používat pro přístup pouze k jim přiřazeným službám. V AKS jsou k dispozici dvě komponenty, které zpracovávají operace a umožňují použití spravovaných identit v luskech:

* **Server NMI (Node Management identity)** je pod tím, který se spouští jako DaemonSet na každém uzlu v clusteru AKS. Server NMI čeká na služby Azure na požadavky pod.
* **Poskytovatel prostředků Azure** se dotáže serveru rozhraní Kubernetes API a ověří mapování identit Azure, které odpovídá poli pod.

Když lusky požadují přístup ke službě Azure, Síťová pravidla přesměrují provoz na server NMI (Node Management identity). Server NMI identifikuje lusky, které vyžadují přístup ke službám Azure na základě jejich vzdálené adresy, a odešle dotaz poskytovateli prostředků Azure. Poskytovatel Azure prostředků zkontroluje mapování identit Azure v clusteru AKS a server NMI pak požádá o přístupový token z Azure Active Directory (AD) na základě mapování identity pod. Azure AD poskytuje přístup k serveru NMI, který je vrácen do pod. Přístup k tomuto přístupovému tokenu může použít ta pod tím, že bude vyžadovat přístup ke službám v Azure.

V následujícím příkladu Vývojář vytvoří pod, který používá spravovanou identitu k vyžádání přístupu k Azure SQL Database:

![Identity pod umožňují automatické vyžádání přístupu k jiným službám.](media/operator-best-practices-identity/pod-identities.png)

1. Operátor clusteru nejdřív vytvoří účet služby, který se dá použít k mapování identit, když lusky požadují přístup ke službám.
1. Server NMI je nasazený pro předávání všech požadavků pod a poskytovatele prostředků Azure pro přístupové tokeny do Azure AD.
1. Vývojář nasadí pod spravovanou identitou, která žádá o přístupový token prostřednictvím serveru NMI.
1. Token se vrátí do pole pod a používá se pro přístup k Azure SQL Database

> [!NOTE]
> Identity pod správou jsou aktuálně ve stavu Preview.

Pokud chcete používat spravované identity pod správou, přečtěte si téma [použití Azure Active Directory pod správou identit ve službě Azure Kubernetes (Preview)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity).

## <a name="next-steps"></a>Další kroky

Tento článek s osvědčenými postupy se zaměřuje na ověřování a autorizaci pro váš cluster a prostředky. Chcete-li implementovat některé z těchto doporučených postupů, přečtěte si následující články:

* [Integrace Azure Active Directory s AKS][aks-aad]
* [Použití identit spravovaných pod Azure Active Directory ve službě Azure Kubernetes (Preview)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity)

Další informace o operacích clusteru v AKS najdete v následujících osvědčených postupech:

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
