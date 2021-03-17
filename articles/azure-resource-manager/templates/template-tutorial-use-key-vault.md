---
title: Použití Azure Key Vault v šablonách
description: Naučte se, jak pomocí Azure Key Vault předat hodnoty zabezpečeného parametru během nasazování šablony Azure Resource Manager (šablona ARM).
author: mumian
ms.date: 03/01/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 388996dc0054192f6d9f3c87e11ca1d15e8a85e1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703881"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-arm-template-deployment"></a>Kurz: Integrace služby Azure Key Vault v nasazení šablony ARM

Naučte se, jak načíst tajné kódy z trezoru klíčů Azure a předat tajné klíče jako parametry při nasazení šablony Azure Resource Manager (šablona ARM). Hodnota parametru se nikdy nezveřejňuje, protože odkazuje jenom na jeho ID trezoru klíčů. Na tajný klíč trezoru klíčů můžete odkazovat pomocí statického ID nebo dynamického ID. V tomto kurzu se používá statické ID. S přístupem ke statickému ID odkazujete na Trezor klíčů v souboru parametrů šablony, nikoli v souboru šablony. Další informace o obou metodách naleznete v tématu [použití Azure Key Vault k předání hodnoty zabezpečeného parametru během nasazování](./key-vault-parameter.md).

V kurzu [Nastavení pořadí nasazení prostředků](./template-tutorial-create-templates-with-dependent-resources.md) můžete vytvořit virtuální počítač (VM). Musíte zadat uživatelské jméno a heslo správce virtuálního počítače. Místo zadání hesla můžete heslo ukládat do trezoru klíčů Azure a pak šablonu přizpůsobit, aby se během nasazování načetlo heslo z trezoru klíčů.

![Diagram znázorňující integraci šablony Správce prostředků s trezorem klíčů](./media/template-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Příprava trezoru klíčů
> * Otevření šablony rychlého startu
> * Úprava souboru parametrů
> * Nasazení šablony
> * Ověření nasazení
> * Vyčištění prostředků

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

Pro Microsoft Learn modul, který používá zabezpečenou hodnotu z trezoru klíčů, najdete informace v tématu [Správa složitých nasazení cloudu pomocí pokročilých funkcí šablon ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* Visual Studio Code s rozšířením nástrojů Správce prostředků Tools. Další informace najdete v tématu [rychlý Start: vytvoření šablon ARM pomocí Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).
* Pokud chcete zvýšit zabezpečení, použijte vygenerované heslo pro účet správce virtuálního počítače. Tady je ukázka pro vygenerování hesla:

    ```console
    openssl rand -base64 32
    ```

    Ověřte, že vygenerovaná hesla splňují požadavky na heslo k virtuálnímu počítači. Každá služba Azure má specifické požadavky na hesla. Požadavky na heslo k virtuálnímu počítači najdete v tématu [Jaké jsou požadavky na heslo při vytváření virtuálního počítače](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="prepare-a-key-vault"></a>Příprava trezoru klíčů

V této části vytvoříte Trezor klíčů a do něj přidáte tajný klíč, abyste mohli načíst tajný klíč při nasazení šablony. Existuje mnoho způsobů, jak vytvořit Trezor klíčů. V tomto kurzu použijete Azure PowerShell k nasazení [šablony ARM](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json). Tato šablona provede dvě věci:

* Vytvoří Trezor klíčů s `enabledForTemplateDeployment` povolenou vlastností. Tato vlastnost musí mít *hodnotu true* , aby mohl proces nasazení šablony přistupovat ke tajným klíčům, které jsou definovány v trezoru klíčů.
* Přidá tajný klíč do trezoru klíčů. Tajný kód uchovává heslo správce virtuálního počítače.

> [!NOTE]
> V případě, že uživatel, který šablonu virtuálního počítače nasazuje, nejste vlastníkem nebo přispěvatelem trezoru klíčů, musí vám vlastník nebo přispěvatel udělit přístup k `Microsoft.KeyVault/vaults/deploy/action` oprávnění pro Trezor klíčů. Další informace najdete v tématu [použití Azure Key Vault k předání hodnoty zabezpečeného parametru během nasazování](./key-vault-parameter.md).

Chcete-li spustit následující skript Azure PowerShell, vyberte možnost **zkusit** pro otevření Azure Cloud Shell. Skript vložíte tak, že kliknete pravým tlačítkem na podokno prostředí a pak vyberete **Vložit**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your user principal name (email address) used to sign in to Azure"
$secretValue = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$keyVaultName = $projectName
$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -keyVaultName $keyVaultName -adUserId $adUserId -secretValue $secretValue

Write-Host "Press [ENTER] to continue ..."
```

> [!IMPORTANT]
> * Název skupiny prostředků je název projektu, ale s připojeným **rgem** . Aby bylo snazší [vyčistit prostředky, které jste vytvořili v tomto kurzu](#clean-up-resources), při [nasazení další šablony](#deploy-the-template)použijte stejný název projektu a název skupiny prostředků.
> * Výchozí název tajného klíče je **vmAdminPassword**. Je pevně zakódované v šabloně.
> * Pokud chcete šabloně povolit načtení tajného kódu, musíte povolit zásady přístupu **s názvem povolení přístupu k Azure Resource Manager pro nasazení šablony** pro Trezor klíčů. Tato zásada je v šabloně povolená. Další informace o zásadách přístupu najdete v tématu [nasazení trezorů klíčů a tajných](./key-vault-parameter.md#deploy-key-vaults-and-secrets)kódů.

Šablona obsahuje jednu výstupní hodnotu s názvem `keyVaultId` . Pomocí tohoto ID společně s tajným názvem načtete tajnou hodnotu později v tomto kurzu. Formát ID prostředku je:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

Když zkopírujete a vložíte ID, může být rozděleno na více řádků. Sloučí řádky a ořízne nadbytečné mezery.

Chcete-li nasazení ověřit, spusťte následující příkaz prostředí PowerShell ve stejném podokně prostředí pro načtení tajného kódu jako nešifrovaný text. Příkaz funguje pouze ve stejné relaci prostředí, protože používá proměnnou `$keyVaultName` , která je definována v předchozím skriptu prostředí PowerShell.

```azurepowershell
$secret = Get-AzKeyVaultSecret -VaultName $keyVaultName -Name "vmAdminPassword"
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)
try {
   $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)
} finally {
   [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)
}
Write-Output $secretValueText
```

Teď jste připravili Trezor klíčů a tajný klíč. V následujících částech se dozvíte, jak přizpůsobit existující šablonu pro načtení tajného klíče během nasazení.

## <a name="open-a-quickstart-template"></a>Otevření šablony rychlého startu

Šablony pro rychlý Start Azure jsou úložiště pro šablony ARM. Místo vytvoření šablony úplně od začátku si můžete najít ukázkovou šablonu a přizpůsobit ji. Šablona použitá v tomto kurzu se nazývá [nasazení jednoduchého virtuálního počítače s Windows](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. V Visual Studio Code vyberte **soubor**  >  **otevřít soubor**.

1. Do pole **název souboru** vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Výběrem **Open** (Otevřít) soubor otevřete. Scénář je stejný jako ten, který se používá v [kurzu: vytvoření šablon ARM se závislými prostředky](./template-tutorial-create-templates-with-dependent-resources.md).
   Šablona definuje šest prostředků:

   * [**Microsoft. Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

   Před přizpůsobením šablony je užitečné, abyste měli základní znalosti šablony.

1. Vyberte **soubor**  >  **Uložit jako** a pak uložte kopii souboru do místního počítače s názvem *azuredeploy.jsv*.

1. Opakujte kroky 1-3 pro otevření následující adresy URL a pak soubor uložte jako *azuredeploy.parameters.jsna*.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Úprava souboru parametrů

Pomocí metody statického ID nemusíte dělat žádné změny v souboru šablony. Načítání tajné hodnoty se provádí konfigurací souboru parametrů šablony.

1. V Visual Studio Code otevřete *azuredeploy.parameters.js* , pokud už není otevřený.
1. Aktualizujte `adminPassword` parametr na:

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
                "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```

    > [!IMPORTANT]
    > Nahraďte hodnotu pro `id` ID prostředku trezoru klíčů, který jste vytvořili v předchozím postupu. `secretName`Je pevně zakódované jako **vmAdminPassword**.  Viz [Příprava trezoru klíčů](#prepare-a-key-vault).

    ![Integrace trezoru klíčů a Správce prostředků šablonou souborů parametrů nasazení virtuálního počítače](./media/template-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. Aktualizujte následující hodnoty:

    * `adminUsername`: Název účtu správce virtuálního počítače.
    * `dnsLabelPrefix`: Pojmenujte `dnsLabelPrefix` hodnotu.

    Příklady názvů naleznete na předchozím obrázku.

1. Uložte změny.

## <a name="deploy-the-template"></a>Nasazení šablony

1. Přihlaste se k [Azure Cloud Shell](https://shell.azure.com)

1. Vyberte prostředí **PowerShell** nebo **bash** (pro CLI) v levém horním rohu a zvolte své preferované prostředí.  Po přepnutí se vyžaduje restartování prostředí.

    ![Azure Portal Cloud Shell nahrát soubor](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Vyberte **Nahrát nebo stáhnout soubory** a potom vyberte **Nahrát**. Do Cloud Shell nahrajte jak *azuredeploy.js* , tak *azuredeploy.parameters.js* . Po nahrání souboru můžete pomocí `ls` příkazu a `cat` příkazu ověřit, jestli se soubor úspěšně nahrál.

1. Spuštěním následujícího skriptu PowerShellu nasaďte šablonu.

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
    $location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$HOME/azuredeploy.json" `
        -TemplateParameterFile "$HOME/azuredeploy.parameters.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    Když šablonu nasadíte, použijte stejnou skupinu prostředků, kterou jste použili v trezoru klíčů. Tento přístup usnadňuje vyčištění prostředků, protože potřebujete odstranit jenom jednu skupinu prostředků namísto dvou.

## <a name="validate-the-deployment"></a>Ověření nasazení

Po úspěšném nasazení virtuálního počítače testujte přihlašovací údaje pomocí hesla uloženého v trezoru klíčů.

1. Otevřete [Azure Portal](https://portal.azure.com).

1. Vyberte **skupiny prostředků**  >  **\<*YourResourceGroupName*>**  >  **simpleWinVM**.
1. V horní části vyberte **připojit** .
1. Vyberte **Stáhnout soubor RDP** a pak podle pokynů se přihlaste k virtuálnímu počítači pomocí hesla uloženého v trezoru klíčů.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už prostředky Azure nepotřebujete, vyčistěte prostředky, které jste nasadili, odstraněním skupiny prostředků.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste načetli tajný klíč z trezoru klíčů Azure. Pak jste tajný klíč použili v nasazení šablony. Informace o tom, jak používat rozšíření virtuálních počítačů k provádění úloh po nasazení, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Nasazování rozšíření virtuálních počítačů](./template-tutorial-deploy-vm-extensions.md)
