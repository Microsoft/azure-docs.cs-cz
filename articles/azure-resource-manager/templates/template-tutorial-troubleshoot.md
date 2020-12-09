---
title: Řešení potíží s nasazeními
description: Naučte se monitorovat a řešit potíže s nasazením šablony Azure Resource Manager (šablony ARM). Zobrazuje protokoly aktivit a historii nasazení.
author: mumian
ms.date: 01/15/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7a44edc7cd09709f14415fa0a92e63558001d46d
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928524"
---
# <a name="tutorial-troubleshoot-arm-template-deployments"></a>Kurz: řešení potíží s nasazeními šablon ARM

Naučte se řešit potíže s nasazením šablony Azure Resource Manager (šablony ARM). V tomto kurzu nastavíte dvě chyby v šabloně a naučíte se, jak pomocí protokolů aktivit a historie nasazení tyto problémy vyřešit.

Existují dva typy chyb, které souvisejí s nasazením šablony:

- **Chyby ověřování** se projeví ve scénářích, které lze určit před nasazením. Patří k nim chyby syntaxe v šabloně nebo pokusy o nasazení prostředků, které by překročily kvóty předplatného.
- **Chyby nasazení** nastávají z podmínek, ke kterým dojde během procesu nasazení. Patří mezi ně pokusy o přístup k paralelně nasazovaným prostředkům.

Oba typy chyb vrací kód chyby, který můžete použít při řešení potíží s nasazením. Oba typy chyb se zobrazí v protokolu aktivit. Chyby ověření se ale nezobrazí v historii nasazení, protože vůbec nedojde k zahájení nasazení.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> - Vytvoření problematické šablony
> - Řešení chyb při ověřování
> - Řešení potíží s chybami nasazení
> - Vyčištění prostředků

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto článku potřebujete:

- Visual Studio Code s rozšířením nástrojů Správce prostředků Tools. Další informace najdete v tématu [rychlý Start: vytvoření šablon ARM pomocí Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

## <a name="create-a-problematic-template"></a>Vytvoření problematické šablony

Otevřete šablonu s názvem [Vytvoření standardního účtu úložiště](https://azure.microsoft.com/resources/templates/101-storage-account-create/) ze [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/)a nastavte dvě problémy se šablonou.

1. Z Visual Studio Code vyberte **soubor** > **otevřít soubor**.
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

5. Vyberte **soubor** > **Uložit jako** a uložte soubor jako **azuredeploy.js** do svého místního počítače.

## <a name="troubleshoot-the-validation-error"></a>Řešení chyby ověřování

Chcete-li nasadit šablonu, přečtěte si část [nasazení šablony](template-tutorial-create-multiple-instances.md#deploy-the-template) .

Z prostředí se zobrazí chyba, která bude vypadat přibližně takto:

```
New-AzResourceGroupDeployment : 4:29:24 PM - Error: Code=InvalidRequestContent; Message=The request content was invalid and could not be deserialized: 'Could not find member 'apiVersion1' on object of type 'TemplateResource'. Path 'properties.template.resources[0].apiVersion1', line 36, position 24.'.
```

Chybová zpráva indikuje, že se jedná o problém s **apiVersion1**.

K opravě problému použijte Visual Studio Code změňte **apiVersion1** na **apiVersion** a pak šablonu uložte.

## <a name="troubleshoot-the-deployment-error"></a>Řešení potíží s chybami nasazení

Chcete-li nasadit šablonu, přečtěte si část [nasazení šablony](template-tutorial-create-multiple-instances.md#deploy-the-template) .

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

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
2. Otevřete skupinu prostředků tak, že vyberete **skupiny prostředků** a potom název skupiny prostředků. V rámci **nasazení** se zobrazí **1 chyba** .

    ![Snímek obrazovky, který zvýrazní neúspěšné nasazení.](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error.png)
3. Vyberte **Podrobnosti o chybě**.

    ![Snímek obrazovky, který zvýrazní odkaz Podrobnosti o chybě.](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error-details.png)

    Chybová zpráva je stejná jako ta, která se zobrazila dříve:

    ![Snímek obrazovky zobrazující podrobnosti o chybě](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error-summary.png)

Chybu můžete najít také v protokolech aktivit:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
2. Vyberte **monitorování**  >  **protokolu aktivit**.
3. K vyhledání protokolu použijte filtry.

    ![Řešení potíží s Správce prostředků kurzu](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-activity-log.png)

Opravte problém pomocí Visual Studio Code a pak šablonu znovu nasaďte.

Seznam běžných chyb najdete v tématu [řešení běžných chyb při nasazení Azure pomocí Azure Resource Manager](common-deployment-errors.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků uvidíte celkem šest prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak řešit chyby nasazení šablon ARM.  Další informace najdete v tématu [řešení běžných chyb při nasazení Azure pomocí Azure Resource Manager](common-deployment-errors.md).
