---
title: Koncepty – přístup a identita ve službě Azure Kubernetes Services (AKS)
description: Přečtěte si o přístupu a identitě ve službě Azure Kubernetes (AKS), včetně Integrace Azure Active Directory, řízení přístupu na základě role (RBAC) a rolí a vazeb.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: mlearned
ms.openlocfilehash: a1ed1eccd7a10d78cd503559469654e5562cde0c
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "67615860"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Možnosti přístupu a identit pro službu Azure Kubernetes (AKS)

Existují různé způsoby ověřování pomocí a zabezpečeného Kubernetes clusterů. Pomocí řízení přístupu na základě rolí (RBAC) můžete uživatelům nebo skupinám udělit přístup pouze k potřebným prostředkům. Pomocí služby Azure Kubernetes Service (AKS) můžete dál zdokonalit strukturu zabezpečení a oprávnění pomocí Azure Active Directory. Tyto přístupy vám pomůžou zabezpečit úlohy aplikací a zákaznická data.

V tomto článku se seznámíte se základními koncepty, které vám pomůžou ověřit a přiřadit oprávnění v AKS:

- [Účty služby Kubernetes](#kubernetes-service-accounts)
- [Integrace Azure Active Directory](#azure-active-directory-integration)
- [Řízení přístupu na základě role (RBAC)](#role-based-access-controls-rbac)
- [Role a ClusterRoles](#roles-and-clusterroles)
- [RoleBindings a ClusterRoleBindings](#rolebindings-and-clusterrolebindings)

## <a name="kubernetes-service-accounts"></a>Účty služby Kubernetes

Jedním z primárních uživatelských typů v Kubernetes je *účet služby*. Účet služby existuje v a spravuje ho rozhraní Kubernetes API. Přihlašovací údaje pro účty služeb se ukládají jako tajné kódy Kubernetes, což umožňuje jejich použití autorizovanými lusky ke komunikaci se serverem API. Většina požadavků rozhraní API poskytuje ověřovací token pro účet služby nebo běžný uživatelský účet.

Běžné uživatelské účty umožňují pružnější přístup pro lidské správce nebo vývojáře, ne jenom služby a procesy. Kubernetes sám neposkytuje řešení pro správu identit, ve kterém jsou uložené běžné uživatelské účty a hesla. Místo toho je možné integrovat do Kubernetes řešení externích identit. V případě AKS clusterů je toto integrované řešení identity Azure Active Directory.

Další informace o možnostech identity v Kubernetes najdete v tématu [ověřování Kubernetes][kubernetes-authentication].

## <a name="azure-active-directory-integration"></a>Integrace Azure Active Directory

Zabezpečení clusterů AKS se dá zvýšit integrací Azure Active Directory (AD). Služba Azure AD je postavená na základě počtu desetiletí správy podnikových identit ve více tenantůch, cloudových adresářích a službě správy identit, které spojují základní adresářové služby, správu přístupu k aplikacím a Identity Protection. Pomocí Azure AD můžete integrovat místní identity do clusterů AKS a poskytnout tak jeden zdroj pro správu a zabezpečení účtů.

![Azure Active Directory integrace s clustery AKS](media/concepts-identity/aad-integration.png)

S integrovanými clustery AKS Azure AD můžete uživatelům nebo skupinám udělit přístup k prostředkům Kubernetes v rámci oboru názvů nebo napříč clusterem. Aby mohl uživatel `kubectl` získat kontext konfigurace, může spustit příkaz [AZ AKS Get-Credentials][az-aks-get-credentials] . Když uživatel potom komunikuje s clusterem AKS s `kubectl`, zobrazí se výzva k přihlášení pomocí přihlašovacích údajů Azure AD. Tento přístup poskytuje jeden zdroj pro správu uživatelských účtů a přihlašovací údaje k heslům. Uživatel má přístup pouze k prostředkům definovaným správcem clusteru.

Ověřování Azure AD v clusterech AKS používá OpenID Connect, což je vrstva identity postavená na protokolu OAuth 2,0. OAuth 2,0 definuje mechanismy pro získání a použití přístupových tokenů pro přístup k chráněným prostředkům a OpenID Connect implementuje ověřování jako rozšíření procesu autorizace OAuth 2,0. Další informace o OpenID připojení najdete v dokumentaci k [otevřenému ID Connect][openid-connect]. Pokud chcete ověřit ověřovací tokeny získané z Azure AD prostřednictvím OpenID Connect, clustery AKS používají ověřování pomocí tokenu Webhooku Kubernetes. Další informace najdete v dokumentaci pro [ověřování tokenů][webhook-token-docs]Webhooku.

## <a name="role-based-access-controls-rbac"></a>Řízení přístupu na základě role (RBAC)

K zajištění podrobného filtrování akcí, které mohou uživatelé provádět, Kubernetes používá řízení přístupu na základě rolí (RBAC). Tento řídicí mechanismus umožňuje přiřadit uživatele nebo skupiny uživatelů, oprávnění k provádění akcí, jako je vytváření nebo úpravy prostředků, nebo zobrazení protokolů ze spuštěných úloh aplikací. Tato oprávnění můžou být vymezená na jeden obor názvů nebo udělená v rámci celého clusteru AKS. Pomocí Kubernetes RBAC vytvoříte *role* pro definování oprávnění a pak jim přiřadíte tyto role uživatelům s *vazbami rolí*.

Další informace najdete v tématu [použití autorizace RBAC][kubernetes-rbac].

### <a name="azure-role-based-access-controls-rbac"></a>Řízení přístupu na základě role v Azure (RBAC)
Jedním z dalších mechanismů řízení přístupu k prostředkům je řízení přístupu na základě role (RBAC) v Azure. Kubernetes RBAC je navržená tak, aby pracovala s prostředky v rámci vašeho clusteru AKS a Azure RBAC je navržená pro práci na prostředcích v rámci vašeho předplatného Azure. Pomocí Azure RBAC můžete vytvořit *definici role* , která bude mít přehled o oprávněních, která se mají použít. Uživateli nebo skupině se pak přiřadí tato definice role pro konkrétní *obor*, což může být individuální prostředek, skupina prostředků nebo celé předplatné.

Další informace najdete v tématu [co je Azure RBAC?][azure-rbac]

## <a name="roles-and-clusterroles"></a>Role a ClusterRoles

Než přiřadíte oprávnění uživatelům pomocí Kubernetes RBAC, nejprve tato oprávnění definujte jako *roli*. Role Kubernetes *udělují* oprávnění. Neexistuje žádný koncept oprávnění *Odepřít* .

Role slouží k udělení oprávnění v rámci oboru názvů. Pokud potřebujete udělit oprávnění napříč celým clusterem nebo prostředky clusteru mimo daný obor názvů, můžete místo toho použít *ClusterRoles*.

ClusterRole funguje stejným způsobem jako udělení oprávnění k prostředkům, ale lze je použít na prostředky v celém clusteru, nikoli na konkrétní obor názvů.

## <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings a ClusterRoleBindings

Jakmile jsou role definované pro udělení oprávnění k prostředkům, přiřaďte tato oprávnění Kubernetes RBAC k *RoleBinding*. Pokud se váš cluster AKS integruje s Azure Active Directory, vytváří vazby způsob, jakým uživatelé Azure AD mají udělená oprávnění k provádění akcí v rámci clusteru.

Vazby role slouží k přiřazení rolí pro daný obor názvů. Tento přístup umožňuje logicky oddělit jeden AKS cluster s uživateli, kteří mají jenom přístup k prostředkům aplikace v jejich přiřazeném oboru názvů. Pokud potřebujete navazovat role napříč celým clusterem nebo prostředky clusteru mimo daný obor názvů, můžete místo toho použít *ClusterRoleBindings*.

ClusterRoleBinding funguje stejným způsobem jako vázání rolí uživatelům, ale lze je použít na prostředky v celém clusteru, nikoli na konkrétní obor názvů. Tento přístup vám umožní udělit správcům nebo pracovníkům podpory přístup ke všem prostředkům v clusteru AKS.

## <a name="next-steps"></a>Další postup

Informace o tom, jak začít s Azure AD a Kubernetes RBAC, najdete v tématu věnovaném [integraci Azure Active Directory s AKS][aks-aad].

Související osvědčené postupy najdete v tématu [osvědčené postupy pro ověřování a autorizaci v AKS][operator-best-practices-identity].

Další informace o základních konceptech Kubernetes a AKS najdete v následujících článcích:

- [Clustery a úlohy Kubernetes/AKS][aks-concepts-clusters-workloads]
- [Zabezpečení Kubernetes/AKS][aks-concepts-security]
- [Virtuální sítě Kubernetes/AKS][aks-concepts-network]
- [Úložiště Kubernetes/AKS][aks-concepts-storage]
- [Škálování Kubernetes/AKS][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v1-protocols-openid-connect-code.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: azure-ad-integration-cli.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
