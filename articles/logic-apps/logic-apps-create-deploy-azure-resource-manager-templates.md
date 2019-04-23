---
title: Nasazení aplikací logiky pomocí šablony Azure Resource Manager – Azure Logic Apps
description: Nasazení aplikací logiky pomocí šablony Azure Resource Manageru
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.date: 10/15/2017
ms.openlocfilehash: 21603ff213bc8babb4145209a76aee0b4150cc12
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997610"
---
# <a name="deploy-logic-apps-with-azure-resource-manager-templates"></a>Nasazení aplikací logiky s využitím šablon Azure Resource Manageru

Jakmile vytvoříte šablonu Azure Resource Manageru pro nasazení aplikace logiky, můžete nasadit šablonu v těchto způsobů:

* [Azure Portal](#portal)
* [Azure PowerShell](#powershell)
* [Azure CLI](#cli)
* [REST API Azure Resource Manageru](../azure-resource-manager/resource-group-template-deploy-rest.md)
* [Kanály Azure DevOps Azure](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Nasadit pomocí webu Azure portal

Šablony aplikace logiky můžete automaticky nasadit do Azure, můžete použít následující **nasadit do Azure** tlačítko, které jste přihlášení k webu Azure portal a vás vyzve k zadání informací o vaší aplikaci logiky. Šablony aplikace logiky nebo parametry lze potom proveďte potřebné změny.

[![Nasazení do Azure](./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Například jste se výzva k zadání těchto informací po přihlášení k webu Azure portal:

* Název předplatného Azure
* Skupinu prostředků, kterou chcete použít
* Umístění aplikace logiky
* Název vaší aplikace logiky
* Test identifikátoru URI
* Přijetí zadané podmínky a ujednání

Další informace najdete v tématu [nasazení prostředků pomocí šablon Azure Resource Manageru a webu Azure portal](../azure-resource-manager/resource-group-template-deploy-portal.md).

## <a name="authorize-oauth-connections"></a>Autorizovat připojení OAuth

Po nasazení aplikace logiky funguje začátku do konce se platné parametry. Však musí i nadále autorizovat připojení OAuth pro generování platným přístupovým tokenem. Pro automatizované nasazení můžete použít skript, který se vyjádří souhlas každé připojení OAuth, jako je například to [ukázkový skript v projektu Githubu LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth). Můžete také povolit připojení OAuth na webu Azure portal nebo v sadě Visual Studio tak, že otevřete aplikaci logiky v návrháři pro Logic Apps.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Nasazení pomocí Azure PowerShellu

K nasazení na konkrétní *skupiny prostředků Azure*, použijte tento příkaz:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

Pokud chcete nasadit na konkrétní předplatné Azure, použijte tento příkaz:

```powershell
New-AzDeployment -Location <location> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](/powershell/module/az.resources/new-azresourcegroupdeployment)
* [`New-AzDeployment`](/powershell/module/az.resources/new-azdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Nasazení s Azure CLI

K nasazení na konkrétní *skupiny prostředků Azure*, použijte tento příkaz:

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Pokud chcete nasadit na konkrétní předplatné Azure, použijte tento příkaz:

```azurecli
az deployment create --location <location> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Další informace najdete v těchto tématech: 

* [Nasazení prostředků pomocí šablon Resource Manageru a rozhraní příkazového řádku Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)
* [`az deployment create`](https://docs.microsoft.com/cli/azure/deployment?view=azure-cli-latest#az-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Nasazení s využitím Azure DevOps

K nasazení šablony aplikace logiky a spravovat prostředí, týmy obvykle používají nástroj, jako [kanály Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) v [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services). Kanály Azure poskytuje [úkol nasazení skupiny prostředků Azure](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) , že můžete přidat do jakékoli sestavení nebo kanál verze.
Pro povolení k nasazení a vytvořit kanál pro vydávání verzí, budete potřebovat Azure Active Directory (AD) [instanční objekt služby](../active-directory/develop/app-objects-and-service-principals.md). Další informace o [pomocí instančních objektů Azure kanály](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure). 

Tady je obecný postup vysoké úrovně pro používání Azure kanály:

1. V kanálech Azure vytvořte prázdný kanálu.

1. Zvolte prostředky, které potřebujete pro kanál, jako jsou šablony aplikace logiky a soubory parametrů šablony, které vygenerujete ručně nebo jako součást procesu sestavení.

1. Pro úlohu agenta, najděte a přidejte **nasazení skupiny prostředků Azure** úloh.

   ![Přidání úkolu "Nasazení skupiny prostředků Azure"](./media/logic-apps-create-deploy-template/add-azure-resource-group-deployment-task.png)

1. Nakonfigurovat [instanční objekt služby](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure). 

1. Přidáte odkazy na šablony aplikace logiky a soubory parametrů šablony.

1. Pokračujte v sestavení kroky v procesu vydávání verzí u jiných prostředí, automatizovaných testů nebo schvalovatelů podle potřeby.

## <a name="get-support"></a>Získat podporu

Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Monitorování Logic Apps](../logic-apps/logic-apps-monitor-your-logic-apps.md)
