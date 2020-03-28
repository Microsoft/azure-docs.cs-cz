---
title: Zabezpečení artefaktů v šablonách
description: Zjistěte, jak zabezpečit artefakty používané v šablonách Azure Resource Manageru.
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: ad6ea3c68ed6f48ac48bbbdafed7f8660df23937
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239231"
---
# <a name="tutorial-secure-artifacts-in-arm-template-deployments"></a>Kurz: Zabezpečení artefaktů v nasazení chytlacích sítí ARM

Zjistěte, jak zabezpečit artefakty používané ve vašich šablonách Azure Resource Manager (ARM) pomocí účtu Azure Storage s podpisy sdíleného přístupu (SAS). Artefakty nasazení jsou všechny soubory, kromě hlavního souboru šablony, které jsou potřebné k dokončení nasazení. Například v [kurzu: Import souborů SQL BACPAC se šablonami ARM](./template-tutorial-deploy-sql-extensions-bacpac.md)vytvoří hlavní šablona instanci Azure SQL Database. Také volá soubor BACPAC k vytvoření tabulek a vložení dat. Soubor BACPAC je artefakt a je uložený v účtu Azure Storage. Klíč účtu úložiště byl použit pro přístup k artefaktu.

V tomto kurzu použijete SAS k udělení omezeného přístupu k souboru BACPAC ve vašem vlastním účtu azure storage. Další informace o SAS naleznete [v tématu Použití sdílených přístupových podpisů (SAS).](../../storage/common/storage-dotnet-shared-access-signature-part-1.md)

Informace o zabezpečení propojené šablony naleznete v [tématu Výuka: Vytvoření propojených šablon ARM](./template-tutorial-create-linked-templates.md).

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Připravte soubor BACPAC.
> * Otevřete existující šablonu.
> * Upravte šablonu.
> * Nasaďte šablonu.
> * Ověřte nasazení.

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* Visual Studio Kód s rozšířením nástroje Správce prostředků. Viz [Použití kódu Visual Studia k vytvoření šablon ARM](./use-vs-code-to-create-template.md).
* [Viz kurz: Import souborů SQL BACPAC se šablonami ARM](./template-tutorial-deploy-sql-extensions-bacpac.md). Šablona použitá v tomto kurzu je ta vyvinutá v tomto kurzu. Odkaz ke stažení dokončené šablony je k dispozici v tomto článku.
* Pro zlepšení zabezpečení použijte pro účet správce SQL Serveru vygenerované heslo. Zde je ukázka, kterou můžete použít ke generování hesla:

    ```console
    openssl rand -base64 32
    ```

    Služba Azure Key Vault je určená k ochraně kryptografických klíčů a dalších tajných klíčů. Další informace naleznete [v tématu: Integrace trezoru klíčů Azure v nasazení šablony ARM](./template-tutorial-use-key-vault.md). Zároveň doporučujeme heslo každé tři měsíce aktualizovat.

## <a name="prepare-a-bacpac-file"></a>Příprava souboru BACPAC

V této části připravíte soubor BACPAC tak, aby byl soubor přístupný bezpečně při nasazení šablony ARM. V této části je pět postupů:

* Stáhněte si soubor BACPAC.
* Vytvořit účet služby Azure Storage
* Vytvořte kontejner objektů blob účtu úložiště.
* Nahrajte soubor BACPAC do kontejneru.
* Načtěte token SAS souboru BACPAC.

1. Vyberte **Zkuste to** otevřít prostředí cloudu. Pak vložte následující skript Prostředí PowerShell do okna prostředí.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name"   # This name is used to generate names for Azure resources, such as storage account name.
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"

    $resourceGroupName = $projectName + "rg"
    $storageAccountName = $projectName + "store"
    $containerName = "bacpacfile" # The name of the Blob container to be created.

    $bacpacURL = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"
    $bacpacFileName = "SQLDatabaseExtension.bacpac" # A file name used for downloading and uploading the BACPAC file.

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacURL -OutFile "$home/$bacpacFileName"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account
    $storageAccount = New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageAccountName `
        -Location $location `
        -SkuName "Standard_LRS"

    $context = $storageAccount.Context

    # Create a container
    New-AzStorageContainer -Name $containerName -Context $context

    # Upload the bacpac file
    Set-AzStorageBlobContent `
        -Container $containerName `
        -File "$home/$bacpacFileName" `
        -Blob $bacpacFileName `
        -Context $context

    # Generate a SAS token
    $bacpacURI = New-AzStorageBlobSASToken `
        -Context $context `
        -Container $containerName `
        -Blob $bacpacFileName `
        -Permission r `
        -ExpiryTime (Get-Date).AddHours(8.0) `
        -FullUri

    $str = $bacpacURI.split("?")

    Write-Host "You need the blob url and the SAS token later in the tutorial:"
    Write-Host $str[0]
    Write-Host (-join ("?", $str[1]))

    Write-Host "Press [ENTER] to continue ..."
    ```

1. Poznamenejte si adresu URL souboru BACPAC a token SAS. Tyto hodnoty potřebujete při nasazení šablony.

## <a name="open-an-existing-template"></a>Otevření existující šablony

V této relaci upravíte šablonu, kterou jste vytvořili v [kurzu: Import ovat soubory SQL BACPAC se šablonami ARM](./template-tutorial-deploy-sql-extensions-bacpac.md) a volat soubor BACPAC tokenem SAS. Šablona vyvinutá v kurzu rozšíření SQL je sdílena v [GitHubu](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. V kódu sady Visual Studio vyberte **Soubor** > **otevřít soubor**.
1. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy2.json
    ```

1. Výběrem **Open** (Otevřít) soubor otevřete.

    V šabloně jsou definovány čtyři prostředky:

   * `Microsoft.Sql/servers`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers).
   * `Microsoft.SQL/servers/firewallRules`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules).
   * `Microsoft.SQL/servers/databases`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).
   * `Microsoft.SQL/server/databases/extensions`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions).

        Je užitečné získat některé základní znalosti šablony před přizpůsobením.
1. Vyberte **Soubor** > **Uložit jako,** chcete-li uložit kopii souboru do místního počítače s názvem *azuredeploy.json*.

## <a name="edit-the-template"></a>Úprava šablony

1. Nahraďte definici parametru storageAccountKey následující definicí parametru:

    ```json
        "_artifactsLocationSasToken": {
          "type": "securestring",
          "metadata": {
            "description": "Specifies the SAS token required to access the artifact location."
          }
        },
    ```

    ![Správce prostředků kurz zabezpečit artefakty parametry](./media/template-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

1. Aktualizujte hodnotu následujících tří prvků prostředku rozšíření SQL:

    ```json
    "storageKeyType": "SharedAccessKey",
    "storageKey": "[parameters('_artifactsLocationSasToken')]",
    "storageUri": "[parameters('bacpacUrl')]",
    ```

Hotová šablona vypadá takto:

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy3.json?range=1-106&highlight=38-43,95-97)]

## <a name="deploy-the-template"></a>Nasazení šablony

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Postup nasazení najdete v části [Nasazení šablony.](./template-tutorial-create-multiple-instances.md#deploy-the-template) Místo toho použijte následující skript nasazení prostředí PowerShell.

```azurepowershell
$projectName = Read-Host -Prompt "Enter the project name that is used earlier"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the sql database admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$bacpacUrl = Read-Host -Prompt "Enter the BACPAC url"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString

$resourceGroupName = $projectName + "rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacUrl $bacpacUrl `
    -TemplateFile "$HOME/azuredeploy.json"

Write-Host "Press [ENTER] to continue ..."
```

Použijte vygenerované heslo. Viz [Požadavky](#prerequisites).
Hodnoty _artifactsLocation, _artifactsLocationSasToken a bacpacFileName naleznete [v tématu Příprava souboru BACPAC](#prepare-a-bacpac-file).

## <a name="verify-the-deployment"></a>Ověření nasazení

Na portálu vyberte databázi SQL z nově nasazené skupiny prostředků. Vyberte **Editor dotazů (Preview)** a zadejte přihlašovací údaje správce. Zobrazí se dvě tabulky importované do databáze.

![Editor dotazů (náhled)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na webu Azure Portal vyberte **skupinu prostředků** v levé nabídce.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků. Ve skupině prostředků se zobrazí celkem šest prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků.**

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nasadili sql server a databázi SQL a importovali soubor BACPAC pomocí tokenu SAS. Informace o nasazování prostředků Azure napříč několika oblastmi a používání postupů bezpečného nasazení najdete tady:

> [!div class="nextstepaction"]
> [Použití postupů bezpečného nasazení](./deployment-manager-tutorial.md)
