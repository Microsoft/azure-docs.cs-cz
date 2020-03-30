---
title: Koncepty – přístup a identita ve službách Azure Kubernetes Services (AKS)
description: Další informace o přístupu a identitě ve službě Azure Kubernetes Service (AKS), včetně integrace Azure Active Directory, řízení přístupu na základě rolí Kubernetes (RBAC) a rolí a vazeb.
services: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: e4945535417f7d8d33308121267ba97e1f835e13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259601"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Možnosti identit a přístupu pro Azure Kubernetes Service (AKS)

Existují různé způsoby ověřování pomocí clusterů Kubernetes a jejich zabezpečení. Pomocí ovládacích prvků přístupu založených na rolích (RBAC) můžete uživatelům nebo skupinám udělit přístup pouze k prostředkům, které potřebují. Pomocí služby Azure Kubernetes Service (AKS) můžete dále vylepšit strukturu zabezpečení a oprávnění pomocí služby Azure Active Directory. Tyto přístupy vám pomohou zabezpečit úlohy aplikací a zákaznická data.

Tento článek představuje základní koncepty, které vám pomohou ověřit a přiřadit oprávnění v AKS:

- [Účty služeb Kubernetes](#kubernetes-service-accounts)
- [Integrace Azure Active Directory](#azure-active-directory-integration)
- [Ovládací prvky přístupu založené na rolích (RBAC)](#role-based-access-controls-rbac)
- [Role a role clusteru](#roles-and-clusterroles)
- [RoleBindings a ClusterRoleBindings](#rolebindings-and-clusterrolebindings)

## <a name="kubernetes-service-accounts"></a>Účty služeb Kubernetes

Jedním z primárních typů uživatelů v Kubernetes je *účet služby*. Účet služby existuje v rozhraní API Kubernetes a spravuje ho. Přihlašovací údaje pro účty služeb jsou uloženy jako tajné kódy Kubernetes, což umožňuje jejich použití autorizovanými pody ke komunikaci se serverem rozhraní API. Většina požadavků rozhraní API poskytuje ověřovací token pro účet služby nebo běžný uživatelský účet.

Běžné uživatelské účty umožňují tradičnější přístup pro lidské správce nebo vývojáře, nejen pro služby a procesy. Kubernetes sám neposkytuje řešení pro správu identit, kde jsou uloženy běžné uživatelské účty a hesla. Místo toho lze do Kubernetes integrovat externí řešení identity. Pro clustery AKS je toto integrované řešení identit azure active directory.

Další informace o možnostech identity v Kubernetes naleznete v tématu [Kubernetes authentication][kubernetes-authentication].

## <a name="azure-active-directory-integration"></a>Integrace Azure Active Directory

Zabezpečení clusterů AKS lze zvýšit s integrací služby Azure Active Directory (AD). Azure AD, postavená na desetiletích správy podnikových identit, je víceklientská služba s cloudovým adresářem a správou identit, která kombinuje základní adresářové služby, správu přístupu k aplikacím a ochranu identit. S Azure AD můžete integrovat místní identity do clusterů AKS a poskytnout tak jediný zdroj pro správu a zabezpečení účtů.

![Integrace Azure Active Directory s clustery AKS](media/concepts-identity/aad-integration.png)

Pomocí clusterů AKS integrovaných s Azure AD můžete uživatelům nebo skupinám udělit přístup k prostředkům Kubernetes v rámci oboru názvů nebo v rámci clusteru. Chcete-li `kubectl` získat kontext konfigurace, uživatel může spustit příkaz [az aks get-credentials.][az-aks-get-credentials] Když uživatel pak spolupracuje s clusteru `kubectl`AKS s , jsou vyzváni k přihlášení pomocí jejich přihlašovacích údajů Azure AD. Tento přístup poskytuje jeden zdroj pro správu uživatelských účtů a pověření hesla. Uživatel má přístup pouze k prostředkům definovaným správcem clusteru.

Ověřování Azure AD v clusterech AKS používá OpenID Connect, vrstvu identity postavenou nad protokolem OAuth 2.0. OAuth 2.0 definuje mechanismy pro získání a použití přístupových tokenů pro přístup k chráněným prostředkům a OpenID Connect implementuje ověřování jako rozšíření procesu autorizace OAuth 2.0. Další informace o openid connect najdete v [dokumentaci k open id connect][openid-connect]. K ověření ověřovacích tokenů získaných z Azure AD prostřednictvím OpenID Connect používají clustery AKS ověřování tokenů Kubernetes Webhook. Další informace naleznete v [dokumentaci k ověřování tokenů Webhook][webhook-token-docs].

## <a name="role-based-access-controls-rbac"></a>Ovládací prvky přístupu založené na rolích (RBAC)

K poskytnutí podrobného filtrování akcí, které mohou uživatelé provádět, používá Kubernetes ovládací prvky přístupu založené na rolích (RBAC). Tento mechanismus řízení umožňuje přiřadit uživatelům nebo skupinám uživatelů oprávnění k vytváření nebo úpravám prostředků nebo zobrazení protokolů ze spuštěných úloh aplikací. Tato oprávnění mohou být vymezena do jednoho oboru názvů nebo udělena v celém clusteru AKS. Pomocí Kubernetes RBAC vytvoříte *role* k definování oprávnění a pak tyto role přiřadíte uživatelům s *vazbami rolí*.

Další informace naleznete [v tématu Použití autorizace RBAC][kubernetes-rbac].

### <a name="azure-role-based-access-controls-rbac"></a>Ovládací prvky přístupu azure založené na rolích (RBAC)
Jeden další mechanismus pro řízení přístupu k prostředkům je ovládací prvky přístupu na základě rolí Azure (RBAC). Kubernetes RBAC je navržený pro práci na prostředcích v rámci clusteru AKS a Azure RBAC je navržený pro práci na prostředky v rámci předplatného Azure. S Azure RBAC vytvoříte *definici role,* která popisuje oprávnění, která mají být použita. Uživateli nebo skupině je pak přiřazena tato definice role pro určitý *obor*, což může být jednotlivý prostředek, skupina prostředků nebo v rámci předplatného.

Další informace najdete v tématu [Co je Azure RBAC?][azure-rbac]

## <a name="roles-and-clusterroles"></a>Role a role clusteru

Před přiřazením oprávnění uživatelům pomocí Kubernetes RBAC nejprve definujte tato oprávnění jako *roli*. Kubernetes role *udělují* oprávnění. Neexistuje žádný koncept *odepřít* povolení.

Role se používají k udělení oprávnění v rámci oboru názvů. Pokud potřebujete udělit oprávnění v celém clusteru nebo clusterovat prostředky mimo daný obor názvů, můžete místo toho použít *ClusterRoles*.

ClusterRole funguje stejným způsobem udělit oprávnění k prostředkům, ale lze použít pro prostředky v celém clusteru, nikoli konkrétní obor názvů.

## <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings a ClusterRoleBindings

Jakmile jsou role definovány pro udělení oprávnění k prostředkům, přiřadíte tato oprávnění RBAC Kubernetes s *rolebinding*. Pokud se váš cluster AKS integruje s Azure Active Directory, vazby jsou způsob, jakým jsou těmto uživatelům Služby Azure AD udělena oprávnění k provádění akcí v rámci clusteru.

Vazby rolí se používají k přiřazení rolí pro daný obor názvů. Tento přístup umožňuje logicky oddělit jeden cluster AKS, přičemž uživatelé mají přístup pouze k prostředkům aplikace v přiřazeném oboru názvů. Pokud potřebujete svázat role v celém clusteru nebo clusterovat prostředky mimo daný obor názvů, můžete místo toho použít *ClusterRoleBindings*.

A ClusterRoleBinding funguje stejným způsobem vázat role pro uživatele, ale lze použít pro prostředky v celém clusteru, nikoli konkrétní obor názvů. Tento přístup umožňuje udělit správcům nebo technikům podpory přístup ke všem prostředkům v clusteru AKS.

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s Azure AD a Kubernetes RBAC, najdete [v tématu Integrace Služby Azure Active Directory s AKS][aks-aad].

Doporučené postupy v přidružených postupech naleznete [v tématu Doporučené postupy pro ověřování a autorizaci v AKS][operator-best-practices-identity].

Další informace o základních konceptech Kubernetes a AKS naleznete v následujících článcích:

- [Kubernetes / AKS clustery a úlohy][aks-concepts-clusters-workloads]
- [Kubernetes / AKS zabezpečení][aks-concepts-security]
- [Kubernetes / AKS virtuální sítě][aks-concepts-network]
- [Kubernetes / AKS úložiště][aks-concepts-storage]
- [Kubernetes / AKS váha][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: azure-ad-integration-cli.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
