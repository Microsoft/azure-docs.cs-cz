---
title: Řešení potíží s nasazeními
description: Learn how to monitor and troubleshoot Azure Resource Manager template deployments. Shows activity logs and deployment history.
author: mumian
ms.date: 01/15/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 32f3f55df2b87b35672d811c63a21b956a8a4b52
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325361"
---
# <a name="tutorial-troubleshoot-resource-manager-template-deployments"></a>Tutorial: Troubleshoot Resource Manager template deployments

Learn how to troubleshoot Resource Manager template deployment errors. In this tutorial, you set up two errors in a template, and learn how to use the activity logs and deployment history to resolve the issues.

There are two types of errors that are related to template deployment:

- **Validation errors** arise from scenarios that can be determined before deployment. Patří k nim chyby syntaxe v šabloně nebo pokusy o nasazení prostředků, které by překročily kvóty předplatného.
- **Deployment errors** arise from conditions that occur during the deployment process. Patří mezi ně pokusy o přístup k paralelně nasazovaným prostředkům.

Oba typy chyb vrací kód chyby, který můžete použít při řešení potíží s nasazením. Both types of errors appear in the activity log. Chyby ověření se ale nezobrazí v historii nasazení, protože vůbec nedojde k zahájení nasazení.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Create a problematic template
> * Troubleshoot validation errors
> * Řešení potíží s chybami nasazení
> * Vyčištění prostředků

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto článku potřebujete:

* Visual Studio Code with Resource Manager Tools extension. See [Use Visual Studio Code to create Azure Resource Manager templates](./resource-manager-tools-vs-code.md).

## <a name="create-a-problematic-template"></a>Create a problematic template

Open a template called [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) from [Azure QuickStart Templates](https://azure.microsoft.com/resources/templates/), and setup two template issues.

1. V nástroji Visual Studio Code vyberte **File** (Soubor) >**Open File** (Otevřít soubor).
2. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Výběrem **Open** (Otevřít) soubor otevřete.
4. Change the **apiVersion** line to the following line:

    ```json
    "apiVersion1": "2018-07-02",
    ```
    - **apiVersion1** is invalid element name. It is a validation error.
    - The API version shall be "2018-07-01".  It is a deployment error.

5. Vyberte **File** (Soubor) >**Save As** (Uložit jako) a soubor uložte jako **azuredeploy.json** na místní počítač.

## <a name="troubleshoot-the-validation-error"></a>Troubleshoot the validation error

Refer to the [Deploy the template](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) section to deploy the template.

You shall get an error from the shell similar to:

```
New-AzResourceGroupDeployment : 4:29:24 PM - Error: Code=InvalidRequestContent; Message=The request content was invalid and could not be deserialized: 'Could not find member 'apiVersion1' on object of type 'TemplateResource'. Path 'properties.template.resources[0].apiVersion1', line 36, position 24.'.
```

The error message indicates the problem is with **apiVersion1**.

Use Visual Studio Code to correct the problem by changing **apiVersion1** to **apiVersion**, and then save the template.

## <a name="troubleshoot-the-deployment-error"></a>Troubleshoot the deployment error

Refer to the [Deploy the template](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) section to deploy the template.

You shall get an error from the shell similar to:

```
New-AzResourceGroupDeployment : 4:48:50 PM - Resource Microsoft.Storage/storageAccounts 'storeqii7x2rce77dc' failed with message '{
  "error": {
    "code": "NoRegisteredProviderFound",
    "message": "No registered resource provider found for location 'centralus' and API version '2018-07-02' for type 'storageAccounts'. The supported api-versions are '2018-07-01, 2018-03-01-preview, 2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01, 2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'. The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast, japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast, australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth, francecentral'."
  }
}'
```

The deployment error can be found from the Azure portal using the following procedure:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
2. Open the resource group by selecting **Resource groups** and then the resource group name. You shall see **1 Failed** under **Deployment**.

    ![Resource Manager tutorial troubleshoot](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error.png)
3. Select **Error details**.

    ![Resource Manager tutorial troubleshoot](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error-details.png)

    The error message is the same as the one shown earlier:

    ![Resource Manager tutorial troubleshoot](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error-summary.png)

You can also find the error from the activity logs:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
2. Select **Monitor** > **Activity log**.
3. Use the filters to find the log.

    ![Resource Manager tutorial troubleshoot](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-activity-log.png)

Use Visual Studio Code to correct the problem, and then redeploy the template.

For a list of common errors, see [Troubleshoot common Azure deployment errors with Azure Resource Manager](./resource-manager-common-deployment-errors.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků uvidíte celkem šest prostředků.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

In this tutorial, you learned how to troubleshoot Resource Manager template deployment errors.  For more information, see [Troubleshoot common Azure deployment errors with Azure Resource Manager](./resource-manager-common-deployment-errors.md).
