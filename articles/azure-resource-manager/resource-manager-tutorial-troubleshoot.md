---
title: Řešení potíží s nasazení Resource Manager | Dokumentace Microsoftu
description: Zjistěte, jak sledovat a odstraňovat potíže nasazení Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/15/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: c917e37d48f2b26d9a1e4d13b76dde5539ce94ea
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56116573"
---
# <a name="tutorial-troubleshoot-resource-manager-template-deployments"></a>Kurz: Řešení potíží s nasazení šablon Resource Manageru

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zjistěte, jak k řešení chyb nasazení šablony Resource Manageru. V tomto kurzu se nastavit dvě chyby v šabloně a zjistěte, jak při řešení problémů pomocí protokolů aktivit a historie nasazení.

Existují dva typy chyb, které se vztahují k nasazení šablony:

- **Chyby ověření** vzniknou scénáře, které se dá určit před nasazením. Patří mezi ně chyby syntaxe v šabloně nebo pokusu o nasazení prostředků, které by došlo k vaší kvóty předplatného. 
- **Chyby nasazení** vznikají z podmínek, které nastanou během procesu nasazení. Patří mezi ně pokusu o přístup k prostředku, který se nasazuje paralelně.

Oba typy chyb vrátí kód chyby, který použijete k řešení potíží s nasazení. Oba typy chyb se zobrazí v protokolu aktivit. Chyby ověření však nejsou zobrazeny v historii nasazení, protože nasazení nikdy nespustili.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Vytvořit šablonu problematické
> * Řešení chyb ověřování
> * Řešení potíží s chybami nasazení
> * Vyčištění prostředků

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

- [Visual Studio Code](https://code.visualstudio.com/) s [rozšířením Nástroje Resource Manageru](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)

## <a name="create-a-problematic-template"></a>Vytvořit šablonu problematické

Otevřete šablonu s názvem [vytvořit účet úložiště úrovně standard](https://azure.microsoft.com/resources/templates/101-storage-account-create/) z [šablony pro rychlý start Azure](https://azure.microsoft.com/resources/templates/)a nastavit dva problémy se šablonou.

1. V nástroji Visual Studio Code vyberte **File** (Soubor) >**Open File** (Otevřít soubor).
2. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Výběrem **Open** (Otevřít) soubor otevřete.
4. Změnit **apiVersion** řádku na následující řádek:

    ```json
    "apiVersion1": "2018-07-02",
    ```
    - **apiVersion1** je název neplatný element. Jedná se chybu ověření.
    - Verze rozhraní API musí být "2018-07-01".  Je chyba nasazení.

5. Vyberte **File** (Soubor) >**Save As** (Uložit jako) a soubor uložte jako **azuredeploy.json** na místní počítač.

## <a name="troubleshoot-the-validation-error"></a>Vyřešte chybu ověřování

Odkazovat [nasazení šablony](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) části k nasazení šablony.

Měla by se zobrazit chybu z prostředí podobné:

```
New-AzResourceGroupDeployment : 4:29:24 PM - Error: Code=InvalidRequestContent; Message=The request content was invalid and could not be deserialized: 'Could not find member 'apiVersion1' on object of type 'TemplateResource'. Path 'properties.template.resources[0].apiVersion1', line 36, position 24.'.
```

Chybová zpráva znamená, že je problém s **apiVersion1**.

Použití Visual Studio Code, chcete-li opravit problém tak, že změníte **apiVersion1** k **apiVersion**a potom uložte šablonu.

## <a name="troubleshoot-the-deployment-error"></a>Odstranění chyby nasazení

Odkazovat [nasazení šablony](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) části k nasazení šablony.

Měla by se zobrazit chybu z prostředí podobné:

```
New-AzResourceGroupDeployment : 4:48:50 PM - Resource Microsoft.Storage/storageAccounts 'storeqii7x2rce77dc' failed with message '{
  "error": {
    "code": "NoRegisteredProviderFound",
    "message": "No registered resource provider found for location 'centralus' and API version '2018-07-02' for type 'storageAccounts'. The supported api-versions are '2018-07-01, 2018-03-01-preview, 2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01, 2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'. The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast, japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast, australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth, francecentral'."
  }
}'
```

Chyba nasazení najdete na webu Azure Portal následujícím postupem:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Otevřete tak, že vyberete skupinu prostředků **skupiny prostředků** a potom název skupiny prostředků. Měly by se zobrazit **1 neúspěšné** pod **nasazení**.

    ![Řešení potíží s kurzu Resource Manageru](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error.png)
3. Vyberte **podrobnosti o chybě**.

    ![Řešení potíží s kurzu Resource Manageru](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error-details.png)

    Chybová zpráva je stejná jako ta je uvedeno výše:

    ![Řešení potíží s kurzu Resource Manageru](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error-summary.png)

Můžete také vyhledat chyby v protokolech aktivity:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **monitorování** > **protokolu aktivit**.
3. Pomocí filtru vyhledejte v protokolu.

    ![Řešení potíží s kurzu Resource Manageru](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-activity-log.png)

Použití Visual Studio Code k odstranění problému a znovu nasaďte šablonu.

Seznam běžných chyb, naleznete v tématu [řešit běžné chyby nasazení v Azure pomocí Azure Resource Manageru](./resource-manager-common-deployment-errors.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků uvidíte celkem šest prostředků.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak řešit chyby nasazení šablony Resource Manageru.  Další informace najdete v tématu [řešit běžné chyby nasazení v Azure pomocí Azure Resource Manageru](./resource-manager-common-deployment-errors.md).
