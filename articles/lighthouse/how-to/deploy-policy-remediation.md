---
title: Nasazení zásady, která se dá napravit
description: Zjistěte, jak naložit zákazníka do azure delegované správy prostředků, což umožňuje přístup k jejich prostředkům a správu prostřednictvím vlastního tenanta.
ms.date: 10/11/2019
ms.topic: conceptual
ms.openlocfilehash: b625e9e3c96866cfbc655a55b770c9ac07a626bd
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985163"
---
# <a name="deploy-a-policy-that-can-be-remediated-within-a-delegated-subscription"></a>Nasazení zásady, kterou lze napravit v rámci delegovaného předplatného

[Azure Lighthouse](../overview.md) umožňuje poskytovatelům služeb vytvářet a upravovat definice zásad v rámci delegovaného předplatného. Chcete-li však nasadit zásady, které používají [úlohu nápravy](../../governance/policy/how-to/remediate-resources.md) (to znamená zásady s [efektem deployIfNotExists](../../governance/policy/concepts/effects.md#deployifnotexists) nebo [modifikovat),](../../governance/policy/concepts/effects.md#modify) budete muset vytvořit [spravovanou identitu](../../active-directory/managed-identities-azure-resources/overview.md) v klientovi zákazníka. Tuto spravovanou identitu můžete použít zásady Azure k nasazení šablony v rámci zásad. Existují kroky potřebné k povolení tohoto scénáře, a to jak při na palubě zákazníka pro azure delegované správy prostředků, a když nasadíte samotné zásady.

## <a name="create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant"></a>Vytvoření uživatele, který může přiřadit role spravované identitě v klientovi zákazníka

Když jste na palubě zákazníka pro správu delegovaných prostředků Azure, použijete [šablonu Azure Resource Manager](onboard-customer.md#create-an-azure-resource-manager-template) spolu se souborem parametrů, který definuje uživatele, skupiny uživatelů a instanční objekty ve vašem správě tenanta, který bude mít přístup k delegovaným prostředkům v tenantovi zákazníka. V souboru parametrů je každému z těchto uživatelů (**principalId**) přiřazena [předdefinovaná role](../../role-based-access-control/built-in-roles.md) (**roleDefinitionId**), která definuje úroveň přístupu.

Chcete-li povolit **principalId** k vytvoření spravované identity v klientovi zákazníka, musíte nastavit jeho **roleDefinitionId** na **správce přístupu uživatele**. I když tato role není obecně podporována, lze ji použít v tomto konkrétním scénáři, což umožňuje uživatelům s tímto oprávněním přiřadit jednu nebo více konkrétních předdefinovaných rolí ke spravovaným identitám. Tyto role jsou **definovány v delegatedRoleDefinitionIds** vlastnost. Zde můžete zahrnout libovolnou předdefinovanou roli s výjimkou správce přístupu uživatelů nebo vlastníka.

Po na palubě zákazníka **principalId** vytvořené v této autorizaci bude moci přiřadit tyto předdefinované role spravované identity v klientovi zákazníka. Nebudou však mít žádná další oprávnění, která jsou obvykle přidružena k roli správce přístupu uživatelů.

Následující příklad ukazuje **principalId,** který bude mít roli správce přístupu uživatele. Tento uživatel bude moci přiřadit dvě předdefinované role spravovaným identitám v klientovi zákazníka: přispěvatele a přispěvatele analýzy protokolů.

```json
{
    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
    "principalIdDisplayName": "Policy Automation Account",
    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "delegatedRoleDefinitionIds": [
         "b24988ac-6180-42a0-ab88-20f7382dd24c",
         "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
    ]
}
```

## <a name="deploy-policies-that-can-be-remediated"></a>Nasazení zásad, které lze napravit

Jakmile vytvoříte uživatele s potřebnými oprávněními, jak je popsáno výše, může tento uživatel nasadit zásady v klientovi zákazníka, které používají nápravné úlohy.

Řekněme například, že jste chtěli povolit diagnostiku prostředků azure key vault u klienta zákazníka, jak je znázorněno v této [ukázce](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring). Uživatel ve správcovním tenantovi s příslušnými oprávněními (jak je popsáno výše) by nasadil [šablonu Azure Resource Manager,](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-keyvault-monitoring/enforceAzureMonitoredKeyVault.json) která by tento scénář povolila.

Všimněte si, že vytvoření přiřazení zásad pro použití s delegovaným odběrem musí být aktuálně provedeno prostřednictvím api, nikoli na webu Azure Portal. Při tom **apiVersion** musí být nastavena na **2019-04-01-preview**, který zahrnuje nové **delegovanéManagedIdentityResourceId** vlastnost. Tato vlastnost umožňuje zahrnout spravovanou identitu, která se nachází v tenantovi zákazníka (ve skupině předplatného nebo prostředků, která byla na palubě pro správu delegovaných prostředků Azure).

Následující příklad ukazuje přiřazení role s **delegovaným IdentityIdentityResourceId**.

```json
"type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2019-04-01-preview",
            "name": "[parameters('rbacGuid')]",
            "dependsOn": [
                "[variables('policyAssignment')]"
            ],
            "properties": {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', variables('rbacContributor'))]",
                "principalType": "ServicePrincipal",
                "delegatedManagedIdentityResourceId": "[concat(subscription().id, '/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment'))]",
                "principalId": "[toLower(reference(concat('/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment')), '2018-05-01', 'Full' ).identity.principalId)]"
            }
```

> [!TIP]
> [Podobná ukázka](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) je k dispozici k předvedení, jak nasadit zásadu, která přidá nebo odebere značku (pomocí efektu změny) do delegovaného předplatného.

## <a name="next-steps"></a>Další kroky

- Další informace o [zásadách Azure](../../governance/policy/index.yml).
- Informace o [spravovaných identitách pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).
