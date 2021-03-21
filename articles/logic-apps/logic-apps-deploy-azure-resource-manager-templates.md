---
title: Nasazování šablon pro aplikace logiky
description: Naučte se, jak nasadit šablony Azure Resource Manager vytvořené pro Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 08/25/2020
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 5f5db3fd88f04e7fe569cd675936445dcf730288
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97705332"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Nasazování šablon Azure Resource Manageru pro Azure Logic Apps

Po vytvoření šablony Azure Resource Manager pro aplikaci logiky můžete šablonu nasadit následujícími způsoby:

* [Azure Portal](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [Azure CLI](#cli)
* [Rozhraní REST API pro Azure Resource Manager](../azure-resource-manager/templates/deploy-rest.md)
* [Azure DevOps](#azure-pipelines)

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

## <a name="deploy-with-visual-studio"></a>Nasazení s využitím sady Visual Studio

Pokud chcete nasadit šablonu aplikace logiky z projektu skupiny prostředků Azure, který jste vytvořili pomocí sady Visual Studio, postupujte podle těchto [kroků a ručně Nasaďte aplikaci logiky](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) do Azure.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Nasazení pomocí Azure PowerShellu

K nasazení do konkrétní *skupiny prostředků Azure* použijte tento příkaz:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Další informace najdete v těchto tématech:

* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../azure-resource-manager/templates/deploy-powershell.md)
* [`New-AzResourceGroupDeployment`](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Nasazení s Azure CLI

K nasazení do konkrétní *skupiny prostředků Azure* použijte tento příkaz:

```azurecli
az deployment group create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Další informace najdete v těchto tématech:

* [Nasazení prostředků pomocí šablon Resource Manageru a rozhraní příkazového řádku Azure](../azure-resource-manager/templates/deploy-cli.md)
* [`az deployment group create`](/cli/azure/deployment/group#az_deployment_group_create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Nasazení s využitím Azure DevOps

Pokud chcete nasadit šablony aplikací logiky a spravovat prostředí, týmy běžně používají nástroj, jako je například [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) v [Azure DevOps](/azure/devops/user-guide/what-is-azure-devops-services). Azure Pipelines poskytuje [úlohu nasazení skupiny prostředků Azure](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) , kterou můžete přidat do libovolného kanálu sestavení nebo vydání. Pro autorizaci k nasazení a generování kanálu vydání budete potřebovat také [instanční objekt](../active-directory/develop/app-objects-and-service-principals.md)Azure Active Directory (AD). Přečtěte si další informace o [používání instančních objektů pomocí Azure Pipelines](/azure/devops/pipelines/library/connect-to-azure).

Další informace o průběžné integraci a průběžném nasazování (CI/CD) pro šablony Azure Resource Manager s Azure Pipelines najdete v těchto tématech a ukázkách:

* [Integrace šablon Správce prostředků s Azure Pipelines](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [Kurz: průběžná integrace šablon Azure Resource Manager s Azure Pipelines](../azure-resource-manager/templates/deployment-tutorial-pipeline.md)
* [Ukázka: připojení k Azure Service Bus frontám z Azure Logic Apps a nasazení pomocí Azure Pipelines ve službě Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Ukázka: připojení k Azure Storage účtům z Azure Logic Apps a nasazení pomocí Azure Pipelines ve službě Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Ukázka: nastavení akce aplikace funkcí pro Azure Logic Apps a nasazení pomocí Azure Pipelines ve službě Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Ukázka: připojení k účtu pro integraci z Azure Logic Apps a nasazení pomocí Azure Pipelines ve službě Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Ukázka: orchestrace Azure Pipelines pomocí Azure Logic Apps](/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

Tady jsou obecné kroky vysoké úrovně pro použití Azure Pipelines:

1. V Azure Pipelines vytvořte prázdný kanál.

1. Vyberte prostředky, které pro kanál potřebujete, například šablonu aplikace logiky a soubory parametrů šablony, které vygenerujete ručně nebo jako součást procesu sestavení.

1. Pro úlohu agenta Najděte a přidejte úlohu **nasazení skupiny prostředků Azure** .

   ![Přidat úlohu nasazení skupiny prostředků Azure](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. Nakonfigurujte pomocí [instančního objektu](/azure/devops/pipelines/library/connect-to-azure).

1. Přidejte odkazy na šablonu aplikace logiky a soubory parametrů šablony.

1. Pokračujte v postupu sestavení kroků v procesu vydávání verzí pro jakékoli jiné prostředí, automatizovaný test nebo schvalovatele podle potřeby.

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>Autorizovat připojení OAuth

Po nasazení vaše aplikace logiky funguje na konci s platnými parametry, ale pro vygenerování platných přístupových tokenů pro [ověřování vašich přihlašovacích údajů](../active-directory/develop/authentication-vs-authorization.md)je stále nutné autorizovat nebo používat předem vytvořená připojení OAuth. Stačí ale nasadit a ověřit prostředky připojení rozhraní API jenom jednou, což znamená, že je nemusíte zahrnovat do následujících nasazení, pokud nepotřebujete aktualizovat informace o připojení. Pokud používáte kanál průběžné integrace a průběžného nasazování, nasadíte jenom aktualizované Logic Apps prostředky a nemusíte je pokaždé znovu autorizovat.

Tady je několik návrhů pro zpracování autorizačních připojení:

* Předběžně autorizovat a sdílejte prostředky připojení rozhraní API napříč Logic Apps, které jsou ve stejné oblasti. Připojení rozhraní API existují jako prostředky Azure nezávisle na Logic Apps. I když aplikace logiky mají závislosti na prostředcích připojení rozhraní API, prostředky připojení rozhraní API nemají závislosti na Logic Apps a zůstávají i po odstranění závislých aplikací logiky. Logic Apps taky můžou používat připojení rozhraní API, která existují v jiných skupinách prostředků. Návrhář aplikace logiky ale podporuje vytváření připojení rozhraní API jenom ve stejné skupině prostředků jako vaše aplikace logiky.

  > [!NOTE]
  > Pokud zvažujete sdílení připojení rozhraní API, ujistěte se, že vaše řešení dokáže [zvládnout možné problémy s omezením](../logic-apps/handle-throttling-problems-429-errors.md#connector-throttling). K omezování dochází na úrovni připojení, takže opakované použití stejného připojení napříč více Logic Apps může zvýšit potenciál pro problémy s omezením.

* Pokud váš scénář zahrnuje služby a systémy, které vyžadují vícefaktorové ověřování, můžete použít skript prostředí PowerShell k poskytnutí souhlasu pro každé připojení OAuth tak, že spustíte pracovní proces nepřetržité integrace jako běžný uživatelský účet na virtuálním počítači, který má aktivní relace prohlížeče s autorizací a již uděleným souhlasem. Můžete například změnit účel ukázkového skriptu poskytnutého [projektem LogicAppConnectionAuth v úložišti GitHub Logic Apps](https://github.com/logicappsio/LogicAppConnectionAuth).

* Ruční autorizaci připojení OAuth otevřením aplikace logiky v návrháři aplikace logiky, a to buď v Azure Portal nebo v aplikaci Visual Studio.

* Pokud k autorizaci připojení používáte [instanční objekt](../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory (Azure AD), zjistěte, jak [zadat parametry instančního objektu v šabloně aplikace logiky](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Monitorování aplikací logiky](../logic-apps/monitor-logic-apps.md)
