---
title: Zabezpečení artefaktů v šablonách
description: Zjistěte, jak zabezpečit artefakty, které používají v šablonách Azure Resource Manageru.
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7069ff363cf274ba855efc9b598d8d01e64e18d1
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250112"
---
# <a name="tutorial-secure-artifacts-in-azure-resource-manager-template-deployments"></a>Kurz: Zabezpečení artefaktů v nasazení šablon Azure Resource Manageru

Naučte se zabezpečit artefakty používané ve vašich Azure Resource Manager šablonách pomocí účtu Azure Storage se sdíleným přístupovým podpisem (SAS). Artefakty nasazení jsou všechny soubory kromě souborů hlavní šablony, které jsou potřebné k dokončení nasazení. Například v [kurzu: Import souborů SQL BacPac pomocí šablon Azure Resource Manager](./template-tutorial-deploy-sql-extensions-bacpac.md)vytvoří hlavní šablona Azure SQL Database instanci. Také zavolá soubor BACPAC a vytvoří tabulky a vloží data. Soubor BACPAC je artefakt a je uložený v účtu Azure Storage. Pro přístup k artefaktu se použil klíč účtu úložiště. 

V tomto kurzu použijete SAS udělit omezený přístup do souboru BACPAC v účtu úložiště Azure. Další informace o SAS najdete v tématu [použití sdílených přístupových podpisů (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Informace o tom, jak zabezpečit propojenou šablonu, najdete v tématu [kurz: Vytvoření propojených Azure Resource Manager šablon](./template-tutorial-create-linked-templates.md).

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Připravte soubor BACPAC.
> * Otevřete existující šablonu.
> * Upravte šablonu.
> * Nasaďte šablonu.
> * Ověření nasazení.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto článku potřebujete:

* Visual Studio Code s rozšířením nástrojů Správce prostředků. Pokud [chcete vytvořit Azure Resource Manager šablony](./use-vs-code-to-create-template.md), přečtěte si téma použití Visual Studio Code.
* [Kurz recenze: Import souborů SQL BacPac pomocí šablon Azure Resource Manager](./template-tutorial-deploy-sql-extensions-bacpac.md). Šablona použitá v tomto kurzu je vyvinutý v tomto kurzu. Odkaz ke stažení dokončené šablony je uvedené v tomto článku.
* Pro zlepšení zabezpečení použijte pro účet správce SQL Serveru vygenerované heslo. Tady je ukázka, kterou můžete použít k vygenerování hesla:

    ```console
    openssl rand -base64 32
    ```

    Služba Azure Key Vault je určená k ochraně kryptografických klíčů a dalších tajných klíčů. Další informace najdete v [kurzu integrace služby Azure Key Vault v nasazení šablony Resource Manageru](./template-tutorial-use-key-vault.md). Zároveň doporučujeme heslo každé tři měsíce aktualizovat.

## <a name="prepare-a-bacpac-file"></a>Příprava souboru BACPAC

V této části připravíte soubor BACPAC tak, aby byl soubor bezpečně přístupný při nasazení šablony Správce prostředků. V této části je pět postupů:

* Stáhněte si soubor BACPAC.
* Vytvořit účet služby Azure Storage
* Vytvořte kontejner objektů BLOB účtu úložiště.
* Nahrání souboru BACPAC do kontejneru.
* Získat token SAS souboru BACPAC.

1. Vyberte **vyzkoušet** a otevřete Cloud Shell. Pak vložte následující skript PowerShellu do okna prostředí.

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

1. Zapište adresu URL souboru BACPAC a token SAS. Tyto hodnoty budete potřebovat při nasazení šablony.

## <a name="open-an-existing-template"></a>Otevřete existující šablonu

V této relaci upravíte šablonu, kterou jste vytvořili v [kurzu: Import souborů SQL BacPac pomocí šablon Azure Resource Manager](./template-tutorial-deploy-sql-extensions-bacpac.md) pro volání souboru BacPac s tokenem SAS. Šablona vytvořená v kurzu rozšíření SQL je sdílená na [GitHubu](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. V nástroji Visual Studio Code vyberte **File** (Soubor)  > **Open File** (Otevřít soubor).
1. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy2.json
    ```

1. Výběrem **Open** (Otevřít) soubor otevřete.

    V šabloně jsou definované čtyři prostředky:

   * `Microsoft.Sql/servers`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers).
   * `Microsoft.SQL/servers/firewallRules`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules).
   * `Microsoft.SQL/servers/databases`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).
   * `Microsoft.SQL/server/databases/extensions`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions).

        Před přizpůsobením šablony je užitečné získat základní informace o této šabloně.
1. Vyberte **File** (Soubor)  > **Save As** (Uložit jako) a soubor uložte na místní počítač pod názvem *azuredeploy.json*.

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

    ![Kurz zabezpečené artefakty parametrů Resource Manageru](./media/template-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

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

Postup nasazení najdete v části [nasazení šablony](./template-tutorial-create-multiple-instances.md#deploy-the-template) . Místo toho použijte následující skript nasazení prostředí PowerShell.

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
Hodnoty _artifactsLocation, _artifactsLocationSasToken a bacpacFileName najdete v tématu [Příprava souboru BacPac](#prepare-a-bacpac-file).

## <a name="verify-the-deployment"></a>Ověření nasazení

Na portálu vyberte databázi SQL z nově nasazené skupiny prostředků. Vyberte **Editor dotazů (Preview)** a zadejte přihlašovací údaje správce. Uvidíte, že se do databáze naimportovaly dvě tabulky.

![Editor dotazů (Preview)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. V Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků. V rámci skupiny prostředků se zobrazí celkem šest prostředků.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nasadili SQL Server a databázi SQL a importovali soubor BACPAC pomocí tokenu SAS. Informace o tom, jak vytvořit kanál Azure pro průběžné vývoj a nasazování šablon Správce prostředků, najdete tady:

> [!div class="nextstepaction"]
> [Kontinuální integrace s kanálem Azure](./template-tutorial-use-azure-pipelines.md)
