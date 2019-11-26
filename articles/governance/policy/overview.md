---
title: Přehled zásad Azure
description: Azure Policy je služba v Azure, pomocí které vytváříte, přiřazujete a spravujete definice zásad ve svém prostředí Azure.
ms.date: 11/25/2019
ms.topic: overview
ms.openlocfilehash: db6a7c592213b0ef8a17466300c37c859e96476b
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484010"
---
# <a name="what-is-azure-policy"></a>Co je Azure Policy?

Governance validates that your organization can achieve its goals through effective and efficient use of IT. It meets this need by creating clarity between business goals and IT projects.

Potýká se vaše společnost s velkým množstvím problémů v oblasti IT, které se zdánlivě nikdy nevyřeší? Dobré zásady správného řízení v oblasti IT zahrnují plánování iniciativ a nastavení priorit na strategické úrovni, která pomáhají překonávat problémy a předcházet jim. This strategic need is where Azure Policy comes in.

Azure Policy je služba v Azure, která slouží k vytváření, přiřazování a správě zásad. Tyto zásady vynucují na vašich prostředcích různá pravidla a účinky, aby tyto prostředky zůstaly kompatibilní s vašimi firemními standardy a smlouvami o úrovni služeb. Azure Policy meets this need by evaluating your resources for non-compliance with assigned policies. Například můžete mít zásadu, která ve vašem prostředí povoluje jenom určitou skladovou položku virtuálních počítačů. Once this policy is implemented, new and existing resources are evaluated for compliance. With the right type of policy, existing resources can be brought into compliance. Later in this documentation, we'll go over more details on how to create and implement policies with Azure Policy.

> [!IMPORTANT]
> Vyhodnocování dodržování předpisů ve službě Azure Policy je teď dostupné pro všechna přiřazení bez ohledu na cenovou úroveň. Pokud vaše přiřazení nezobrazují data dodržování předpisů, ujistěte se, že je předplatné zaregistrované u poskytovatele prostředků Microsoft.PolicyInsights.

[!INCLUDE [service-provider-management-toolkit](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-is-it-different-from-rbac"></a>Čím se to liší od RBAC?

There are a few key differences between Azure Policy and role-based access control (RBAC). RBAC se zaměřuje na akce uživatelů v různých oborech. You might be added to the contributor role for a resource group, allowing you to make changes to that resource group. Azure Policy focuses on resource properties during deployment and for already existing resources. Azure Policy controls properties such as the types or locations of resources. Unlike RBAC, Azure Policy is a default allow and explicit deny system.

### <a name="rbac-permissions-in-azure-policy"></a>Oprávnění RBAC ve službě Azure Policy

Služba Azure Policy má několik oprávnění, která se označují jako operace, ve dvou poskytovatelích prostředků:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Řada předdefinovaných rolí uděluje oprávnění k prostředkům Azure Policy. The **Resource Policy Contributor** role includes most Azure Policy operations. **Owner** has full rights. Both **Contributor** and **Reader** can use all read Azure Policy operations, but **Contributor** can also trigger remediation.

Pokud žádná z předdefinovaných rolí nemá požadovaná oprávnění, vytvořte [vlastní roli](../../role-based-access-control/custom-roles.md).

## <a name="policy-definition"></a>Definice zásady

Postup vytváření a implementace zásady v Azure Policy začíná vytvořením definice zásady. Every policy definition has conditions under which it's enforced. And, it has a defined effect that takes place if the conditions are met.

In Azure Policy, we offer several built-in policies that are available by default. Například:

- **Allowed Storage Account SKUs**: Determines if a storage account being deployed is within a set of SKU sizes. Its effect is to deny all storage accounts that don't adhere to the set of defined SKU sizes.
- **Allowed Resource Type**: Defines the resource types that you can deploy. Its effect is to deny all resources that aren't part of this defined list.
- **Allowed Locations**: Restricts the available locations for new resources. Účinkem je vynucení vašich požadavků na geografické dodržování předpisů.
- **Allowed Virtual Machine SKUs**: Specifies a set of virtual machine SKUs that you can deploy.
- **Add a tag to resources**: Applies a required tag and its default value if it's not specified by the deploy request.
- **Enforce tag and its value**: Enforces a required tag and its value to a resource.
- **Not allowed resource types**: Prevents a list of resource types from being deployed.

To implement these policy definitions (both built-in and custom definitions), you'll need to assign them. Jakékoli z těchto zásad můžete přiřadit prostřednictvím webu Azure Portal, PowerShellu nebo Azure CLI.

Policy evaluation happens with several different actions, such as policy assignment or policy updates. For a complete list, see [Policy evaluation triggers](./how-to/get-compliance-data.md#evaluation-triggers).

Další informace o strukturách definic zásad najdete v článku [Struktura definic zásad](./concepts/definition-structure.md).

## <a name="policy-assignment"></a>Přiřazení zásad

Přiřazení zásady je definice zásady, která byla přiřazena, aby proběhla v rámci zadaného oboru. Tento obor může sahat od [skupiny pro správu](../management-groups/overview.md) až po skupinu prostředků. Termín *obor* označuje všechny skupiny prostředků, předplatná nebo skupiny pro správu, ke kterým je definice zásady přiřazená. Všechny podřízené prostředky dědí přiřazení zásad. This design means that a policy applied to a resource group is also applied to resources in that resource group. Z přiřazení zásad však můžete vyloučit určitý podobor.

Například v oboru předplatného můžete přiřadit zásadu, která brání vytváření síťových prostředků. You could exclude a resource group in that subscription that is intended for networking infrastructure. You then grant access to this networking resource group to users that you trust with creating networking resources.

In another example, you might want to assign a resource type allow list policy at the management group level. A potom přiřadit mnohem mírnější zásady, které povolují více typů prostředků, u podřízené skupiny správy nebo dokonce přímo u předplatných. To ale nebude fungovat, protože zásady představují systém, kdy se explicitně zamítá. Místo toho musíte podřízenou skupinu správy nebo předplatné vyloučit z přiřazení zásad na úrovni skupiny správy. Potom přiřadíte mnohem mírnější zásady na úrovni podřízené skupiny správy nebo předplatného. If any policy results in a resource getting denied, then the only way to allow the resource is to modify the denying policy.

Další informace o nastavování definic a přiřazení zásad prostřednictvím portálu najdete v tématu [Vytvoření přiřazení zásady pro identifikaci neodpovídajících prostředků v prostředí Azure](assign-policy-portal.md). K dispozici jsou také kroky pro [PowerShell](assign-policy-powershell.md) a [Azure CLI](assign-policy-azurecli.md).

## <a name="policy-parameters"></a>Parametry zásad

Parametry zásad pomáhají zjednodušit správu zásad tím, že snižují počet definic zásad, které musíte vytvářet. Parametry můžete definovat při vytváření definice zásady a tím ji více zobecnit. Následně můžete tuto definici zásady použít opakovaně pro různé scénáře. Provedete to předáváním různých hodnot při přiřazování této definice zásady. Například můžete pro každé předplatné zadat jednu sadu umístění.

Parameters are defined when creating a policy definition. When a parameter is defined, it's given a name and optionally given a value. Pro zásadu můžete například definovat parametr s názvem *location* (umístění). Následně mu můžete při přiřazování zásady předávat různé hodnoty, například *EastUS* nebo *WestUS*.

For more information about policy parameters, see [Definition structure - Parameters](./concepts/definition-structure.md#parameters).

## <a name="initiative-definition"></a>Definice iniciativy

Definice iniciativy je kolekce definic zásad, které jsou přizpůsobené pro dosažení jednoho zastřešujícího cíle. Definice iniciativ zjednodušují správu a přiřazování definic zásad. Toto zjednodušení spočívá v seskupování sad zásad do jedné položky. Mohli byste například vytvořit iniciativu s názvem **Povolení monitorování v Azure Security Center** s cílem monitorovat všechna dostupná doporučení zabezpečení v Azure Security Center.

> [!NOTE]
> The SDK, such as Azure CLI and Azure PowerShell, use properties and parameters named **PolicySet** to refer to initiatives.

V rámci této iniciativy byste měli například tyto definice zásad:

- **Monitorování nešifrované služby SQL Database v Security Center** – pro monitorování nešifrovaných databází a serverů SQL.
- **Monitor OS vulnerabilities in Security Center** – For monitoring servers that don't satisfy the configured baseline.
- **Monitorování chybějící služby Endpoint Protection v Security Center** – pro monitorování serverů bez nainstalovaného agenta služby Endpoint Protection.

## <a name="initiative-assignment"></a>Přiřazení iniciativy

Podobně jako přiřazení zásady je přiřazení iniciativy definicí iniciativy přiřazenou ke konkrétnímu oboru. Přiřazení iniciativ omezují potřebu vytváření několika definic iniciativ pro každý obor. Tento obor může také sahat od skupiny pro správu až po skupinu prostředků.

Each initiative is assignable to different scopes. One initiative can be assigned to both **subscriptionA** and **subscriptionB**.

## <a name="initiative-parameters"></a>Parametry iniciativ

Podobně jako parametry zásad pomáhají parametry iniciativ zjednodušit správu iniciativ tím, že snižují redundanci. Initiative parameters are parameters being used by the policy definitions within the initiative.

Jako příklad může posloužit scénář, ve kterém máte definici iniciativy **initiativeC** s definicemi zásad **policyA** a **policyB**, z nichž každá očekává jiný typ parametru:

| Zásady | Název parametru |Typ parametru  |Poznámka |
|---|---|---|---|
| policyA | allowedLocations | pole  |Tento parametr jako hodnotu očekává seznam řetězců, protože typ parametru byl definovaný jako pole. |
| policyB | allowedSingleLocation |string |Tento parametr jako hodnotu očekává jedno slovo, protože typ parametru byl definovaný jako řetězec. |

V tomto scénáři máte při definování parametrů iniciativy pro **initiativeC** tři možnosti:

- Použít parametry definic zásad v rámci této iniciativy: V tomto příkladu se *allowedLocations* a *allowedSingleLocation* stanou parametry iniciativy pro **initiativeC**.
- Zadat hodnoty do parametrů definic zásad v rámci této definice iniciativy. In this example, you can provide a list of locations to **policyA's parameter – allowedLocations** and **policyB's parameter – allowedSingleLocation**. Hodnoty můžete zadat také při přiřazování této iniciativy.
- Zadat seznam možností *hodnot*, které se můžou použít při přiřazování této iniciativy. Když přiřadíte tuto iniciativu, zděděné parametry z definic zásad v rámci této iniciativy můžou mít pouze hodnoty z tohoto zadaného seznamu.

When creating value options in an initiative definition, you're unable to input a different value during the initiative assignment because it's not part of the list.

## <a name="maximum-count-of-azure-policy-objects"></a>Maximum count of Azure Policy objects

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="recommendations-for-managing-policies"></a>Doporučení pro správu zásad

Here are a few pointers and tips to keep in mind:

- Start with an audit effect instead of a deny effect to track impact of your policy definition on the resources in your environment. If you have scripts already in place to autoscale your applications, setting a deny effect may hinder such automation tasks already in place.

- Consider organizational hierarchies when creating definitions and assignments. We recommend creating definitions at higher levels such as the management group or subscription level. Then, create the assignment at the next child level. If you create a definition at a management group, the assignment can be scoped down to a subscription or resource group within that management group.

- We recommend creating and assigning initiative definitions even for a single policy definition.
  For example, you have policy definition *policyDefA* and create it under initiative definition *initiativeDefC*. If you create another policy definition later for *policyDefB* with goals similar to *policyDefA*, you can add it under *initiativeDefC* and track them together.

- Once you've created an initiative assignment, policy definitions added to the initiative also become part of that initiatives assignments.

- When an initiative assignment is evaluated, all policies within the initiative are also evaluated.
  If you need to evaluate a policy individually, it's better to not include it in an initiative.

## <a name="video-overview"></a>Video overview

Následující přehled služby Azure Policy se týká sestavení 2018. For slides or video download, visit [Govern your Azure environment through Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) on Channel 9.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="next-steps"></a>Další kroky

Získali jste přehled o službě Azure Policy a některých klíčových konceptech. Tady je návrh dalších kroků:

- [Assign a policy definition using the portal](./assign-policy-portal.md).
- [Assign a policy definition using the Azure CLI](./assign-policy-azurecli.md).
- [Assign a policy definition using PowerShell](./assign-policy-powershell.md).