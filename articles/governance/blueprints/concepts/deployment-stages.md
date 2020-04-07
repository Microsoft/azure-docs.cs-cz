---
title: Fáze nasazení podrobného plánu
description: Seznamte se s kroky souvisejícími se zabezpečením a artefakty, kterými procházejí služby Azure Blueprints při vytváření přiřazení podrobného plánu.
ms.date: 11/13/2019
ms.topic: conceptual
ms.openlocfilehash: 61d19c84cd659b9df3a272c5c2743944e51df06e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677318"
---
# <a name="stages-of-a-blueprint-deployment"></a>Fáze nasazení podrobného plánu

Když se nasadí podrobný plán, služba Azure Blueprints provádí řadu akcí k nasazení prostředků definovaných v podrobném plánu. Tento článek obsahuje podrobnosti o tom, co zahrnuje každý krok.

Nasazení podrobného plánu se aktivuje přiřazením podrobného plánu k předplatnému nebo [aktualizací existujícího přiřazení](../how-to/update-existing-assignments.md). Během nasazení Azure Blueprints provede následující kroky na vysoké úrovni:

> [!div class="checklist"]
> - Azure Blueprints udělilpráva vlastníků
> - Objekt přiřazení podrobného plánu je vytvořen.
> - Volitelné – Azure Blueprints vytvoří **spravované identity přiřazené k systému**
> - Spravovaná identita nasazuje artefakty podrobného plánu
> - Služba Azure Blueprints a práva spravované identity **přiřazená k systému** jsou odvolána

## <a name="azure-blueprints-granted-owner-rights"></a>Azure Blueprints udělilpráva vlastníků

Instanční objekt služby Azure Blueprints je při použití spravované identity [spravované spravované identity přiřazené k systému](../../../active-directory/managed-identities-azure-resources/overview.md) udělena práva vlastníka přiřazeným předplatným nebo předplatným. Udělená role umožňuje Azure Blueprints vytvořit a později odvolat spravované identity **přiřazené systémem.** Pokud používáte **uživatelem přiřazenou spravovanou** identitu, hlavní povinný služeb Azure Blueprints nezíská a nepotřebuje práva vlastníka k předplatnému.

Práva jsou udělena automaticky, pokud je přiřazení provedeno prostřednictvím portálu. Pokud se však přiřazení provádí prostřednictvím rozhraní REST API, je třeba udělení práv provést pomocí samostatného volání rozhraní API. Azure Blueprints AppId `f71766dc-90d9-4b7d-bd9d-4499c4331c3f`je , ale instanční objekt se liší podle tenanta. Pomocí [rozhraní Azure Active Directory Graph API](../../../active-directory/develop/active-directory-graph-api.md) a služby REST [koncového boduPrincipals](/graph/api/resources/serviceprincipal) získejte instanční objekt. Potom udělte azure blueprints role _vlastníka_ prostřednictvím [portálu](../../../role-based-access-control/role-assignments-portal.md), [Azure CLI](../../../role-based-access-control/role-assignments-cli.md), [Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md), [REST API](../../../role-based-access-control/role-assignments-rest.md)nebo šablony [Správce prostředků](../../../role-based-access-control/role-assignments-template.md).

Služba Azure Blueprints přímo nenasazuje prostředky.

## <a name="the-blueprint-assignment-object-is-created"></a>Objekt přiřazení podrobného plánu je vytvořen.

Uživatel, skupina nebo instanční objekt přiřadí podrobný plán k předplatnému. Objekt přiřazení existuje na úrovni předplatného, kde byl přiřazen podrobný plán. Prostředky vytvořené nasazením se neprovádějí v kontextu nasazovací entity.

Při vytváření přiřazení podrobného plánu je vybrán typ [spravované identity.](../../../active-directory/managed-identities-azure-resources/overview.md) Výchozí hodnota je **spravovaná** identita přiřazená systémem. Lze zvolit **spravovanou** identitu přiřazenou uživatelem. Při použití spravované identity **přiřazené uživateli** musí být před vytvořením přiřazení podrobného plánu definována a udělena oprávnění. Předdefinované role [Vlastník](../../../role-based-access-control/built-in-roles.md#owner) a [Operátor podrobného plánu](../../../role-based-access-control/built-in-roles.md#blueprint-operator) mají potřebná `blueprintAssignment/write` oprávnění k vytvoření přiřazení, které používá spravovanou identitu **přiřazenou uživatelem.**

## <a name="optional---azure-blueprints-creates-system-assigned-managed-identity"></a>Volitelné – Azure Blueprints vytvoří spravované identity přiřazené k systému

Když je během přiřazení [k systému vybraná spravovaná identita,](../../../active-directory/managed-identities-azure-resources/overview.md) Azure Blueprints vytvoří identitu a udělí spravovanou identitu roli [vlastníka.](../../../role-based-access-control/built-in-roles.md#owner) Pokud [je upgradováno existující přiřazení](../how-to/update-existing-assignments.md), Azure Blueprints používá dříve vytvořenou spravovanou identitu.

Spravovaná identita související s přiřazením podrobného plánu se používá k nasazení nebo opětovnému nasazení prostředků definovaných v podrobném plánu. Tento návrh zabraňuje přiřazení nechtěně zasahovat do sebe.
Tento návrh také podporuje funkci [uzamčení prostředků](./resource-locking.md) tím, že řídí zabezpečení každého nasazeného prostředku z podrobného plánu.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>Spravovaná identita nasazuje artefakty podrobného plánu

Spravovaná identita pak aktivuje nasazení Správce prostředků artefaktů v rámci podrobného plánu v definovaném [pořadí řazení](./sequencing-order.md). Pořadí lze upravit tak, aby artefakty závislé na jiných artefaktů jsou nasazeny ve správném pořadí.

Selhání přístupu nasazení je často výsledkem úrovně přístupu udělené spravované identity. Služba Azure Blueprints spravuje životní cyklus zabezpečení spravované identity **přiřazené k systému.** Uživatel je však zodpovědný za správu práv a životního cyklu spravované identity **přiřazené uživateli.**

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>Služba Podrobného plánu a práva spravované identity přiřazená systémem jsou odvolána.

Po dokončení nasazení Azure Blueprints odvolá práva **systémově přiřazené** spravované identity z předplatného. Služba Azure Blueprints pak odvolá svá práva z předplatného. Odebrání práv zabrání Azure Blueprints stát trvalým vlastníkem na předplatné.

## <a name="next-steps"></a>Další kroky

- Pochopit, jak používat [statické a dynamické parametry](parameters.md).
- Naučte se přizpůsobit [pořadí sekvencování podrobných plánů](sequencing-order.md).
- Zjistěte, jak využít [zamykání prostředků podrobného plánu](resource-locking.md).
- Přečtěte si, jak [aktualizovat existující přiřazení](../how-to/update-existing-assignments.md).
- Vyřešte problémy během přiřazení podrobného plánu s [obecným řešením potíží](../troubleshoot/general.md).
