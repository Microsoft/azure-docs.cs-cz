---
title: Koncepty – přístup a identita ve službě Azure Kubernetes Services (AKS)
description: Přečtěte si o přístupu a identitě ve službě Azure Kubernetes (AKS), včetně Integrace Azure Active Directory, řízení přístupu na základě role Kubernetes (Kubernetes RBAC) a rolí a vazeb.
services: container-service
ms.topic: conceptual
ms.date: 03/24/2021
author: palma21
ms.author: jpalma
ms.openlocfilehash: 76871565e0bb4ca1811d46531d07b89181d07e19
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105914"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Možnosti identit a přístupu pro Azure Kubernetes Service (AKS)

V různých způsobech můžete ověřovat, autorizovat, zabezpečovat a řídit přístup k Kubernetes clusterům. 
* Pomocí Kubernetes řízení přístupu založeného na rolích (Kubernetes RBAC) můžete uživatelům, skupinám a účtům služeb udělit přístup pouze k potřebným prostředkům. 
* Pomocí služby Azure Kubernetes Service (AKS) můžete dál zvyšovat strukturu zabezpečení a oprávnění prostřednictvím Azure Active Directory a Azure RBAC. 

Kubernetes RBAC a AKS vám pomůžou zabezpečit přístup ke clusteru a poskytovat jenom minimální požadovaná oprávnění vývojářům a operátorům.

V tomto článku se seznámíte se základními koncepcemi, které vám pomůžou ověřit a přiřadit oprávnění v AKS.

## <a name="aks-service-permissions"></a>Oprávnění služby AKS

Při vytváření clusteru AKS generuje nebo upravuje prostředky, které potřebuje (například virtuální počítače a síťové karty) k vytvoření a spuštění clusteru jménem uživatele. Tato identita se liší od oprávnění identity clusteru, které se vytváří při vytváření clusteru.

### <a name="identity-creating-and-operating-the-cluster-permissions"></a>Identita, která vytváří a pracuje s oprávněními clusteru

Identita, která vytváří a provozuje cluster, vyžaduje následující oprávnění.

| Oprávnění | Důvod |
|---|---|
| `Microsoft.Compute/diskEncryptionSets/read` | Vyžaduje se pro čtení ID sady šifrování disku. |
| `Microsoft.Compute/proximityPlacementGroups/write` | Vyžaduje se pro aktualizaci skupin umístění blízkosti. |
| `Microsoft.Network/applicationGateways/read` <br/> `Microsoft.Network/applicationGateways/write` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | Vyžaduje se ke konfiguraci aplikačních bran a připojení k podsíti. |
| `Microsoft.Network/virtualNetworks/subnets/join/action` | Vyžaduje se ke konfiguraci skupiny zabezpečení sítě pro podsíť při použití vlastní virtuální sítě.|
| `Microsoft.Network/publicIPAddresses/join/action` <br/> `Microsoft.Network/publicIPPrefixes/join/action` | Vyžaduje se ke konfiguraci odchozích veřejných IP adres na Standard Load Balancer. |
| `Microsoft.OperationalInsights/workspaces/sharedkeys/read` <br/> `Microsoft.OperationalInsights/workspaces/read` <br/> `Microsoft.OperationsManagement/solutions/write` <br/> `Microsoft.OperationsManagement/solutions/read` <br/> `Microsoft.ManagedIdentity/userAssignedIdentities/assign/action` | Vyžaduje se k vytváření a aktualizaci Log Analyticsch pracovních prostorů a monitorování Azure pro kontejnery. |

### <a name="aks-cluster-identity-permissions"></a>Oprávnění pro identitu clusteru AKS

Identitu clusteru AKS, která je vytvořená a přidružená k clusteru AKS, používá následující oprávnění. Každé oprávnění se používá z následujících důvodů:

| Oprávnění | Důvod |
|---|---|
| `Microsoft.ContainerService/managedClusters/*`  <br/> | Vyžaduje se pro vytváření uživatelů a provoz clusteru.
| `Microsoft.Network/loadBalancers/delete` <br/> `Microsoft.Network/loadBalancers/read` <br/> `Microsoft.Network/loadBalancers/write` | Vyžaduje se ke konfiguraci nástroje pro vyrovnávání zatížení pro službu Vyrovnávání zatížení. |
| `Microsoft.Network/publicIPAddresses/delete` <br/> `Microsoft.Network/publicIPAddresses/read` <br/> `Microsoft.Network/publicIPAddresses/write` | Je nutné najít a nakonfigurovat veřejné IP adresy pro službu Vyrovnávání zatížení. |
| `Microsoft.Network/publicIPAddresses/join/action` | Vyžaduje se pro konfiguraci veřejných IP adres pro službu Vyrovnávání zatížení. |
| `Microsoft.Network/networkSecurityGroups/read` <br/> `Microsoft.Network/networkSecurityGroups/write` | Je nutné vytvořit nebo odstranit pravidla zabezpečení pro službu Vyrovnávání zatížení. |
| `Microsoft.Compute/disks/delete` <br/> `Microsoft.Compute/disks/read` <br/> `Microsoft.Compute/disks/write` <br/> `Microsoft.Compute/locations/DiskOperations/read` | Vyžaduje se ke konfiguraci AzureDisks. |
| `Microsoft.Storage/storageAccounts/delete` <br/> `Microsoft.Storage/storageAccounts/listKeys/action` <br/> `Microsoft.Storage/storageAccounts/read` <br/> `Microsoft.Storage/storageAccounts/write` <br/> `Microsoft.Storage/operations/read` | Vyžaduje se ke konfiguraci účtů úložiště pro AzureFile nebo AzureDisk. |
| `Microsoft.Network/routeTables/read` <br/> `Microsoft.Network/routeTables/routes/delete` <br/> `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` <br/> `Microsoft.Network/routeTables/write` | Vyžaduje se ke konfiguraci směrovacích tabulek a tras pro uzly. |
| `Microsoft.Compute/virtualMachines/read` | Vyžaduje se k vyhledání informací pro virtuální počítače v VMAS, jako jsou zóny, doména selhání, velikost a datové disky. |
| `Microsoft.Compute/virtualMachines/write` | Vyžaduje se k připojení AzureDisks k virtuálnímu počítači v VMAS. |
| `Microsoft.Compute/virtualMachineScaleSets/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/instanceView/read` | Vyžaduje se k vyhledání informací o virtuálních počítačích v sadě škálování virtuálních počítačů, jako jsou zóny, doména selhání, velikost a datové disky. |
| `Microsoft.Network/networkInterfaces/write` | Vyžadováno pro přidání virtuálního počítače do VMAS do fondu adres back-endu nástroje pro vyrovnávání zatížení. |
| `Microsoft.Compute/virtualMachineScaleSets/write` | Vyžaduje se pro přidání škály virtuálních počítačů do back-endového fondu adres služby Load Balancer a navýšení kapacity uzlů v sadě škálování virtuálního počítače. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/write` | Vyžaduje se pro připojení AzureDisks a přidání virtuálního počítače ze sady škálování virtuálního počítače do nástroje pro vyrovnávání zatížení. |
| `Microsoft.Network/networkInterfaces/read` | Vyžadováno pro hledání interních IP adres a back-endové fondy adres pro virtuální počítače v VMAS. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read` | Vyžadováno pro hledání interních IP adres a back-endové fondy adres pro virtuální počítač v sadě škálování virtuálního počítače. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipconfigurations/publicipaddresses/read` | Vyžaduje se najít veřejné IP adresy pro virtuální počítač v sadě škálování virtuálního počítače. |
| `Microsoft.Network/virtualNetworks/read` <br/> `Microsoft.Network/virtualNetworks/subnets/read` | Vyžaduje se k ověření, jestli podsíť existuje pro interní nástroj pro vyrovnávání zatížení v jiné skupině prostředků. |
| `Microsoft.Compute/snapshots/delete` <br/> `Microsoft.Compute/snapshots/read` <br/> `Microsoft.Compute/snapshots/write` | Vyžaduje se ke konfiguraci snímků pro AzureDisk. |
| `Microsoft.Compute/locations/vmSizes/read` <br/> `Microsoft.Compute/locations/operations/read` | Vyžadovaná k nalezení velikostí virtuálních počítačů pro hledání AzureDisk limitů svazků. |

### <a name="additional-cluster-identity-permissions"></a>Další oprávnění pro identitu clusteru

Při vytváření clusteru s konkrétními atributy budete pro identitu clusteru potřebovat následující dodatečná oprávnění. Vzhledem k tomu, že tato oprávnění nejsou přiřazena automaticky, musíte je po vytvoření přidat do identity clusteru.

| Oprávnění | Důvod |
|---|---|
| `Microsoft.Network/networkSecurityGroups/write` <br/> `Microsoft.Network/networkSecurityGroups/read` | Vyžaduje se, pokud používáte skupinu zabezpečení sítě v jiné skupině prostředků. Vyžaduje se ke konfiguraci pravidel zabezpečení pro službu Vyrovnávání zatížení. |
| `Microsoft.Network/virtualNetworks/subnets/read` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | Vyžaduje se, pokud používáte podsíť v jiné skupině prostředků, jako je třeba vlastní virtuální síť. |
| `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` | Vyžaduje se, pokud používáte podsíť přidruženou k tabulce směrování v jiné skupině prostředků, jako je například vlastní virtuální síť s vlastní směrovací tabulkou. Vyžaduje se, aby se ověřilo, jestli pro podsíť v jiné skupině prostředků už existuje podsíť. |
| `Microsoft.Network/virtualNetworks/subnets/read` | Vyžaduje se, pokud používáte interní nástroj pro vyrovnávání zatížení v jiné skupině prostředků. Vyžaduje se, aby se ověřilo, jestli už podsíť pro interní nástroj pro vyrovnávání zatížení ve skupině prostředků existuje. |
| `Microsoft.Network/privatednszones/*` | Vyžaduje se, pokud používáte privátní zónu DNS v jiné skupině prostředků, jako je třeba vlastní privateDNSZone. |

## <a name="kubernetes-rbac"></a>Kubernetes RBAC

Kubernetes RBAC poskytuje podrobné filtrování akcí uživatele. Pomocí tohoto mechanismu řízení:
* Uživatelům nebo skupinám uživatelů přiřadíte oprávnění k vytváření a úpravám prostředků nebo zobrazení protokolů ze spuštěných úloh aplikací. 
* Můžete nastavit rozsah oprávnění na jeden obor názvů nebo v celém clusteru AKS. 
* Vytvoříte *role* pro definování oprávnění a pak těmto rolím přiřadíte uživatelům s *vazbami rolí*.

Další informace najdete v tématu [použití autorizace KUBERNETES RBAC][kubernetes-rbac].

### <a name="roles-and-clusterroles"></a>Role a ClusterRoles

#### <a name="roles"></a>Role
Před přiřazením oprávnění uživatelům s Kubernetes RBAC budete definovat oprávnění uživatele jako *roli*. Udělení oprávnění v rámci oboru názvů pomocí rolí. 

> [!NOTE]
> Role Kubernetes *udělují* oprávnění; neodepře  oprávnění.

Pokud chcete udělit oprávnění napříč celým clusterem nebo prostředky clusteru mimo daný obor názvů, můžete místo toho použít *ClusterRoles*.

#### <a name="clusterroles"></a>ClusterRoles

ClusterRole udělí a uplatňuje oprávnění pro prostředky v celém clusteru, nikoli na konkrétní obor názvů.

### <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings a ClusterRoleBindings

Jakmile budete mít definované role pro udělení oprávnění k prostředkům, přiřadíte jim oprávnění Kubernetes RBAC k *RoleBinding*. Pokud se váš cluster AKS [integruje s Azure Active Directory (Azure AD)](#azure-ad-integration), RoleBindings udělí uživatelům Azure AD oprávnění k provádění akcí v rámci clusteru. Podívejte se, jak v [nástroji řídit přístup k prostředkům clusteru pomocí řízení přístupu na základě role Kubernetes a identit Azure Active Directory](azure-ad-rbac.md).

#### <a name="rolebindings"></a>RoleBindings

Přiřaďte role uživatelům pro daný obor názvů pomocí RoleBindings. Pomocí RoleBindings můžete logicky oddělit jeden cluster AKS a povolit uživatelům přístup k prostředkům aplikace v jejich přiřazeném oboru názvů. 

Chcete-li vytvořit vazby mezi rolemi v celém clusteru nebo prostředky clusteru mimo daný obor názvů, místo toho použijte *ClusterRoleBindings*.

#### <a name="clusterrolebinding"></a>Vazby rolí clusteru

S ClusterRoleBinding můžete navazovat role na uživatele a použít je na prostředky v celém clusteru, nikoli na konkrétní obor názvů. Tento přístup vám umožní udělit správcům nebo pracovníkům podpory přístup ke všem prostředkům v clusteru AKS.


> [!NOTE]
> Microsoft/AKS provádí jakékoli akce clusteru s uživatelským souhlasem v rámci předdefinované role Kubernetes `aks-service` a předdefinované vazby role `aks-service-rolebinding` . 
> 
> Tato role umožňuje AKS řešit a diagnostikovat problémy s clustery, ale nemůže měnit oprávnění ani vytvářet role nebo vazby rolí ani jiné akce s vysokou úrovní oprávnění. Přístup k rolím je povolený jenom v aktivních lístkech podpory s přístupem JIT (just-in-time). Přečtěte si další informace o [zásadách podpory AKS](support-policies.md).


### <a name="kubernetes-service-accounts"></a>Účty služby Kubernetes

*Účty služeb* jsou jedním z hlavních typů uživatelů v Kubernetes. Rozhraní Kubernetes API uchovává a spravuje účty služeb. Přihlašovací údaje účtu služby se ukládají jako tajné kódy Kubernetes a umožňují jejich použití autorizovanými lusky ke komunikaci se serverem API. Většina požadavků rozhraní API poskytuje ověřovací token pro účet služby nebo běžný uživatelský účet.

Běžné uživatelské účty umožňují pružnější přístup pro lidské správce nebo vývojáře, ne jenom služby a procesy. I když Kubernetes neposkytuje řešení pro správu identit k ukládání běžných uživatelských účtů a hesel, můžete integrovat řešení externích identit do Kubernetes. V případě AKS clusterů je toto integrované řešení identit Azure AD.

Další informace o možnostech identity v Kubernetes najdete v tématu [ověřování Kubernetes][kubernetes-authentication].

## <a name="azure-ad-integration"></a>Integrace Azure AD

Zvyšte zabezpečení clusteru AKS pomocí integrace služby Azure AD. Služba Azure AD je postavená na základě počtu desetiletí Enterprise Identity Management, což je víceklientské cloudové služby pro správu identit, které spojují základní adresářové služby, správu přístupu k aplikacím a Identity Protection. Pomocí Azure AD můžete integrovat místní identity do clusterů AKS a poskytnout tak jeden zdroj pro správu a zabezpečení účtů.

![Azure Active Directory integrace s clustery AKS](media/concepts-identity/aad-integration.png)

S integrovanými clustery AKS Azure AD můžete uživatelům nebo skupinám udělit přístup k prostředkům Kubernetes v rámci oboru názvů nebo napříč clusterem. 

1. Pro získání `kubectl` kontextu konfigurace spustí uživatel příkaz [AZ AKS Get-Credentials][az-aks-get-credentials] . 
1. Když uživatel komunikuje s clusterem AKS s `kubectl` , zobrazí se jim výzva k přihlášení pomocí přihlašovacích údajů Azure AD. 

Tento přístup poskytuje jeden zdroj pro správu uživatelských účtů a přihlašovací údaje k heslům. Uživatel má přístup pouze k prostředkům definovaným správcem clusteru.

Ověřování Azure AD je k dispozici pro clustery AKS s OpenID Connect. OpenID Connect je vrstva identity postavená nad protokolem OAuth 2,0. Další informace o OpenID připojení najdete v dokumentaci k [otevřenému ID Connect][openid-connect]. V rámci clusteru Kubernetes se ověřování pomocí [tokenu Webhooku][webhook-token-docs] používá k ověření ověřovacích tokenů. Ověřování tokenu Webhooku je nakonfigurované a spravované jako součást clusteru AKS.

### <a name="webhook-and-api-server"></a>Webhook a Server API

![Tok ověřování serveru Webhook a API](media/concepts-identity/auth-flow.png)

Jak je znázorněno na obrázku výše, Server rozhraní API volá server Webhooku AKS a provede následující kroky:

1. `kubectl` používá klientskou aplikaci Azure AD k přihlašování uživatelů pomocí [toku udělení autorizace zařízení OAuth 2,0](../active-directory/develop/v2-oauth2-device-code.md).
2. Azure AD poskytuje access_token, id_token a refresh_token.
3. Uživatel odešle požadavek na `kubectl` access_token z `kubeconfig` .
4. `kubectl` odešle access_token serveru rozhraní API.
5. Server rozhraní API je nakonfigurovaný se serverem Webhooku ověřování a provede ověření.
6. Server Webhooku ověřování potvrdí, že je podpis JSON Web Token platný, kontrolou veřejného podpisového klíče Azure AD.
7. Serverová aplikace používá k dotazování členství přihlášeného uživatele ze služby MS Graph API uživateli zadané přihlašovací údaje.
8. Do serveru rozhraní API se pošle odpověď s informacemi o uživateli, jako je například deklarace identity přístupového tokenu (UPN) hlavního názvu uživatele (UPN), a členství uživatele ve skupině na základě ID objektu.
9. Rozhraní API provede rozhodnutí o autorizaci na základě role Kubernetes nebo RoleBinding.
10. Po ověření Server API vrátí odpověď na `kubectl` .
11. `kubectl` poskytuje zpětnou vazbu uživateli.
 
Naučte se integrovat AKS s Azure AD s [využitím našeho průvodce integrací Azure AD spravovaného AKS](managed-aad.md).

## <a name="azure-role-based-access-control"></a>Řízení přístupu na základě role v Azure

Řízení přístupu na základě role (RBAC) v Azure je autorizační systém založený na [Azure Resource Manager](../azure-resource-manager/management/overview.md) , který poskytuje jemně odstupňovanou správu prostředků Azure.

| Systém RBAC | Description |
|---|---|
| Kubernetes RBAC | Tato funkce je navržená pro práci na Kubernetesch prostředcích v rámci clusteru AKS. |
| Azure RBAC | Navrženo pro práci s prostředky v rámci vašeho předplatného Azure. |

Pomocí Azure RBAC můžete vytvořit *definici role* , která bude mít přehled o oprávněních, která se mají použít. Pak uživateli nebo skupině přiřadíte tuto definici role prostřednictvím *přiřazení role* pro konkrétní *obor*. Oborem může být individuální prostředek, skupina prostředků nebo celé předplatné.

Další informace najdete v tématu [co je řízení přístupu na základě role Azure (Azure RBAC)?][azure-rbac]

Existují dvě úrovně přístupu, které jsou potřeba k plnému provozu clusteru AKS: 
* [Přístup k prostředku AKS ve vašem předplatném Azure](#azure-rbac-to-authorize-access-to-the-aks-resource). 
  * Řízení škálování nebo upgrade clusteru pomocí rozhraní API AKS.
  * Vyžádání `kubeconfig` .
* Přístup k rozhraní Kubernetes API. Tento přístup je řízený jedním z těchto:
  * [KUBERNETES RBAC](#kubernetes-rbac) (tradičně).
  * [Integrace služby Azure RBAC s AKS pro autorizaci Kubernetes](#azure-rbac-for-kubernetes-authorization-preview).

### <a name="azure-rbac-to-authorize-access-to-the-aks-resource"></a>Azure RBAC pro autorizaci přístupu k prostředku AKS

Pomocí služby Azure RBAC můžete uživatelům (nebo identitám) poskytnout podrobný přístup k AKS prostředkům v jednom nebo několika předplatných. Můžete například použít [roli Přispěvatel služby Azure Kubernetes](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role) ke škálování a upgradu clusteru. Mezitím má jiný uživatel s [rolí správce clusteru služby Azure Kubernetes](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) oprávnění k vyžádání správce `kubeconfig` .

Případně můžete uživateli poskytnout roli obecného [přispěvatele](../role-based-access-control/built-in-roles.md#contributor) . Role obecného přispěvatele umožňuje uživatelům provádět výše uvedená oprávnění a každou akci u prostředku AKS, s výjimkou oprávnění ke správě.

[Pomocí Azure RBAC definujte přístup ke konfiguračnímu souboru Kubernetes v AKS](control-kubeconfig-access.md).

### <a name="azure-rbac-for-kubernetes-authorization-preview"></a>Azure RBAC pro autorizaci Kubernetes (Preview)

S integrací Azure RBAC AKS použije server Webhooku Kubernetes pro autorizaci, abyste mohli spravovat oprávnění a přiřazení prostředků clusteru integrovaného s Azure AD pomocí definice role Azure a přiřazení rolí.

![Azure RBAC pro Kubernetes autorizační tok](media/concepts-identity/azure-rbac-k8s-authz-flow.png)

Jak je znázorněno na výše uvedeném diagramu, při použití Integrace Azure RBAC budou všechny požadavky na rozhraní API Kubernetes následovat po stejném toku ověřování, jak je vysvětleno v [části věnované integraci Azure Active Directory](#azure-ad-integration). 

Pokud identita, kterou tento požadavek provádí, existuje ve službě Azure AD, Azure provede tým se Kubernetes RBAC k autorizaci žádosti. Pokud identita existuje mimo Azure AD (tj. účet služby Kubernetes), autorizaci pozastaví normální Kubernetes RBAC.

V tomto scénáři použijete mechanismy a rozhraní API Azure RBAC k přiřazení předdefinovaných rolí uživatelům nebo k vytváření vlastních rolí, stejně jako byste s Kubernetes rolemi. 

Pomocí této funkce neudělíte uživatelům oprávnění k AKS prostředku napříč předplatnými, ale zároveň nakonfigurujete roli a oprávnění pro všechny tyto clustery řídící přístup k rozhraní Kubernetes API. Můžete například udělit `Azure Kubernetes Service RBAC Viewer` roli v oboru předplatného. Příjemce role bude moci vypsat a načíst všechny Kubernetes objekty ze všech clusterů bez změny.

> [!IMPORTANT]
> Před použitím této funkce je potřeba povolit Azure RBAC pro autorizaci Kubernetes. Pokud potřebujete další podrobnosti a podrobné pokyny, postupujte podle pokynů v tématu [použití Azure RBAC pro Kubernetes autorizaci](manage-azure-rbac.md) .

#### <a name="built-in-roles"></a>Vestavěné role

AKS poskytuje následující čtyři předdefinované role. Jsou podobné [integrovaným rolím Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles) s několika rozdíly, jako je podpora CRDs. Podívejte se na úplný seznam akcí povolených jednotlivými [integrovanými rolemi Azure](../role-based-access-control/built-in-roles.md).

| Role                                | Popis  |
|-------------------------------------|--------------|
| Prohlížeč RBAC pro službu Azure Kubernetes  | Povoluje přístup jen pro čtení k zobrazení většiny objektů v oboru názvů. <br> Nepovoluje zobrazení rolí nebo vazeb rolí.<br> Nepovoluje zobrazení `Secrets` . Čtení `Secrets` obsahu umožňuje přístup k `ServiceAccount` přihlašovacím údajům v oboru názvů, což by mohlo povolit přístup k rozhraní API jako jakékoli `ServiceAccount` v oboru názvů (formu eskalace oprávnění).  |
| Zapisovač RBAC služby Azure Kubernetes | Povoluje přístup pro čtení a zápis většiny objektů v oboru názvů. <br> Nepovoluje zobrazování a úpravy rolí nebo vazeb rolí. <br> Umožňuje přístup k `Secrets` luskům a jejich spuštění jako libovolný ServiceAccount v oboru názvů, takže se dá použít k získání úrovní přístupu rozhraní API všech ServiceAccount v oboru názvů. |
| Správce RBAC služby Azure Kubernetes  | Povoluje přístup správce, který má být udělen v rámci oboru názvů. <br> Umožňuje přístup pro čtení a zápis většiny prostředků v oboru názvů (nebo oboru clusteru), včetně možnosti vytvářet role a vazby rolí v rámci oboru názvů. <br> Nepovoluje přístup pro zápis k kvótě prostředků nebo samotnému oboru názvů. |
| Správce clusteru RBAC služby Azure Kubernetes  | Umožňuje přístupu super uživatele k provedení jakékoli akce u libovolného prostředku. <br> Poskytuje plnou kontrolu nad všemi prostředky v clusteru a ve všech oborech názvů. |


## <a name="summary"></a>Souhrn

V tabulce najdete stručný přehled toho, jak se uživatelé můžou ověřit, aby se Kubernetesy, když je povolená integrace služby Azure AD. Ve všech případech je sekvence příkazů uživatele následující:
1. Spusťte `az login` ověřování v Azure.
1. Spusťte `az aks get-credentials` ke stažení pověření pro cluster do `.kube/config` .
1. Spusťte `kubectl` příkazy. 
   * První příkaz může aktivovat ověřování pomocí prohlížeče pro ověřování v clusteru, jak je popsáno v následující tabulce.

V Azure Portal můžete najít:
* Na kartě **Access Control** se zobrazí odkaz *udělení role* (Grant role Azure RBAC), na který se odkazuje ve druhém sloupci. 
* Na kartě **Konfigurace** se zobrazí skupina Azure AD pro správu clusteru.
  * Také Nalezeno s názvem parametru `--aad-admin-group-object-ids` v rozhraní příkazového řádku Azure CLI.


| Description        | Požadováno udělení role| Skupiny Azure AD pro správu clusteru | Kdy je použít |
| -------------------|------------|----------------------------|-------------|
| Starší přihlašovací údaje správce pomocí klientského certifikátu| **Role správce Azure Kubernetes**. Tato role umožňuje `az aks get-credentials` použití s `--admin` příznakem, který do uživatele stáhne [starší certifikát Správce clusteru (mimo Azure AD)](control-kubeconfig-access.md) `.kube/config` . Toto je jediný účel role správce Azure Kubernetes.|Není k dispozici|Pokud jste trvale zablokovali přístup k platné skupině Azure AD s přístupem k vašemu clusteru.| 
| Azure AD s ručním (cluster) RoleBindings| **Role uživatele Azure Kubernetes**. Roli "uživatel" lze `az aks get-credentials` použít bez `--admin` příznaku. (Toto je jediný účel "role uživatele Azure Kubernetes".) Výsledkem je, že na clusteru s podporou Azure AD je stažení [prázdné položky](control-kubeconfig-access.md) do `.kube/config` , která aktivuje ověřování založené na prohlížeči při prvním použití nástrojem `kubectl` .| Uživatel není v žádné z těchto skupin. Vzhledem k tomu, že uživatel není ve všech skupinách správců clusteru, budou jeho práva řízena výhradně všemi RoleBindings nebo ClusterRoleBindings, které byly nastaveny pomocí Správce clusterů. (Cluster) RoleBindings je [navržený jako uživatelé Azure AD nebo skupiny Azure AD](azure-ad-rbac.md) `subjects` . Pokud žádné takové vazby nejsou nastavené, uživatel nebude moct excute žádné `kubectl` příkazy.|Pokud chcete jemně odstupňované řízení přístupu a nepoužíváte Azure RBAC pro autorizaci Kubernetes. Všimněte si, že uživatel, který nastavuje vazby, se musí přihlásit jednou z dalších metod uvedených v této tabulce.|
| Azure AD podle člena skupiny správců| Stejný jako výše uvedený|Uživatel je členem jedné ze skupin, které jsou zde uvedeny. AKS automaticky generuje ClusterRoleBinding, který váže všechny uvedené skupiny k `cluster-admin` roli Kubernetes. Takže uživatelé v těchto skupinách můžou spouštět všechny `kubectl` příkazy jako `cluster-admin` .|Pokud chcete uživatelům pohodlně udělit úplná práva správce a _nepoužíváte_ pro autorizaci KUBERNETES Azure RBAC.|
| Azure AD s Azure RBAC pro autorizaci Kubernetes|Dvě role: <br> Nejprve **role uživatele Azure Kubernetes** (jak je uvedeno výše). <br> Druhý, jedna z "Azure Kubernetes Service **RBAC**..." výše uvedené role nebo vlastní alternativy.|Pole role správce na kartě konfigurace není důležité, pokud je povolená možnost Azure RBAC pro Kubernetes autorizaci.|Pro autorizaci Kubernetes používáte Azure RBAC. Tento přístup poskytuje jemně odstupňovaný ovládací prvek bez nutnosti nastavovat RoleBindings nebo ClusterRoleBindings.|

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak začít s Azure AD a Kubernetes RBAC, najdete v tématu věnovaném [integraci Azure Active Directory s AKS][aks-aad].
- Související osvědčené postupy najdete v tématu [osvědčené postupy pro ověřování a autorizaci v AKS][operator-best-practices-identity].
- Pokud chcete začít s Azure RBAC pro autorizaci Kubernetes, přečtěte si téma [použití služby Azure RBAC k autorizaci přístupu v rámci clusteru Azure Kubernetes Service (AKS)](manage-azure-rbac.md).
- Pokud chcete začít s zabezpečením `kubeconfig` souboru, přečtěte si téma [omezení přístupu ke konfiguračnímu souboru clusteru](control-kubeconfig-access.md) .

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
[upgrade-per-cluster]: ../azure-monitor/containers/container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli
