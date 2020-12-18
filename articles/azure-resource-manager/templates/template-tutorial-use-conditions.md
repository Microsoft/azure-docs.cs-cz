---
title: Použít podmínku v šablonách
description: Zjistěte, jak nasazovat prostředky Azure na základě podmínek. Ukazuje, jak nasadit nový prostředek nebo použít existující prostředek.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: bc6dc5b385a39ddb1c4f1663649ea21e5ed14767
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2020
ms.locfileid: "97669308"
---
# <a name="tutorial-use-condition-in-arm-templates"></a>Kurz: Používání podmínky v šablonách ARM

Přečtěte si, jak nasadit prostředky Azure na základě podmínek v šabloně Azure Resource Manager (šablona ARM).

V kurzu [Nastavení pořadí nasazování prostředků](./template-tutorial-create-templates-with-dependent-resources.md) vytvoříte virtuální počítač, virtuální síť a několik dalších závislých prostředků včetně účtu úložiště. Místo nutnosti pokaždé vytvářet nový účet úložiště umožníte uživatelům vybrat si mezi vytvořením nového účtu úložiště a použitím existujícího účtu úložiště. Docílíte toho definováním dalšího parametru. Pokud je hodnota parametru **Nová**, vytvoří se nový účet úložiště. V opačném případě se použije existující účet úložiště se zadaným názvem.

![Diagram podmínky použití šablony Správce prostředků](./media/template-tutorial-use-conditions/resource-manager-template-use-condition-diagram.png)

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Otevření šablony pro rychlý start
> * Úprava šablony
> * Nasazení šablony
> * Vyčištění prostředků

Tento kurz se zabývá pouze základním scénářem použití podmínek. Další informace najdete tady:

* [Struktura souboru šablony: podmínka](conditional-resource-deployment.md)
* [Podmíněně nasaďte prostředek v ŠABLONĚ ARM](/azure/architecture/building-blocks/extending-templates/conditional-deploy).
* [Funkce šablony: if](./template-functions-logical.md#if).
* [Funkce porovnání pro šablony ARM](./template-functions-comparison.md)

Microsoft Learn modul, který pokrývá podmínky, najdete v tématu [Správa složitých nasazení cloudu pomocí pokročilých funkcí šablon ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* Visual Studio Code s rozšířením nástrojů Správce prostředků Tools. Další informace najdete v tématu [rychlý Start: vytvoření šablon ARM pomocí Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).
* Pro zlepšení zabezpečení použijte pro účet správce virtuálního počítače vygenerované heslo. Tady ukázka generování hesla:

    ```console
    openssl rand -base64 32
    ```

    Služba Azure Key Vault je určená k ochraně kryptografických klíčů a dalších tajných klíčů. Další informace najdete v tématu [kurz: integrace Azure Key Vault v nasazení šablony ARM](./template-tutorial-use-key-vault.md). Zároveň doporučujeme heslo každé tři měsíce aktualizovat.

## <a name="open-a-quickstart-template"></a>Otevření šablony pro rychlý start

Šablony pro rychlý Start Azure jsou úložiště pro šablony ARM. Místo vytvoření šablony úplně od začátku si můžete najít ukázkovou šablonu a přizpůsobit ji. Šablona používaná v tomto kurzu má název [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Nasazení jednoduchého virtuálního počítače s Windows).

1. Z Visual Studio Code vyberte **soubor**  >  **otevřít soubor**.
1. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Výběrem **Open** (Otevřít) soubor otevřete.
1. Šablona definuje šest prostředků:

   * [**Microsoft. Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

    Před přizpůsobením šablony je vhodné si projít odkaz na šablonu.

1. Vyberte **soubor**  >  **Uložit jako** a uložte kopii souboru do místního počítače s názvem _azuredeploy.js_.

## <a name="modify-the-template"></a>Úprava šablony

Ve stávající šabloně proveďte dvě změny:

* Přidejte parametr názvu účtu úložiště. Uživatelé můžou zadat buď název nového účtu úložiště, nebo název existujícího účtu úložiště.
* Přidejte nový parametr s názvem `newOrExisting` . Nasazení používá tento parametr k určení toho, jestli se má vytvořit nový účet úložiště, nebo použít existující účet úložiště.

Tady je postup, jak tyto změny provést:

1. Ve Visual Studio Code otevřete soubor _azuredeploy.json_.
1. Nahraďte tři `variables('storageAccountName')` řetězcem `parameters('storageAccountName')` v celé šabloně.
1. Odeberte definici následující proměnné:

    ![Snímek obrazovky, který zvýrazní definice proměnných, které je třeba odebrat.](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-remove-storageaccountname.png)

1. Do začátku oddílu Parameters přidejte následující dva parametry:

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

    Stisknutím kombinace kláves ALT + SHIFT + F naformátujte šablonu v Visual Studio Code.

    Aktualizovaná definice parametrů vypadá takto:

    ![Resource Manager – použití podmínky](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

1. Na začátek definice účtu úložiště přidejte následující řádek.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    Podmínka kontroluje hodnotu parametru `newOrExisting` . Pokud je hodnota parametru **new**, nasazení vytvoří účet úložiště.

    Aktualizovaná definice účtu úložiště vypadá takto:

    ![Snímek obrazovky zobrazující aktualizovanou definici účtu úložiště](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
1. Aktualizujte `storageUri` vlastnost definice prostředku virtuálního počítače na tuto hodnotu:

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    Tato změna je nezbytná v případě, že použijete existující účet úložiště v jiné skupině prostředků.

1. Uložte změny.

## <a name="deploy-the-template"></a>Nasazení šablony

1. Přihlaste se k [Azure Cloud Shell](https://shell.azure.com)

1. Vyberte prostředí **PowerShell** nebo **bash** (pro CLI) v levém horním rohu a zvolte své preferované prostředí. Po přepnutí se vyžaduje restartování prostředí.

    ![Azure Portal Cloud Shell nahrát soubor](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Vyberte **Nahrát nebo stáhnout soubory** a potom vyberte **Nahrát**. Viz předchozí snímek obrazovky. Vyberte soubor, který jste uložili v předchozí části. Po nahrání souboru můžete pomocí `ls` příkazu a `cat` příkazu ověřit, jestli se soubor úspěšně nahrál.

1. Spuštěním následujícího skriptu PowerShellu nasaďte šablonu.

    > [!IMPORTANT]
    > Název účtu úložiště musí být jedinečný v rámci Azure. Název musí obsahovat jenom malá písmena nebo číslice. Nemůže být delší než 24 znaků. Název účtu úložiště je název projektu s připojeným **úložištěm** . Ujistěte se, že název projektu a název vygenerovaného účtu úložiště splňují požadavky na název účtu úložiště.

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name and resource names"
    $newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
    $location = Read-Host -Prompt "Enter the Azure location (i.e. centralus)"
    $vmAdmin = Read-Host -Prompt "Enter the admin username"
    $vmPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $vmAdmin `
        -adminPassword $vmPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -storageAccountName $storageAccountName `
        -newOrExisting $newOrExisting `
        -TemplateFile "$HOME/azuredeploy.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    > [!NOTE]
    > Pokud je toto nasazení `newOrExisting` **nové**, ale účet úložiště se zadaným názvem účtu úložiště už existuje, nasazení se nezdařilo.

Zkuste provést jiné nasazení se `newOrExisting` **stávajícím** nastavením na existující a zadat existující účet úložiště. Pokud chcete vytvořit účet úložiště předem, přečtěte si téma [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků. Pokud chcete odstranit skupinu prostředků, vyberte **zkusit** pro otevření Cloud Shell. Skript PowerShellu vložíte tak, že kliknete pravým tlačítkem na podokno prostředí a pak vyberete **Vložit**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili šablonu, která uživatelům umožňuje vybrat si mezi vytvořením nového účtu úložiště a použitím existujícího účtu úložiště. Informace o načítání tajných klíčů ze služby Azure Key Vault a jejich použití jako hesel v nasazení šablony najdete tady:

> [!div class="nextstepaction"]
> [Integrace služby Key Vault v nasazení šablony](./template-tutorial-use-key-vault.md)
