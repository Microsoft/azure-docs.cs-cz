---
title: Fáze nasazení podrobného plánu
description: Přečtěte si postup, podrobného plánu služby prochází během nasazení.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: a3ab5589cd327b73f2e66540da5c49343c4449cd
ms.sourcegitcommit: 4133f375862fdbdec07b70de047d70c66ac29d50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/15/2019
ms.locfileid: "57999167"
---
# <a name="stages-of-a-blueprint-deployment"></a>Fáze nasazení podrobného plánu

Když plán, podle kterého se nasadí, je obnáší sérii akcí za kterou služba Azure plány nasadit prostředky definované v podrobný plán. Tento článek obsahuje podrobnosti o co zahrnuje každého kroku.

Podrobný plán nasazení se aktivuje při přiřazení podrobný plán k předplatnému nebo [Aktualizace existujícího přiřazení](../how-to/update-existing-assignments.md). Během nasazení plány provedou se následující postup vysoké úrovně:

> [!div class="checklist"]
> - Udělit vlastnická práva podrobné plány
> - Je vytvořen objekt přiřazení podrobného plánu
> - Nepovinné - plány vytvoří **systém přiřadil** spravované identity
> - Spravovaná identita nasadí artefakty podrobného plánu.
> - Podrobný plán služby a **systém přiřadil** byly odvolány, práva spravovaná identita

## <a name="blueprints-granted-owner-rights"></a>Udělit vlastnická práva podrobné plány

Instanční objekt Azure plány je byla udělena práva vlastníka přiřazené předplatné nebo předplatné. Udělená role umožňuje podrobné plány. k vytvoření a později přístup, [systém přiřadil se identita spravované](../../../active-directory/managed-identities-azure-resources/overview.md).

Práva jsou udělena automaticky, pokud se přiřazuje přes portál. Ale pokud přiřazení se provádí prostřednictvím rozhraní REST API, udělení oprávnění musí být samostatné rozhraní API volat. ID aplikace Azure podrobného plánu se `f71766dc-90d9-4b7d-bd9d-4499c4331c3f`, ale instanční objekt služby se liší podle klienta. Použití [Azure Active Directory Graph API](../../../active-directory/develop/active-directory-graph-api.md) a koncový bod REST [servicePrincipals](/graph/api/resources/serviceprincipal) zobrazíte instanční objekt služby. Udělte plány Azure _vlastníka_ role prostřednictvím [portál](../../../role-based-access-control/role-assignments-portal.md), [rozhraní příkazového řádku Azure](../../../role-based-access-control/role-assignments-cli.md), [prostředí Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md), [REST Rozhraní API](../../../role-based-access-control/role-assignments-rest.md), nebo [šablony Resource Manageru](../../../role-based-access-control/role-assignments-template.md).

Plány služby není přímo nasadit prostředky.

## <a name="the-blueprint-assignment-object-is-created"></a>Je vytvořen objekt přiřazení podrobného plánu

Uživatele, skupinu nebo instanční objekt služby přiřadí podrobný plán předplatného. Přiřazení je objekt na úrovni předplatného, kde byl podrobný plán přiřazený. Prostředky vytvořené v rámci nasazení není vše nastaveno v kontextu nasazení entity.

Při vytváření přiřazení podrobného plánu typu [se identita spravované](../../../active-directory/managed-identities-azure-resources/overview.md) zaškrtnuto. Výchozí hodnota je **systém přiřadil** spravovaná identita. A **uživatelsky přiřazené** je možné zvolit spravovanou identitu. Při použití **uživatelsky přiřazené** spravované identity, musí být definován a udělená oprávnění, před vytvořením přiřazení podrobného plánu.

## <a name="optional---blueprints-creates-system-assigned-managed-identity"></a>Nepovinné - plány vytvoří systém přiřadil spravované identity

Když [systém přiřadil se identita spravované](../../../active-directory/managed-identities-azure-resources/overview.md) je vybrán během přiřazení, plány vytvoří identitu a udělí spravovanou identitu [vlastníka](../../../role-based-access-control/built-in-roles.md#owner) role. Pokud [upgradovat existující přiřazení](../how-to/update-existing-assignments.md), plány používá dříve vytvořenou identitou.

Spravovaná identita související se přiřazení podrobného plánu se používá k nasazení nebo znovu nasadit prostředky definované v podrobný plán. Tento návrh se vyhnete přiřazení neúmyslně zasahovala do sebe navzájem.
Tento návrh podporuje také [uzamčení prostředků](./resource-locking.md) funkce kontrolou zabezpečení všech nasazených prostředků z podrobný plán.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>Spravovaná identita nasadí artefakty podrobného plánu.

Spravovaná identita potom aktivuje nasazení Resource Manager artefaktů v rámci podrobný plán v definovaném [pořadí řazení](./sequencing-order.md). Zajistěte, aby artefakty, které jsou závislé na jiné artefakty jsou nasazené ve správném pořadí lze upravit pořadí.

K selhání přístupu v nasazení je často výsledkem úroveň přístupu k identitě spravované. Plány služby spravuje životní cyklus zabezpečení **systém přiřadil** spravovaná identita. Však uživatel zodpovídá za správu práv a životního cyklu **uživatelsky přiřazené** spravovaná identita.

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>Odvolání podrobného plánu služby a práva systém přiřadil spravované identity

Po dokončení nasazení se plány odvolá práva **systém přiřadil** spravované identity z předplatného. Plány služby, pak odvolá jeho práva z předplatného. Odebrání oprávnění zabrání plány stávají trvalé vlastníka na příslušný odběr.

## <a name="next-steps"></a>Další postup

- Principy použití [statických a dynamických parametrů](parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../how-to/update-existing-assignments.md)
- Řešení potíží při přiřazení podrobného plánu – [obecné řešení potíží](../troubleshoot/general.md)