---
title: Zapojení zákazníků Azure Delegovaná správa prostředků – maják Azure
description: Zjistěte, jak připojit zákazníkům Azure delegované správy prostředků, umožňuje jejich prostředky k přístupu a spravovat prostřednictvím vašeho vlastního tenanta.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 1885a6220f14de234710b6980b5d3b6a6172bb7e
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809859"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Zapojení zákazníků Azure Delegovaná správa prostředků

Tento článek vysvětluje, jak, jako poskytovatel služeb, můžete si zaregistrovat zákazníka ke správě prostředků Azure delegované umožňuje jejich delegované prostředky (předplatných nebo skupinách prostředků) k přístupu a spravovat prostřednictvím Azure Active Directory (vlastní) Tenant Azure AD). Zatímco budeme odkazovat poskytovatelů služeb a zákazníků, podnikům správu více tenantů můžete použít stejný postup pro konsolidaci jejich prostředí pro správu.

Pokud spravujete více zákazníkům prostředky, můžete tento proces opakovat. Potom když oprávněný uživatel přihlásí k vašemu tenantovi, tohoto uživatele můžete být oprávnění napříč obory tenantů zákazníka k provádění operací správy bez nutnosti se ke každému klientovi jednotlivých zákazníků.

Vaše ID Microsoft Partner Network (MPN) můžete přidružit vaše předplatná připojili ke sledování vliv mezi zákazníky. Další informace najdete v tématu [propojit ID partnera k účtům Azure](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started).

> [!NOTE]
> Zákazníci mohou mít připojili automaticky při nákupu nabídky spravované služby (veřejné nebo soukromé), kterou jste publikovali na webu Azure Marketplace. Další informace najdete v tématu [publikování spravovaných služeb nabízí na webu Azure Marketplace](publish-managed-services-offers.md). Můžete také použít procesu registrace je zde popsáno nabídkou publikovaných na webu Azure Marketplace.

Proces zařazení do systému vyžaduje akce, které mají být provedeny z v rámci tenanta i poskytovatele služeb a zákazníka tenanta. Všechny tyto kroky jsou popsané v tomto článku.

> [!IMPORTANT]
> V současné době nemůžete připojit předplatné (nebo skupinu prostředků v rámci předplatného) pro Azure delegovat správu prostředků, pokud odběr využívá Azure Databricks. Podobně pokud odběr byl zaregistrován pro registraci se **Microsoft.ManagedServices** poskytovatele prostředků, nebude možné vytvořit pracovní prostor Databricks pro toto předplatné v tuto chvíli.

## <a name="gather-tenant-and-subscription-details"></a>Získat podrobnosti o tenanta a předplatné

K připojení zákazníka tenanta, musí mít aktivní předplatné Azure. Budete potřebovat znát následující:

- ID tenanta tenanta poskytovatele služeb (kde budete spravovat prostředky zákazníka)
- ID tenanta zákazníka tenanta (který má prostředky spravované poskytovatelem služeb)
- ID předplatného pro každé konkrétní předplatné v tenantovi zákazníka, které budou spravovány poskytovatelem služeb (nebo, která obsahuje skupiny prostředků, které bude spravovat poskytovatele služeb)

Pokud tyto informace ještě nemáte, můžete jej načíst v jednom z následujících způsobů.

### <a name="azure-portal"></a>portál Azure

Vaše ID tenanta můžete zobrazit ukázáním myší název vašeho účtu na pravé straně horním rohu webu Azure portal, nebo výběrem **přepnout adresář**. Vyberte a zkopírujte si ID tenanta, vyhledejte "Azure Active Directory" z portálu a potom vyberte **vlastnosti** a zkopírujte hodnotu uvedenou v **ID adresáře** pole. Pokud chcete najít ID předplatného, vyhledejte "Předplatné" a pak vyberte ID odpovídající předplatné.

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


## <a name="ensure-the-customers-subscription-is-registered-for-onboarding"></a>Ujistěte se, že zákazníka předplatné je registrováno pro připojení

Každé předplatné musí být autorizované pro registraci tak, že ručně zaregistrujete **Microsoft.ManagedServices** poskytovatele prostředků. Zákazník může registrace předplatného podle kroků uvedených v [poskytovatelé a typy prostředků Azure](../../azure-resource-manager/resource-manager-supported-services.md).

Odběratele můžete ověřit, že předplatné připravené pro registraci v jednom z následujících způsobů.

### <a name="azure-portal"></a>portál Azure

1. Na webu Azure Portal vyberte předplatné.
1. Vyberte **poskytovatelů prostředků**.
1. Ujistěte se, že **Microsoft.ManagedServices** ukazovat **registrované**.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Set-AzContext -Subscription <subscriptionId>
Get-AzResourceProvider -ProviderNameSpace 'Microsoft.ManagedServices'
```

Měla by se vrátit výsledky podobné následujícím:

```output
ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationDefinitions}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationAssignments}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {operations}
Locations         : {}
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set –subscription <subscriptionId>
az provider show –namespace "Microsoft.ManagedServices" –-output table
```

Měla by se vrátit výsledky podobné následujícím:

```output
Namespace                  RegistrationState
-------------------------  -------------------
Microsoft.ManagedServices  Registered
```

## <a name="define-roles-and-permissions"></a>Definování rolí a oprávnění

Jako poskytovatel služeb můžete použít několik nabídek s jednoho zákazníka, které vyžadují různý přístup pro různé obory.

Abychom usnadnili správu, doporučujeme použít skupiny uživatelů Azure AD pro každou roli díky tomu umožňuje přidání nebo odebrání jednotlivých uživatelů do skupiny, spíše než přiřazení oprávnění přímo na tohoto uživatele. Můžete také přiřadit role instančního objektu. Ujistěte se, postupujte podle principu nejnižší úrovně oprávnění tak, aby uživatelé pouze oprávnění potřebná k dokončení jejich úloh pomáhá snižovat riziko neúmyslných chyb. Další informace najdete v tématu [doporučené postupy pro zabezpečení](../concepts/recommended-security-practices.md).

> [!NOTE]
> Přiřazení role musí používat řízení přístupu na základě role (RBAC) [předdefinované role](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles). Všechny předdefinované role jsou aktuálně podporovány se správou prostředků Azure delegované s výjimkou vlastníka a žádné předdefinované role s [DataActions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions) oprávnění. Předdefinovaná role správce přístupu uživatelů se podporuje pro omezené použití, jak je popsáno níže. Vlastní role a [rolí správce v klasickém modelu předplatného](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators) také nejsou podporovány.

Aby bylo možné definovat autorizace, budete potřebovat znát hodnoty ID pro každého uživatele, skupiny uživatelů nebo instanční objekt služby, ke kterému chcete udělit přístup. Budete také potřebovat ID definice role pro každou předdefinovaná role, kterou chcete přiřadit. Pokud je ještě nemáte, můžete je načíst v jednom z následujících způsobů.



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
az ad group list –-query "[?displayName == '<yourGroupName>'].objectId" –-output tsv

# To retrieve the objectId for an Azure AD user
az ad user show –-upn-or-object-id "<yourUPN>" –-query "objectId" –-output tsv

# To retrieve the objectId for an SPN
az ad sp list –-query "[?displayName == '<spDisplayName>'].objectId" –-output tsv

# To retrieve role definition IDs
az role definition list –-name "<roleName>" | grep name
```

## <a name="create-an-azure-resource-manager-template"></a>Vytvoření šablony Azure Resource Manageru

Pro připojení vašich zákazníků, bude potřeba vytvořit [Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/) šablony, která zahrnuje následující:

|Pole  |Definice  |
|---------|---------|
|**mspName**     |Název poskytovatele služeb         |
|**mspOfferDescription**     |Stručný popis nabídky (například "Contoso nabídky virtuálních počítačů management")      |
|**managedByTenantId**     |Vaše ID tenanta         |
|**Povolení**     |**PrincipalId** hodnoty uživatelů/skupin/hlavní názvy služby z vašeho tenanta, každý s **principalIdDisplayName** umožňující vaše zákazníky, pochopit účel autorizaci a namapované na předdefinované **roleDefinitionId** hodnotu k určení úrovně přístupu         |

Pro připojení předplatné zákazníka, použijte příslušnou šablonu Azure Resource Manageru, poskytující v našich [ukázkové úložiště](https://github.com/Azure/Azure-Lighthouse-samples/), společně s odpovídající parametry soubor, který upravit, aby odpovídaly konfiguraci a definovat vaše povolení. Samostatné šablony jsou k dispozici v závislosti na tom, jestli jste připojení celé předplatné, skupinu prostředků nebo více skupin prostředků v rámci předplatného. Poskytujeme také šablonu, kterou můžete použít pro zákazníky, kteří nabídku nákupu na spravovanou službu, kterou jste publikovali na webu Azure Marketplace, pokud chcete připojit svoje předplatná tímto způsobem.

|**Pro připojení to**  |**Pomocí této šablony Azure Resource Manageru**  |**Tento parametr soubor a upravit** |
|---------|---------|---------|
|Subscription   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resource group   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Více skupin prostředků v rámci předplatného   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Předplatné (při použití nabídky publikovaných na webu Azure Marketplace)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> Proces je zde popsáno, vyžaduje samostatné nasazení pro každé předplatné právě připojili.
> 
> Samostatná nasazení se rovněž vyžadují, pokud se registrace skupiny více zdrojů v rámci různých předplatných. Ale připojení více skupin prostředků v rámci jednoho předplatného můžete udělat v jedno nasazení.

Následující příklad ukazuje upravenou **resourceProjection.parameters.json** souboru, který bude používat pro připojení předplatného. Soubory parametr skupiny prostředků (umístěný ve [rg delegovaný správy prostředků](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) složky) jsou podobné, ale také **rgName** parametr k identifikaci konkrétní prostředek skupiny bude zprovoznění.

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
Poslední autorizace ve výše uvedeném příkladu se přidá **principalId** s rolí správce uživatelských přístupů (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). Při přiřazování této role, je nutné uvést **delegatedRoleDefinitionIds** vlastnost a jednu nebo více předdefinovaných rolí. Uživatel vytvořené v tomto autorizace bude moct přiřadit tyto předdefinované role spravovaným identitám. Všimněte si, že žádná další oprávnění, které jsou obvykle spojené s rolí správce uživatelských přístupů uplatní na tohoto uživatele.

## <a name="deploy-the-azure-resource-manager-templates"></a>Nasazení šablon Azure Resource Manageru

Po aktualizaci vám v souboru parametrů, musí zákazník nasazení šablony Resource Manageru v tenantovi zákazníka jako nasazení úrovně předplatného. Samostatného nasazení je potřeba pro každé předplatné, které chcete připojit ke správě prostředků Azure delegované (nebo u každého odběru, který obsahuje skupiny prostředků, které chcete připojit).

> [!IMPORTANT]
> Nasazení je třeba provést pomocí účtu bez hostovaného v tenantovi zákazníka, který má [předdefinovanou roli vlastníka](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) pro předplatné právě připojili (nebo který obsahuje skupiny prostředků, které jsou právě připojili).

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

## <a name="confirm-successful-onboarding"></a>Potvrďte úspěšnou registraci

Když zákazníky na základě předplatného bylo úspěšně připojili ke správě prostředků Azure delegované, budou uživatelé v tenantovi poskytovatele služeb k odběru a jejích prostředků (Pokud kterým byl udělen přístup k němu pomocí výše uvedeného procesu samostatně nebo jako člen skupiny Azure AD s příslušnými oprávněními). Pokud to pokud chcete potvrdit, zkontrolujte, ujistěte se, že předplatné, zobrazí se v jednom z následujících způsobů.  

### <a name="azure-portal"></a>portál Azure

V tenantovi poskytovatele služeb:

1. Přejděte [stránku zákazníkům](view-manage-customers.md).
2. Vyberte **zákazníkům**.
3. Ověřte, že se můžete předplatná se název nabídky, které jste zadali v šabloně Resource Manageru.

V tenantovi zákazníka:

1. Přejděte [stránku poskytovatelů služby](view-manage-service-providers.md).
2. Vyberte **služby poskytovatele nabídky**.
3. Ověřte, že se můžete předplatná se název nabídky, které jste zadali v šabloně Resource Manageru.

> [!NOTE]
> Může trvat několik minut poté, co vaše nasazení je kompletní předtím, než se aktualizace se projeví na webu Azure Portal.

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

## <a name="next-steps"></a>Další postup

- Další informace o [napříč tenanty činnosti správy](../concepts/cross-tenant-management-experience.md).
- [Umožňuje zobrazit a spravovat zákazníky](view-manage-customers.md) tak, že přejdete do **zákazníci** na webu Azure Portal.
