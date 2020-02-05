---
title: Monitorování Azure AD B2C s využitím Azure Monitor
titleSuffix: Azure AD B2C
description: Naučte se protokolovat události Azure AD B2C pomocí Azure Monitor pomocí delegované správy prostředků.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.author: marsma
ms.subservice: B2C
ms.date: 02/03/2020
ms.openlocfilehash: 108c9c1112327a3fcadeff4c4074f31f976a4e3d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026754"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Monitorování Azure AD B2C s využitím Azure Monitor

Pomocí Azure Monitor můžete směrovat události aktivity využití Azure Active Directory B2C (Azure AD B2C) do různých řešení monitorování. Protokoly můžete uchovávat pro dlouhodobé použití nebo integraci s nástroji SIEM (Security Information and Event Management) třetích stran, abyste získali přehled o vašem prostředí.

Události protokolu můžete směrovat do:

* Účet úložiště Azure.
* Centrum událostí Azure (a integrujte je s vašimi logickými instancemi Splunk a sumo).
* Pracovní prostor Azure Log Analytics (pro analýzu dat, vytváření řídicích panelů a upozornění na konkrétní události).

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto článku nasadíte Azure Resource Manager šablonu pomocí modulu Azure PowerShell.

* Verze [modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) 6.13.1 nebo vyšší

Můžete také použít [Azure Cloud Shell](https://shell.azure.com), která zahrnuje nejnovější verzi modulu Azure PowerShell.

## <a name="delegated-resource-management"></a>Delegovaná Správa prostředků

Azure AD B2C využívá [Azure Active Directory monitorování](../active-directory/reports-monitoring/overview-monitoring.md). Pokud chcete povolit *nastavení diagnostiky* v Azure Active Directory v rámci vašeho tenanta Azure AD B2C, použijte [delegovanou správu prostředků](../lighthouse/concepts/azure-delegated-resource-management.md).

K nakonfigurování instance Azure Monitor v rámci tenanta, který obsahuje vaše předplatné Azure ( **zákazníka**), autorizujete uživatele v adresáři Azure AD B2C ( **poskytovatelem služeb**). Pokud chcete vytvořit autorizaci, nasadíte šablonu [Azure Resource Manager](../azure-resource-manager/index.yml) do svého tenanta služby Azure AD obsahujícího předplatné. Následující části vás provedou procesem.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

V tenantovi Azure Active Directory (Azure AD), který obsahuje vaše předplatné Azure (*ne* adresář obsahující Azure AD B2C tenanta), [vytvořte skupinu prostředků](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups). Použijte následující hodnoty:

* **Předplatné**: Vyberte své předplatné Azure.
* **Skupina prostředků**: zadejte název pro skupinu prostředků. Například *Azure-AD-B2C-monitor*.
* **Oblast**: vyberte umístění Azure. Například *USA (střed)* .

## <a name="delegate-resource-management"></a>Delegovat správu prostředků

Dále shromážděte následující informace:

**ID adresáře** vašeho adresáře Azure AD B2C (označuje se také jako ID tenanta).

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) jako uživatel s rolí *Správce uživatelů* (nebo vyšší).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. Vyberte možnost **Azure Active Directory**, vyberte možnost **vlastnosti**.
1. Poznamenejte si **ID adresáře**.

**ID objektu** Azure AD B2C skupiny nebo uživatele, které chcete udělit oprávnění *přispěvatele* ke skupině prostředků, kterou jste vytvořili dříve v adresáři, který obsahuje vaše předplatné.

Pro usnadnění správy doporučujeme použít pro každou roli *skupiny* uživatelů Azure AD, což umožňuje přidat nebo odebrat jednotlivé uživatele do skupiny místo přiřazování oprávnění přímo tomuto uživateli. V tomto návodu přidáte uživatele.

1. Pokud jste v Azure Portal stále vybrali **Azure Active Directory** , vyberte možnost **Uživatelé**a potom vyberte uživatele.
1. Poznamenejte si **ID objektu**uživatele.

### <a name="create-an-azure-resource-manager-template"></a>Vytvoření šablony Azure Resource Manageru

Pokud chcete připojit klienta služby Azure AD ( **zákazníka**), vytvořte [šablonu Azure Resource Manager](../lighthouse/how-to/onboard-customer.md) pro vaši nabídku s následujícími informacemi. Hodnoty `mspOfferName` a `mspOfferDescription` se zobrazí při zobrazení podrobností nabídky na [stránce poskytovatelé služeb](../lighthouse/how-to/view-manage-service-providers.md) Azure Portal.

| Pole   | Definice |
|---------|------------|
| `mspOfferName`                     | Název popisující tuto definici. Například *Azure AD B2C spravované služby*. Tato hodnota se zobrazí zákazníkovi jako název nabídky. |
| `mspOfferDescription`              | Stručný popis vaší nabídky Například *povolí Azure monitor v Azure AD B2C*.|
| `rgName`                           | Název skupiny prostředků, kterou jste v tenantovi Azure AD vytvořili dříve. Například *Azure-AD-B2C-monitor*. |
| `managedByTenantId`                | **ID adresáře** vašeho tenanta Azure AD B2C (označuje se také jako ID tenanta). |
| `authorizations.value.principalId` | **ID objektu** skupiny B2C nebo uživatele, který bude mít přístup k prostředkům v tomto předplatném Azure. Pro tento návod zadejte ID objektu uživatele, které jste si poznamenali dříve. |

Stažení šablony Azure Resource Manager a souborů parametrů:

- [rgDelegatedResourceManagement.json](https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)
- [rgDelegatedResourceManagement.parameters.json](https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)

Dále aktualizujte soubor parametrů s hodnotami, které jste si poznamenali dříve. Následující fragment kódu JSON ukazuje příklad souboru parametrů šablony Azure Resource Manager. Pro `authorizations.value.roleDefinitionId`použijte [předdefinovanou hodnotu role](../role-based-access-control/built-in-roles.md) *přispěvatele*`b24988ac-6180-42a0-ab88-20f7382dd24c`.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Azure AD B2C Managed Services"
        },
        "mspOfferDescription": {
            "value": "Enables Azure Monitor in Azure AD B2C"
        },
        "rgName": {
            "value": "azure-ad-b2c-monitor"
        },
        "managedByTenantId": {
            "value": "<Replace with DIRECTORY ID of Azure AD B2C tenant (tenant ID)>"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "<Replace with user's OBJECT ID>",
                    "principalIdDisplayName": "Azure AD B2C tenant administrator",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                }
            ]
        }
    }
}
```

### <a name="deploy-the-azure-resource-manager-templates"></a>Nasazení šablon Azure Resource Manager

Po aktualizaci souboru parametrů nasaďte šablonu Azure Resource Manager do tenanta Azure jako nasazení na úrovni předplatného. Vzhledem k tomu, že se jedná o nasazení na úrovni předplatného, nejde ho iniciovat v Azure Portal. Nasazení můžete nasadit pomocí modulu Azure PowerShell nebo rozhraní příkazového řádku Azure CLI. Níže je uvedena metoda Azure PowerShell.

Přihlaste se k adresáři, který obsahuje vaše předplatné, pomocí [Connect-AzAccount](/powershell/azure/authenticate-azureps). Použijte příznak `-tenant` k vynucení ověřování do správného adresáře.

```PowerShell
Connect-AzAccount -tenant contoso.onmicrosoft.com
```

Pomocí rutiny [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) můžete zobrazit seznam předplatných, ke kterým má aktuální účet přístup v TENANTOVI Azure AD. Poznamenejte si ID předplatného, které chcete zamítnout do svého tenanta Azure AD B2C.

```PowerShell
Get-AzSubscription
```

Potom přepněte do předplatného, které chcete zamítnout do tenanta Azure AD B2C:

``` PowerShell
Select-AzSubscription <subscription ID>
```

Nakonec nasaďte šablonu Azure Resource Manager a soubory parametrů, které jste stáhli a aktualizovali dříve. Nahraďte hodnoty `Location`, `TemplateFile`a `TemplateParameterFile` odpovídajícím způsobem.

```PowerShell
New-AzDeployment -Name "AzureADB2C" `
                 -Location "centralus" `
                 -TemplateFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.json" `
                 -TemplateParameterFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.parameters.json" `
                 -Verbose
```

Úspěšné nasazení šablony vytvoří výstup podobný následujícímu (výstup zkrácen pro zkrácení):

```Console
PS /usr/csuser/clouddrive> New-AzDeployment -Name "AzureADB2C" `
>>                  -Location "centralus" `
>>                  -TemplateFile "rgDelegatedResourceManagement.json" `
>>                  -TemplateParameterFile "rgDelegatedResourceManagement.parameters.json" `
>>                  -Verbose
WARNING: Breaking changes in the cmdlet 'New-AzDeployment' :
WARNING:  - The cmdlet 'New-AzSubscriptionDeployment' is replacing this cmdlet.


WARNING: NOTE : Go to https://aka.ms/azps-changewarnings for steps to suppress this breaking change warning, and other information on breaking changes in Azure PowerShell.
VERBOSE: 7:25:14 PM - Template is valid.
VERBOSE: 7:25:15 PM - Create template deployment 'AzureADB2C'
VERBOSE: 7:25:15 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:42 PM - Resource Microsoft.ManagedServices/registrationDefinitions '44444444-4444-4444-4444-444444444444' provisioning status is succeeded
VERBOSE: 7:25:48 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:53 PM - Resource Microsoft.Resources/deployments 'rgAssignment' provisioning status is running
VERBOSE: 7:25:53 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:59 PM - Resource Microsoft.ManagedServices/registrationAssignments '11111111-1111-1111-1111-111111111111' provisioning status is running
VERBOSE: 7:26:17 PM - Checking deployment status in 5 seconds
VERBOSE: 7:26:23 PM - Resource Microsoft.ManagedServices/registrationAssignments '11111111-1111-1111-1111-111111111111' provisioning status is succeeded
VERBOSE: 7:26:23 PM - Checking deployment status in 5 seconds
VERBOSE: 7:26:29 PM - Resource Microsoft.Resources/deployments 'rgAssignment' provisioning status is succeeded

DeploymentName          : AzureADB2C
Location                : centralus
ProvisioningState       : Succeeded
Timestamp               : 1/31/20 7:26:24 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                   Type                       Value
                          =====================  =========================  ==========
                          mspOfferName           String                     Azure AD B2C Managed Services
                          mspOfferDescription    String                     Enables Azure Monitor in Azure AD B2C
...
```

Až šablonu nasadíte, může trvat několik minut, než se projekce prostředků dokončí. Než budete moct přejít k další části a vybrat předplatné, možná budete muset několik minut počkat (obvykle ne víc než pět).

## <a name="select-your-subscription"></a>Vyberte své předplatné.

Po nasazení šablony a dokončení projekce prostředku počkejte několik minut, přiřaďte své předplatné ke svému Azure AD B2C adresáři pomocí následujících kroků.

1. Pokud jste aktuálně přihlášení, **odhlaste** se z Azure Portal. Tato a následující krok se provádí za účelem aktualizace přihlašovacích údajů v relaci portálu.
1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu správce Azure AD B2C.
1. Na panelu nástrojů portálu vyberte ikonu **adresář + předplatné** .
1. Vyberte adresář, který obsahuje vaše předplatné.

    ![Přepnout adresář](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)
1. Ověřte, že jste vybrali správný adresář a předplatné. V tomto příkladu jsou vybrané všechny adresáře a odběry.

    ![Všechny adresáře vybrané ve filtru předplatných adresářových &](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="configure-diagnostic-settings"></a>Konfigurace nastavení diagnostiky

Po delegování správy prostředků a výběru předplatného budete připraveni [vytvořit nastavení diagnostiky](../active-directory/reports-monitoring/overview-monitoring.md) v Azure Portal.

Postup konfigurace nastavení monitorování pro Azure AD B2C protokoly aktivit:

1. Přihlaste se k [Portálu Azure](https://portal.azure.com/).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. Vyberte **Azure Active Directory**
1. V části **monitorování**vyberte **nastavení diagnostiky**.
1. Vyberte **+ Přidat nastavení diagnostiky**.

    ![Podokno nastavení diagnostiky v Azure Portal](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

## <a name="next-steps"></a>Další kroky

Další informace o přidání a konfiguraci nastavení diagnostiky v Azure Monitor najdete v tomto kurzu v dokumentaci k Azure Monitor:

[Kurz: shromáždění a analýza protokolů prostředků z prostředku Azure](/azure-monitor/learn/tutorial-resource-logs.md)
