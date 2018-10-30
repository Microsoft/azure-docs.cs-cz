---
title: Používání podmínek v šablonách Azure Resource Manageru | Microsoft Docs
description: Zjistěte, jak nasazovat prostředky Azure na základě podmínek.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/18/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 552b39c520396942fa81f963c0cfa1c8c7b47db4
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49456962"
---
# <a name="tutorial-use-condition-in-azure-resource-manager-templates"></a>Kurz: Používání podmínek v šablonách Azure Resource Manageru

Zjistěte, jak nasazovat prostředky Azure na základě podmínek. 

Scénář v tomto kurzu je podobný scénáři v [kurzu vytváření šablon Azure Resource Manageru se závislými prostředky](./resource-manager-tutorial-create-templates-with-dependent-resources.md). V tomto kurzu vytvoříte virtuální počítač, virtuální síť a několik dalších závislých prostředků včetně účtu úložiště. Místo nutnosti pokaždé vytvářet nový účet úložiště umožníte uživatelům vybrat si mezi vytvořením nového účtu úložiště a použitím existujícího účtu úložiště. Docílíte toho definováním dalšího parametru. Pokud hodnota tohoto parametru bude „new“, vytvoří se nový účet úložiště.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Otevření šablony rychlého startu
> * Úprava šablony
> * Nasazení šablony
> * Vyčištění prostředků

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* [Visual Studio Code](https://code.visualstudio.com/) s [rozšířením Nástroje Resource Manageru](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)

## <a name="open-a-quickstart-template"></a>Otevření šablony pro rychlý start

Šablony pro rychlý start Azure slouží jako úložiště šablon Resource Manageru. Místo vytvoření šablony úplně od začátku si můžete najít ukázkovou šablonu a přizpůsobit ji. Šablona používaná v tomto kurzu má název [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Nasazení jednoduchého virtuálního počítače s Windows).

1. V nástroji Visual Studio Code vyberte **File** (Soubor) >**Open File** (Otevřít soubor).
2. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Výběrem **Open** (Otevřít) soubor otevřete.
4. Vyberte **File** (Soubor) >**Save As** (Uložit jako) a soubor uložte na místní počítač pod názvem **azuredeploy.json**.

## <a name="modify-the-template"></a>Úprava šablony

Ve stávající šabloně proveďte dvě změny:

* Přidejte parametr názvu účtu úložiště. Uživatelé můžou zadat buď název nového účtu úložiště, nebo název existujícího účtu úložiště.
* Přidejte nový parametr **newOrExisting**. Nasazení tento parametr použije k určení, jestli se má vytvořit nový účet úložiště nebo použít existující.

1. Ve Visual Studio Code otevřete soubor **azuredeploy.json**.
2. V celé šabloně nahraďte **variables('storageAccountName')** za **parameters('storageAccountName')**.  V šabloně se **variables('storageAccountName')** vyskytuje třikrát.
3. Odeberte definici následující proměnné:

    ```json
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",
    ```
4. Přidejte do šablony následující dva parametry:

    ```json
    "storageAccountName": {
      "type": "string"
    },    
    "newOrExisting": {
      "type": "string", 
      "allowedValues": [
        "new", 
        "existing"
      ]
    },
    ```
    Aktualizovaná definice parametrů vypadá takto:

    ![Resource Manager – použití podmínky](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

5. Na začátek definice účtu úložiště přidejte následující řádek.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    Podmínka zkontroluje hodnotu parametru **newOrExisting**. Pokud je hodnota parametru **new**, nasazení vytvoří účet úložiště.

    Aktualizovaná definice účtu úložiště vypadá takto:

    ![Resource Manager – použití podmínky](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
6. Aktualizujte **storageUri** s použitím následující hodnoty:

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    Tato změna je nezbytná v případě, že použijete existující účet úložiště v jiné skupině prostředků.

7. Uložte změny.

## <a name="deploy-the-template"></a>Nasazení šablony

Postupujte podle pokynů v tématu [Nasazení šablony](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) a nasaďte šablonu.

Při nasazování šablony pomocí Azure PowerShellu je potřeba zadat jeden další parametr:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
$newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
$location = Read-Host -Prompt "Enter the Azure location (i.e. centralus)"
$vmAdmin = Read-Host -Prompt "Enter the admin username"
$vmPassword = Read-Host -Prompt "Enter the admin password"
$dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
$vmPW = ConvertTo-SecureString -String $vmPassword -AsPlainText -Force
New-AzureRmResourceGroupDeployment -Name mydeployment1018 -ResourceGroupName $resourceGroupName `
    -adminUsername $vmAdmin -adminPassword $vmPW `
    -dnsLabelPrefix $dnsLabelPrefix -storageAccountName $storageAccountName -newOrExisting $newOrExisting `
    -TemplateFile azuredeploy.json
```

> [!NOTE]
> Pokud má parametr **newOrExisting** hodnotu **new**, ale účet úložiště se zadaným názvem již existuje, nasazení selže.

Zkuste provést další nasazení s parametrem **newOrExisting** nastaveným na hodnotu existing a zadejte existující účet úložiště. Pokud chcete vytvořit účet úložiště předem, přečtěte si téma [Vytvoření účtu úložiště](../storage/common/storage-quickstart-create-account.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků uvidíte celkem šest prostředků.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili šablonu, která uživatelům umožňuje vybrat si mezi vytvořením nového účtu úložiště a použitím existujícího účtu úložiště. Virtuální počítač vytvořený v tomto kurzu vyžaduje uživatelské jméno a heslo správce. Místo předávání hesla během nasazení můžete heslo předem uložit ve službě Azure Key Vault a během nasazení ho načíst. Informace o načítání tajných klíčů ze služby Azure Key Vault a jejich použití v nasazení šablony najdete tady:

> [!div class="nextstepaction"]
> [Integrace služby Key Vault v nasazení šablony](./resource-manager-tutorial-use-key-vault.md)
