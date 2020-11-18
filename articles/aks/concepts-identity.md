---
title: Koncepty – přístup a identita ve službě Azure Kubernetes Services (AKS)
description: Přečtěte si o přístupu a identitě ve službě Azure Kubernetes (AKS), včetně Integrace Azure Active Directory, řízení přístupu na základě role Kubernetes (Kubernetes RBAC) a rolí a vazeb.
services: container-service
ms.topic: conceptual
ms.date: 07/07/2020
author: palma21
ms.author: jpalma
ms.openlocfilehash: ca167a2ae313c29581d40fe921a8742b9b6b61fe
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686051"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Možnosti identit a přístupu pro Azure Kubernetes Service (AKS)

Existují různé způsoby ověřování, řízení přístupu, autorizaci a zabezpečení clusterů Kubernetes. Pomocí Kubernetes řízení přístupu založeného na rolích (Kubernetes RBAC) můžete uživatelům, skupinám a účtům služeb udělit přístup pouze k potřebným prostředkům. Pomocí služby Azure Kubernetes Service (AKS) můžete dál zdokonalit strukturu zabezpečení a oprávnění pomocí Azure Active Directory a Azure RBAC. Tyto přístupy vám pomůžou zabezpečit přístup ke clusteru a poskytnout jenom minimální požadovaná oprávnění vývojářům a operátorům.

V tomto článku se seznámíte se základními koncepty, které vám pomůžou ověřit a přiřadit oprávnění v AKS:

- [Kubernetes řízení přístupu na základě role (Kubernetes RBAC)](#kubernetes-role-based-access-control-kubernetes-rbac)
  - [Role a ClusterRoles](#roles-and-clusterroles)
  - [RoleBindings a ClusterRoleBindings](#rolebindings-and-clusterrolebindings) 
  - [Účty služby Kubernetes](#kubernetes-service-accounts)
- [Integrace Azure Active Directory](#azure-active-directory-integration)
- [Azure RBAC](#azure-role-based-access-control-azure-rbac)
  - [Azure RBAC pro autorizaci přístupu k prostředku AKS](#azure-rbac-to-authorize-access-to-the-aks-resource)
  - [Azure RBAC pro autorizaci Kubernetes (Preview)](#azure-rbac-for-kubernetes-authorization-preview)


## <a name="kubernetes-role-based-access-control-kubernetes-rbac"></a>Kubernetes řízení přístupu na základě role (Kubernetes RBAC)

K zajištění podrobného filtrování akcí, které mohou uživatelé provádět, Kubernetes používá řízení přístupu na základě role Kubernetes (Kubernetes RBAC). Tento řídicí mechanismus umožňuje přiřadit uživatele nebo skupiny uživatelů, oprávnění k provádění akcí, jako je vytváření nebo úpravy prostředků, nebo zobrazení protokolů ze spuštěných úloh aplikací. Tato oprávnění můžou být vymezená na jeden obor názvů nebo udělená v rámci celého clusteru AKS. Pomocí Kubernetes RBAC vytvoříte *role* pro definování oprávnění a pak jim přiřadíte tyto role uživatelům s *vazbami rolí*.

Další informace najdete v tématu [použití autorizace KUBERNETES RBAC][kubernetes-rbac].


### <a name="roles-and-clusterroles"></a>Role a ClusterRoles

Než přiřadíte oprávnění uživatelům pomocí Kubernetes RBAC, nejprve tato oprávnění definujte jako *roli*. Role Kubernetes *udělují* oprávnění. Neexistuje žádný koncept oprávnění *Odepřít* .

Role slouží k udělení oprávnění v rámci oboru názvů. Pokud potřebujete udělit oprávnění napříč celým clusterem nebo prostředky clusteru mimo daný obor názvů, můžete místo toho použít *ClusterRoles*.

ClusterRole funguje stejným způsobem jako udělení oprávnění k prostředkům, ale lze je použít na prostředky v celém clusteru, nikoli na konkrétní obor názvů.

### <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings a ClusterRoleBindings

Jakmile jsou role definované pro udělení oprávnění k prostředkům, přiřaďte tato oprávnění Kubernetes RBAC k *RoleBinding*. Pokud se váš cluster AKS [integruje s Azure Active Directory](#azure-active-directory-integration), vazby jsou tím, jak jsou těmto uživatelům služby Azure AD udělena oprávnění k provádění akcí v rámci clusteru, viz jak v [řízení přístupu k prostředkům clusteru pomocí řízení přístupu založeného na rolích Kubernetes a Azure Active Directory identit](azure-ad-rbac.md).

Vazby role slouží k přiřazení rolí pro daný obor názvů. Tento přístup umožňuje logicky oddělit jeden AKS cluster s uživateli, kteří mají jenom přístup k prostředkům aplikace v jejich přiřazeném oboru názvů. Pokud potřebujete navazovat role napříč celým clusterem nebo prostředky clusteru mimo daný obor názvů, můžete místo toho použít *ClusterRoleBindings*.

ClusterRoleBinding funguje stejným způsobem jako vázání rolí uživatelům, ale lze je použít na prostředky v celém clusteru, nikoli na konkrétní obor názvů. Tento přístup vám umožní udělit správcům nebo pracovníkům podpory přístup ke všem prostředkům v clusteru AKS.


> [!NOTE]
> Všechny akce clusteru prováděné Microsoftem a AKS se provádí s souhlasem uživatele v rámci předdefinované Kubernetes role `aks-service` a předdefinované vazby role `aks-service-rolebinding` . Tato role umožňuje AKS řešit a diagnostikovat problémy s clustery, ale nemůže měnit oprávnění ani vytvářet role nebo vazby rolí ani jiné akce s vysokou úrovní oprávnění. Přístup k rolím je povolený jenom v aktivních lístkech podpory s přístupem JIT (just-in-time). Přečtěte si další informace o [zásadách podpory AKS](support-policies.md).


### <a name="kubernetes-service-accounts"></a>Účty služby Kubernetes

Jedním z primárních uživatelských typů v Kubernetes je *účet služby*. Účet služby existuje v a spravuje ho rozhraní Kubernetes API. Přihlašovací údaje pro účty služeb se ukládají jako tajné kódy Kubernetes, což umožňuje jejich použití autorizovanými lusky ke komunikaci se serverem API. Většina požadavků rozhraní API poskytuje ověřovací token pro účet služby nebo běžný uživatelský účet.

Běžné uživatelské účty umožňují pružnější přístup pro lidské správce nebo vývojáře, ne jenom pro služby a procesy. Kubernetes sám neposkytuje řešení pro správu identit, ve kterém jsou uložené běžné uživatelské účty a hesla. Místo toho je možné integrovat do Kubernetes řešení externích identit. V případě AKS clusterů je toto integrované řešení identity Azure Active Directory.

Další informace o možnostech identity v Kubernetes najdete v tématu [ověřování Kubernetes][kubernetes-authentication].

## <a name="azure-active-directory-integration"></a>Integrace Azure Active Directory

Zabezpečení clusterů AKS se dá zvýšit integrací Azure Active Directory (AD). Služba Azure AD je postavená na základě počtu desetiletí správy podnikových identit ve více tenantůch, cloudových adresářích a službě správy identit, které spojují základní adresářové služby, správu přístupu k aplikacím a Identity Protection. Pomocí Azure AD můžete integrovat místní identity do clusterů AKS a poskytnout tak jeden zdroj pro správu a zabezpečení účtů.

![Azure Active Directory integrace s clustery AKS](media/concepts-identity/aad-integration.png)

S integrovanými clustery AKS Azure AD můžete uživatelům nebo skupinám udělit přístup k prostředkům Kubernetes v rámci oboru názvů nebo napříč clusterem. Aby mohl `kubectl` uživatel získat kontext konfigurace, může spustit příkaz [AZ AKS Get-Credentials][az-aks-get-credentials] . Když uživatel potom komunikuje s clusterem AKS s `kubectl` , zobrazí se jim výzva k přihlášení pomocí přihlašovacích údajů Azure AD. Tento přístup poskytuje jeden zdroj pro správu uživatelských účtů a přihlašovací údaje k heslům. Uživatel má přístup pouze k prostředkům definovaným správcem clusteru.

Ověřování Azure AD je k dispozici pro clustery AKS s OpenID Connect. OpenID Connect je vrstva identity postavená nad protokolem OAuth 2,0. Další informace o OpenID připojení najdete v dokumentaci k [otevřenému ID Connect][openid-connect]. V rámci clusteru Kubernetes se ověřování pomocí [tokenu Webhooku][webhook-token-docs] používá k ověření ověřovacích tokenů. Ověřování tokenu Webhooku je nakonfigurované a spravované jako součást clusteru AKS.

### <a name="webhook-and-api-server"></a>Webhook a Server API

![Tok ověřování serveru Webhook a API](media/concepts-identity/auth-flow.png)

Jak je znázorněno na obrázku výše, Server rozhraní API volá server Webhooku AKS a provede následující kroky:

1. Klientská aplikace Azure AD se používá v kubectl k přihlašování uživatelů pomocí [toku udělení autorizace zařízení OAuth 2,0](../active-directory/develop/v2-oauth2-device-code.md).
2. Azure AD poskytuje access_token, id_token a refresh_token.
3. Uživatel vytvoří požadavek na kubectl s access_token z kubeconfig.
4. Kubectl odesílá access_token do APIServer.
5. Server rozhraní API je nakonfigurovaný se serverem Webhooku ověřování a provede ověření.
6. Server Webhooku ověřování potvrdí, že je podpis JSON Web Token platný, kontrolou veřejného podpisového klíče Azure AD.
7. Serverová aplikace používá k dotazování členství přihlášeného uživatele ze služby MS Graph API uživateli zadané přihlašovací údaje.
8. Do APIServer se pošle odpověď s informacemi o uživateli, jako je například deklarace identity přístupového tokenu (UPN) hlavního názvu uživatele (UPN), a členství uživatele ve skupině na základě ID objektu.
9. Rozhraní API provede rozhodnutí o autorizaci na základě role Kubernetes nebo RoleBinding.
10. Po ověření Server API vrátí odpověď na kubectl.
11. Kubectl poskytuje zpětnou vazbu uživateli.
 
**Naučte se integrovat AKS do [AAD.](managed-aad.md)**

## <a name="azure-role-based-access-control-azure-rbac"></a>Řízení přístupu na základě role Azure (Azure RBAC)

Azure RBAC je autorizačním systémem postaveným na [Azure Resource Manager](../azure-resource-manager/management/overview.md) , který poskytuje jemně odstupňovanou správu přístupu k prostředkům Azure.

 Služba Azure RBAC je navržená tak, aby fungovala s prostředky v rámci vašeho předplatného Azure, zatímco Kubernetes RBAC je navržená pro práci na Kubernetesch prostředcích v rámci 

Pomocí Azure RBAC můžete vytvořit *definici role* , která bude mít přehled o oprávněních, která se mají použít. Uživateli nebo skupině se pak přiřadí tato definice role prostřednictvím *přiřazení role* pro konkrétní *obor*, což může být individuální prostředek, skupina prostředků nebo celé předplatné.

Další informace najdete v tématu [co je řízení přístupu na základě role Azure (Azure RBAC)?][azure-rbac]

Existují dvě úrovně přístupu, které jsou potřeba k plnému provozu clusteru AKS: 
1. [Přístup k prostředku AKS ve vašem předplatném Azure](#azure-rbac-to-authorize-access-to-the-aks-resource). Tento proces vám umožní řídit, jak škálovat nebo upgradovat cluster pomocí rozhraní AKS API, a také načíst vaše kubeconfig.
2. Přístup k rozhraní Kubernetes API. Tento přístup se řídí buď [KUBERNETES RBAC](#kubernetes-role-based-access-control-kubernetes-rbac) (tradičně), nebo [integrací Azure RBAC s AKS pro autorizaci Kubernetes](#azure-rbac-for-kubernetes-authorization-preview) .

### <a name="azure-rbac-to-authorize-access-to-the-aks-resource"></a>Azure RBAC pro autorizaci přístupu k prostředku AKS

Pomocí služby Azure RBAC můžete uživatelům (nebo identitám) poskytnout podrobný přístup k AKS prostředkům v jednom nebo několika předplatných. Můžete mít například [roli Přispěvatel služby Azure Kubernetes](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role) , která umožňuje provádět akce jako škálování a upgradovat cluster. I když by jiný uživatel mohl mít [roli Správce clusteru služby Azure Kubernetes](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) , která uděluje oprávnění pouze k vyžádání kubeconfig správce.

Další možností je poskytnout uživateli obecnou roli [přispěvatele](../role-based-access-control/built-in-roles.md#contributor) , která by zahrnovala výše uvedená oprávnění a každou akci v prostředku AKS s výjimkou oprávnění ke správě samotného.

Podívejte se na Další informace o použití služby Azure RBAC k zabezpečení přístupu k souboru kubeconfig, který poskytuje přístup k rozhraní [Kubernetes API.](control-kubeconfig-access.md)

### <a name="azure-rbac-for-kubernetes-authorization-preview"></a>Azure RBAC pro autorizaci Kubernetes (Preview)

Díky integraci Azure RBAC AKS použije server Webhooku Kubernetes pro autorizaci, který vám umožní spravovat oprávnění a přiřazení prostředků clusteru K8s integrovaných do Azure AD pomocí definice role Azure a přiřazení rolí.

![Azure RBAC pro Kubernetes autorizační tok](media/concepts-identity/azure-rbac-k8s-authz-flow.png)

Jak je vidět na výše uvedeném diagramu, při použití Integrace Azure RBAC všechny požadavky na rozhraní Kubernetes API se budou řídit stejným tokem ověřování, jak je vysvětleno v [části Azure Active Integration](#azure-active-directory-integration). 

Ale i po tom, místo výhradně se spoléháte na Kubernetes RBAC o autorizaci, je požadavek ve skutečnosti ověřený v Azure, pokud identita, která požadavek vytvořila, existuje v AAD. Pokud identita v AAD neexistuje, třeba účet služby Kubernetes, služba Azure RBAC se nepřipojí a bude to normální Kubernetes RBAC.

V tomto scénáři můžete uživatelům udělit jednu ze čtyř integrovaných rolí nebo vytvářet vlastní role, jako byste to provedli s rolemi Kubernetes, ale v tomto případě pomocí mechanismů Azure RBAC a rozhraní API. 

Tato funkce vám umožní například nejen udělit uživatelům oprávnění k prostředku AKS napříč předplatnými, ale nastavit a udělit jim role a oprávnění, která budou mít v každém z těchto clusterů, které řídí přístup k rozhraní Kubernetes API. Můžete například udělit `Azure Kubernetes Service RBAC Viewer` roli v oboru předplatného a její příjemce bude moci vypsat a načíst všechny Kubernetes objekty ze všech clusterů, ale nebude je upravovat.


#### <a name="built-in-roles"></a>Vestavěné role

AKS poskytuje následující čtyři předdefinované role. Jsou podobné [integrovaným rolím Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles) , ale s několika rozdíly, jako je podpora CRDs. Úplný seznam akcí povolených všemi integrovanými rolemi najdete [tady](../role-based-access-control/built-in-roles.md).

| Role                                | Popis  |
|-------------------------------------|--------------|
| Prohlížeč RBAC pro službu Azure Kubernetes  | Povoluje přístup jen pro čtení k zobrazení většiny objektů v oboru názvů. Nepovoluje zobrazení rolí nebo vazeb rolí. Tato role nepovoluje zobrazení `Secrets` , protože čtení obsahu tajných kódů umožňuje přístup k `ServiceAccount` přihlašovacím údajům v oboru názvů, což by mohlo povolit přístup k rozhraní API jako jakékoli `ServiceAccount` v oboru názvů (forma eskalace oprávnění).  |
| Zapisovač RBAC služby Azure Kubernetes | Povoluje přístup pro čtení a zápis většiny objektů v oboru názvů. Tato role nepovoluje prohlížení a úpravy rolí nebo vazeb rolí. Tato role však umožňuje přístup k `Secrets` luskům a jejich spuštění jako libovolných ServiceAccount v oboru názvů, takže se dá použít k získání úrovní přístupu rozhraní API všech ServiceAccount v oboru názvů. |
| Správce RBAC služby Azure Kubernetes  | Povoluje přístup správce, který má být udělen v rámci oboru názvů. Umožňuje přístup pro čtení a zápis většiny prostředků v oboru názvů (nebo oboru clusteru), včetně možnosti vytvářet role a vazby rolí v rámci oboru názvů. Tato role nepovoluje přístup pro zápis k kvótě prostředků nebo samotnému oboru názvů. |
| Správce clusteru RBAC služby Azure Kubernetes  | Umožňuje přístupu super uživatele k provedení jakékoli akce u libovolného prostředku. Poskytuje plnou kontrolu nad všemi prostředky v clusteru a ve všech oborech názvů. |

**Pokud chcete zjistit, jak povolit Azure RBAC pro autorizaci Kubernetes, [Přečtěte si sem](manage-azure-rbac.md).**

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak začít s Azure AD a Kubernetes RBAC, najdete v tématu věnovaném [integraci Azure Active Directory s AKS][aks-aad].
- Související osvědčené postupy najdete v tématu [osvědčené postupy pro ověřování a autorizaci v AKS][operator-best-practices-identity].
- Pokud chcete začít s Azure RBAC pro autorizaci Kubernetes, přečtěte si téma [použití služby Azure RBAC k autorizaci přístupu v rámci clusteru Azure Kubernetes Service (AKS)](manage-azure-rbac.md).
- Pokud chcete začít zabezpečit soubor kubeconfig, přečtěte si téma [omezení přístupu ke konfiguračnímu souboru clusteru](control-kubeconfig-access.md) .

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
[openid-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
