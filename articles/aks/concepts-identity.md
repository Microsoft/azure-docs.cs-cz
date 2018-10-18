---
title: Principy – přístupu a identit ve službě Azure Kubernetes služby (AKS)
description: Další informace o přístupu a identit ve službě Azure Kubernetes Service (AKS), včetně integrace služby Azure Active Directory, Kubernetes řízení přístupu na základě role (RBAC) a role a vazby.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: iainfou
ms.openlocfilehash: 0af3133a1f9a903874c25bf34af0fbf99da8af14
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49381001"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Možnosti přístupu a identit pro službu Azure Kubernetes Service (AKS)

Existují různé způsoby ověření a zabezpečení clusterů Kubernetes. Pomocí řízení přístupu na základě rolí (RBAC), můžete udělit uživatele nebo skupiny přístup k prostředkům, které potřebují. S Azure Kubernetes Service (AKS), můžete dále vylepšit struktura zabezpečení a oprávnění pomocí Azure Active Directory. Tyto přístupy pomáhá vám zabezpečit úlohy aplikací a dat zákazníků.

Tento článek představuje základní koncepty, které vám pomůžou ověřit a přiřadit oprávnění ve službě AKS:

- [Účty služby Kubernetes](#kubernetes-service-accounts)
- [Integrace s Azure Active Directory](#azure-active-directory-integration)
- [Řízení přístupu na základě rolí (RBAC)](#role-based-access-controls-rbac)
- [Role a ClusterRoles](#roles-and-clusterroles)
- [RoleBindings a ClusterRoleBindings](#rolebindings-and-clusterrolebindings)

## <a name="kubernetes-service-accounts"></a>Účty služby Kubernetes

Jeden z typů primárního uživatele v Kubernetes je *účet služby*. Účet služby existuje v a spravuje ho rozhraní Kubernetes API. Pověření pro účty služeb se ukládají jako tajné kódy Kubernetes, což umožňuje jejich oprávnění podů používané pro komunikaci se serverem rozhraní API. Většina požadavků rozhraní API poskytuje ověřovací token pro účet služby nebo jako běžný uživatelský účet.

Běžné uživatelské účty umožňují více tradiční přístup neustálou přítomnost správců nebo vývojáře, ne jenom služeb a procesů. Kubernetes sám neposkytuje řešení správy identit, ve kterém jsou uložené se vytvářejí běžné uživatelské účty a hesla. Místo toho je možné integrovat řešení pro externí identity do Kubernetes. Pro AKS clustery je toto řešení integrované identity Azure Active Directory.

Další informace o možnostech identity v Kubernetes najdete v tématu [Kubernetes ověřování][kubernetes-authentication].

## <a name="azure-active-directory-integration"></a>Integrace Azure Active Directory

Zabezpečení clusteru AKS dá vylepšit integrací sady Azure Active Directory (AD). Vytvořené na základě mnohaletých podnikové správy identit Azure AD je více tenantů, cloudový adresář a služba pro správu identit, která kombinuje základní adresářové služby, správu přístupu k aplikacím a ochranu identity. Pomocí Azure AD můžete integrovat místních identit do clusterů AKS k zajištění jeden zdroj pro správu účtů a zabezpečení.

![Integrace Azure Active Directory s clustery AKS](media/concepts-identity/aad-integration.png)

S využitím clusterů Azure AKS integrované s Active Directory můžete udělit uživatelům nebo skupinám přístup k prostředkům Kubernetes v oboru názvů nebo v clusteru. Získání `kubectl` kontextu konfigurace, může uživatel spouštět [az aks get-credentials] [ az-aks-get-credentials] příkazu. Když uživatel potom komunikuje s clusterem AKS s `kubectl`, zobrazí se výzva k přihlášení pomocí přihlašovacích údajů Azure AD. Tento přístup poskytuje jeden zdroj pro správu účtu uživatele a heslo pověření. Uživatel může pouze k prostředkům, tak jak je definoval správce clusteru.

Ověřování Azure AD v clusteru AKS pomocí OpenID Connect, vrstvu identit postavené na protokol OAuth 2.0. Definuje mechanismy získání a přístup k chráněným prostředkům použití přístupových tokenů OAuth 2.0 a OpenID Connect implementuje ověřování jako rozšíření proces autorizace OAuth 2.0. Další informace o OpenID Connect, najdete v článku [Open ID Connect dokumentaci][openid-connect]. Ověření ověřovacích tokenů získaných ze služby Azure AD pomocí OpenID Connect, AKS clustery používají ověřování pomocí tokenu Webhooku Kubernetes. Další informace najdete v tématu [dokumentace k ověřování pomocí tokenu Webhooku][webhook-token-docs].

## <a name="role-based-access-controls-rbac"></a>Řízení přístupu na základě rolí (RBAC)

K poskytování granulární filtrování akce, které mohou uživatelé provádět, používá Kubernetes řízení přístupu na základě rolí (RBAC). Tento mechanismus ovládací prvek umožňuje přiřadit uživatele nebo skupiny uživatelů, oprávnění k provádění akcí, jako je vytvoření nebo úpravám prostředků nebo zobrazit protokoly spouštění úlohy aplikací. Tato oprávnění můžete s rozsahem jednoho oboru názvů nebo udělená napříč celou clusteru AKS. Pomocí Kubernetes RBAC, vytvoříte *role* definovat oprávnění, a pak přiřadit tyto role uživatelů s *role vazby*.

Další informace najdete v tématu [pomocí RBAC se podařilo Autorizovat][kubernetes-rbac].

### <a name="azure-role-based-access-controls-rbac"></a>Řízení přístupu Azure na základě rolí (RBAC)
Jeden další mechanismus pro řízení přístupu k prostředkům se řízení přístupu Azure na základě rolí (RBAC). Kubernetes RBAC je navržen pro práci s prostředky v rámci clusteru AKS a Azure RBAC je navržen pro práci s prostředky v rámci vašeho předplatného Azure. S Azure RBAC, vytvoříte *definice role* , která jsou popsána oprávnění má být použita. Uživatel nebo skupina se pak přiřadí tato definice role pro konkrétní *oboru*, což může být samostatný prostředek, prostředek skupiny, nebo v rámci předplatného.

Další informace najdete v tématu [co je Azure RBAC?][azure-rbac]

## <a name="roles-and-clusterroles"></a>Role a ClusterRoles

Před přiřazením oprávnění pro uživatele s Kubernetes RBAC, nejprve definovat oprávnění jako *Role*. Role Kubernetes *udělit* oprávnění. Neexistuje žádný koncept *Odepřít* oprávnění.

Role se používají k udělení oprávnění v oboru názvů. Pokud je potřeba udělit oprávnění napříč celý cluster, nebo k prostředkům clusteru mimo daný obor názvů, můžete místo toho použít *ClusterRoles*.

ClusterRole funguje stejně jako k udělení oprávnění k prostředkům, ale můžete použít k prostředkům přes celý cluster, nikoli konkrétní obor názvů.

## <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings a ClusterRoleBindings

Po role se definují pro udělení oprávnění k prostředkům, přiřadíte tato oprávnění Kubernetes RBAC s *RoleBinding*. Pokud váš cluster AKS se integruje s Azure Active Directory, vazby jsou, jak jsou tyto služby Azure AD uživatelům udělit oprávnění k provádění akcí v rámci clusteru.

Role vazby se používají k přiřazování rolí pro daný obor názvů. Tento přístup umožňuje logické oddělování jeden cluster AKS, s uživateli pouze přístup k prostředkům aplikace v jejich přiřazené oboru názvů. Pokud je potřeba vytvořit vazbu role přes celý cluster, nebo k prostředkům clusteru mimo daný obor názvů, můžete místo toho použít *ClusterRoleBindings*.

ClusterRoleBinding funguje stejně jako k vytvoření vazby role pro uživatele, ale můžete použít k prostředkům přes celý cluster, nikoli konkrétní obor názvů. Tento přístup umožňuje udělit správcům nebo podpora technici přístup ke všem prostředkům v clusteru AKS.

## <a name="next-steps"></a>Další postup

Začínáme s Azure AD a Kubernetes RBAC, naleznete v tématu [integrace Azure Active Directory s AKS][aks-aad].

Další informace o základní Kubernetes a AKS koncepty najdete v následujících článcích:

- [Kubernetes / AKS clustery a úlohy][aks-concepts-clusters-workloads]
- [Kubernetes / zabezpečení AKS][aks-concepts-security]
- [Kubernetes / virtuální sítě AKS][aks-concepts-network]
- [Kubernetes / AKS storage][aks-concepts-storage]
- [Kubernetes snížit nebo navýšit AKS][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v1-protocols-openid-connect-code.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: aad-integration.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
