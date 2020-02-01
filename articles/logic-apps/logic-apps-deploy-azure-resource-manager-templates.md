---
title: Nasazování šablon pro aplikace logiky
description: Naučte se, jak nasadit šablony Azure Resource Manager vytvořené pro Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/01/2019
ms.openlocfilehash: 662f8c2a4184e858d33dc40effed363ef3ec17e5
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76904956"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Nasazení šablon Azure Resource Manager pro Azure Logic Apps

Po vytvoření šablony Azure Resource Manager pro aplikaci logiky můžete šablonu nasadit následujícími způsoby:

* [Azure Portal](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [Azure CLI](#cli)
* [Azure Resource Manager REST API](../azure-resource-manager/templates/deploy-rest.md)
* [Azure Pipelines Azure DevOps](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Nasazení prostřednictvím Azure Portal

K automatickému nasazení šablony aplikace logiky do Azure můžete vybrat následující tlačítko **nasadit do Azure** , které vás přihlásí k Azure Portal a zobrazí výzvu k zadání informací o vaší aplikaci logiky. V šabloně nebo parametrech aplikace logiky pak můžete provést všechny nezbytné změny.

[![Nasazení do Azure](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Například po přihlášení k Azure Portal budete vyzváni k zadání následujících informací:

* Název předplatného Azure
* Skupina prostředků, kterou chcete použít
* Umístění aplikace logiky
* Název vaší aplikace logiky
* Identifikátor URI testu
* Přijetí zadaných podmínek a ujednání

Další informace najdete v těchto tématech:

* [Přehled: Automatizace nasazení pro Logic Apps pomocí šablon Azure Resource Manager](logic-apps-azure-resource-manager-templates-overview.md)
* [Nasazení prostředků pomocí šablon Azure Resource Manager a Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>Nasazení pomocí sady Visual Studio

Pokud chcete nasadit šablonu aplikace logiky z projektu skupiny prostředků Azure, který jste vytvořili pomocí sady Visual Studio, postupujte podle těchto [kroků a ručně Nasaďte aplikaci logiky](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) do Azure.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Nasazení pomocí Azure PowerShellu

K nasazení do konkrétní *skupiny prostředků Azure*použijte tento příkaz:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Další informace najdete v těchto tématech:

* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Nasazení pomocí rozhraní příkazového řádku Azure

K nasazení do konkrétní *skupiny prostředků Azure*použijte tento příkaz:

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Další informace najdete v těchto tématech:

* [Nasazení prostředků pomocí šablon Resource Manageru a rozhraní příkazového řádku Azure](../azure-resource-manager/templates/deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Nasazení s využitím Azure DevOps

Pokud chcete nasadit šablony aplikací logiky a spravovat prostředí, týmy běžně používají nástroj, jako je například [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) v [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services). Azure Pipelines poskytuje [úlohu nasazení skupiny prostředků Azure](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) , kterou můžete přidat do libovolného kanálu sestavení nebo vydání. Pro autorizaci k nasazení a generování kanálu vydání budete potřebovat také [instanční objekt](../active-directory/develop/app-objects-and-service-principals.md)Azure Active Directory (AD). Přečtěte si další informace o [používání instančních objektů pomocí Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure).

Další informace o průběžné integraci a průběžném nasazování (CI/CD) pro šablony Azure Resource Manager s Azure Pipelines najdete v těchto tématech a ukázkách:

* [Integrace šablon Správce prostředků s Azure Pipelines](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [Kurz: průběžná integrace pro šablony Azure Resource Manager s Azure Pipelines](../azure-resource-manager/templates/template-tutorial-use-azure-pipelines.md)
* [Ukázka: připojení k Azure Service Bus frontám z Azure Logic Apps a nasazení pomocí Azure Pipelines ve službě Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Ukázka: připojení k Azure Storage účtům z Azure Logic Apps a nasazení pomocí Azure Pipelines ve službě Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Ukázka: nastavení akce aplikace funkcí pro Azure Logic Apps a nasazení pomocí Azure Pipelines ve službě Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Ukázka: připojení k účtu pro integraci z Azure Logic Apps a nasazení pomocí Azure Pipelines ve službě Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

Tady jsou obecné kroky vysoké úrovně pro použití Azure Pipelines:

1. V Azure Pipelines vytvořte prázdný kanál.

1. Vyberte prostředky, které pro kanál potřebujete, například šablonu aplikace logiky a soubory parametrů šablony, které vygenerujete ručně nebo jako součást procesu sestavení.

1. Pro úlohu agenta Najděte a přidejte úlohu **nasazení skupiny prostředků Azure** .

   ![Přidat úlohu nasazení skupiny prostředků Azure](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. Nakonfigurujte pomocí [instančního objektu](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure).

1. Přidejte odkazy na šablonu aplikace logiky a soubory parametrů šablony.

1. Pokračujte v sestavení kroky v procesu vydávání verzí u jiných prostředí, automatizovaných testů nebo schvalovatelů podle potřeby.

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>Autorizovat připojení OAuth

Po nasazení aplikace logiky funguje na konci až po platné parametry. Přesto však musíte autorizovat všechna připojení OAuth pro vygenerování platných přístupových tokenů pro [ověřování vašich přihlašovacích údajů](../active-directory/develop/authentication-scenarios.md). Tady je způsob, jak můžete autorizovat připojení OAuth:

* Pro automatizovaná nasazení můžete použít skript, který poskytuje souhlas pro každé připojení OAuth. Tady je ukázkový skript v GitHubu v projektu [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) .

* Pokud chcete ručně autorizovat připojení OAuth, otevřete aplikaci logiky v návrháři aplikace logiky, a to buď v Azure Portal nebo v aplikaci Visual Studio. V Návrháři autorizujte všechna požadovaná připojení.

Pokud k autorizaci připojení používáte [instanční objekt](../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory (Azure AD), zjistěte, jak [zadat parametry instančního objektu v šabloně aplikace logiky](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Monitorování Logic Apps](../logic-apps/monitor-logic-apps.md)
