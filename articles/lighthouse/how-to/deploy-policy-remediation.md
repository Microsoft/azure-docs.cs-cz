---
title: Nasazení zásady, která se dá opravit
description: Naučte se, jak začlenit správu delegovaných prostředků do Azure, aby k nim bylo možné získat a spravovat jejich prostředky prostřednictvím vašeho vlastního tenanta.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 10/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 3bc85d202b9ba230130716aad34ce233037a3346
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301972"
---
# <a name="deploy-a-policy-that-can-be-remediated-within-a-delegated-subscription"></a>Nasazení zásady, která se dá opravit v rámci delegovaného předplatného

Azure Lighthouse umožňuje poskytovatelům služeb vytvářet a upravovat definice zásad v rámci delegovaného předplatného. Pokud ale chcete nasadit zásady, které používají [úlohu nápravy](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources) (to znamená zásady s efektem [deployIfNotExists](https://docs.microsoft.com/azure/governance/policy/concepts/effects#deployifnotexists) nebo [úprav](https://docs.microsoft.com/azure/governance/policy/concepts/effects#modify) ), budete muset vytvořit [spravovanou identitu](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) v tenantovi zákazníka. Tuto spravovanou identitu můžou použít Azure Policy k nasazení šablony v rámci zásad. V takovém případě jsou vyžadovány kroky, pokud zadáváte zákazníka pro správu delegovaných prostředků Azure a při nasazení samotné zásady.

## <a name="create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant"></a>Vytvoření uživatele, který může přiřadit role ke spravované identitě v tenantovi zákazníka

Při registraci zákazníka pro správu delegovaných prostředků Azure použijete [šablonu Azure Resource Manager](https://docs.microsoft.com/azure/lighthouse/how-to/onboard-customer#create-an-azure-resource-manager-template) společně se souborem parametrů, který definuje uživatele, skupiny uživatelů a instanční objekty ve vašem spravovaném tenantovi, které budou mít přístup k delegované prostředky v tenantovi zákazníka. V souboru parametrů má každý z těchto uživatelů (**principalId**) přiřazenou [předdefinovanou roli](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) (**roleDefinitionId**), která definuje úroveň přístupu.

Aby **principalId** mohl vytvořit spravovanou identitu v tenantovi zákazníka, musíte nastavit její **RoleDefinitionId** na **Správce přístupu uživatele**. I když tato role není obecně podporovaná, dá se použít v tomto konkrétním scénáři, takže uživatelé s tímto oprávněním můžou k spravovaným identitám přiřadit jednu nebo více specifických rolí. Tyto role jsou definovány ve vlastnosti **delegatedRoleDefinitionIds** . Do této služby můžete zahrnout jakoukoli vestavěnou roli s výjimkou správce nebo vlastníka přístupu uživatele.

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

Po vytvoření uživatele s potřebnými oprávněními, jak je popsáno výše, může tento uživatel nasadit zásady v klientovi zákazníka, který používá nápravné úlohy.

Řekněme například, že jste chtěli povolit diagnostiku na Azure Key Vault prostředky v tenantovi zákazníka, jak je znázorněno v této [ukázce](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring). Uživatel v tenantovi pro správu s příslušnými oprávněními (jak je popsáno výše) nasadí [šablonu Azure Resource Manager](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring/enforceAzureMonitoredKeyVault.json) , která tento scénář povolí.

Všimněte si, že vytvoření přiřazení zásady pro použití s delegovaným předplatným se musí v současnosti dělat prostřednictvím rozhraní API, nikoli v Azure Portal. V takovém případě musí být **apiVersion** nastavené na **2019-04-01-Preview**, což zahrnuje novou vlastnost **delegatedManagedIdentityResourceId** . Tato vlastnost umožňuje zahrnout spravovanou identitu, která se nachází v tenantovi zákazníka (v rámci předplatného nebo skupiny prostředků, která je připojená ke správě delegovaných prostředků Azure).

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
> [Podobná ukázka](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag) je k dispozici k předvedení, jak nasadit zásadu, která přidává nebo odebírá značku (pomocí efektu úprav) k delegovanému předplatnému.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [Azure Policy](https://docs.microsoft.com/azure/governance/policy/).
- Seznamte se se [spravovanými identitami pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
