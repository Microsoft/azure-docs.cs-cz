---
title: Použití trezoru klíčů Azure v šablonách
description: Zjistěte, jak pomocí služby Azure Key Vault předávat hodnoty zabezpečených parametrů během nasazení šablony Resource Manageru.
author: mumian
ms.date: 05/23/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: e49fafc2889b98d013d77471f8177fd85a307cc8
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754893"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-arm-template-deployment"></a>Kurz: Integrace azure key vaultu v nasazení šablony ARM

Zjistěte, jak načíst tajné klíče z trezoru klíčů Azure a předat tajné klíče jako parametry při nasazení šablony Správce prostředků Azure (ARM). Hodnota parametru není nikdy vystavena, protože odkazujete pouze na ID trezoru klíčů. Další informace naleznete [v tématu Azure Key Vault předat hodnotu zabezpečeného parametru během nasazení](./key-vault-parameter.md).

V kurzu [Nastavit pořadí nasazení prostředků](./template-tutorial-create-templates-with-dependent-resources.md) vytvoříte virtuální počítač (VM). Musíte zadat uživatelské jméno a heslo správce virtuálního počítače. Místo zadání hesla můžete heslo předem uložit do trezoru klíčů Azure a potom přizpůsobit šablonu tak, aby se heslo načetlo z trezoru klíčů během nasazení.

![Diagram zobrazující integraci šablony Správce prostředků s trezorem klíčů](./media/template-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Příprava trezoru klíčů
> * Otevření šablony rychlého startu
> * Úprava souboru parametrů
> * Nasazení šablony
> * Ověření nasazení
> * Vyčištění prostředků

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* Visual Studio Kód s rozšířením Nástroje Správce prostředků. Viz [Použití kódu Visual Studia k vytvoření šablon ARM](use-vs-code-to-create-template.md).
* Chcete-li zvýšit zabezpečení, použijte vygenerované heslo pro účet správce virtuálního počítače. Tady je ukázka pro generování hesla:

    ```console
    openssl rand -base64 32
    ```
    Ověřte, zda generované heslo splňuje požadavky na heslo virtuálního počítače. Každá služba Azure má specifické požadavky na hesla. Požadavky na heslo virtuálního počítače najdete [v tématu Jaké jsou požadavky na heslo při vytváření virtuálního počítače?](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="prepare-a-key-vault"></a>Příprava trezoru klíčů

V této části vytvoříte trezor klíčů a přidáte do něj tajný klíč, abyste mohli klíč při nasazení šablony načíst. Existuje mnoho způsobů, jak vytvořit trezor klíčů. V tomto kurzu použijete Azure PowerShell k nasazení [šablony ARM](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json). Tato šablona provádí následující akce:

* Vytvoří trezor klíčů `enabledForTemplateDeployment` s povolenou vlastností. Tato vlastnost musí být *true* před procesem nasazení šablony přístup k tajným kódům, které jsou definovány v trezoru klíčů.
* Přidá tajný klíč do trezoru klíčů. Tajný klíč ukládá heslo správce virtuálního počítače.

> [!NOTE]
> Jako uživatel, který nasazuje šablonu virtuálního počítače, pokud nejste vlastníkem trezoru klíčů nebo přispěvatelem, musí vám vlastník nebo přispěvatel udělit přístup k oprávnění *Microsoft.KeyVault/vaults/deploy/action* pro trezor klíčů. Další informace naleznete [v tématu Azure Key Vault předat hodnotu zabezpečeného parametru během nasazení](./key-vault-parameter.md).

Pokud chcete spustit následující skript Azure PowerShellu, vyberte **Vyzkoušet** ho, aby se otevřel Azure Cloud Shell. Chcete-li skript vložit, klepněte pravým tlačítkem myši na podokno prostředí a vyberte příkaz **Vložit**.

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
```

> [!IMPORTANT]
> * Název skupiny zdrojů je název projektu, ale s **rg** připojen k němu. Chcete-li usnadnit [vyčištění prostředků, které jste vytvořili v tomto kurzu](#clean-up-resources), použijte stejný název projektu a název skupiny zdrojů při nasazení další [šablony](#deploy-the-template).
> * Výchozí název tajného klíče je **vmAdminPassword**. Je to pevně zakódované v šabloně.
> * Chcete-li šablonu povolit k načtení tajného klíče, musíte povolit zásady přístupu s názvem "Povolit přístup ke Správci prostředků Azure pro nasazení šablony" pro trezor klíčů. Tato zásada je povolena v šabloně. Další informace o zásadách přístupu naleznete v tématu [Nasazení trezorů klíčů a tajných klíčů](./key-vault-parameter.md#deploy-key-vaults-and-secrets).

Šablona má jednu výstupní hodnotu, nazvanou *keyVaultId*. Při nasazení virtuálního počítače si poznamenejte hodnotu ID pro pozdější použití. Formát ID prostředku je:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

Když zkopírujete a vložíte ID, může být rozděleno do více řádků. Sloučit čáry a zkrátit další mezery.

Chcete-li ověřit nasazení, spusťte následující příkaz prostředí PowerShell ve stejném podokně prostředí, abyste načetli tajný klíč ve prostém textu. Příkaz funguje pouze ve stejné relaci prostředí, protože používá proměnnou *$keyVaultName*, která je definována v předchozím skriptu prostředí PowerShell.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Teď jste připravili trezor na klíče a tajemství. V následujících částech se ukazuje, jak přizpůsobit existující šablonu pro načtení tajného klíče během nasazení.

## <a name="open-a-quickstart-template"></a>Otevření šablony rychlého startu

Azure Quickstart Templates je úložiště pro šablony ARM. Místo vytvoření šablony úplně od začátku si můžete najít ukázkovou šablonu a přizpůsobit ji. Šablona, která se používá v tomto kurzu se nazývá [Nasazení jednoduchého virtuálního počítače se systémem Windows](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. V kódu sady Visual Studio vyberte **Soubor** > **otevřít soubor**.

1. Do pole **Název souboru** vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Výběrem **Open** (Otevřít) soubor otevřete. Scénář je stejný jako ten, který se používá v [kurzu: Vytvoření ARM šablony se závislými prostředky](./template-tutorial-create-templates-with-dependent-resources.md).
   Šablona definuje pět zdrojů:

   * `Microsoft.Storage/storageAccounts`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

   Je užitečné mít některé základní znalosti šablony před přizpůsobením.

1. Vyberte **Uložit soubor** > **jako**a uložte kopii souboru do místního počítače s názvem *azuredeploy.json*.

1. Opakováním kroků 1-3 otevřete následující adresu URL a uložte soubor jako *soubor azuredeploy.parameters.json*.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Úprava souboru parametrů

V souboru šablony není nutné provádět žádné změny.

1. V kódu Visual Studia otevřete *azuredeploy.parameters.json,* pokud ještě není otevřená.
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
    > Nahraďte hodnotu **id ID** id trezoru klíčů, který jste vytvořili v předchozím postupu.

    ![Integrace trezoru klíčů a souboru parametrů nasazení virtuálních strojů šablony Správce prostředků](./media/template-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. Aktualizujte následující hodnoty:

    * **adminUsername**: Název účtu správce virtuálního počítače.
    * **dnsLabelPrefix**: Pojmenujte hodnotu dnsLabelPrefix.

    Příklady názvů naleznete v předchozím obrázku.

1. Uložte změny.

## <a name="deploy-the-template"></a>Nasazení šablony

Postupujte podle pokynů v [části Nasazení šablony](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Nahrajte *azuredeploy.json* i *azuredeploy.parameters.json* do Cloud Shellu a pak k nasazení šablony použijte následující skript PowerShellu:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

Při nasazování šablony použijte stejnou skupinu prostředků, kterou jste použili v trezoru klíčů. Tento přístup usnadňuje vyčištění prostředků, protože je třeba odstranit pouze jednu skupinu prostředků namísto dvou.

## <a name="validate-the-deployment"></a>Ověření nasazení

Po úspěšném nasazení virtuálního počítače otestujte přihlašovací údaje pomocí hesla, které je uloženo v trezoru klíčů.

1. Otevřete [portál Azure](https://portal.azure.com).

1. Vyberte **skupiny** > **\<prostředků*YourResourceGroupName*>** > **simpleWinVM**.
1. V horní části vyberte **připojit.**
1. Vyberte **Stáhnout soubor RDP**a podle pokynů se přihlaste k virtuálnímu počítači pomocí hesla uloženého v trezoru klíčů.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete prostředky Azure, vyčistěte prostředky, které jste nasadili, odstraněním skupiny prostředků.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste získali tajný klíč z trezoru klíčů Azure. Potom jste použili tajný klíč v nasazení šablony. Informace o tom, jak používat rozšíření virtuálních strojů k provádění úloh po nasazení, najdete v tématu:

> [!div class="nextstepaction"]
> [Nasazování rozšíření virtuálních počítačů](./template-tutorial-deploy-vm-extensions.md)
