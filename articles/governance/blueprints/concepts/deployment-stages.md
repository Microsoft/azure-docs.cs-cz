---
title: Fáze nasazení podrobného plánu
description: Seznamte se s kroky, pomocí kterých Azure Blueprint služby procházejí během nasazování.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
ms.openlocfilehash: 4645edde5163f1c8bca787416f5465e5a8f2d355
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978527"
---
# <a name="stages-of-a-blueprint-deployment"></a>Fáze nasazení podrobného plánu

Po nasazení podrobného plánu služba Azure Modrotiskys povede k nasazení prostředků definovaných v podrobném plánu. Tento článek obsahuje podrobné informace o tom, co každý krok zahrnuje.

Nasazení podrobného plánu se aktivuje přiřazením podrobného plánu k předplatnému nebo [aktualizací stávajícího přiřazení](../how-to/update-existing-assignments.md). Během nasazení provede plány následující kroky vysoké úrovně:

> [!div class="checklist"]
> - Plány s udělenými oprávněními vlastníka
> - Vytvoří se objekt přiřazení podrobného plánu.
> - Volitelné – modrotisky vytvoří spravovanou identitu **přiřazenou systémem** .
> - Artefakty podrobného plánu nasazují spravované identity
> - Oprávnění služby **podrobného plánu a systémem přiřazená** ke spravovaným identitám se odvolají.

## <a name="blueprints-granted-owner-rights"></a>Plány s udělenými oprávněními vlastníka

Instančnímu objektu služby Azure modrotisky se uděluje oprávnění vlastníka přiřazeného předplatného nebo předplatného. Přidělená role umožňuje vytvořit a později odvolat [spravovanou identitu přiřazenou systémem](../../../active-directory/managed-identities-azure-resources/overview.md).

Práva se udělují automaticky v případě, že se přiřazení provádí prostřednictvím portálu. Pokud je však přiřazení provedeno prostřednictvím REST API, udělení práv musí být provedeno pomocí samostatného volání rozhraní API. Azure Blueprint AppId je `f71766dc-90d9-4b7d-bd9d-4499c4331c3f`, ale instanční objekt se liší podle tenanta. K získání instančního objektu použijte [Azure Active Directory Graph API](../../../active-directory/develop/active-directory-graph-api.md) a [servicePrincipals](/graph/api/resources/serviceprincipal) koncového bodu REST. Pak udělte Azure modrotisky roli _vlastníka_ prostřednictvím [portálu](../../../role-based-access-control/role-assignments-portal.md), rozhraní příkazového [řádku azure CLI](../../../role-based-access-control/role-assignments-cli.md), [Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md), [REST API](../../../role-based-access-control/role-assignments-rest.md)nebo [šablony Správce prostředků](../../../role-based-access-control/role-assignments-template.md).

Služba modrotisky neimplementuje přímo prostředky.

## <a name="the-blueprint-assignment-object-is-created"></a>Vytvoří se objekt přiřazení podrobného plánu.

Uživatel, skupina nebo instanční objekt přiřadí k předplatnému plán. Objekt přiřazení existuje na úrovni předplatného, ve které byl plán plánu přiřazen. Prostředky vytvořené nasazením se neprovádí v kontextu nasazování entity.

Při vytváření přiřazení podrobného plánu se zvolí typ [spravované identity](../../../active-directory/managed-identities-azure-resources/overview.md) . Výchozím nastavením je spravovaná identita **přiřazená systémem** . Může být zvolena spravovaná identita **přiřazená uživatelem** . Při použití spravované identity **přiřazené uživatelem** musí být definováno a uděleno oprávnění před vytvořením přiřazení podrobného plánu.

## <a name="optional---blueprints-creates-system-assigned-managed-identity"></a>Volitelné – modrotisky vytvoří spravovanou identitu přiřazenou systémem.

Když je při přiřazení vybraná [spravovaná identita přiřazená systémem](../../../active-directory/managed-identities-azure-resources/overview.md) , modrotisky vytvoří identitu a udělí spravované identitě roli [vlastníka](../../../role-based-access-control/built-in-roles.md#owner) . Pokud [je existující přiřazení upgradované](../how-to/update-existing-assignments.md), budou se v nich používat dřív vytvořená spravovaná identita.

Spravovaná identita související s přiřazením podrobného plánu se používá k nasazení nebo opětovnému nasazení prostředků definovaných v podrobném plánu. Tento návrh zabraňuje neúmyslnému narušování přiřazení.
Tento návrh také podporuje funkci [zamykání prostředků](./resource-locking.md) tím, že řídí zabezpečení všech nasazených prostředků z podrobného plánu.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>Artefakty podrobného plánu nasazují spravované identity

Spravovaná identita poté aktivuje Správce prostředků nasazení artefaktů v plánu v rámci definovaného [pořadí řazení](./sequencing-order.md). Pořadí může být upraveno, aby bylo zajištěno, že artefakty závislé na jiných artefaktech jsou nasazeny ve správném pořadí.

Příčinou selhání přístupu nasazení je často výsledek úrovně přístupu udělené spravované identitě. Služba modrotisky spravuje životní cyklus zabezpečení spravované identity **přiřazené systémem** . Uživatel ale zodpovídá za správu práv a životního cyklu **uživatelsky přiřazené** spravované identity.

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>Oprávnění služby podrobného plánu a systémem přiřazená ke spravovaným identitám se odvolají.

Až se nasazení dokončí, plány odvolají práva spravované identity **přiřazené systémem** z předplatného. Služba modrotisky potom odvolá svá práva k předplatnému. Odebrání práv brání tomu, aby se podrobné plány staly trvalým vlastníkem předplatného.

## <a name="next-steps"></a>Další kroky

- Principy použití [statických a dynamických parametrů](parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../how-to/update-existing-assignments.md)
- Řešení potíží při přiřazení podrobného plánu – [obecné řešení potíží](../troubleshoot/general.md)