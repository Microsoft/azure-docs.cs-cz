---
title: Řešení potíží s nasazeními
description: Zjistěte, jak monitorovat a řešit potíže s nasazeními šablon Azure Resource Manageru. Zobrazuje protokoly aktivit a historii nasazení.
author: mumian
ms.date: 01/15/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 30b66414e87f642bc72b8723ebff57f2e9009f17
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239236"
---
# <a name="tutorial-troubleshoot-arm-template-deployments"></a>Kurz: Poradce při potížích s nasazením šablony ARM

Zjistěte, jak řešit potíže s chybami nasazení šablony Azure Resource Manager (ARM). V tomto kurzu nastavíte dvě chyby v šabloně a dozvíte se, jak pomocí protokolů aktivit a historie nasazení vyřešit problémy.

Existují dva typy chyb, které souvisejí s nasazením šablony:

- **Chyby ověření** vznikají ze scénářů, které lze určit před nasazením. Patří k nim chyby syntaxe v šabloně nebo pokusy o nasazení prostředků, které by překročily kvóty předplatného.
- **Chyby nasazení** vznikají z podmínek, ke kterým dochází během procesu nasazení. Patří mezi ně pokusy o přístup k paralelně nasazovaným prostředkům.

Oba typy chyb vrací kód chyby, který můžete použít při řešení potíží s nasazením. Oba typy chyb se zobrazí v protokolu aktivit. Chyby ověření se ale nezobrazí v historii nasazení, protože vůbec nedojde k zahájení nasazení.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Vytvoření problematické šablony
> * Poradce při potížích s ověřením
> * Řešení potíží s chybami nasazení
> * Vyčištění prostředků

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* Visual Studio Kód s rozšířením Nástroje Správce prostředků. Viz [Použití kódu Visual Studia k vytvoření šablon ARM](use-vs-code-to-create-template.md).

## <a name="create-a-problematic-template"></a>Vytvoření problematické šablony

Otevřete šablonu s názvem [Vytvoření standardního účtu úložiště](https://azure.microsoft.com/resources/templates/101-storage-account-create/) ze šablon Azure [QuickStart templates](https://azure.microsoft.com/resources/templates/)a nastavte dva problémy se šablonami.

1. V kódu sady Visual Studio vyberte **Soubor**>**otevřít soubor**.
2. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Výběrem **Open** (Otevřít) soubor otevřete.
4. Změňte řádek **apiVersion** na následující řádek:

    ```json
    "apiVersion1": "2018-07-02",
    ```
    - **apiVersion1** je neplatný název prvku. Jedná se o chybu ověření.
    - Verze ROZHRANÍ API bude "2018-07-01".  Jedná se o chybu nasazení.

5. Vyberte **Soubor**>**Uložit jako,** chcete-li soubor uložit jako **azuredeploy.json** do místního počítače.

## <a name="troubleshoot-the-validation-error"></a>Poradce při potížích s chybou ověření

Viz [část Nasazení šablony](quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) k nasazení šablony.

Z granátu se zobrazí chyba podobná:

```
New-AzResourceGroupDeployment : 4:29:24 PM - Error: Code=InvalidRequestContent; Message=The request content was invalid and could not be deserialized: 'Could not find member 'apiVersion1' on object of type 'TemplateResource'. Path 'properties.template.resources[0].apiVersion1', line 36, position 24.'.
```

Chybová zpráva označuje, že problém je s **apiVersion1**.

Pomocí kódu Visual Studio problém opravíte změnou **apiVersion1** na **apiVersion**a potom šablonu uložte.

## <a name="troubleshoot-the-deployment-error"></a>Poradce při potížích s chybou nasazení

Viz [část Nasazení šablony](quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) k nasazení šablony.

Z granátu se zobrazí chyba podobná:

```
New-AzResourceGroupDeployment : 4:48:50 PM - Resource Microsoft.Storage/storageAccounts 'storeqii7x2rce77dc' failed with message '{
  "error": {
    "code": "NoRegisteredProviderFound",
    "message": "No registered resource provider found for location 'centralus' and API version '2018-07-02' for type 'storageAccounts'. The supported api-versions are '2018-07-01, 2018-03-01-preview, 2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01, 2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'. The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast, japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast, australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth, francecentral'."
  }
}'
```

Chybu nasazení najdete na webu Azure Portal pomocí následujícího postupu:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Otevřete skupinu prostředků výběrem **skupin prostředků** a potom názvu skupiny prostředků. Uvidíte **1 Selhání v** rámci **nasazení**.

    ![Poradce při potížích s kurzem správce prostředků](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error.png)
3. Vyberte **podrobnosti o chybě**.

    ![Poradce při potížích s kurzem správce prostředků](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error-details.png)

    Chybová zpráva je stejná jako ta, která byla zobrazena dříve:

    ![Poradce při potížích s kurzem správce prostředků](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error-summary.png)

Chybu můžete také najít v protokolech aktivit:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Vyberte **možnost Sledovat** > **protokol aktivit**.
3. Pomocí filtrů vyhledejte protokol.

    ![Poradce při potížích s kurzem správce prostředků](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-activity-log.png)

K opravě problému použijte kód sady Visual Studio a potom šablonu znovu nasaďte.

Seznam běžných chyb najdete [v tématu Poradce při potížích s běžnými chybami nasazení Azure ve Správci prostředků Azure](common-deployment-errors.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure vyberte **skupinu prostředků** z levé nabídky.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků uvidíte celkem šest prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků.**

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli, jak řešit chyby nasazení šablony ARM.  Další informace najdete [v tématu Poradce při potížích s běžnými chybami nasazení Azure pomocí Správce prostředků Azure](common-deployment-errors.md).
