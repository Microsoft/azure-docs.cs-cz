---
title: Osvědčené postupy pro správu identity
titleSuffix: Azure Kubernetes Service
description: Seznamte se s osvědčenými postupy pro postupy, jak spravovat ověřování a autorizaci pro clustery ve službě Azure Kubernetes (AKS).
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: jpalma
author: palma21
ms.openlocfilehash: de84e3e2a8da3e1b5195978a8a2204fdfa2108d7
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105098"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro ověřování a autorizaci ve službě Azure Kubernetes (AKS)

Při nasazení a údržbě clusterů ve službě Azure Kubernetes Service (AKS) implementujete způsoby správy přístupu k prostředkům a službám. Bez těchto ovládacích prvků:
* Účty můžou mít přístup k zbytečným prostředkům a službám. 
* Sledování, kterou sadu přihlašovacích údajů bylo použito k provedení změn, může být obtížné.

Tento článek o osvědčených postupech se zaměřuje na to, jak operátor clusteru může spravovat přístup a identitu pro clustery AKS. V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Ověřte uživatele clusteru AKS pomocí Azure Active Directory.
> * Řízení přístupu k prostředkům pomocí Kubernetes řízení přístupu založeného na rolích (Kubernetes RBAC).
> * Využijte Azure RBAC k podrobnému řízení přístupu k AKS prostředku, Kubernetes rozhraní API ve velkém měřítku a `kubeconfig` .
> * Pomocí spravované identity můžete sami ověřit své lusky s ostatními službami.

## <a name="use-azure-active-directory-azure-ad"></a>Použití Azure Active Directory (Azure AD)

> **Osvědčené postupy** 
> 
> Nasaďte clustery AKS s integrací služby Azure AD. Pomocí Azure AD se soustřeďuje na součást správy identit. Všechny změny v uživatelském účtu nebo skupině se automaticky aktualizují v rámci přístupu ke clusteru AKS. Rozsah uživatelů nebo skupin na minimální počet oprávnění pomocí [rolí, ClusterRoles nebo vazeb](#use-kubernetes-role-based-access-control-kubernetes-rbac)

Vývojáři clusteru Kubernetes a vlastníci aplikací potřebují přístup k různým prostředkům. Kubernetes nemá řešení pro správu identit, které vám umožní řídit prostředky, se kterými můžou uživatelé pracovat. Místo toho se cluster obvykle integruje s existujícím řešením identity. Zadejte Azure AD: řešení pro správu identit připravené na podnik, které se integruje s AKS clustery.

Clustery s integrovanými službami Azure AD v AKS umožňují vytvářet *role* nebo *ClusterRoles* definující přístupová oprávnění k prostředkům. Pak budete role *navazovat* na uživatele nebo skupiny z Azure AD. Další informace o těchto Kubernetes RBAC najdete v [Další části](#use-kubernetes-role-based-access-control-kubernetes-rbac). Integrace Azure AD a jak řídit přístup k prostředkům, najdete v následujícím diagramu:

![Ověřování na úrovni clusteru pro integraci Azure Active Directory s AKS](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. Vývojář se ověřuje pomocí Azure AD.
1. Koncový bod pro vystavení tokenu Azure AD vydá přístupový token.
1. Vývojář provede akci pomocí tokenu Azure AD, jako je například `kubectl create pod` .
1. Kubernetes ověří token ve službě Azure AD a načte členství ve skupině vývojářů.
1. Kubernetes RBAC a zásady clusteru se aplikují.
1. Žádost vývojáře je úspěšná na základě předchozího ověření členství ve skupině Azure AD a Kubernetes RBAC a zásady.

Pokud chcete vytvořit cluster AKS, který používá Azure AD, přečtěte si téma věnované [integraci Azure Active Directory s AKS][aks-aad].

## <a name="use-kubernetes-role-based-access-control-kubernetes-rbac"></a>Použití řízení přístupu založeného na rolích Kubernetes (Kubernetes RBAC)

> **Osvědčené postupy**
> 
> Definujte oprávnění uživatele nebo skupiny pro prostředky clusteru s Kubernetes RBAC. Vytvořte role a vazby, které přiřadí minimální počet požadovaných oprávnění. Integrací se službou Azure AD můžete automaticky aktualizovat všechny stavy uživatelů nebo změny členství ve skupinách a zachovat přístup k prostředkům clusteru v aktuálním stavu.

V Kubernetes poskytujete podrobné řízení přístupu k prostředkům clusteru. Můžete definovat oprávnění na úrovni clusteru nebo konkrétní obory názvů. Určíte, které prostředky se dají spravovat a s jakými oprávněními. Tyto role pak můžete použít pro uživatele nebo skupiny s vazbou. Další informace o *rolích*, *ClusterRoles* a *vazbách* najdete v tématu [Možnosti přístupu a identit pro Azure Kubernetes Service (AKS)][aks-concepts-identity].

Jako příklad můžete vytvořit roli s úplným přístupem k prostředkům v oboru názvů s názvem *finance-App*, jak je znázorněno v následujícím příkladu manifestu YAML:

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

Pak vytvoříte RoleBinding a svážete uživatele Azure AD *developer1 \@ contoso.com* s RoleBinding, jak je znázorněno v následujícím manifestu YAML:

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

Pokud se *developer1 \@ contoso.com* ověřuje v rámci clusteru AKS, mají úplná oprávnění k prostředkům v oboru názvů *finance-aplikace* . Tímto způsobem můžete logicky oddělit a řídit přístup k prostředkům. Kubernetes RBAC používejte ve spojení s Azure AD – integrací.

Pokud chcete zjistit, jak používat skupiny Azure AD k řízení přístupu k prostředkům Kubernetes pomocí Kubernetes RBAC, přečtěte si téma [řízení přístupu k prostředkům clusteru pomocí řízení přístupu na základě rolí a Azure Active Directory identit v AKS][azure-ad-rbac].

## <a name="use-azure-rbac"></a>Použití Azure RBAC 
> **Osvědčené postupy** 
> 
> Pomocí Azure RBAC definujte minimální požadovaná oprávnění uživatelů a skupin pro AKS prostředků v jednom nebo několika předplatných.

Existují dvě úrovně přístupu, které jsou potřeba k plnému provozu clusteru AKS: 
1. Přístup k prostředku AKS ve vašem předplatném Azure. 

    Tato úroveň přístupu vám umožní:
      * Řízení škálování nebo upgrade clusteru pomocí rozhraní API AKS
      * Vyžádání `kubeconfig` .

    Chcete-li zjistit, jak řídit přístup k AKS prostředku a `kubeconfig` , najdete informace v části [omezení přístupu ke konfiguračnímu souboru clusteru](control-kubeconfig-access.md).

2. Přístup k rozhraní Kubernetes API. 
    
    Tato úroveň přístupu se řídí těmito možnostmi:
    * [KUBERNETES RBAC](#use-kubernetes-role-based-access-control-kubernetes-rbac) (tradičně) nebo 
    * Integrací Azure RBAC s AKS pro autorizaci Kubernetes.

    Pokud chcete zjistit, jak členit oprávnění k rozhraní Kubernetes API pomocí Azure RBAC, přečtěte si téma [použití Azure RBAC pro autorizaci Kubernetes](manage-azure-rbac.md).

## <a name="use-pod-managed-identities"></a>Použití identit pod správou

> **Osvědčené postupy** 
> 
> Nepoužívejte pevná pověření v rámci lusků nebo imagí kontejnerů, protože jsou ohrožena ozářením nebo zneužitím. Místo toho použijte *identity pod* , pokud chcete automaticky požádat o přístup pomocí centrálního řešení identit Azure AD. 

> [!NOTE]
> Identity pod jsou určené pro použití jenom pro systémy Linux a image kontejnerů. Už brzy bude dostupná podpora identit pod správou pro kontejnery Windows.

Pro přístup k jiným službám Azure, jako je Cosmos DB, Key Vault nebo Blob Storage, musí mít přístup k přihlašovacím údajům. Můžete definovat přístupová pověření pomocí Image kontejneru nebo je vložit jako tajný kód Kubernetes. V obou případech byste je museli vytvořit a přiřadit ručně. Obvykle se tyto přihlašovací údaje použijí v různých luskech a nejsou pravidelně otočeny.

S využitím identit pod správou pro prostředky Azure automaticky vyžádáte přístup ke službám přes Azure AD. Identity pod správou teď jsou nyní ve verzi Preview pro AKS. Další informace najdete v tématu [použití Azure Active Directory pod správou identit ve službě Azure Kubernetes (Preview) [( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity) dokumentace pro začátek. 

Místo ručního definování přihlašovacích údajů pro lusky se v reálném čase požádají o přístupový token, a to pomocí něj získat přístup jenom k jim přiřazeným službám. V AKS jsou k dispozici dvě komponenty, které zpracovávají operace a umožňují použití spravovaných identit v luskech:

* **Server NMI (Node Management identity)** je pod tím, který se spouští jako DaemonSet na každém uzlu v clusteru AKS. Server NMI čeká na služby Azure na požadavky pod.
* **Poskytovatel prostředků Azure** se dotáže serveru rozhraní Kubernetes API a ověří mapování identit Azure, které odpovídá poli pod.

Když lusky požadují přístup ke službě Azure, Síťová pravidla přesměrují provoz na server NMI. 
1. Server NMI:
    * Identifikuje lusky požadující přístup ke službám Azure na základě jejich vzdálené adresy.
    * Zadá dotaz na poskytovatele prostředků Azure. 
1. Poskytovatel prostředků Azure kontroluje mapování identit Azure v clusteru AKS.
1. Server NMI požádá o přístupový token ze služby Azure AD na základě mapování totožnosti pod. 
1. Azure AD poskytuje přístup k serveru NMI, který je vrácen do pod. 
    * Přístup k tomuto přístupovému tokenu může použít ta pod tím, že bude vyžadovat přístup ke službám v Azure.

V následujícím příkladu Vývojář vytvoří pod, který používá spravovanou identitu k vyžádání přístupu k Azure SQL Database:

![Identity pod umožňují automatické vyžádání přístupu k jiným službám.](media/operator-best-practices-identity/pod-identities.png)

1. Operátor clusteru vytvoří účet služby pro mapování identit, když lusky požadují přístup ke službám.
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
