---
title: Vytvoření automatizace zabezpečení pro konkrétní výstrahy zabezpečení pomocí šablony Azure Resource Manager (šablona ARM)
description: Naučte se, jak vytvořit automatizaci Azure Security Center pro aktivaci aplikace logiky, která se aktivuje konkrétními výstrahami Security Center pomocí šablony Azure Resource Manager (šablona ARM).
services: azure-resource-manager
author: memildin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: memildin
ms.date: 08/20/2020
ms.openlocfilehash: 12b7c86e528af6c174f456add4d29a92239cd01e
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92678018"
---
# <a name="quickstart-create-an-automatic-response-to-a-specific-security-alert-using-an-arm-template"></a>Rychlý Start: Vytvoření automatické odpovědi na konkrétní výstrahu zabezpečení pomocí šablony ARM

V tomto rychlém startu se dozvíte, jak pomocí šablony Azure Resource Manager (šablony ARM) vytvořit automatizaci pracovního postupu, která spustí aplikaci logiky, když Azure Security Center obdrží konkrétní výstrahy zabezpečení.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure** . Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-securitycenter-create-automation-for-alertnamecontains%2fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Seznam rolí a oprávnění potřebných pro práci s funkcí automatizace pracovních postupů v Azure Security Center najdete v tématu [Automatizace pracovního postupu](workflow-automation.md).

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-securitycenter-create-automation-for-alertnamecontains/).

:::code language="json" source="~/quickstart-templates/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json":::

### <a name="relevant-resources"></a>Relevantní prostředky

- [**Microsoft. Security/automations**](/azure/templates/microsoft.security/automations): automatizace, která spustí aplikaci logiky po přijetí výstrahy Azure Security Center, která obsahuje konkrétní řetězec.
- [**Microsoft. Logic/Workflows**](/azure/templates/microsoft.logic/workflows): prázdná aplikace logiky, která se aktivuje.

Další šablony pro rychlý Start Security Center najdete v těchto [komunitách](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Security&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Nasazení šablony

- **PowerShell** :

  ```azurepowershell-interactive
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
  ```

- Rozhraní příkazového **řádku** :

  ```azurecli-interactive
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  az group deployment create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
  ```

- **Portál** :

  [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-securitycenter-create-automation-for-alertnamecontains%2fazuredeploy.json)

  Další informace o této možnosti nasazení najdete v tématu nasazení [šablon z úložiště GitHub pomocí tlačítka nasazení](../azure-resource-manager/templates/deploy-to-azure-button.md).

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Pomocí Azure Portal můžete ověřit, jestli je automatizace pracovního postupu nasazená.

1. Z [Azure Portal](https://portal.azure.com)otevřete **Security Center** .
1. V horním řádku nabídek vyberte ikonu filtru a vyberte konkrétní předplatné, na které jste nasadili novou automatizaci pracovního postupu.
1. Z bočního panelu Security Center otevřete **automatizaci pracovních postupů** a vyhledejte novou automatizaci.
    :::image type="content" source="./media/quickstart-automation-alert/validating-template-run.png" alt-text="Seznam nakonfigurovaných Automation" lightbox="./media/quickstart-automation-alert/validating-template-run.png":::
    >[!TIP]
    > Pokud máte v předplatném mnoho automatizace pracovních postupů, použijte možnost **filtrovat podle názvu** .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte automatizaci pracovního postupu pomocí Azure Portal.

1. Z [Azure Portal](https://portal.azure.com)otevřete **Security Center** .
1. V horním řádku nabídek vyberte ikonu filtru a vyberte konkrétní předplatné, na které jste nasadili novou automatizaci pracovního postupu.
1. Z bočního panelu Security Center otevřete **automatizaci pracovních postupů** a najděte automatizaci, která se má odstranit.
    :::image type="content" source="./media/quickstart-automation-alert/deleting-workflow-automation.png" alt-text="Seznam nakonfigurovaných Automation" lightbox="./media/quickstart-automation-alert/deleting-workflow-automation.png":::
1. Zaškrtněte políčko pro položku, která se má odstranit.
1. Na panelu nástrojů vyberte možnost **Odstranit** .

## <a name="next-steps"></a>Další kroky

Podrobný kurz, který vás provede procesem vytvoření šablony, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Kurz: vytvoření a nasazení první šablony ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
