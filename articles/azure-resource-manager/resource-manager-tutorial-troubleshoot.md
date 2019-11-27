---
title: Řešení potíží s nasazeními
description: Naučte se monitorovat a řešit potíže s nasazeními šablon Azure Resource Manager. Zobrazuje protokoly aktivit a historii nasazení.
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
# <a name="tutorial-troubleshoot-resource-manager-template-deployments"></a>Kurz: řešení potíží s nasazením Správce prostředků šablon

Naučte se řešit potíže s nasazením Správce prostředků šablon. V tomto kurzu nastavíte dvě chyby v šabloně a naučíte se, jak pomocí protokolů aktivit a historie nasazení tyto problémy vyřešit.

Existují dva typy chyb, které souvisejí s nasazením šablony:

- **Chyby ověřování** se projeví ve scénářích, které lze určit před nasazením. Patří k nim chyby syntaxe v šabloně nebo pokusy o nasazení prostředků, které by překročily kvóty předplatného.
- **Chyby nasazení** nastávají z podmínek, ke kterým dojde během procesu nasazení. Patří mezi ně pokusy o přístup k paralelně nasazovaným prostředkům.

Oba typy chyb vrací kód chyby, který můžete použít při řešení potíží s nasazením. V protokolu aktivit se zobrazí oba typy chyb. Chyby ověření se ale nezobrazí v historii nasazení, protože vůbec nedojde k zahájení nasazení.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Vytvoření problematické šablony
> * Řešení chyb při ověřování
> * Řešení potíží s chybami nasazení
> * Vyčištění prostředků

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* Visual Studio Code s rozšířením nástrojů Správce prostředků Tools. Pokud [chcete vytvořit Azure Resource Manager šablony](./resource-manager-tools-vs-code.md), přečtěte si téma použití Visual Studio Code.

## <a name="create-a-problematic-template"></a>Vytvoření problematické šablony

Otevřete šablonu s názvem [Vytvoření standardního účtu úložiště](https://azure.microsoft.com/resources/templates/101-storage-account-create/) ze [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/)a nastavte dvě problémy se šablonou.

1. V nástroji Visual Studio Code vyberte **File** (Soubor) >**Open File** (Otevřít soubor).
2. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Výběrem **Open** (Otevřít) soubor otevřete.
4. Změňte **apiVersion** čáru na následující řádek:

    ```json
    "apiVersion1": "2018-07-02",
    ```
    - **apiVersion1** je neplatný název elementu. Jedná se o chybu ověřování.
    - Verze rozhraní API musí být "2018-07-01".  Jedná se o chybu nasazení.

5. Vyberte **File** (Soubor) >**Save As** (Uložit jako) a soubor uložte jako **azuredeploy.json** na místní počítač.

## <a name="troubleshoot-the-validation-error"></a>Řešení chyby ověřování

Chcete-li nasadit šablonu, přečtěte si část [nasazení šablony](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) .

Z prostředí se zobrazí chyba, která bude vypadat přibližně takto:

```
New-AzResourceGroupDeployment : 4:29:24 PM - Error: Code=InvalidRequestContent; Message=The request content was invalid and could not be deserialized: 'Could not find member 'apiVersion1' on object of type 'TemplateResource'. Path 'properties.template.resources[0].apiVersion1', line 36, position 24.'.
```

Chybová zpráva indikuje, že se jedná o problém s **apiVersion1**.

K opravě problému použijte Visual Studio Code změňte **apiVersion1** na **apiVersion**a pak šablonu uložte.

## <a name="troubleshoot-the-deployment-error"></a>Řešení potíží s chybami nasazení

Chcete-li nasadit šablonu, přečtěte si část [nasazení šablony](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) .

Z prostředí se zobrazí chyba, která bude vypadat přibližně takto:

```
New-AzResourceGroupDeployment : 4:48:50 PM - Resource Microsoft.Storage/storageAccounts 'storeqii7x2rce77dc' failed with message '{
  "error": {
    "code": "NoRegisteredProviderFound",
    "message": "No registered resource provider found for location 'centralus' and API version '2018-07-02' for type 'storageAccounts'. The supported api-versions are '2018-07-01, 2018-03-01-preview, 2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01, 2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'. The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast, japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast, australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth, francecentral'."
  }
}'
```

Chybu nasazení najdete v Azure Portal pomocí následujícího postupu:

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com).
2. Otevřete skupinu prostředků tak, že vyberete **skupiny prostředků** a potom název skupiny prostředků. V rámci **nasazení**se zobrazí **1 chyba** .

    ![Řešení potíží s Správce prostředků kurzu](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error.png)
3. Vyberte **Podrobnosti o chybě**.

    ![Řešení potíží s Správce prostředků kurzu](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error-details.png)

    Chybová zpráva je stejná jako ta, která se zobrazila dříve:

    ![Řešení potíží s Správce prostředků kurzu](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error-summary.png)

Chybu můžete najít také v protokolech aktivit:

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com).
2. Vyberte **monitorování** > **protokolu aktivit**.
3. K vyhledání protokolu použijte filtry.

    ![Řešení potíží s Správce prostředků kurzu](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-activity-log.png)

Opravte problém pomocí Visual Studio Code a pak šablonu znovu nasaďte.

Seznam běžných chyb najdete v tématu [řešení běžných chyb při nasazení Azure pomocí Azure Resource Manager](./resource-manager-common-deployment-errors.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků uvidíte celkem šest prostředků.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak řešit problémy s chybami při nasazování šablon Správce prostředků.  Další informace najdete v tématu [řešení běžných chyb při nasazení Azure pomocí Azure Resource Manager](./resource-manager-common-deployment-errors.md).
