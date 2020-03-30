---
title: Nasazování šablon pro aplikace logiky
description: Zjistěte, jak nasadit šablony Azure Resource Manageru vytvořené pro Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/01/2019
ms.openlocfilehash: 95bfa7b1918da09e4f5913eeb2b57c290b093efe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270443"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Nasazení šablon Azure Resource Manageru pro aplikace Azure Logic Apps

Po vytvoření šablony Azure Resource Manager pro aplikaci logiky, můžete nasadit šablonu těmito způsoby:

* [Portál Azure](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [Azure CLI](#cli)
* [Rozhraní REST správce prostředků Azure](../azure-resource-manager/templates/deploy-rest.md)
* [Azure DevOps](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Nasazení prostřednictvím portálu Azure

Pokud chcete automaticky nasadit šablonu aplikace logiky do Azure, můžete zvolit následující tlačítko **Nasazení do Azure,** které vás přihlásí na portál Azure a vyzve vás k zadání informací o vaší aplikaci logiky. Potom můžete provést všechny nezbytné změny šablony nebo parametrů aplikace logiky.

[![Nasazení do Azure](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Po přihlášení k webu Azure Portal se například zobrazí výzva k zadání následujících informací:

* Název předplatného Azure
* Skupina prostředků, kterou chcete použít
* Umístění aplikace logiky
* Název vaší aplikace logiky
* Testovací identifikátor URI
* Přijetí stanovených podmínek

Další informace najdete v těchto tématech:

* [Přehled: Automatizace nasazení pro aplikace logiky pomocí šablon Azure Resource Manageru](logic-apps-azure-resource-manager-templates-overview.md)
* [Nasazení prostředků pomocí šablon Azure Resource Manageru a portálu Azure](../azure-resource-manager/templates/deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>Nasazení s využitím sady Visual Studio

Pokud chcete nasadit šablonu aplikace logiky z projektu Skupiny prostředků Azure, který jste vytvořili pomocí Visual Studia, [postupujte takto a ručně nasadit aplikaci logiky](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) do Azure.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Nasazení pomocí Azure PowerShellu

Chcete-li nasadit do určité *skupiny prostředků Azure*, použijte následující příkaz:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Další informace najdete v těchto tématech:

* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Nasazení s Azure CLI

Chcete-li nasadit do určité *skupiny prostředků Azure*, použijte následující příkaz:

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Další informace najdete v těchto tématech:

* [Nasazení prostředků pomocí šablon Resource Manageru a rozhraní příkazového řádku Azure](../azure-resource-manager/templates/deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Nasazení pomocí Azure DevOps

K nasazení šablon aplikací logiky a správy prostředí týmy běžně používají nástroj, jako jsou [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) v [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services). Azure Pipelines poskytuje [úlohu nasazení skupiny prostředků Azure,](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) kterou můžete přidat do libovolného kanálu sestavení nebo vydání. Chcete-li oprávnění k nasazení a generování kanálu vydání, budete také potřebovat [instanční objekt služby](../active-directory/develop/app-objects-and-service-principals.md)Azure Active Directory (AD). Další informace o [používání instančních objektů s Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure).

Další informace o průběžné integraci a průběžném nasazování (CI/CD) pro šablony Azure Resource Manageru s Azure Pipelines najdete v těchto tématech a ukázkách:

* [Integrace šablon Správce prostředků s Azure Pipelines](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [Kurz: Průběžná integrace šablon Azure Resource Manageru s Azure Pipelines](../azure-resource-manager/templates/template-tutorial-use-azure-pipelines.md)
* [Ukázka: Připojení k frontám Azure Service Bus z Azure Logic Apps a nasazení s Azure Pipelines v Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Ukázka: Připojení k účtům Azure Storage z Azure Logic Apps a nasazení s Azure Pipelines v Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Ukázka: Nastavení akce aplikace funkce pro Azure Logic Apps a nasazení s Azure Pipelines v Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Ukázka: Připojení k účtu integrace z Azure Logic Apps a nasazení s Azure Pipelines v Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Ukázka: Orchestrace Azure Pipelines pomocí Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

Tady jsou obecné kroky na vysoké úrovni pro používání Azure Pipelines:

1. V Azure Pipelines vytvořte prázdný kanál.

1. Vyberte prostředky, které potřebujete pro kanál, jako je například šablona aplikace logiky a soubory parametrů šablony, které generujete ručně nebo jako součást procesu sestavení.

1. Pro úlohu agenta najděte a přidejte úlohu **nasazení skupiny prostředků Azure.**

   ![Přidání úlohy Nasazení skupiny prostředků Azure](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. Konfigurace s [instančním objektem](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure).

1. Přidejte odkazy na soubory šablony aplikace logiky a parametrů šablony.

1. Pokračujte v vytváření kroků v procesu vydávání pro jakékoli jiné prostředí, automatizovaný test nebo schvalovatele podle potřeby.

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>Autorizace připojení OAuth

Po nasazení aplikace logiky funguje od konce do konce s platnými parametry. Je však nutné ještě autorizovat všechna připojení OAuth generovat platné přístupové tokeny pro [ověřování vašich pověření](../active-directory/develop/authentication-scenarios.md). Zde jsou způsoby, jak můžete autorizovat připojení OAuth:

* Pro automatická nasazení můžete použít skript, který poskytuje souhlas pro každé připojení OAuth. Tady je ukázkový skript v GitHubu v projektu [LogicAppConnectionAuth.](https://github.com/logicappsio/LogicAppConnectionAuth)

* Pokud chcete ručně autorizovat připojení OAuth, otevřete aplikaci logiky v Návrháři aplikací logiky, buď na webu Azure Portal, nebo ve Visual Studiu. V návrháři autorizujte všechna požadovaná připojení.

Pokud místo toho k autorizaci připojení použijete [hlavní objekt služby](../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory (Azure AD), přečtěte si, jak [zadat parametry hlavního povinného adresáře v šabloně aplikace logiky](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Monitorování aplikací logiky](../logic-apps/monitor-logic-apps.md)
