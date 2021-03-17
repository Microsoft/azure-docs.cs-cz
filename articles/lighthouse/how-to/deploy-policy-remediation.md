---
title: Nasazení zásady, která se dá napravit
description: Pokud chcete nasadit zásady, které používají úlohu nápravy prostřednictvím Azure Lighthouse, musíte vytvořit spravovanou identitu v tenantovi zákazníka.
ms.date: 01/14/2021
ms.topic: how-to
ms.openlocfilehash: 01070133241117596bdf2b8e1e7c3fa101fc656c
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2021
ms.locfileid: "98233878"
---
# <a name="deploy-a-policy-that-can-be-remediated-within-a-delegated-subscription"></a>Nasazení zásady, která se dá opravit v rámci delegovaného předplatného

[Azure Lighthouse](../overview.md) umožňuje poskytovatelům služeb vytvářet a upravovat definice zásad v rámci delegovaného předplatného. Pokud ale chcete nasadit zásady, které používají [úlohu nápravy](../../governance/policy/how-to/remediate-resources.md) (to znamená zásady s efektem [deployIfNotExists](../../governance/policy/concepts/effects.md#deployifnotexists) nebo [úprav](../../governance/policy/concepts/effects.md#modify) ), budete muset vytvořit [spravovanou identitu](../../active-directory/managed-identities-azure-resources/overview.md) v tenantovi zákazníka. Tuto spravovanou identitu můžou použít Azure Policy k nasazení šablony v rámci zásad. V takovém případě jsou vyžadovány kroky, pokud zadáváte zákazníka pro správu delegovaných prostředků Azure a při nasazení samotné zásady.

> [!TIP]
> I když v tomto tématu odkazujeme na poskytovatele služeb a zákazníky, můžou podniky, které [spravují víc tenantů](../concepts/enterprise.md) , používat stejné procesy.

## <a name="create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant"></a>Vytvoření uživatele, který může přiřadit role ke spravované identitě v tenantovi zákazníka

Když zařadíte zákazníka do Azure Lighthouse, použijete [šablonu Azure Resource Manager](onboard-customer.md#create-an-azure-resource-manager-template) společně se souborem parametrů k definování autorizací, která udělují přístup k delegovaným prostředkům v tenantovi zákazníka. Každé autorizaci Určuje **principalId** , který odpovídá uživateli, skupině nebo instančnímu objektu služby Azure AD ve správě tenanta, a **roleDefinitionId** , který odpovídá [předdefinované roli Azure](../../role-based-access-control/built-in-roles.md) , která bude udělena.

Aby **principalId** mohl vytvořit spravovanou identitu v tenantovi zákazníka, musíte nastavit její **RoleDefinitionId** na **Správce přístupu uživatele**. I když tato role není obecně podporovaná, dá se použít v tomto konkrétním scénáři, což umožňuje uživatelským účtům s tímto oprávněním přiřadit k spravovaným identitám jednu nebo více konkrétních integrovaných rolí. Tyto role jsou definované ve vlastnosti **delegatedRoleDefinitionIds** a můžou zahrnovat libovolnou [podporovanou integrovanou roli Azure](../concepts/tenants-users-roles.md#role-support-for-azure-lighthouse) s výjimkou správce nebo vlastníka přístupu uživatele.

Po zprovoznění zákazníka bude **principalId** vytvořený v této autorizaci moci přiřadit tyto předdefinované role ke spravovaným identitám v tenantovi zákazníka. Nebudou ale mít žádná další oprávnění obvykle přidružená k roli správce přístupu uživatelů.

Následující příklad ukazuje **principalId** , který bude mít roli správce přístupu uživatele. Tento uživatel bude moci přiřadit dvě předdefinované role ke spravovaným identitám v tenantovi zákazníka: přispěvatel a přispěvatel Log Analytics.

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

## <a name="deploy-policies-that-can-be-remediated"></a>Nasazení zásad, které se dají opravit

Po vytvoření uživatele s potřebnými oprávněními, jak je popsáno výše, může tento uživatel nasadit zásady, které používají nápravné úkoly v rámci delegovaných předplatných zákazníků.

Řekněme například, že jste chtěli povolit diagnostiku na Azure Key Vault prostředky v tenantovi zákazníka, jak je znázorněno v této [ukázce](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring). Uživatel v tenantovi pro správu s příslušnými oprávněními (jak je popsáno výše) nasadí [šablonu Azure Resource Manager](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-keyvault-monitoring/enforceAzureMonitoredKeyVault.json) , která tento scénář povolí.

Všimněte si, že vytvoření přiřazení zásady pro použití s delegovaným předplatným se musí v současnosti dělat prostřednictvím rozhraní API, nikoli v Azure Portal. V takovém případě musí být **apiVersion** nastavené na **2019-04-01-Preview**, což zahrnuje novou vlastnost **delegatedManagedIdentityResourceId** . Tato vlastnost umožňuje zahrnout spravovanou identitu, která se nachází v tenantovi zákazníka (v rámci předplatného nebo skupiny prostředků, která se připojila k Azure Lighthouse).

Následující příklad ukazuje přiřazení role s **delegatedManagedIdentityResourceId**.

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
> [Podobná ukázka](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) je k dispozici k předvedení, jak nasadit zásadu, která přidává nebo odebírá značku (pomocí efektu úprav) k delegovanému předplatnému.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [Azure Policy](../../governance/policy/index.yml).
- Seznamte se se [spravovanými identitami pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).
