---
title: Vytváření šablon aplikací logiky pro nasazení
description: Naučte se vytvářet Azure Resource Manager šablony pro automatizaci nasazení v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/26/2019
ms.openlocfilehash: 4535e6bf11f8c2abf20b1b323925c3fc3299d362
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90971778"
---
# <a name="create-azure-resource-manager-templates-to-automate-deployment-for-azure-logic-apps"></a>Vytváření šablon Azure Resource Manageru pro automatizaci nasazení pro Azure Logic Apps

Tento článek popisuje způsoby, jak můžete vytvořit [šablonu Azure Resource Manager](../azure-resource-manager/management/overview.md) pro aplikaci logiky, která vám umožní automatizovat vytváření a nasazení aplikace logiky. Přehled struktury a syntaxe pro šablonu, která obsahuje definici pracovního postupu a další prostředky nezbytné pro nasazení, najdete v tématu [Přehled: Automatizace nasazení pro Logic Apps pomocí šablon Azure Resource Manager](logic-apps-azure-resource-manager-templates-overview.md).

Azure Logic Apps poskytuje [předem vytvořenou šablonu Azure Resource Manager aplikace logiky](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) , kterou můžete použít, nejen pro vytváření aplikací logiky, ale také k definování prostředků a parametrů, které se mají použít pro nasazení. Tuto šablonu můžete použít pro vlastní obchodní scénáře nebo šablonu přizpůsobit, aby splňovala vaše požadavky.

> [!IMPORTANT]
> Zajistěte, aby připojení ve vaší šabloně používala stejnou skupinu prostředků Azure a umístění jako vaše aplikace logiky.

Další informace o Azure Resource Manager šablonách najdete v těchto tématech:

* [Azure Resource Manager struktura a syntaxe šablony](../azure-resource-manager/templates/template-syntax.md)
* [Vytváření šablon Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Vývoj šablon Azure Resource Manageru pro konzistenci cloudu](../azure-resource-manager/templates/templates-cloud-consistency.md)

<a name="visual-studio"></a>

## <a name="create-templates-with-visual-studio"></a>Vytváření šablon pomocí sady Visual Studio

Pro nejjednodušší způsob, jak vytvořit platné šablony aplikace logiky, které jsou většinou připravené k nasazení, použijte Visual Studio (edice Community nebo vyšší) a nástroje Azure Logic Apps pro Visual Studio. Pak můžete [vytvořit aplikaci logiky v aplikaci Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) nebo [Najít a stáhnout existující aplikaci logiky z Azure Portal do sady Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

Stažením aplikace logiky získáte šablonu, která obsahuje definice pro vaši aplikaci logiky a další prostředky, například připojení. Šablona také *parameterizes*nebo definuje parametry pro, hodnoty používané k nasazení aplikace logiky a dalších prostředků. Hodnoty pro tyto parametry můžete zadat v samostatném souboru parametrů. Tímto způsobem můžete tyto hodnoty snadněji změnit v závislosti na potřebách nasazení. Další informace najdete v těchto tématech:

* [Vytváření aplikací logiky pomocí sady Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Správa aplikací logiky s využitím sady Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)

<a name="azure-powershell"></a>

## <a name="create-templates-with-azure-powershell"></a>Vytváření šablon pomocí Azure PowerShell

Správce prostředků šablony můžete vytvořit pomocí Azure PowerShell s [modulem LogicAppTemplate](https://github.com/jeffhollan/LogicAppTemplateCreator). Tento Open source modul nejprve vyhodnotí vaši aplikaci logiky a všechna připojení, která používá aplikace logiky. Modul pak vygeneruje šablony prostředků s potřebnými parametry pro nasazení.

Předpokládejme například, že máte aplikaci logiky, která přijímá zprávu z fronty Azure Service Bus a odesílá data do Azure SQL Database. Modul zachovává veškerou logiku Orchestrace a parameterizes připojovací řetězce SQL a Service Bus, takže můžete tyto hodnoty zadat a změnit na základě potřeb nasazení.

Tyto ukázky ukazují, jak vytvořit a nasadit Logic Apps pomocí Azure Resource Manager šablon, Azure Pipelines v Azure DevOps a Azure PowerShell:

* [Ukázka: připojení k Azure Service Bus frontám z Azure Logic Apps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Ukázka: připojení k Azure Storage účtům z Azure Logic Apps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Ukázka: nastavte akci aplikace Function App pro Azure Logic Apps](/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Ukázka: připojení k účtu pro integraci z Azure Logic Apps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

### <a name="install-powershell-modules"></a>Nainstalovat moduly PowerShellu

1. Pokud jste to ještě neudělali, nainstalujte [Azure PowerShell](/powershell/azure/install-az-ps).

1. Nejjednodušší způsob, jak nainstalovat modul LogicAppTemplate z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/LogicAppTemplate), získáte spuštěním tohoto příkazu:

   ```powershell
   Install-Module -Name LogicAppTemplate
   ```

   Pokud chcete aktualizovat na nejnovější verzi, spusťte tento příkaz:

   ```powershell
   Update-Module -Name LogicAppTemplate
   ```

Pokud chcete nainstalovat ručně, postupujte podle kroků v části GitHub pro [Tvůrce šablon aplikace logiky](https://github.com/jeffhollan/LogicAppTemplateCreator).

### <a name="install-azure-resource-manager-client"></a>Instalace klienta Azure Resource Manager

Aby modul LogicAppTemplate fungoval s jakýmkoli přístupovým tokenem tenanta Azure a předplatným, nainstalujte [nástroj Azure Resource Manager Client](https://github.com/projectkudu/ARMClient), což je jednoduchý nástroj příkazového řádku, který volá rozhraní API Azure Resource Manager.

Když `Get-LogicAppTemplate` příkaz spustíte s tímto nástrojem, příkaz nejprve získá přístupový token prostřednictvím nástroje ARMClient, předává token do skriptu PowerShellu a vytvoří šablonu jako soubor JSON. Další informace o tomto nástroji najdete v tomto [článku o nástroji Azure Resource Manager Client](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html).

### <a name="generate-template-with-powershell"></a>Generování šablony pomocí PowerShellu

Pokud chcete vygenerovat šablonu po instalaci modulu LogicAppTemplate a rozhraní příkazového [řádku Azure CLI](/cli/azure/), spusťte tento příkaz PowerShellu:

```powershell
$parameters = @{
    Token = (az account get-access-token | ConvertFrom-Json).accessToken
    LogicApp = '<logic-app-name>'
    ResourceGroup = '<Azure-resource-group-name>'
    SubscriptionId = $SubscriptionId
    Verbose = $true
}

Get-LogicAppTemplate @parameters | Out-File C:\template.json
```

Pokud chcete postupovat podle doporučení pro potrubí v tokenu z [nástroje Azure Resource Manager Client](https://github.com/projectkudu/ARMClient), spusťte tento příkaz místo toho, kde `$SubscriptionId` je vaše ID předplatného Azure:

```powershell
$parameters = @{
    LogicApp = '<logic-app-name>'
    ResourceGroup = '<Azure-resource-group-name>'
    SubscriptionId = $SubscriptionId
    Verbose = $true
}

armclient token $SubscriptionId | Get-LogicAppTemplate @parameters | Out-File C:\template.json
```

Po extrakci pak můžete vytvořit soubor parametrů z šablony spuštěním tohoto příkazu:

```powershell
Get-ParameterTemplate -TemplateFile $filename | Out-File '<parameters-file-name>.json'
```

Pro extrakci pomocí Azure Key Vault odkazů (pouze statické) spusťte tento příkaz:

```powershell
Get-ParameterTemplate -TemplateFile $filename -KeyVault Static | Out-File $fileNameParameter
```

| Parametry | Povinné | Popis |
|------------|----------|-------------|
| TemplateFile | Yes | Cesta k souboru šablony |
| KeyVault | No | Výčet, který popisuje, jak zpracovat možné hodnoty trezoru klíčů. Výchozí formát je `None`. |
||||

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nasazování šablon pro aplikace logiky](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
