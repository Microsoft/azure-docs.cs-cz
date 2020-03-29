---
title: Monitorování Azure AD B2C pomocí Azure Monitoru
titleSuffix: Azure AD B2C
description: Zjistěte, jak protokolovat události Azure AD B2C pomocí Azure Monitor pomocí správy delegovaných prostředků.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.author: mimart
ms.subservice: B2C
ms.date: 02/10/2020
ms.openlocfilehash: acba378badb41324b2124b84833407da920a0e00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190054"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Monitorování Azure AD B2C pomocí Azure Monitoru

Azure Monitor slouží ke směrování přihlášení azure active directory B2C (Azure AD B2C) a [auditování](view-audit-logs.md) protokolů do různých řešení monitorování. Protokoly můžete uchovávat pro dlouhodobé použití nebo integrovat s nástroji pro správu informací o zabezpečení a událostí (SIEM) třetích stran, abyste získali přehled o vašem prostředí.

Události protokolu můžete směrovat do:

* Účet [úložiště](../storage/blobs/storage-blobs-introduction.md)Azure .
* Centrum [událostí](../event-hubs/event-hubs-about.md) Azure (a integrovat s instancemi Splunk a Sumo Logic).
* Pracovní [prostor Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md) (k analýze dat, vytváření řídicích panelů a upozornění na konkrétní události).

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

## <a name="prerequisites"></a>Požadavky

Postup v tomto článku nasadíte pomocí modulu Azure PowerShell pomocí šablony Azure ResourceShell.

* [Modul Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) verze 6.13.1 nebo vyšší

Můžete taky použít [Azure Cloud Shell](https://shell.azure.com), který obsahuje nejnovější verzi modulu Azure PowerShell.

## <a name="delegated-resource-management"></a>Správa delegovaných prostředků

Azure AD B2C využívá [monitorování Služby Azure Active Directory](../active-directory/reports-monitoring/overview-monitoring.md). Chcete-li povolit *nastavení diagnostiky* ve službě Azure Active Directory v rámci klienta Azure AD B2C, použijte [delegovanou správu prostředků](../lighthouse/concepts/azure-delegated-resource-management.md).

Autorizace uživatele nebo skupiny v adresáři Azure AD B2C **(poskytovatel služeb)** ke konfiguraci instance Azure Monitor v rámci klienta, který obsahuje vaše předplatné Azure **(zákazník).** Chcete-li vytvořit autorizaci, nasadíte šablonu [Azure Resource Manager](../azure-resource-manager/index.yml) do vašeho tenanta Azure AD obsahujícího předplatné. Následující části vás provedou procesem.

## <a name="create-or-choose-resource-group"></a>Vytvořit nebo vybrat skupinu prostředků

Toto je skupina prostředků obsahující cílový účet úložiště Azure, centrum událostí nebo pracovní prostor Log Analytics pro příjem dat z Azure Monitoru. Název skupiny prostředků zadáte při nasazení šablony Azure Resource Manager.

[Vytvořte skupinu prostředků](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) nebo zvolte existující v tenantovi Služby Azure Active Directory (Azure AD), který obsahuje vaše předplatné Azure, *ne* adresář, který obsahuje vašeho klienta Azure AD B2C.

Tento příklad používá skupinu prostředků s názvem *azure-ad-b2c-monitor* v oblasti *centrální USA.*

## <a name="delegate-resource-management"></a>Delegovat správu zdrojů

Dále shromážděte následující informace:

**ID adresáře** vašeho adresáře Azure AD B2C (označované také jako ID klienta).

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) jako uživatel s rolí *správce uživatele* (nebo vyšší).
1. Vyberte ikonu **Directory + Subscription** na panelu nástrojů portálu a pak vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. Vyberte **Azure Active Directory**, vyberte **Vlastnosti**.
1. Zaznamenejte **ID adresáře**.

**ID objektu** skupiny Azure AD B2C nebo uživatele, který chcete udělit *přispěvateli* oprávnění ke skupině prostředků, kterou jste vytvořili dříve v adresáři obsahujícím vaše předplatné.

Chcete-li usnadnit správu, doporučujeme použít *skupiny* uživatelů Azure AD pro každou roli, což vám umožní přidat nebo odebrat jednotlivé uživatele do skupiny, nikoli přiřazovat oprávnění přímo tomuto uživateli. V tomto návodu přidáte uživatele.

1. Když **je služba Azure Active Directory** pořád vybraná na webu Azure Portal, vyberte **Uživatelé**a pak vyberte uživatele.
1. Zaznamenejte **ID objektu**uživatele .

### <a name="create-an-azure-resource-manager-template"></a>Vytvoření šablony Azure Resource Manageru

Chcete-li napalubě klienta Azure AD **(zákazník),** vytvořte [šablonu Azure Resource Manager](../lighthouse/how-to/onboard-customer.md) pro vaši nabídku s následujícími informacemi. A `mspOfferName` `mspOfferDescription` hodnoty jsou viditelné při zobrazení podrobností o nabídce na [stránce zprostředkovatelé služeb](../lighthouse/how-to/view-manage-service-providers.md) na webu Azure Portal.

| Pole   | Definice |
|---------|------------|
| `mspOfferName`                     | Název popisující tuto definici. Například *spravované služby Azure AD B2C*. Tato hodnota je zákazníkovi zobrazena jako název nabídky. |
| `mspOfferDescription`              | Stručný popis vaší nabídky. Například *povolí Azure Monitor v Azure AD B2C*.|
| `rgName`                           | Název skupiny prostředků, kterou vytvoříte dříve ve vašem tenantovi Azure AD. Například *azure-ad-b2c-monitor*. |
| `managedByTenantId`                | **ID adresáře** vašeho klienta Azure AD B2C (označované také jako ID klienta). |
| `authorizations.value.principalId` | **ID objektu** skupiny B2C nebo uživatele, který bude mít přístup k prostředkům v tomto předplatném Azure. Pro tento návod zadejte ID objektu uživatele, které jste zaznamenali dříve. |

Stáhněte si šablonu a soubory parametrů Azure Resource Manageru:

- [rgDelegovánoResourceManagement.json](https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)
- [rgDelegatedResourceManagement.parameters.json](https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)

Dále aktualizujte soubor parametrů s hodnotami, které jste zaznamenali dříve. Následující fragment JSON ukazuje příklad souboru parametrů šablony Azure Resource Manageru. V `authorizations.value.roleDefinitionId`případě , použijte [předdefinovanou](../role-based-access-control/built-in-roles.md) hodnotu `b24988ac-6180-42a0-ab88-20f7382dd24c`role pro *roli přispěvatele*.

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

### <a name="deploy-the-azure-resource-manager-templates"></a>Nasazení šablon Azure Resource Manageru

Po aktualizaci souboru parametrů nasaďte šablonu Azure Resource Manager u tenanta Azure jako nasazení na úrovni předplatného. Vzhledem k tomu, že se jedná o nasazení na úrovni předplatného, nelze jej inicializovat na webu Azure Portal. Můžete nasadit pomocí modulu Azure PowerShell nebo Azure CLI. Metoda Azure PowerShell je zobrazena níže.

Přihlaste se k adresáři obsahujícímu předplatné pomocí [služby Connect-AzAccount](/powershell/azure/authenticate-azureps). Pomocí `-tenant` příznaku vynuťte ověřování do správného adresáře.

```PowerShell
Connect-AzAccount -tenant contoso.onmicrosoft.com
```

Pomocí rutiny [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) uveďte předplatná, ke kterým má mít přístup běžný účet v rámci klienta Azure AD. Zaznamenejte ID předplatného, které chcete promítnout do klienta Azure AD B2C.

```PowerShell
Get-AzSubscription
```

Dále přepněte na předplatné, které chcete promítnout do tenanta Azure AD B2C:

``` PowerShell
Select-AzSubscription <subscription ID>
```

Nakonec nasaďte šablonu a soubory parametrů Azure Resource Manageru, které jste stáhli a aktualizovali dříve. Odpovídajícím `Location`způsobem `TemplateFile`nahraďte hodnoty , a `TemplateParameterFile` hodnoty.

```PowerShell
New-AzDeployment -Name "AzureADB2C" `
                 -Location "centralus" `
                 -TemplateFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.json" `
                 -TemplateParameterFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.parameters.json" `
                 -Verbose
```

Úspěšné nasazení šablony vytváří výstup podobný následujícímu (výstup zkrácen pro stručnost):

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

Po nasazení šablony může trvat několik minut, než se projekce prostředků dokončí. Možná budete muset počkat několik minut (obvykle ne více než pět) před přechodem na další část vybrat předplatné.

## <a name="select-your-subscription"></a>Vyberte své předplatné.

Po nasazení šablony a čekali několik minut na dokončení projekce prostředků, přidružte své předplatné k adresáři Azure AD B2C s následujícími kroky.

1. Pokud jste teď přihlášení, **odhlaste** se z webu Azure Portal. Tento a následující krok se provádí k aktualizaci přihlašovacích údajů v relaci portálu.
1. Přihlaste se k [portálu Azure pomocí](https://portal.azure.com) svého účtu pro správu Azure AD B2C.
1. Na panelu nástrojů portálu vyberte ikonu **Adresář + Odběr.**
1. Vyberte adresář, který obsahuje vaše předplatné.

    ![Přepnout adresář](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)
1. Ověřte, zda jste vybrali správný adresář a předplatné. V tomto příkladu jsou vybrány všechny adresáře a odběry.

    ![Všechny adresáře vybrané ve filtru & odběr adresáře](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="configure-diagnostic-settings"></a>Konfigurace nastavení diagnostiky

Nastavení diagnostiky definují, kam mají být odesílány protokoly a metriky pro prostředek. Možné destinace jsou:

- [Účet služby Azure Storage](../azure-monitor/platform/resource-logs-collect-storage.md)
- [Řešení event hubů.](../azure-monitor/platform/resource-logs-stream-event-hubs.md)
- [Pracovní prostor Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md)

Pokud jste tak ještě neučinili, vytvořte instanci zvoleného cílového typu ve skupině prostředků, kterou jste zadali v [šabloně Azure Resource Manager](#create-an-azure-resource-manager-template).

### <a name="create-diagnostic-settings"></a>Vytvoření nastavení diagnostiky

Jste připraveni [k vytvoření nastavení diagnostiky](../active-directory/reports-monitoring/overview-monitoring.md) na webu Azure Portal.

Konfigurace nastavení monitorování pro protokoly aktivit Azure AD B2C:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
1. Vyberte ikonu **Directory + Subscription** na panelu nástrojů portálu a pak vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. Výběr **služby Azure Active Directory**
1. V části **Monitorování** vyberte **Nastavení diagnostiky**.
1. Pokud jsou v prostředku existující nastavení, zobrazí se již nakonfigurovaný seznam nastavení. Buď vyberte **Přidat diagnostické nastavení,** chcete-li přidat nové nastavení, nebo **Upravit** nastavení pro úpravu existujícího nastavení. Každé nastavení může mít maximálně jeden z jednotlivých typů cílů.

    ![Podokno nastavení diagnostiky na webu Azure Portal](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. Zadejte název nastavení, pokud ho ještě nemá.
1. Chcete-li protokoly odeslat, zaškrtněte políčko pro každý cíl. Vyberte **Konfigurovat,** chcete-li určit jejich nastavení, jak je popsáno v následující tabulce.

    | Nastavení | Popis |
    |:---|:---|
    | Archivovat v účtu úložiště | Název účtu úložiště. |
    | Streamovat do centra událostí | Obor názvů, kde je centrum událostí vytvořeno (pokud se jedná o první protokoly streamování) nebo do datového proudu (pokud již existují prostředky, které jsou datovým proudem této kategorie protokolu do tohoto oboru názvů).
    | Odeslání do Log Analytics | Název pracovního prostoru. |

1. Vyberte **AuditLogs** a **SignInLogs**.
1. Vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

Další informace o přidávání a konfiguraci diagnostických nastavení v Azure Monitoru najdete v [tématu Kurz: Shromažďování a analýza protokolů prostředků z prostředku Azure](../azure-monitor/insights/monitor-azure-resource.md).

Informace o streamování protokolů Azure AD do centra událostí najdete [v tématu Kurz: Streamování protokolů služby Azure Active Directory do centra událostí Azure](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
