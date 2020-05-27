---
title: Nasazení zákazníků do správy delegovaných prostředků Azure
description: Naučte se, jak začlenit správu delegovaných prostředků do Azure, aby k nim bylo možné získat a spravovat jejich prostředky prostřednictvím vašeho vlastního tenanta.
ms.date: 05/26/2020
ms.topic: conceptual
ms.openlocfilehash: a6cdfea7e0520aa704e70a12784f7a7ba5d6aa6d
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "83871120"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Nasazení zákazníků do správy delegovaných prostředků Azure

V tomto článku se dozvíte, jak můžete jako poskytovatel služeb připojit zákazníka k delegované správě prostředků Azure a povolit, aby jejich delegované prostředky (předplatná a/nebo skupiny prostředků) byly dostupné a spravované prostřednictvím vašeho vlastního tenanta Azure Active Directory (Azure AD). V takovém případě budeme odkazováni na poskytovatele služeb a zákazníky a podniky, které [spravují víc tenantů](../concepts/enterprise.md) , můžou stejný postup použít k nastavení Azure Lighthouse a konsolidaci prostředí pro správu.

Tento postup můžete opakovat, pokud spravujete prostředky pro více zákazníků. Když se pak ověřený uživatel přihlásí k vašemu tenantovi, může být tento uživatel autorizovaný v rámci oborů zákaznických tenantů, aby mohl provádět operace správy, aniž by se musel přihlašovat ke každému tenantovi zákazníka.

Pokud chcete sledovat svůj dopad napříč zapojením zákazníků a získávat rozpoznávání, přidružte své ID Microsoft Partner Network (MPN) k alespoň jednomu uživatelskému účtu, který má přístup ke každému z vašich integrovaných předplatných. Všimněte si, že budete muset toto přidružení provést v tenantovi poskytovatele služeb. Pro zjednodušení doporučujeme vytvořit v tenantovi účet instančního objektu, který je přidružený k vašemu ID MPN, a udělit každému zákazníkovi přístup ke čtečce IT. Další informace najdete v tématu [propojení ID partnera s účty Azure](../../billing/billing-partner-admin-link-started.md). 

> [!NOTE]
> Při nákupu nabídky spravované služby (veřejné nebo soukromé), kterou jste publikovali do Azure Marketplace, je také možné připojit zákazníky. Další informace najdete v tématu [publikování nabídek spravovaných služeb na Azure Marketplace](publish-managed-services-offers.md). Můžete také použít proces zprovoznění popsaný tady spolu s nabídkou publikovanou do Azure Marketplace.

Proces zprovoznění vyžaduje akce, které se mají provést v rámci tenanta poskytovatele služeb i z tenanta zákazníka. Všechny tyto kroky jsou popsány v tomto článku.

## <a name="gather-tenant-and-subscription-details"></a>Získat podrobnosti o klientovi a předplatném

Pokud chcete připojit tenanta zákazníka, musí mít aktivní předplatné Azure. Budete potřebovat následující informace:

- ID tenanta tenanta poskytovatele služeb (kde budete spravovat prostředky zákazníka)
- ID tenanta tenanta zákazníka (který bude mít prostředky spravované poskytovatelem služeb)
- ID předplatných pro každé konkrétní předplatné v tenantovi zákazníka, které bude spravovat poskytovatel služeb (nebo který obsahuje skupiny prostředků, které bude spravovat poskytovatel služeb).

> [!NOTE]
> I když chcete jen začlenit jednu nebo více skupin prostředků v rámci předplatného, je nutné nasazení provést na úrovni předplatného, takže budete potřebovat ID předplatného.

Pokud tyto hodnoty ID již nemáte, můžete je načíst jedním z následujících způsobů. Ujistěte se, že používáte tyto přesné hodnoty v nasazení.

### <a name="azure-portal"></a>portál Azure

ID tenanta si můžete zobrazit tak, že najedete myší na název účtu v horní pravé části Azure Portal, nebo výběrem **přepínače Adresář**. Pokud chcete vybrat a zkopírovat ID tenanta, vyhledejte na portálu "Azure Active Directory", pak vyberte **vlastnosti** a zkopírujte hodnotu zobrazenou v poli **ID adresáře** . Pokud chcete najít ID předplatného v tenantovi zákazníka, vyhledejte "Subscriptions" a pak vyberte příslušné ID předplatného.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```

> [!NOTE]
> Při připojování předplatného (nebo jedné nebo více skupin prostředků v rámci předplatného) pomocí popsaného postupu se poskytovatel prostředků **Microsoft. ManagedServices** zaregistruje pro toto předplatné.

## <a name="define-roles-and-permissions"></a>Definování rolí a oprávnění

Jako poskytovatel služeb můžete chtít pro jednoho zákazníka provést několik úloh, které vyžadují různý přístup pro různé obory. Můžete definovat tolik autorizací, kolik potřebujete k přiřazení [předdefinovaných rolí řízení přístupu na základě role (RBAC)](../../role-based-access-control/built-in-roles.md) uživatelům ve vašem tenantovi.

Pro usnadnění správy doporučujeme použít pro každou roli skupiny uživatelů Azure AD, což umožňuje přidat nebo odebrat jednotlivé uživatele do skupiny místo přiřazování oprávnění přímo tomuto uživateli. Také je možné přiřadit role k instančnímu objektu. Nezapomeňte postupovat podle principu minimálního oprávnění, aby uživatelé měli jenom oprávnění potřebná k dokončení své úlohy. Doporučení a informace o podporovaných rolích najdete [v tématu Klienti, uživatelé a role ve scénářích Azure Lighthouse](../concepts/tenants-users-roles.md).

> [!IMPORTANT]
> Aby bylo možné přidat oprávnění pro skupinu Azure AD, musí být **typ skupiny** **zabezpečení** , a ne **Sada Office 365**. Tato možnost je vybrána při vytváření skupiny. Další informace najdete v tématu [Vytvoření základní skupiny a přidání členů pomocí Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Aby bylo možné definovat autorizaci, budete muset znát hodnoty ID pro každého uživatele, skupinu uživatelů nebo instanční objekt v tenantovi poskytovatele služeb, ke kterému chcete udělit přístup. Také budete potřebovat ID definice role pro každou předdefinovanou roli, kterou chcete přiřadit. Pokud je ještě nemáte, můžete je načíst spuštěním příkazů níže v rámci tenanta poskytovatele služeb.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>').objectId

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --id "<yourUPN>" --query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```
> [!TIP]
> Při připojování zákazníka doporučujeme přiřadit [roli odstranění registrace spravovaných služeb](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) , aby uživatelé ve vašem tenantovi mohli později v případě potřeby [Odebrat přístup k delegování](remove-delegation.md) . Pokud tato role není přiřazená, delegované prostředky může odebrat jenom uživatel v tenantovi zákazníka.

## <a name="create-an-azure-resource-manager-template"></a>Vytvoření šablony Azure Resource Manager

K připojení zákazníka budete muset vytvořit šablonu [Azure Resource Manager](../../azure-resource-manager/index.yml) pro vaši nabídku s následujícími informacemi. Hodnoty **mspOfferName** a **mspOfferDescription** se zákazníkům zobrazí při zobrazení podrobností nabídky na [stránce poskytovatelé služeb](view-manage-service-providers.md) Azure Portal.

|Pole  |Definice  |
|---------|---------|
|**mspOfferName**     |Název popisující tuto definici. Tato hodnota se zobrazí zákazníkovi jako název nabídky.         |
|**mspOfferDescription**     |Stručný popis vaší nabídky (například "nabídka správy virtuálních počítačů contoso").      |
|**managedByTenantId**     |Vaše ID tenanta.          |
|**autorizace**     |Hodnoty **principalId** pro uživatele/skupiny/hlavní názvy služby z vašeho tenanta, každý s **principalIdDisplayName** , které vašemu zákazníkovi pomůžou pochopit účel autorizace a namapovány na integrovanou hodnotu **roleDefinitionId** , která určuje úroveň přístupu.      |

Proces zprovoznění vyžaduje šablonu Azure Resource Manager (poskytnutou v [úložišti ukázek](https://github.com/Azure/Azure-Lighthouse-samples/)) a odpovídající soubor parametrů, který upravíte tak, aby odpovídal vaší konfiguraci a definoval vaše autorizace.

Šablona, kterou zvolíte, bude záviset na tom, jestli se chystáte registrovat celé předplatné, skupinu prostředků nebo víc skupin prostředků v rámci předplatného. Poskytujeme také šablonu, která se dá použít pro zákazníky, kteří si zakoupili nabídku spravované služby, kterou jste publikovali na Azure Marketplace, pokud upřednostňujete jejich odběry tímto způsobem.

|K zaregistrování  |Použít tuto šablonu Azure Resource Manager  |A upravit tento soubor parametrů |
|---------|---------|---------|
|Předplatné   |[delegatedResourceManagement. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement. Parameters. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Skupina prostředků   |[rgDelegatedResourceManagement. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement. Parameters. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Několik skupin prostředků v rámci předplatného   |[multipleRgDelegatedResourceManagement. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement. Parameters. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Předplatné (při použití nabídky publikované do Azure Marketplace)   |[marketplaceDelegatedResourceManagement. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement. Parameters. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> Proces, který je zde popsán, vyžaduje samostatné nasazení na úrovni předplatného pro každé připojení na úrovni předplatného, a to i v případě, že odběry provádíte ve stejném tenantovi zákazníka. Pokud se připojujete k několika skupinám prostředků v rámci různých předplatných ve stejném tenantovi zákazníka, vyžaduje se také samostatné nasazení. Připojování více skupin prostředků v rámci jednoho předplatného se ale dá udělat v jednom nasazení na úrovni předplatného.
>
> Pro stejné předplatné (nebo skupiny prostředků v rámci předplatného se taky vyžadují samostatná nasazení). Každá použitá nabídka musí používat jiný **mspOfferName**.

Následující příklad ukazuje upravený soubor **delegatedResourceManagement. Parameters. JSON** , který lze použít k zaregistrování předplatného. Soubory parametrů skupiny prostředků (nacházející se ve složce [RG-delegované pro správu prostředků](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) ) jsou podobné, ale také obsahují parametr **RgName** pro identifikaci konkrétních skupin prostředků, které se mají připojit.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "df4602a3-920c-435f-98c4-49ff031b9ef6"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "0afd8497-7bff-4873-a7ff-b19a6b7b332c",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "9fe47fff-5655-4779-b726-2cf02b07c7c7",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```

Poslední autorizace v předchozím příkladu přidá **principalId** s rolí správce přístupu uživatele (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). Při přiřazování této role musíte zahrnout vlastnost **delegatedRoleDefinitionIds** a jednu nebo více předdefinovaných rolí. Uživatel vytvořený v této autorizaci bude moci přiřadit tyto předdefinované role ke [spravovaným identitám](../../active-directory/managed-identities-azure-resources/overview.md) v tenantovi zákazníka, který je potřeba k [nasazení zásad, které se dají opravit](deploy-policy-remediation.md). Pro tohoto uživatele se nebudou vztahovat žádná další oprávnění normálně přidružená k roli správce přístupu uživatele.

## <a name="deploy-the-azure-resource-manager-templates"></a>Nasazení šablon Azure Resource Manager

Po aktualizaci souboru parametrů musí uživatel v tenantovi zákazníka nasadit šablonu Azure Resource Manager v rámci svého tenanta jako nasazení na úrovni předplatného. Pro každé předplatné, které chcete připojit ke správě delegovaných prostředků Azure (nebo pro každé předplatné, které obsahuje skupiny prostředků, které chcete připojit, je potřeba samostatné nasazení).

Vzhledem k tomu, že se jedná o nasazení na úrovni předplatného, nejde ho iniciovat v Azure Portal. Nasazení se dá provést pomocí PowerShellu nebo rozhraní příkazového řádku Azure CLI, jak vidíte níže.

> [!IMPORTANT]
> Toto nasazení na úrovni předplatného musí provést jiný účet než host v tenantovi zákazníka, který má [předdefinovanou roli](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) předplatného pro odběr (nebo který obsahuje skupiny prostředků, které jsou připojené). Pokud chcete zobrazit všechny uživatele, kteří můžou delegovat předplatné, uživatel v tenantovi zákazníka může vybrat předplatné ve Azure Portal, otevřít **řízení přístupu (IAM)** a [Zobrazit všechny uživatele s rolí vlastníka](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).
>
> Pokud bylo předplatné vytvořeno prostřednictvím [programu Cloud Solution Provider (CSP)](../concepts/cloud-solution-provider.md), může nasazení provést libovolný uživatel, který má v tenantovi poskytovatele služeb roli [agenta správce](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) .

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment create --name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment create --name <deploymentName> \
                     --location <AzureRegion> \
                     --template-uri <templateUri> \
                     --parameters <parameterFile> \
                     --verbose
```

## <a name="confirm-successful-onboarding"></a>Potvrzení úspěšného zprovoznění

Po úspěšném připojení zákaznického předplatného do Azure delegované správy prostředků uvidí uživatelé v tenantovi poskytovatele služeb předplatné a jeho prostředky (pokud jim k ní byl udělen přístup prostřednictvím výše uvedeného procesu), a to buď jednotlivě, nebo jako člen skupiny Azure AD s příslušnými oprávněními. Potvrďte to tak, že zkontrolujete, že se odběr zobrazuje jedním z následujících způsobů:  

### <a name="azure-portal"></a>portál Azure

V tenantovi poskytovatele služeb:

1. Přejděte na [stránku Moji zákazníci](view-manage-customers.md).
2. Vyberte možnost **zákazníci**.
3. Potvrďte, že si můžete zobrazit odběry s názvem nabídky, který jste zadali v šabloně Správce prostředků.

> [!IMPORTANT]
> Aby bylo možné zobrazit delegované předplatné ve [svých zákaznících](view-manage-customers.md), uživatelé v tenantovi poskytovatele služeb musí mít přiřazenou roli [Čtenář](../../role-based-access-control/built-in-roles.md#reader) (nebo jinou předdefinovanou roli, která zahrnuje přístup ke čtenářům), pokud bylo předplatné pro správu delegovaných prostředků Azure zaregistrované.

V tenantovi zákazníka:

1. Přejděte na [stránku poskytovatelé služeb](view-manage-service-providers.md).
2. Vyberte **nabídky poskytovatele služeb**.
3. Potvrďte, že si můžete zobrazit odběry s názvem nabídky, který jste zadali v šabloně Správce prostředků.

> [!NOTE]
> Po dokončení nasazení může trvat několik minut, než se aktualizace projeví v Azure Portal.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [prostředích pro správu mezi klienty](../concepts/cross-tenant-management-experience.md).
- V **Azure Portal můžete** [Zobrazit a spravovat zákazníky](view-manage-customers.md) .
- Naučte se, jak [Odebrat přístup k](remove-delegation.md) dříve zadanému delegování.
