---
title: Osvědčené postupy pro správu identity
titleSuffix: Azure Kubernetes Service
description: Seznamte se s osvědčenými postupy pro postupy, jak spravovat ověřování a autorizaci pro clustery ve službě Azure Kubernetes (AKS).
services: container-service
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: a63a756448f9c7202c79c3b4625fc99d4a90dc52
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014053"
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

## <a name="use-pod-identities"></a>Použití identity pod

**Doprovodné materiály k osvědčeným postupům** – nepoužívejte pevná pověření v rámci lusků ani imagí kontejnerů, protože jsou ohroženy ozářením nebo zneužitím. Místo toho použijte identity pod, pokud chcete automaticky požádat o přístup pomocí centrálního řešení identit Azure AD. Identity pod jsou určené pro použití jenom pro systémy Linux a image kontejnerů.

Když lusky potřebují přístup k jiným službám Azure, jako jsou Cosmos DB, Key Vault nebo Blob Storage, potřebuje přístup k přihlašovacím údajům. Tyto přístupové přihlašovací údaje by se daly definovat s imagí kontejneru nebo vložené jako Kubernetes tajný klíč, ale je potřeba je ručně vytvořit a přiřadit. Přihlašovací údaje se často použijí v různých luskech a nejsou pravidelně otočené.

Spravované identity pro prostředky Azure (aktuálně implementované jako přidružený otevřený zdrojový projekt AKS) vám umožní automaticky požádat o přístup ke službám přes Azure AD. Ručně nedefinujete přihlašovací údaje pro lusky, místo toho žádají o přístupový token v reálném čase a můžou ho použít k přístupu pouze k jim přiřazeným službám. V AKS jsou v rámci operátoru clusteru nasazené dvě komponenty, aby se mohly používat spravované identity v luskech:

* **Server NMI (Node Management identity)** je pod tím, který se spouští jako DaemonSet na každém uzlu v clusteru AKS. Server NMI čeká na služby Azure na požadavky pod.
* **Spravovaný řadič identity (MIC)** je střední pod s oprávněními k dotazování serveru rozhraní Kubernetes API a kontroluje mapování identit Azure, které odpovídá poli pod.

Když lusky požadují přístup ke službě Azure, Síťová pravidla přesměrují provoz na server NMI (Node Management identity). Server NMI identifikuje lusky, které vyžadují přístup ke službám Azure na základě jejich vzdálené adresy, a odešle dotaz na spravovaný řadič identity (MIC). MIKROFON kontroluje mapování identit Azure v clusteru AKS a server NMI pak požádá o přístupový token z Azure Active Directory (AD) na základě mapování identity pod. Azure AD poskytuje přístup k serveru NMI, který je vrácen do pod. Přístup k tomuto přístupovému tokenu může použít ta pod tím, že bude vyžadovat přístup ke službám v Azure.

V následujícím příkladu Vývojář vytvoří pod, který používá spravovanou identitu k vyžádání přístupu k Azure SQL Database:

![Identity pod umožňují automatické vyžádání přístupu k jiným službám.](media/operator-best-practices-identity/pod-identities.png)

1. Operátor clusteru nejdřív vytvoří účet služby, který se dá použít k mapování identit, když lusky požadují přístup ke službám.
1. Server NMI a mikrofon jsou nasazeny pro předávání všech požadavků na přístupových tokenů do služby Azure AD.
1. Vývojář nasadí pod spravovanou identitou, která žádá o přístupový token prostřednictvím serveru NMI.
1. Token se vrátí do pole pod a používá se pro přístup k Azure SQL Database

> [!NOTE]
> Spravované identity pod jsou open source projekt a technická podpora Azure ji nepodporuje.

Pokud chcete použít identity pod, přečtěte si téma [Azure Active Directory identity pro aplikace Kubernetes][aad-pod-identity].

## <a name="next-steps"></a>Další kroky

Tento článek s osvědčenými postupy se zaměřuje na ověřování a autorizaci pro váš cluster a prostředky. Chcete-li implementovat některé z těchto doporučených postupů, přečtěte si následující články:

* [Integrace Azure Active Directory s AKS][aks-aad]
* [Použití spravovaných identit pro prostředky Azure s AKS][aad-pod-identity]

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
