---
title: Použití Azure Key Vault v šablonách
description: Zjistěte, jak pomocí služby Azure Key Vault předávat hodnoty zabezpečených parametrů během nasazení šablony Resource Manageru.
author: mumian
ms.date: 05/23/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: bae67b0177823ab4558085db67423edea062fa3c
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250057"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-resource-manager-template-deployment"></a>Kurz: Integrace Azure Key Vault v nasazení šablon Správce prostředků

Naučte se, jak načíst tajné kódy z trezoru klíčů Azure a předat tajné klíče jako parametry při nasazení Azure Resource Manager. Hodnota parametru se nikdy nezveřejňuje, protože odkazuje jenom na jeho ID trezoru klíčů. Další informace najdete v tématu [použití Azure Key Vault k předání hodnoty zabezpečeného parametru během nasazování](./key-vault-parameter.md).

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

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto článku potřebujete:

* Visual Studio Code s rozšířením nástrojů Správce prostředků Tools. Pokud [chcete vytvořit Azure Resource Manager šablony](use-vs-code-to-create-template.md), přečtěte si téma použití Visual Studio Code.
* Pokud chcete zvýšit zabezpečení, použijte vygenerované heslo pro účet správce virtuálního počítače. Tady je ukázka pro vygenerování hesla:

    ```console
    openssl rand -base64 32
    ```
    Ověřte, že vygenerovaná hesla splňují požadavky na heslo k virtuálnímu počítači. Každá služba Azure má specifické požadavky na hesla. Požadavky na heslo k virtuálnímu počítači najdete v tématu [Jaké jsou požadavky na heslo při vytváření virtuálního počítače](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="prepare-a-key-vault"></a>Příprava trezoru klíčů

V této části vytvoříte Trezor klíčů a do něj přidáte tajný klíč, abyste mohli načíst tajný klíč při nasazení šablony. Existuje mnoho způsobů, jak vytvořit Trezor klíčů. V tomto kurzu použijete Azure PowerShell k nasazení [Správce prostředků šablony](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json). Tato šablona provede následující:

* Vytvoří Trezor klíčů s povolenou vlastností `enabledForTemplateDeployment`. Tato vlastnost musí mít *hodnotu true* , aby mohl proces nasazení šablony přistupovat ke tajným klíčům, které jsou definovány v trezoru klíčů.
* Přidá tajný klíč do trezoru klíčů. Tajný kód uchovává heslo správce virtuálního počítače.

> [!NOTE]
> Jako uživatel, který nasazuje šablonu virtuálního počítače, pokud nejste vlastníkem nebo přispěvatelem trezoru klíčů, vlastník nebo přispěvatel vám musí udělit přístup ke službě *Microsoft. webkey trezor/trezory/* k oprávněním k nasazení/akci pro Trezor klíčů. Další informace najdete v tématu [použití Azure Key Vault k předání hodnoty zabezpečeného parametru během nasazování](./key-vault-parameter.md).

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
```

> [!IMPORTANT]
> * Název skupiny prostředků je název projektu, ale s připojeným **rgem** . Aby bylo snazší [vyčistit prostředky, které jste vytvořili v tomto kurzu](#clean-up-resources), při [nasazení další šablony](#deploy-the-template)použijte stejný název projektu a název skupiny prostředků.
> * Výchozí název tajného klíče je **vmAdminPassword**. Je pevně zakódované v šabloně.
> * Pokud chcete šabloně povolit načtení tajného kódu, musíte povolit zásadu přístupu nazvanou povolit přístup k Azure Resource Manager nasazení šablony pro Trezor klíčů. Tato zásada je v šabloně povolená. Další informace o zásadách přístupu najdete v tématu [nasazení trezorů klíčů a tajných](./key-vault-parameter.md#deploy-key-vaults-and-secrets)kódů.

Šablona obsahuje jednu výstupní hodnotu s názvem *keyVaultId*. Pokud nasadíte virtuální počítač, zapište hodnotu ID pro pozdější použití. Formát ID prostředku je:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

Když zkopírujete a vložíte ID, může být rozděleno na více řádků. Sloučí řádky a ořízne nadbytečné mezery.

Chcete-li nasazení ověřit, spusťte následující příkaz prostředí PowerShell ve stejném podokně prostředí pro načtení tajného kódu jako nešifrovaný text. Příkaz funguje pouze ve stejné relaci prostředí, protože používá proměnnou *$keyVaultName*, která je definována v předchozím skriptu prostředí PowerShell.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Teď jste připravili Trezor klíčů a tajný klíč. V následujících částech se dozvíte, jak přizpůsobit existující šablonu pro načtení tajného klíče během nasazení.

## <a name="open-a-quickstart-template"></a>Otevření šablony rychlého startu

Šablony pro rychlý Start Azure jsou úložiště pro šablony Správce prostředků. Místo vytvoření šablony úplně od začátku si můžete najít ukázkovou šablonu a přizpůsobit ji. Šablona použitá v tomto kurzu se nazývá [nasazení jednoduchého virtuálního počítače s Windows](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. V Visual Studio Code vyberte **soubor** > **otevřít soubor**.

1. Do pole **název souboru** vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Výběrem **Open** (Otevřít) soubor otevřete. Scénář je stejný jako ten, který se používá v [kurzu: vytváření Azure Resource Manager šablon se závislými prostředky](./template-tutorial-create-templates-with-dependent-resources.md).
   Šablona definuje pět prostředků:

   * `Microsoft.Storage/storageAccounts`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

   Před přizpůsobením šablony je užitečné, abyste měli základní znalosti šablony.

1. Vyberte **soubor** > **Uložit jako**a pak uložte kopii souboru do místního počítače s názvem *azuredeploy. JSON*.

1. Opakujte kroky 1-3 pro otevření následující adresy URL a pak soubor uložte jako *azuredeploy. Parameters. JSON*.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Úprava souboru parametrů

V souboru šablony není nutné provádět žádné změny.

1. V Visual Studio Code otevřete *azuredeploy. Parameters. JSON* , pokud ještě není otevřený.
1. Aktualizujte parametr `adminPassword` na:

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
    > Nahraďte hodnotu pro **ID** ID prostředku trezoru klíčů, který jste vytvořili v předchozím postupu.

    ![Integrace trezoru klíčů a Správce prostředků šablonou souborů parametrů nasazení virtuálního počítače](./media/template-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. Aktualizací následujících hodnot:

    * **adminUsername**: název účtu správce virtuálního počítače.
    * **dnsLabelPrefix**: pojmenujte hodnotu dnsLabelPrefix.

    Příklady názvů naleznete na předchozím obrázku.

1. Uložte změny.

## <a name="deploy-the-template"></a>Nasazení šablony

Postupujte podle pokynů v části [nasazení šablony](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Nahrajte *azuredeploy. JSON* a *azuredeploy. Parameters. Cloud Shell JSON* a pomocí následujícího skriptu PowerShellu nasaďte šablonu:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

Když šablonu nasadíte, použijte stejnou skupinu prostředků, kterou jste použili v trezoru klíčů. Tento přístup usnadňuje vyčištění prostředků, protože potřebujete odstranit jenom jednu skupinu prostředků namísto dvou.

## <a name="validate-the-deployment"></a>Ověření nasazení

Po úspěšném nasazení virtuálního počítače testujte přihlašovací údaje pomocí hesla uloženého v trezoru klíčů.

1. Otevřete [portál Azure](https://portal.azure.com).

1. Vyberte **skupiny prostředků** >  **\<*YourResourceGroupName*>**  > **simpleWinVM**.
1. V horní části vyberte **připojit** .
1. Vyberte **Stáhnout soubor RDP**a pak podle pokynů se přihlaste k virtuálnímu počítači pomocí hesla uloženého v trezoru klíčů.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už prostředky Azure nepotřebujete, vyčistěte prostředky, které jste nasadili, odstraněním skupiny prostředků.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste načetli tajný klíč z trezoru klíčů Azure. Pak jste tajný klíč použili v nasazení šablony. Informace o vytváření propojených šablon najdete tady:

> [!div class="nextstepaction"]
> [Vytvoření propojených šablon](./template-tutorial-create-linked-templates.md)
