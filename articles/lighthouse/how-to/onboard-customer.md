---
title: Připojení zákazníka k delegované správě prostředků Azure – Azure Lighthouse
description: Naučte se, jak začlenit správu delegovaných prostředků do Azure, aby k nim bylo možné získat a spravovat jejich prostředky prostřednictvím vašeho vlastního tenanta.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 08/29/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: dabee74dc757a8ccdc4384662f5c9bc09a1e5fbe
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70165029"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Nasazení zákazníků do správy delegovaných prostředků Azure

V tomto článku se dozvíte, jak můžete jako poskytovatel služeb připojit zákazníka k delegované správě prostředků Azure a povolit, aby jejich delegované prostředky (předplatná a/nebo skupiny prostředků) byly dostupné a spravované prostřednictvím vašich vlastních Azure Active Directory ( Tenant Azure AD). V takovém případě budeme odkazováni na poskytovatele služeb a zákazníky a podniky, které spravují víc tenantů, můžou stejný postup využít k konsolidaci prostředí pro správu.

Tento postup můžete opakovat, pokud spravujete prostředky pro více zákazníků. Když se pak ověřený uživatel přihlásí k vašemu tenantovi, může být tento uživatel autorizovaný v rámci oborů zákaznických tenantů, aby mohl provádět operace správy, aniž by se musel přihlašovat ke každému tenantovi zákazníka.

Můžete přidružit své ID Microsoft Partner Network (MPN) k připojeným předplatným, abyste mohli sledovat váš dopad napříč zapojením zákazníků a získávat rozpoznávání. Další informace najdete v tématu [propojení ID partnera s účty Azure](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started). Všimněte si, že budete muset toto přidružení provést samostatně pro každého tenanta zákazníka, ve kterém spravujete prostředky. 

> [!NOTE]
> Zákazníci se můžou připojit automaticky při nákupu nabídky spravované služby (veřejné nebo soukromé), kterou jste publikovali na Azure Marketplace. Další informace najdete v tématu [publikování nabídek spravovaných služeb na Azure Marketplace](publish-managed-services-offers.md). Můžete také použít proces zprovoznění, který je zde popsán, pomocí nabídky publikované do Azure Marketplace.

Proces zprovoznění vyžaduje akce, které se mají provést v rámci tenanta poskytovatele služeb i z tenanta zákazníka. Všechny tyto kroky jsou popsány v tomto článku.

> [!IMPORTANT]
> V současné době nemůžete připojit předplatné (nebo skupinu prostředků v rámci předplatného) pro správu delegovaných prostředků Azure, pokud předplatné používá Azure Databricks. Podobně platí, že pokud bylo předplatné zaregistrované pro registraci u poskytovatele prostředků **Microsoft. ManagedServices** , nebudete moct v tomto okamžiku vytvořit pracovní prostor datacihly pro toto předplatné.

## <a name="gather-tenant-and-subscription-details"></a>Získat podrobnosti o klientovi a předplatném

Pokud chcete připojit tenanta zákazníka, musí mít aktivní předplatné Azure. Budete potřebovat následující informace:

- ID tenanta tenanta poskytovatele služeb (kde budete spravovat prostředky zákazníka)
- ID tenanta tenanta zákazníka (který bude mít prostředky spravované poskytovatelem služeb)
- ID předplatných pro každé konkrétní předplatné v tenantovi zákazníka, které bude spravovat poskytovatel služeb (nebo který obsahuje skupiny prostředků, které bude spravovat poskytovatel služeb)

Pokud tyto informace ještě nemáte, můžete si je načíst jedním z následujících způsobů.

### <a name="azure-portal"></a>portál Azure

ID tenanta si můžete zobrazit tak, že najedete myší na název účtu v horní pravé části Azure Portal, nebo výběrem **přepínače Adresář**. Pokud chcete vybrat a zkopírovat ID tenanta, vyhledejte na portálu "Azure Active Directory", pak vyberte **vlastnosti** a zkopírujte hodnotu zobrazenou v poli **ID adresáře** . Pokud chcete najít ID předplatného, vyhledejte "Subscriptions" a pak vyberte příslušné ID předplatného.

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

Jako poskytovatel služeb můžete chtít použít několik nabídek s jedním zákazníkem a vyžadovat pro různé obory jiný přístup.

Pro usnadnění správy doporučujeme použít pro každou roli skupiny uživatelů Azure AD, což umožňuje přidat nebo odebrat jednotlivé uživatele do skupiny místo přiřazování oprávnění přímo tomuto uživateli. Také je možné přiřadit role k instančnímu objektu. Nezapomeňte postupovat podle principu minimálního oprávnění, aby uživatelé měli jenom oprávnění potřebná k dokončení své úlohy, což pomáhá snižovat pravděpodobnost neúmyslného výskytu chyb. Další informace najdete v tématu [Doporučené postupy zabezpečení](../concepts/recommended-security-practices.md).

> [!NOTE]
> Přiřazení rolí musí používat [předdefinované role](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)řízení přístupu na základě role (RBAC). Všechny předdefinované role se v současné době podporují se správou delegovaných prostředků Azure s výjimkou vlastník a všech vestavěných rolí [](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions) s oprávněním dataactions. Integrovaná role správce přístupu uživatele je podporována pro omezené použití, jak je popsáno níže. Vlastní role a [role správců pro klasický odběr](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators) se také nepodporují.

Aby bylo možné definovat autorizaci, budete muset znát hodnoty ID pro každého uživatele, skupinu uživatelů nebo instanční objekt, ke kterému chcete udělit přístup. Také budete potřebovat ID definice role pro každou předdefinovanou roli, kterou chcete přiřadit. Pokud je ještě nemáte, můžete je načíst jedním z následujících způsobů.

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
az ad user show --upn-or-object-id "<yourUPN>" –-query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```

## <a name="create-an-azure-resource-manager-template"></a>Vytvoření šablony Azure Resource Manageru

K připojení zákazníka budete muset vytvořit šablonu [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) , která obsahuje následující:

|Pole  |Definice  |
|---------|---------|
|**mspName**     |Název poskytovatele služeb         |
|**mspOfferDescription**     |Stručný popis vaší nabídky (například "nabídka správy virtuálních počítačů contoso")      |
|**managedByTenantId**     |ID tenanta         |
|**autorizace**     |Hodnoty **principalId** pro uživatele/skupiny/hlavní názvy služby z vašeho tenanta, z nichž každá má **principalIdDisplayName** , aby vašemu zákazníkovi pomohly pochopit účel autorizace a namapovanou na integrovanou hodnotu **roleDefinitionId** s určením úroveň přístupu         |

K zaregistrování předplatného zákazníka použijte příslušnou šablonu Azure Resource Manager, kterou poskytujeme v [úložišti ukázek](https://github.com/Azure/Azure-Lighthouse-samples/), spolu s odpovídajícím souborem parametrů, který upravíte tak, aby odpovídal vaší konfiguraci a definoval vaše autorizace. Samostatné šablony jsou uvedené v závislosti na tom, jestli se chystáte registrovat celé předplatné, skupinu prostředků nebo víc skupin prostředků v rámci předplatného. Poskytujeme také šablonu, která se dá použít pro zákazníky, kteří si zakoupili nabídku spravované služby, kterou jste publikovali na Azure Marketplace, pokud upřednostňujete jejich odběry tímto způsobem.

|**K zaregistrování**  |**Použít tuto šablonu Azure Resource Manager**  |**A upravit tento soubor parametrů** |
|---------|---------|---------|
|Subscription   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resource group   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Několik skupin prostředků v rámci předplatného   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Předplatné (při použití nabídky publikované do Azure Marketplace)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> Proces, který je zde popsán, vyžaduje samostatné nasazení pro každé připojení k odběru.
> 
> Pokud se připojujete k několika skupinám prostředků v rámci různých předplatných, vyžadují se i samostatná nasazení. Připojování více skupin prostředků v rámci jednoho předplatného se ale dá udělat v jednom nasazení.

Následující příklad ukazuje upravený soubor **resourceProjection. Parameters. JSON** , který se použije k zaregistrování předplatného. Soubory parametrů skupiny prostředků (nacházející se ve složce RG-delegované pro [správu prostředků](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) ) jsou podobné, ale také obsahují parametr **RgName** pro identifikaci konkrétních skupin prostředků, které se mají připojit.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspName": {
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
Poslední autorizace v předchozím příkladu přidá **principalId** s rolí správce přístupu uživatele (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). Při přiřazování této role musíte zahrnout vlastnost **delegatedRoleDefinitionIds** a jednu nebo více předdefinovaných rolí. Uživatel vytvořený v této autorizaci bude moci přiřadit tyto předdefinované role ke spravovaným identitám. Pro tohoto uživatele se nepoužijí žádná jiná oprávnění, která se běžně nevztahují k roli správce přístupu uživatele.

## <a name="deploy-the-azure-resource-manager-templates"></a>Nasazení šablon Azure Resource Manager

Po aktualizaci souboru parametrů zákazník musí nasadit šablonu správy prostředků v tenantovi zákazníka jako nasazení na úrovni předplatného. Pro každé předplatné, které chcete připojit ke správě delegovaných prostředků Azure (nebo pro každé předplatné, které obsahuje skupiny prostředků, které chcete připojit, je potřeba samostatné nasazení).

> [!IMPORTANT]
> Nasazení musí provést účet bez hosta v tenantovi zákazníka, který má [předdefinovanou roli](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) předplatného pro odběr (nebo který obsahuje skupiny prostředků, které jsou připojené).

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment create –-name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment create –-name <deploymentName \
                     –-location <AzureRegion> \
                     --template-uri <templateUri> \
                     --parameters <parameterFile> \
                     --verbose
```

## <a name="confirm-successful-onboarding"></a>Potvrzení úspěšného zprovoznění

Po úspěšném připojení zákaznického předplatného na delegované řízení prostředků v Azure budou uživatelé v tenantovi poskytovatele služeb moci zobrazit předplatné a jeho prostředky (pokud jim byl udělen přístup prostřednictvím výše uvedeného postupu). buď samostatně, nebo jako člen skupiny Azure AD s příslušnými oprávněními). Potvrďte to tak, že zkontrolujete, že se odběr zobrazuje jedním z následujících způsobů:  

### <a name="azure-portal"></a>portál Azure

V tenantovi poskytovatele služeb:

1. Přejděte na [stránku Moji zákazníci](view-manage-customers.md).
2. Vyberte možnost **zákazníci**.
3. Potvrďte, že si můžete zobrazit odběry s názvem nabídky, který jste zadali v šabloně Správce prostředků.

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
- V Azure Portal můžete [Zobrazit a spravovat zákazníky](view-manage-customers.md) .
