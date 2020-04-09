---
title: Nasazení zákazníků do správy delegovaných prostředků Azure
description: Zjistěte, jak naložit zákazníka do azure delegované správy prostředků, což umožňuje přístup k jejich prostředkům a správu prostřednictvím vlastního tenanta.
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: 9eef2e73de7812fc65104131dfd319bf8dfb014e
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984585"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Nasazení zákazníků do správy delegovaných prostředků Azure

Tento článek vysvětluje, jak můžete jako poskytovatel služeb naložit zákazníka do azure delegované správy prostředků a umožnit přístup k delegovaným prostředkům (předplatným a skupinám prostředků) a spravovat je prostřednictvím vlastního tenanta Azure Active Directory (Azure AD). I když budeme odkazovat na poskytovatele služeb a zákazníky zde, [podniky spravující více klientů](../concepts/enterprise.md) můžete použít stejný proces ke konsolidaci jejich prostředí pro správu.

Tento proces můžete zopakovat, pokud spravujete prostředky pro více zákazníků. Když se autorizovaný uživatel přihlásí k vašemu tenantovi, může být tento uživatel autorizován napříč obory klienta zákazníků k provádění operací správy, aniž by se musel přihlašovat ke každému jednotlivému klientovi zákazníka.

Chcete-li sledovat dopad na příčky zákazníků a získat uznání, přidružte ID sítě partnerů microsoftu (MPN) k alespoň jednomu uživatelskému účtu, který má přístup ke každému z vašich založených předplatných. Všimněte si, že budete muset provést toto přidružení v tenantovi poskytovatele služeb. Pro jednoduchost doporučujeme vytvořit účet instančního objektu ve vašem tenantovi, který je přidružen vaše MPN ID a udělit mu přístup readeru každému zákazníkovi, kterého na palubě. Další informace najdete v článku [Propojení ID partnera s účty Azure](../../billing/billing-partner-admin-link-started.md). 

> [!NOTE]
> Zákazníci mohou být také na palubě při nákupu nabídky spravovaných služeb (veřejné nebo soukromé), kterou jste publikovali na Azure Marketplace. Další informace najdete [v tématu Publikování nabídek spravovaných služeb na Azure Marketplace](publish-managed-services-offers.md). Můžete také použít proces registrace popsané zde spolu s nabídkou publikovanou na Azure Marketplace.

Proces registrace vyžaduje, aby byly akce převzaty z klienta poskytovatele služeb i od klienta zákazníka. Všechny tyto kroky jsou popsány v tomto článku.

## <a name="gather-tenant-and-subscription-details"></a>Shromažďování podrobností o tenantovi a předplatném

Chcete-li napalubě klienta zákazníka, musí mít aktivní předplatné Azure. Budete potřebovat následující:

- ID klienta tenanta poskytovatele služeb (kde budete spravovat prostředky zákazníka)
- ID klienta klienta zákazníka (které bude mít prostředky spravované poskytovatelem služeb)
- ID předplatného pro každé konkrétní předplatné v klientovi zákazníka, které bude spravovat poskytovatel služeb (nebo který obsahuje skupiny prostředků), které budou spravovány poskytovatelem služeb).

> [!NOTE]
> I v případě, že chcete pouze napalubě jedné nebo více skupin prostředků v rámci předplatného, nasazení musí být provedeno na úrovni předplatného, takže budete potřebovat ID předplatného.

Pokud tyto hodnoty ID ještě nemáte, můžete je načíst jedním z následujících způsobů. Ujistěte se, a použít tyto přesné hodnoty ve vašem nasazení.

### <a name="azure-portal"></a>portál Azure

ID vašeho klienta se dá zobrazit tak, že najedete na název vašeho účtu na pravé horní straně portálu Azure nebo vyberete **switch adresáře**. Pokud chcete vybrat a zkopírovat ID klienta, vyhledejte "Azure Active Directory" z portálu, pak vyberte **Vlastnosti** a zkopírujte hodnotu zobrazenou v poli **ID adresáře.** Pokud chcete najít ID předplatného v tenantovi zákazníka, vyhledejte "Předplatná" a vyberte příslušné ID předplatného.

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
> Při registraci předplatného (nebo jedné nebo více skupin prostředků v rámci předplatného) pomocí zde popsaného procesu bude pro toto předplatné zaregistrován poskytovatel prostředků **Microsoft.ManagedServices.**

## <a name="define-roles-and-permissions"></a>Definování rolí a oprávnění

Jako poskytovatel služeb můžete chtít provést více úkolů pro jednoho zákazníka, které vyžadují jiný přístup pro různé obory. Můžete definovat tolik autorizací, kolik potřebujete k přiřazení [předdefinovaných rolí řízení přístupu na základě rolí (RBAC)](../../role-based-access-control/built-in-roles.md) uživatelům ve vašem tenantovi.

Chcete-li usnadnit správu, doporučujeme použít skupiny uživatelů Azure AD pro každou roli, což vám umožní přidat nebo odebrat jednotlivé uživatele do skupiny, nikoli přiřazovat oprávnění přímo tomuto uživateli. Můžete také přiřadit role instančnímu objektu. Ujistěte se, že dodržovat zásadu nejnižší oprávnění tak, aby uživatelé měli pouze oprávnění potřebná k dokončení jejich úlohy. Doporučení a informace o podporovaných rolích najdete [v tématu Tenants, users a roles in Azure Lighthouse scenarios](../concepts/tenants-users-roles.md).

> [!IMPORTANT]
> Chcete-li přidat oprávnění pro skupinu Azure AD, musí být **typ skupiny** **zabezpečení** a ne **Office 365**. Tato možnost je vybrána při vytvoření skupiny. Další informace najdete [v tématu Vytvoření základní skupiny a přidání členů pomocí služby Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Chcete-li definovat autorizace, musíte znát hodnoty ID pro každého uživatele, skupinu uživatelů nebo instanční objekt v tenantovi poskytovatele služeb, kterému chcete udělit přístup. Budete také potřebovat ID definice role pro každou předdefinovanou roli, kterou chcete přiřadit. Pokud je ještě nemáte, můžete je načíst spuštěním níže uvedených příkazů z klienta poskytovatele služeb.

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
> Doporučujeme přiřadit [přiřazení registrace spravovaných služeb odstranit roli](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) při registrace zákazníka, aby uživatelé ve vašem tenantovi mohli v případě potřeby později [odebrat přístup k delegování.](#remove-access-to-a-delegation) Pokud tato role není přiřazena, delegované prostředky mohou být odebrány pouze uživatelem v tenantovi zákazníka.

## <a name="create-an-azure-resource-manager-template"></a>Vytvoření šablony Azure Resource Manageru

Chcete-li napalubě svého zákazníka, budete muset vytvořit šablonu [Azure Resource Manager](../../azure-resource-manager/index.yml) pro vaši nabídku s následujícími informacemi. Hodnoty **mspOfferName** a **mspOfferDescription** budou viditelné pro zákazníka při zobrazení podrobností o nabídce na [stránce poskytovatelů služeb](view-manage-service-providers.md) na portálu Azure.

|Pole  |Definice  |
|---------|---------|
|**mspOfferName**     |Název popisující tuto definici. Tato hodnota je zákazníkovi zobrazena jako název nabídky.         |
|**mspOfferDescription**     |Stručný popis vaší nabídky (například "Nabídka správy virtuálních počítačových společností Contoso").      |
|**managedByTenantId**     |Vaše tenantské ID.          |
|**Povolení**     |**PrincipalId** hodnoty pro uživatele/skupiny/názvy SPN z vašeho tenanta, každý s **principalIdDisplayName** pomoci zákazníkovi pochopit účel autorizace a mapovány na předdefinovanou value **roleDefinitionId** určit úroveň přístupu.      |

Proces registrace vyžaduje šablonu Azure Resource Manager (k dispozici v [našich ukázkách úložiště)](https://github.com/Azure/Azure-Lighthouse-samples/)a odpovídající parametry souboru, který upravíte tak, aby odpovídaly vaší konfiguraci a definovat autorizace.

Šablona, kterou zvolíte, bude záviset na tom, zda v rámci předplatného zapisujete celé předplatné, skupinu prostředků nebo více skupin prostředků. Poskytujeme také šablonu, kterou lze použít pro zákazníky, kteří si zakoupili nabídku spravovaných služeb, kterou jste publikovali na Azure Marketplace, pokud dáváte přednost jejich předplatnému tímto způsobem.

|Chcete-li na palubě tohoto  |Použití této šablony Správce prostředků Azure  |A upravit tento soubor parametrů |
|---------|---------|---------|
|Předplatné   |[delegovánoResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegovánoResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Skupina prostředků   |[rgDelegovánoResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Více skupin prostředků v rámci předplatného   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Předplatné (při použití nabídky publikované na Azure Marketplace)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> Proces popsaný zde vyžaduje samostatné nasazení na úrovni předplatného pro každé předplatné, které je na palubě, i když jste onboarding předplatná ve stejném klientovi zákazníka. Samostatná nasazení jsou také vyžadována, pokud zapisujete více skupin prostředků v rámci různých předplatných ve stejném klientovi zákazníka. Však onboarding více skupin prostředků v rámci jednoho předplatného lze provést v jednom nasazení na úrovni předplatného.
>
> Samostatná nasazení jsou také vyžadována pro více nabídek, které se používají pro stejné předplatné (nebo skupiny prostředků v rámci předplatného). Každá použitá nabídka musí používat jiný **název mspOfferName**.

Následující příklad ukazuje upravený **delegovaný soubor ResourceManagement.parameters.json,** který lze použít k napalubí předplatného. Soubory parametrů skupiny prostředků (umístěné ve složce [rg-delegated-resource-management)](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) jsou podobné, ale také obsahují parametr **rgName** k identifikaci konkrétní skupiny prostředků, které mají být zahrnuty.

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

Poslední autorizace ve výše uvedeném příkladu přidá **principalId** s rolí správce přístupu uživatele (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). Při přiřazování této role je nutné zahrnout vlastnost **delegatedRoleDefinitionIds** a jednu nebo více předdefinovaných rolí. Uživatel vytvořený v této autorizaci bude moci přiřadit tyto předdefinované role [spravovaným identitám](../../active-directory/managed-identities-azure-resources/overview.md) v klientovi zákazníka, což je nutné k [nasazení zásad, které lze napravit](deploy-policy-remediation.md). Pro tohoto uživatele nebudou platit žádná další oprávnění, která jsou obvykle přidružena k roli Správce přístupu uživatelů.

## <a name="deploy-the-azure-resource-manager-templates"></a>Nasazení šablon Azure Resource Manageru

Po aktualizaci souboru parametrů musí uživatel v tenantovi zákazníka nasadit šablonu Azure Resource Manager v rámci svého tenanta jako nasazení na úrovni předplatného. Samostatné nasazení je potřeba pro každé předplatné, které chcete na palubě azure delegované správy prostředků (nebo pro každé předplatné, které obsahuje skupiny prostředků, které chcete na palubě).

Vzhledem k tomu, že se jedná o nasazení na úrovni předplatného, nelze jej inicializovat na webu Azure Portal. Nasazení může být provedeno pomocí Prostředí PowerShell nebo Azure CLI, jak je znázorněno níže.

> [!IMPORTANT]
> Toto nasazení na úrovni předplatného musí být provedeno nehostúčet v tenantovi zákazníka, který má [předdefinovanou roli vlastníka](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) pro předplatné jsou na palubě (nebo který obsahuje skupiny prostředků, které jsou na palubě). Chcete-li zobrazit všechny uživatele, kteří mohou delegovat předplatné, uživatel v tenantovi zákazníka můžete vybrat předplatné na webu Azure Portal, otevřete **řízení přístupu (IAM)** a [zobrazit všechny uživatele s rolí Vlastník](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

### <a name="powershell"></a>PowerShell

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

## <a name="confirm-successful-onboarding"></a>Potvrdit úspěšné zaškolení

Pokud bylo předplatné zákazníka úspěšně založeno pro správu delegovaných prostředků Azure, uživatelé v tenantovi poskytovatele služeb budou moci zobrazit předplatné a jeho prostředky (pokud jim byl udělen přístup prostřednictvím výše uvedeného procesu, a to buď jednotlivě, nebo jako člen skupiny Azure AD s příslušnými oprávněními). Chcete-li to potvrdit, zkontrolujte, zda se předplatné zobrazí jedním z následujících způsobů.  

### <a name="azure-portal"></a>portál Azure

V tenantovi poskytovatele služeb:

1. Přejděte na [stránku Moji zákazníci](view-manage-customers.md).
2. Vyberte **zákazníci**.
3. Zkontrolujte, zda vidíte předplatné s názvem nabídky, který jste zadali v šabloně Správce prostředků.

> [!IMPORTANT]
> Aby bylo možné zobrazit delegované předplatné v [moji zákazníci](view-manage-customers.md), musí být uživatelům v tenantovi poskytovatele služeb udělena role [Čtečka](../../role-based-access-control/built-in-roles.md#reader) (nebo jiná předdefinovaná role, která zahrnuje přístup ke čtečce), když bylo předplatné integrované pro správu delegovaných prostředků Azure.

V klientovi zákazníka:

1. Přejděte na [stránku Poskytovatelé služeb](view-manage-service-providers.md).
2. Vyberte **nabídky poskytovatele služeb**.
3. Zkontrolujte, zda vidíte předplatné s názvem nabídky, který jste zadali v šabloně Správce prostředků.

> [!NOTE]
> Může trvat několik minut po dokončení nasazení, než se aktualizace projeví na webu Azure Portal.

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

## <a name="remove-access-to-a-delegation"></a>Odebrání přístupu k delegování

Ve výchozím nastavení mohou uživatelé v tenantovi zákazníka, kteří mají příslušná oprávnění, odebrat přístup poskytovatele služeb k delegovaným prostředkům na [stránce Zprostředkovatelé služeb](view-manage-service-providers.md#add-or-remove-service-provider-offers) na portálu Azure. Pokud tak učiní, žádný uživatel v tenantovi poskytovatele služeb bude mít přístup k prostředkům, které byly dříve delegovány.

Pokud jste byli na palubě uživatelů s [přiřazení maže spravované služby odstranit roli](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) při nástupu zákazníka pro azure delegované správy prostředků, tito uživatelé budou také moci odebrat delegování.

Následující příklad ukazuje přiřazení udělující **roli Odstranění při registraci spravovaných služeb,** která může být zahrnuta do souboru parametrů:

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

Uživatel s tímto oprávněním můžete odebrat delegování jedním z následujících způsobů.

### <a name="azure-portal"></a>portál Azure

1. Přejděte na [stránku Moji zákazníci](view-manage-customers.md).
2. Vyberte **delegování**.
3. Najděte delegování, které chcete odebrat, a vyberte ikonu koše, která se zobrazí v řádku.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory 

Login-AzAccount

# Select the subscription that is delegated - or contains the delegated resource group(s)

Select-AzSubscription -SubscriptionName "<subscriptionName>"

# Get the registration assignment

Get-AzManagedServicesAssignment -Scope "/subscriptions/{delegatedSubscriptionId}"

# Delete the registration assignment

Remove-AzManagedServicesAssignment -ResourceId "/subscriptions/{delegatedSubscriptionId}/providers/Microsoft.ManagedServices/registrationAssignments/{assignmentGuid}"
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory

az login

# Select the subscription that is delegated – or contains the delegated resource group(s)

az account set -s <subscriptionId/name>

# List registration assignments

az managedservices assignment list

# Delete the registration assignment

az managedservices assignment delete --assignment <id or full resourceId>
```

## <a name="next-steps"></a>Další kroky

- Další informace o [prostředích správy mezi tenanty](../concepts/cross-tenant-management-experience.md).
- [Zobrazte a spravujte zákazníky](view-manage-customers.md) tak, že přejdete na můj **zákazník** na webu Azure Portal.
