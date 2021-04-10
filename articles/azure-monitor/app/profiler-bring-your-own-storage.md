---
title: Konfigurace BYOS (Přineste si vlastní úložiště) pro Profiler & Snapshot Debugger
description: Konfigurace BYOS (Přineste si vlastní úložiště) pro Profiler & Snapshot Debugger
ms.topic: conceptual
author: renatosalas
ms.author: regutier
ms.date: 01/14/2021
ms.reviewer: mbullwin
ms.openlocfilehash: 9c3ff91cbfb6423099040a6ea46eeb66f5461f48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100589661"
---
# <a name="configure-bring-your-own-storage-byos-for-application-insights-profiler-and-snapshot-debugger"></a>Konfigurace Přineste si vlastní úložiště (BYOS) pro Application Insights Profiler a Snapshot Debugger

## <a name="what-is-bring-your-own-storage-byos-and-why-might-i-need-it"></a>Co přináší vlastní úložiště (BYOS) a proč ho můžu potřebovat? 
Když použijete Application Insights Profiler nebo Snapshot Debugger, artefakty vygenerované vaší aplikací se nahrají do účtů Azure Storage přes veřejný Internet. Tyto účty jsou placeny a kontrolovány společností Microsoft ke zpracování a analýze. Microsoft řídí zásady správy životního a doby života pro tyto artefakty.

Pomocí Přineste si vlastní úložiště se tyto artefakty nahrají do účtu úložiště, který ovládáte. To znamená, že budete řídit zásady šifrování na místě, zásady správy životního cyklu a přístup k síti. Budete ale odpovědní za náklady spojené s tímto účtem úložiště.

> [!NOTE]
> Pokud povolujete privátní propojení, je nutné použít vlastní úložiště. Další informace o privátním odkazu pro Application Insights [najdete v dokumentaci.](../logs/private-link-security.md)
>
> Pokud povolujete Customer-Managed klíče, je nutné uvést vlastní úložiště. Další informace o Customer-Managed klíčů pro Application Insights [najdete v dokumentaci.](../logs/customer-managed-keys.md)

## <a name="how-will-my-storage-account-be-accessed"></a>Jak bude k účtu úložiště přistup?
1. Agenti, kteří jsou spuštěni ve vašem Virtual Machines nebo App Service, nahrávají artefakty (profily, snímky a symboly) do kontejnerů objektů BLOB ve vašem účtu. Tento proces zahrnuje kontaktování služby Application Insights Profiler nebo Snapshot Debugger k získání tokenu SAS (sdíleného přístupového podpisu) k novému objektu BLOB ve vašem účtu úložiště.
1. Služba Application Insights Profiler nebo Snapshot Debugger bude analyzovat příchozí objekt BLOB a zapsat zpět výsledky analýzy a soubory protokolu do úložiště objektů BLOB. V závislosti na dostupné výpočetní kapacitě k tomuto procesu může dojít kdykoli po nahrání.
1. Když zobrazíte trasování profileru nebo analýzu ladicího programu snímků, služba načte výsledky analýzy z úložiště objektů BLOB.

## <a name="prerequisites"></a>Požadavky
* Ujistěte se, že jste svůj účet úložiště vytvořili ve stejném umístění jako prostředek Application Insights. Například Pokud je prostředek Application Insights Západní USA 2, musí být váš účet úložiště také v Západní USA 2. 
* Udělte roli Přispěvatel dat objektů BLOB úložiště do aplikace AAD "přístup k důvěryhodnému úložišti diagnostické služby" v účtu úložiště prostřednictvím uživatelského rozhraní Access Control (IAM).
* Pokud je povolené soukromé propojení, nakonfigurujte další nastavení tak, aby umožňovalo připojení k naší důvěryhodné službě Microsoftu z vašeho Virtual Network. 

## <a name="how-to-enable-byos"></a>Jak povolit BYOS

### <a name="create-storage-account"></a>Vytvoření účtu úložiště
Vytvořte značku – nový účet úložiště (pokud ho ještě nemáte) ve stejném umístění jako váš Application Insights prostředek.
Pokud váš Application Insights prostředek je zapnutý `West US 2` , váš účet úložiště musí být v `West US 2` .

### <a name="grant-access-to-diagnostic-services-to-your-storage-account"></a>Udělení přístupu ke službě Diagnostic Services vašemu účtu úložiště
Účet úložiště BYOS bude propojený s prostředkem Application Insights. Pro každý prostředek Application Insights může existovat jen jeden účet úložiště a oba musí být ve stejném umístění. Můžete použít stejný účet úložiště s více než jedním prostředkem Application Insights.

Nejdřív je potřeba udělit službě Application Insights Profiler a Snapshot Debugger přístup k účtu úložiště. Pokud chcete udělit přístup, přidejte roli `Storage Blob Data Contributor` do aplikace AAD pojmenované `Diagnostic Services Trusted Storage Access` přes stránku Access Control (IAM) ve vašem účtu úložiště, jak je znázorněno na obrázku 1,0. 

Kroky: 
1. V části přidat přiřazení role klikněte na tlačítko Přidat. 
1. Vyberte roli Přispěvatel dat objektů BLOB úložiště. 
1. V části přiřazení přístupu k vyberte možnost uživatel, skupina nebo instanční objekt služby Azure AD. 
1. Hledat & vybrat aplikaci přístup k důvěryhodnému úložišti diagnostické služby 
1. Uložit změny

_![ Obrázek 1,0](media/profiler-bring-your-own-storage/figure-10.png)_ 
 _Obrázek 1,0_ 

Po přidání role se tato role zobrazí v části "přiřazení rolí", podobně jako na obrázku 1,1. 
_![ Obrázek 1,1](media/profiler-bring-your-own-storage/figure-11.png)_ 
 _Obrázek 1,1_ 

Pokud používáte také privátní odkaz, je vyžadována jedna další konfigurace, která umožňuje připojení k důvěryhodné službě společnosti Microsoft z vašeho Virtual Network. Informace najdete v [dokumentaci zabezpečení sítě úložiště](../../storage/common/storage-network-security.md#trusted-microsoft-services).

### <a name="link-your-storage-account-with-your-application-insights-resource"></a>Propojte svůj účet úložiště s vaším prostředkem Application Insights
Chcete-li nakonfigurovat BYOS pro diagnostiku na úrovni kódu (Profiler/ladicí program), existují tři možnosti:

* Používání rutin Azure PowerShell
* Použití rozhraní příkazového řádku Azure (CLI)
* Pomocí šablony Azure Resource Manageru

#### <a name="configure-using-azure-powershell-cmdlets"></a>Konfigurace pomocí rutin Azure PowerShell

1. Ujistěte se, že máte nainstalovanou AZ PowerShell 4.2.0 nebo novější.

    Pokud chcete nainstalovat Azure PowerShell, přečtěte si [oficiální dokumentaci k Azure PowerShell](/powershell/azure/install-az-ps).

1. Nainstalujte Application Insights rozšíření PowerShell.
    ```powershell
    Install-Module -Name Az.ApplicationInsights -Force
    ```

1. Přihlaste se pomocí účtu Azure.
    ```powershell
    Connect-AzAccount -Subscription "{subscription_id}"
    ```

    Další informace o tom, jak se přihlásit, najdete v [dokumentaci k Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

1. Odeberte předchozí účet úložiště propojený s vaším prostředkem Application Insights.

    Vzorku
    ```powershell
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "{resource_group_name}" -Name "{application_insights_name}"
    Remove-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id
    ```

    Příklad:
    ```powershell
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "byos-test" -Name "byos-test-westus2-ai"
    Remove-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id
    ```

1. Připojte svůj účet úložiště k vašemu prostředku Application Insights.
    
    Vzorku
    ```powershell
    $storageAccount = Get-AzStorageAccount -ResourceGroupName "{resource_group_name}" -Name "{storage_account_name}"
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "{resource_group_name}" -Name "{application_insights_name}"
    New-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id -LinkedStorageAccountResourceId $storageAccount.Id
    ```

    Příklad:
    ```powershell
    $storageAccount = Get-AzStorageAccount -ResourceGroupName "byos-test" -Name "byosteststoragewestus2"
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "byos-test" -Name "byos-test-westus2-ai"
    New-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id -LinkedStorageAccountResourceId $storageAccount.Id
    ```

#### <a name="configure-using-azure-cli"></a>Konfigurace pomocí Azure CLI

1. Ujistěte se, že máte nainstalované rozhraní příkazového řádku Azure CLI.

    Pokud chcete nainstalovat rozhraní příkazového řádku Azure, přečtěte si [oficiální dokumentaci k Azure CLI](/cli/azure/install-azure-cli).

1. Nainstalujte rozšíření Application Insights CLI.
    ```powershell
    az extension add -n application-insights
    ```

1. Připojte svůj účet úložiště k vašemu prostředku Application Insights.

    Vzorku
    ```powershell
    az monitor app-insights component linked-storage link --resource-group "{resource_group_name}" --app "{application_insights_name}" --storage-account "{storage_account_name}"
    ```
    
    Příklad:
    ```powershell
    az monitor app-insights component linked-storage link --resource-group "byos-test" --app "byos-test-westus2-ai" --storage-account "byosteststoragewestus2"
    ```
    
    Očekávaný výstup:
    ```powershell
    {
      "id": "/subscriptions/{subscription}/resourcegroups/byos-test/providers/microsoft.insights/components/byos-test-westus2-ai/linkedstorageaccounts/serviceprofiler",
      "linkedStorageAccount": "/subscriptions/{subscription}/resourceGroups/byos-test/providers/Microsoft.Storage/storageAccounts/byosteststoragewestus2",
      "name": "serviceprofiler",
      "resourceGroup": "byos-test",
      "type": "microsoft.insights/components/linkedstorageaccounts"
    }
    ```

    > [!NOTE]
    > K provádění aktualizací v propojených účtech úložiště k vašemu Application Insights prostředku si přečtěte [dokumentaci k Application Insights CLI](/cli/azure/ext/application-insights/monitor/app-insights/component/linked-storage).

#### <a name="configure-using-azure-resource-manager-template"></a>Konfigurace pomocí šablony Azure Resource Manager

1. Vytvořte soubor šablony Azure Resource Manager s následujícím obsahem (byos.template.json).
    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "applicationinsights_name": {
          "type": "String"
        },
        "storageaccount_name": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [
        {
          "name": "[concat(parameters('applicationinsights_name'), '/serviceprofiler')]",
          "type": "Microsoft.Insights/components/linkedStorageAccounts",
          "apiVersion": "2020-03-01-preview",
          "properties": {
            "linkedStorageAccount": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageaccount_name'))]"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. Spuštěním následujícího příkazu PowerShellu nasaďte předchozí šablonu (Vytvořte propojený účet úložiště).

    Vzorku
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "{your_resource_name}" -TemplateFile "{local_path_to_arm_template}"
    ```

    Příklad:
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "byos-test" -TemplateFile "D:\Docs\byos.template.json"
    ```

1. Po zobrazení výzvy v konzole PowerShellu zadejte následující parametry:
    
    |           Parametr           |                                Popis                               |
    |-------------------------------|--------------------------------------------------------------------------|
    | application_insights_name     | Název prostředku Application Insights, který má povolit BYOS.            |
    | storage_account_name          | Název prostředku účtu úložiště, který budete používat jako BYOS |
    
    Očekávaný výstup:
    ```powershell
    Supply values for the following parameters:
    (Type !? for Help.)
    application_insights_name: byos-test-westus2-ai
    storage_account_name: byosteststoragewestus2
    
    DeploymentName          : byos.template
    ResourceGroupName       : byos-test
    ProvisioningState       : Succeeded
    Timestamp               : 4/16/2020 1:24:57 AM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name                            Type                       Value
                              ==============================  =========================  ==========
                              application_insights_name        String                     byos-test-westus2-ai
                              storage_account_name             String                     byosteststoragewestus2
                              
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

1. Povolte diagnostiku na úrovni kódu (Profiler/ladicí program) na úlohy, které vás zajímají, prostřednictvím Azure Portal. (App Service > Application Insights) _![ Obrázek 2,0](media/profiler-bring-your-own-storage/figure-20.png)_ 
 _Obrázek 2,0_

## <a name="troubleshooting"></a>Řešení potíží
### <a name="template-schema-schema_uri-isnt-supported"></a>Schéma šablony {schema_uri} se nepodporuje.
* Ujistěte se, že `$schema` je vlastnost šablony platná. Musí splňovat následující vzor: `https://schema.management.azure.com/schemas/{schema_version}/deploymentTemplate.json#`
* Ujistěte se, že `schema_version` je šablona v rámci platných hodnot: `2014-04-01-preview, 2015-01-01, 2018-05-01, 2019-04-01, 2019-08-01` .
    Chybová zpráva:
    ```powershell
    New-AzResourceGroupDeployment : 11:53:49 AM - Error: Code=InvalidTemplate; Message=Deployment template validation failed: 'Template schema
    'https://schema.management.azure.com/schemas/2020-01-01/deploymentTemplate.json#' is not supported. Supported versions are
    '2014-04-01-preview,2015-01-01,2018-05-01,2019-04-01,2019-08-01'. Please see https://aka.ms/arm-template for usage details.'.
    ```

### <a name="no-registered-resource-provider-found-for-location-location"></a>Nenašel se žádný registrovaný poskytovatel prostředků pro umístění {Location}.
* Ujistěte se, že `apiVersion` je v prostředku `microsoft.insights/components` `2015-05-01` .
* Ujistěte se, že `apiVersion` je v prostředku `linkedStorageAccount` `2020-03-01-preview` .
    Chybová zpráva:
    ```powershell
    New-AzResourceGroupDeployment : 6:18:03 PM - Resource microsoft.insights/components 'byos-test-westus2-ai' failed with message '{
      "error": {
        "code": "NoRegisteredProviderFound",
        "message": "No registered resource provider found for location 'westus2' and API version '2020-03-01-preview' for type 'components'. The supported api-versions are '2014-04-01,
    2014-08-01, 2014-12-01-preview, 2015-05-01, 2018-05-01-preview'. The supported locations are ', eastus, southcentralus, northeurope, westeurope, southeastasia, westus2, uksouth,
    canadacentral, centralindia, japaneast, australiaeast, koreacentral, francecentral, centralus, eastus2, eastasia, westus, southafricanorth, northcentralus, brazilsouth, switzerlandnorth,
    australiasoutheast'."
      }
    }'
    ```
### <a name="storage-account-location-should-match-ai-component-location"></a>Umístění účtu úložiště by mělo odpovídat umístění komponenty AI.
* Ujistěte se, že umístění prostředku Application Insights je stejné jako účet úložiště.
    Chybová zpráva:
    ```powershell
    New-AzResourceGroupDeployment : 1:01:12 PM - Resource microsoft.insights/components/linkedStorageAccounts 'byos-test-centralus-ai/serviceprofiler' failed with message '{
      "error": {
        "code": "BadRequest",
        "message": "Storage account location should match AI component location",
        "innererror": {
          "trace": [
            "System.ArgumentException"
          ]
        }
      }
    }'
    ```

Obecné řešení potíží s profilerem najdete v [dokumentaci k řešení potíží s profilerem](profiler-troubleshooting.md).

Obecné Snapshot Debugger řešení potíží najdete v dokumentaci k [řešení potíží s Snapshot Debugger](snapshot-debugger-troubleshoot.md). 

## <a name="faqs"></a>Nejčastější dotazy
* Pokud mám povolený Profiler nebo snímek a pak jsem povolil BYOS, budou se moje data migrovat do mého účtu úložiště?
    _Ne, ne._

* Bude BYOS pracovat se šifrováním v klidovém a Customer-Managedm klíči?
    _Ano, BYOS je požadavek na to, aby byl Profiler/ladicí program povolený pomocí Customer-Manager klíčů._

* Bude BYOS pracovat v prostředí, které je izolované od Internetu?
    _Ano. Ve skutečnosti je BYOS požadavek pro izolované síťové scénáře._

* Bude BYOS pracovat, když byly povoleny oba Customer-Managed klíče i soukromé odkazy? 
    _Ano, může to být možné._

* Pokud jsem povolil BYOS, můžu se zpátky pomocí účtů úložiště diagnostické služby ukládat shromážděná data? 
    _Ano, můžete, ale momentálně nepodporujeme migraci dat z vaší BYOS._

* Po povolení BYOS se převezmou všechny související náklady spojené s IT, což jsou úložiště a sítě? 
    _Ano_
