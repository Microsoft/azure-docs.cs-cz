---
title: Fáze nasazení podrobného plánu
description: Seznamte se s kroky souvisejícími s zabezpečením a artefakty, které služby Azure modrotisky procházejí během vytváření přiřazení podrobného plánu.
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: 473f8c7957994401ea6000ecc0d8023a89f8c349
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105560144"
---
# <a name="stages-of-a-blueprint-deployment"></a>Fáze nasazení podrobného plánu

Po nasazení podrobného plánu služba Azure Modrotiskys povede k nasazení prostředků definovaných v podrobném plánu. Tento článek obsahuje podrobné informace o tom, co každý krok zahrnuje.

Nasazení podrobného plánu se aktivuje přiřazením podrobného plánu k předplatnému nebo [aktualizací stávajícího přiřazení](../how-to/update-existing-assignments.md). Během nasazování získá Azure provedou následující kroky vysoké úrovně:

> [!div class="checklist"]
> - Azure modrotisky s udělenými právy vlastníka
> - Vytvoří se objekt přiřazení podrobného plánu.
> - Volitelné – Azure modrotisky vytvoří spravovanou identitu **přiřazenou systémem** .
> - Artefakty podrobného plánu nasazují spravované identity
> - Odvolat se služba Azure modrotisky **Service a spravovaná** práva spravované identity.

## <a name="azure-blueprints-granted-owner-rights"></a>Azure modrotisky s udělenými právy vlastníka

Instančnímu objektu služby Azure modrotisky se uděluje oprávnění vlastníka přiřazeného předplatného nebo předplatného, když se používá spravovaná identita [přiřazená systémem](../../../active-directory/managed-identities-azure-resources/overview.md) . Udělená role umožňuje službě Azure modrotisky vytvořit a později odvolat spravovanou identitu **přiřazenou systémem** . Pokud používáte spravovanou identitu **přiřazenou uživatelem** , instanční objekt služby Azure modrotiskys nezíská a nepotřebuje oprávnění vlastníka k tomuto předplatnému.

Práva se udělují automaticky v případě, že se přiřazení provádí prostřednictvím portálu. Pokud je však přiřazení provedeno prostřednictvím REST API, udělení práv musí být provedeno pomocí samostatného volání rozhraní API. Služba Azure modrotisky AppId je `f71766dc-90d9-4b7d-bd9d-4499c4331c3f` , ale instanční objekt se liší podle tenanta. K získání instančního objektu použijte [Azure Active Directory Graph API](/graph/migrate-azure-ad-graph-planning-checklist) a [servicePrincipals](/graph/api/resources/serviceprincipal) koncového bodu REST. Pak udělte Azure modrotisky roli _vlastníka_ prostřednictvím [portálu](../../../role-based-access-control/role-assignments-portal.md), rozhraní příkazového [řádku azure CLI](../../../role-based-access-control/role-assignments-cli.md), [Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md), [REST API](../../../role-based-access-control/role-assignments-rest.md)nebo [šablony Azure Resource Manager](../../../role-based-access-control/role-assignments-template.md).

Služba Azure Modrotiskys neimplementuje přímo prostředky.

## <a name="the-blueprint-assignment-object-is-created"></a>Vytvoří se objekt přiřazení podrobného plánu.

Uživatel, skupina nebo instanční objekt přiřadí k předplatnému plán. Objekt přiřazení existuje na úrovni předplatného, ve které byl plán plánu přiřazen. Prostředky vytvořené nasazením se neprovádí v kontextu nasazování entity.

Při vytváření přiřazení podrobného plánu se zvolí typ [spravované identity](../../../active-directory/managed-identities-azure-resources/overview.md) . Výchozím nastavením je spravovaná identita **přiřazená systémem** . Může být zvolena spravovaná identita **přiřazená uživatelem** . Při použití spravované identity **přiřazené uživatelem** musí být definováno a uděleno oprávnění před vytvořením přiřazení podrobného plánu. Pro vytvoření [](../../../role-based-access-control/built-in-roles.md#owner) [](../../../role-based-access-control/built-in-roles.md#blueprint-operator) `blueprintAssignment/write` přiřazení, které používá **uživatelem přiřazenou** identitu, musí mít předdefinované role jak vlastní, tak i operátor podrobného plánu.

## <a name="optional---azure-blueprints-creates-system-assigned-managed-identity"></a>Volitelné – Azure modrotisky vytvoří spravovanou identitu přiřazenou systémem.

Když je při přiřazení vybraná [spravovaná identita přiřazená systémem](../../../active-directory/managed-identities-azure-resources/overview.md) , Azure modrotisky vytvoří identitu a udělí spravované identitě roli [vlastníka](../../../role-based-access-control/built-in-roles.md#owner) . Pokud [je existující přiřazení upgradované](../how-to/update-existing-assignments.md), Azure modrotisky použije dříve vytvořenou spravovanou identitu.

Spravovaná identita související s přiřazením podrobného plánu se používá k nasazení nebo opětovnému nasazení prostředků definovaných v podrobném plánu. Tento návrh zabraňuje neúmyslnému narušování přiřazení.
Tento návrh také podporuje funkci [zamykání prostředků](./resource-locking.md) tím, že řídí zabezpečení všech nasazených prostředků z podrobného plánu.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>Artefakty podrobného plánu nasazují spravované identity

Spravovaná identita poté aktivuje Správce prostředků nasazení artefaktů v plánu v rámci definovaného [pořadí řazení](./sequencing-order.md). Pořadí může být upraveno, aby bylo zajištěno, že artefakty závislé na jiných artefaktech jsou nasazeny ve správném pořadí.

Příčinou selhání přístupu nasazení je často výsledek úrovně přístupu udělené spravované identitě. Služba Azure Modrotiskys spravuje životní cyklus zabezpečení spravované identity **přiřazené systémem** . Uživatel ale zodpovídá za správu práv a životního cyklu **uživatelsky přiřazené** spravované identity.

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>Oprávnění služby podrobného plánu a systémem přiřazená ke spravovaným identitám se odvolají.

Až se nasazení dokončí, Azure modrotisky odvolá práva spravované identity **přiřazené systémem** z předplatného. Služba Azure modrotisky potom odvolá svá práva k předplatnému. Odebrání práv brání tomu, aby se Azure modrotisky staly trvalým vlastníkem předplatného.

## <a name="next-steps"></a>Další kroky

- Principy použití [statických a dynamických parametrů](./parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](./sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](./resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../how-to/update-existing-assignments.md)
- Řešení potíží při přiřazení podrobného plánu – [obecné řešení potíží](../troubleshoot/general.md)