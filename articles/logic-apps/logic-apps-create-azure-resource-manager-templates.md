---
title: Vytvoření šablon aplikací logiky pro nasazení
description: Zjistěte, jak vytvořit šablony Azure Resource Manageru pro automatizaci nasazení v Aplikacích Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/26/2019
ms.openlocfilehash: 1fdee9a5d90fc065e198d880f9d0dea10804b881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972633"
---
# <a name="create-azure-resource-manager-templates-to-automate-deployment-for-azure-logic-apps"></a>Vytvoření šablon Azure Resource Managerpro automatizaci nasazení pro Azure Logic Apps

Chcete-li vám pomoci automatizovat vytváření a nasazování aplikace logiky, tento článek popisuje způsoby, jak můžete vytvořit [šablonu Azure Resource Manager](../azure-resource-manager/management/overview.md) pro aplikaci logiky. Přehled o struktuře a syntaxi šablony, která obsahuje definici pracovního postupu a další prostředky potřebné pro nasazení, najdete v [tématu Přehled: Automatizace nasazení pro aplikace logiky pomocí šablon Azure Resource Manageru](logic-apps-azure-resource-manager-templates-overview.md).

Azure Logic Apps poskytuje [předem připravenou šablonu aplikace Azure Resource Manager,](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) kterou můžete znovu použít, a to nejen pro vytváření aplikací logiky, ale také k definování prostředků a parametrů, které se mají použít pro nasazení. Tuto šablonu můžete použít pro vlastní obchodní scénáře nebo ji přizpůsobit tak, aby vyhovovala vašim požadavkům.

> [!IMPORTANT]
> Ujistěte se, že připojení ve vaší šabloně používají stejnou skupinu prostředků Azure a umístění jako aplikace logiky.

Další informace o šablonách Azure Resource Manageru najdete v těchto tématech:

* [Struktura a syntaxe šablony Azure Resource Manageru](../azure-resource-manager/templates/template-syntax.md)
* [Autor šablony Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Vývoj šablon Azure Resource Manageru pro konzistenci cloudu](../azure-resource-manager/templates/templates-cloud-consistency.md)

<a name="visual-studio"></a>

## <a name="create-templates-with-visual-studio"></a>Vytváření šablon pomocí sady Visual Studio

Nejjednodušší způsob, jak vytvořit platné parametrizované šablony aplikace logiky, které jsou většinou připravené pro nasazení, použijte Visual Studio (free Community Edition nebo vyšší) a Nástroje Azure Logic Apps pro Visual Studio. Potom můžete buď [vytvořit aplikaci logiky ve Visual Studiu,](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) nebo [najít a stáhnout existující aplikaci logiky z portálu Azure do Sady Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

Stažením aplikace logiky získáte šablonu, která obsahuje definice pro aplikaci logiky a další prostředky, jako jsou připojení. Šablona také parametrizuje nebo definuje parametry pro hodnoty používané pro nasazení aplikace *logiky*a dalších prostředků. Hodnoty pro tyto parametry můžete zadat v samostatném souboru parametrů. Tímto způsobem můžete snadněji změnit tyto hodnoty na základě vašich potřeb nasazení. Další informace najdete v těchto tématech:

* [Vytváření aplikací logiky pomocí Visual Studia](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Správa aplikací logiky s využitím sady Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)

<a name="azure-powershell"></a>

## <a name="create-templates-with-azure-powershell"></a>Vytváření šablon pomocí Azure PowerShellu

Šablony Správce prostředků můžete vytvořit pomocí Prostředí Azure PowerShell s [modulem LogicAppTemplate](https://github.com/jeffhollan/LogicAppTemplateCreator). Tento open source modul nejprve vyhodnotí vaši aplikaci logiky a všechna připojení, která aplikace logiky používá. Modul pak generuje prostředky šablony s nezbytnými parametry pro nasazení.

Předpokládejme například, že máte aplikaci logiky, která obdrží zprávu z fronty Azure Service Bus a nahraje data do databáze Azure SQL. Modul zachová veškerou logiku orchestrace a parametrizuje připojovací řetězce SQL a Service Bus, takže můžete tyto hodnoty poskytnout a změnit na základě vašich potřeb nasazení.

Tyto ukázky ukazují, jak vytvářet a nasazovat aplikace logiky pomocí šablon Azure Resource Manager, Azure Pipelines v Azure DevOps a Azure PowerShellu:

* [Ukázka: Připojení k frontám Azure Service Bus z Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Ukázka: Připojení k účtům Azure Storage z Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Ukázka: Nastavení akce aplikace funkcí pro aplikace Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Ukázka: Připojení k účtu integrace z Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

### <a name="install-powershell-modules"></a>Instalace modulů PowerShellu

1. Pokud jste to ještě neudělali, nainstalujte [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

1. Nejjednodušší způsob instalace modulu LogicAppTemplate z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/LogicAppTemplate)spusťte tento příkaz:

   ```text
   PS> Install-Module -Name LogicAppTemplate
   ```

   Chcete-li aktualizovat na nejnovější verzi, spusťte tento příkaz:

   ```text
   PS> Update-Module -Name LogicAppTemplate
   ```

Nebo chcete-li nainstalovat ručně, postupujte podle kroků v GitHubu pro [Tvůrce šablony aplikace Logika](https://github.com/jeffhollan/LogicAppTemplateCreator).

### <a name="install-azure-resource-manager-client"></a>Instalace klienta Azure Resource Manager

Aby modul LogicAppTemplate fungoval s libovolným klientem Azure a tokenem přístupu k předplatnému, nainstalujte [klientský nástroj Azure Resource Manager](https://github.com/projectkudu/ARMClient), což je jednoduchý nástroj příkazového řádku, který volá rozhraní API Azure Resource Manager.

Při spuštění `Get-LogicAppTemplate` příkazu pomocí tohoto nástroje příkaz nejprve získá přístupový token prostřednictvím nástroje ARMClient, kanály token do skriptu PowerShell a vytvoří šablonu jako soubor JSON. Další informace o nástroji najdete v tomto [článku o klientském nástroji Azure Resource Manager](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html).

### <a name="generate-template-with-powershell"></a>Generovat šablonu pomocí PowerShellu

Chcete-li vygenerovat šablonu po instalaci modulu LogicAppTemplate a [rozhraní příkazového příkazu Azure ,](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)spusťte tento příkaz Prostředí PowerShell:

```text
PS> Get-LogicAppTemplate -Token (az account get-access-token | ConvertFrom-Json).accessToken -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

Pokud se chcete řídit doporučením pro potrubí v tokenu z [klientského nástroje Azure Resource Manager](https://github.com/projectkudu/ARMClient), spusťte tento příkaz, kde `$SubscriptionId` je id předplatného Azure:

```text
PS> armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

Po extrakci pak můžete vytvořit soubor parametrů ze šablony spuštěním tohoto příkazu:

```text
PS> Get-ParameterTemplate -TemplateFile $filename | Out-File '<parameters-file-name>.json'
```

Pro extrakci s odkazy Azure Key Vault (pouze statické) spusťte tento příkaz:

```text
PS> Get-ParameterTemplate -TemplateFile $filename -KeyVault Static | Out-File $fileNameParameter
```

| Parametry | Požaduje se | Popis |
|------------|----------|-------------|
| Soubor šablony | Ano | Cesta k souboru šablony |
| KeyVault | Ne | Výčtu, který popisuje, jak zpracovat možné hodnoty trezoru klíčů. Výchozí formát je `None`. |
||||

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nasazování šablon pro aplikace logiky](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
