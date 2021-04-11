---
title: Podrobnosti o struktuře výjimky zásad
description: Popisuje definici výjimky zásad, kterou používá Azure Policy k vyloučení prostředků z vyhodnocení iniciativ nebo definic.
ms.date: 03/31/2021
ms.topic: conceptual
ms.openlocfilehash: ecf956d7507dfa2168e4f2591e4b661423801365
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106091731"
---
# <a name="azure-policy-exemption-structure"></a>Azure Policy struktura výjimky

Funkce výjimky Azure Policy (Preview) se používá k _vyloučení_ hierarchie prostředků nebo jednotlivých prostředků z vyhodnocení iniciativ nebo definic. Prostředky, jejichž _počet_ neplatí pro celkové dodržování předpisů, ale nelze je vyhodnotit nebo mít dočasnou výjimku. Další informace najdete v tématu [vysvětlení oboru v Azure Policy](./scope.md). Výjimky Azure Policy fungují pouze s [režimy správce prostředků](./definition-structure.md#resource-manager-modes) a nefungují s [režimy poskytovatele prostředků](./definition-structure.md#resource-provider-modes).

> [!IMPORTANT]
> Tato funkce je ve **verzi Preview** volná. Podrobnosti o cenách najdete v tématu [Azure Policy ceny](https://azure.microsoft.com/pricing/details/azure-policy/). Další informace o verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

Pomocí formátu JSON vytvoříte výjimku zásad. Výjimka zásad obsahuje prvky pro:

- zobrazované jméno
- description
- zprostředkovatele identity
- přiřazení zásad
- definice zásad v rámci iniciativy
- kategorie výjimky
- vypršení platnosti

> [!NOTE]
> Výjimka zásad je vytvořena jako podřízený objekt v hierarchii prostředků nebo individuální prostředek udělil výjimku, takže cíl není zahrnut do definice výjimky.

Následující JSON například ukazuje výjimku zásady v kategorii **zřeknutí** se prostředků do přiřazení iniciativy s názvem `resourceShouldBeCompliantInit` . Prostředek se _vyloučí_ jenom ze dvou definic zásad v iniciativě, definice `customOrgPolicy` vlastní zásady (Reference `requiredTags` ) a předdefinovaná definice zásad ' Povolit umístění ' (ID: `e56962a6-4747-49cd-b67b-bf8b01975c4c` , Referenční dokumentace `allowedLocations` ):

```json
{
    "id": "/subscriptions/{subId}/resourceGroups/ExemptRG/providers/Microsoft.Authorization/policyExemptions/resourceIsNotApplicable",
    "name": "resourceIsNotApplicable",
    "type": "Microsoft.Authorization/policyExemptions",
    "properties": {
        "displayName": "This resource is scheduled for deletion",
        "description": "This resources is planned to be deleted by end of quarter and has been granted a waiver to the policy.",
        "metadata": {
            "requestedBy": "Storage team",
            "approvedBy": "IA",
            "approvedOn": "2020-07-26T08:02:32.0000000Z",
            "ticketRef": "4baf214c-8d54-4646-be3f-eb6ec7b9bc4f"
        },
        "policyAssignmentId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyAssignments/resourceShouldBeCompliantInit",
        "policyDefinitionReferenceIds": [
            "requiredTags",
            "allowedLocations"
        ],
        "exemptionCategory": "waiver",
        "expiresOn": "2020-12-31T23:59:00.0000000Z"
    }
}
```

Fragment kódu související iniciativy s porovnáním, které `policyDefinitionReferenceIds` používá výjimka zásady:

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/customOrgPolicy",
        "policyDefinitionReferenceId": "requiredTags",
        "parameters": {
            "reqTags": {
                "value": "[parameters('init_reqTags')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
        "policyDefinitionReferenceId": "allowedLocations",
        "parameters": {
            "listOfAllowedLocations": {
                "value": "[parameters('init_listOfAllowedLocations')]"
            }
        }
    }
]
```

## <a name="display-name-and-description"></a>Zobrazované jméno a popis

K identifikaci výjimky zásad a zadání kontextu pro jeho použití s konkrétním prostředkem použijte **Zobrazovaný** **název a popis** . hodnota **DisplayName** má maximální délku _128_ znaků a **popis** nesmí být delší než _512_ znaků.

## <a name="metadata"></a>Metadata

Vlastnost **metadata** umožňuje vytvořit jakoukoli podřízenou vlastnost nutnou k uložení relevantních informací. V příkladu výše vlastnosti **requestedBy**, **approvedBy**, **approvedOn** a **ticketRef** obsahují hodnoty zákazníků k poskytnutí informací o tom, kdo požadoval výjimku, kdo ji schválil a kdy a interní sledovací lístek pro požadavek. Tyto vlastnosti **metadat** jsou příklady, ale nejsou povinné a **metadata** nejsou omezena na tyto podřízené vlastnosti.

## <a name="policy-assignment-id"></a>ID přiřazení zásady

Toto pole musí být úplný název cesty buď přiřazení zásad, nebo přiřazení iniciativy.
`policyAssignmentId` je řetězec, nikoli pole. Tato vlastnost definuje, ze kterého přiřazení je nadřazená hierarchie prostředků nebo jednotlivé prostředky _vyjmuta_ .

## <a name="policy-definition-ids"></a>ID definice zásad

Pokud `policyAssignmentId` je pro přiřazení iniciativy, dá se `policyDefinitionReferenceIds` vlastnost použít k určení, které definice zásad v iniciativě má prostředek předmětu výjimku. Vzhledem k tomu, že prostředek může být vyjmut z jedné nebo více zahrnutých definic zásad, je tato vlastnost _pole_. Hodnoty se musí shodovat s hodnotami v definici iniciativy v `policyDefinitions.policyDefinitionReferenceId` polích.

## <a name="exemption-category"></a>Kategorie výjimky

Existují dvě kategorie výjimek a používají se k seskupení výjimek:

- **Zmírnění**: výjimka je udělená, protože záměr zásad je splněn jinou metodou.
- **Výjimka: výjimka** je udělená, protože stav prostředku, který nedodržuje předpisy, je dočasně přijatý. Dalším důvodem pro použití této kategorie je prostředek nebo hierarchie prostředků, které by měly být vyloučené z jedné nebo více definic v iniciativě, ale neměly by být vyloučeny z celé iniciativy.

## <a name="expiration"></a>Konec platnosti

Chcete-li nastavit, kdy již není hierarchie prostředků nebo jednotlivý prostředek _vyjmuta_ z přiřazení, nastavte `expiresOn` vlastnost. Tato volitelná vlastnost musí být ve formátu Universal ISO 8601 DateTime `yyyy-MM-ddTHH:mm:ss.fffffffZ` .

> [!NOTE]
> Výjimky zásad se po `expiresOn` dosažení data neodstraní. Objekt je uchován pro uchování záznamu, ale výjimka již není dodržena.

## <a name="required-permissions"></a>Požadovaná oprávnění

Oprávnění Azure RBAC potřebná pro správu objektů výjimky zásad jsou ve `Microsoft.Authorization/policyExemptions` skupině operací. Předdefinované role [přispěvatele zásad prostředků](../../../role-based-access-control/built-in-roles.md#resource-policy-contributor) a [Správce zabezpečení](../../../role-based-access-control/built-in-roles.md#security-admin) mají `read` `write` oprávnění a oprávnění a [zapisovač dat (Preview)](../../../role-based-access-control/built-in-roles.md#policy-insights-data-writer-preview) má oprávnění a `read` .

Výjimky mají další bezpečnostní opatření kvůli dopadu na udělení výjimky. Kromě nutnosti `Microsoft.Authorization/policyExemptions/write` operace s hierarchií prostředků nebo jednotlivými prostředky musí tvůrce výjimky mít v `exempt/Action` cílovém přiřazení operaci.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [struktuře definic zásad](./definition-structure.md).
- Zjistěte, jak [programově vytvářet zásady](../how-to/programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](../how-to/get-compliance-data.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](../how-to/remediate-resources.md).
- Seznamte se s tím, co skupina pro správu [organizuje vaše prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).