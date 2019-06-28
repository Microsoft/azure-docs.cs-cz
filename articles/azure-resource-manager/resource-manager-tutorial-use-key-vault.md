---
title: Integrace služby Azure Key Vault v nasazení šablony Resource Manageru | Microsoft Docs
description: Zjistěte, jak pomocí služby Azure Key Vault předávat hodnoty zabezpečených parametrů během nasazení šablony Resource Manageru.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 05/23/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: b91a3488750795e8ee9df6602bb2b3df8a9b08ec
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67436578"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-resource-manager-template-deployment"></a>Kurz: Integrace Azure Key Vault ve vašem nasazení šablony Resource Manageru

Zjistěte, jak načíst tajné kódy z trezoru klíčů Azure a předat tajné klíče jako parametry při nasazení Azure Resource Manageru. Hodnota tohoto parametru je nikdy přístupný, protože odkazovat jenom jeho ID služby key vault Další informace najdete v tématu [použití Azure Key Vault k předání zabezpečený parametr. hodnoty během nasazení](./resource-manager-keyvault-parameter.md).

V [nastavení pořadí nasazení prostředků](./resource-manager-tutorial-create-templates-with-dependent-resources.md) kurzu, vytvořte virtuální počítač (VM). Budete muset zadat uživatelské jméno správce virtuálního počítače a heslo. Místo zadání hesla, můžete předem uložení hesla v Azure key vault a pak upravit šablonu, kterou chcete načíst heslo ze služby key vault během nasazení.

![Diagram zobrazující integrace šablonu Resource Manageru pomocí služby key vault](./media/resource-manager-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Příprava služby key vault
> * Otevření šablony rychlého startu
> * Úprava souboru parametrů
> * Nasazení šablony
> * Ověření nasazení
> * Vyčištění prostředků

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* [Visual Studio Code](https://code.visualstudio.com/) s [rozšíření nástroje Správce prostředků](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Pro zvýšení zabezpečení, použijte generované heslo pro účet správce virtuálního počítače. Tady je ukázka pro generování hesla:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Ověřte, že vygenerované heslo splňuje požadavky na heslo virtuálního počítače. Každá služba Azure má specifické požadavky na hesla. Požadavky na heslo virtuálního počítače, naleznete v tématu [jaké jsou požadavky na heslo, když vytváříte virtuální počítač?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="prepare-a-key-vault"></a>Příprava služby key vault

V této části vytvoříte trezor klíčů a tajného kódu do něj přidat tak, aby při nasazování šablony můžete získat tajný kód. Existuje mnoho způsobů, jak vytvořit trezor klíčů. V tomto kurzu použijete Azure PowerShell k nasazení [šablony Resource Manageru](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json). Tato šablona provede následující akce:

* Vytvoří trezor klíčů s `enabledForTemplateDeployment` vlastnost povolena. Tato vlastnost musí mít *true* před nasazením šablony procesu můžete přístup k tajné kódy, které jsou definovány ve službě key vault.
* Přidá tajného kódu trezoru klíčů. Tajný kód uloží heslo správce virtuálního počítače.

> [!NOTE]
> Jako uživatel, který je nasazení šablony virtuálního počítače, pokud si nejste vlastník nebo Přispěvatel trezoru klíčů, musí vlastník nebo Přispěvatel udělit přístup k *Microsoft.KeyVault/vaults/deploy/action* oprávnění pro Trezor klíčů. Další informace najdete v tématu [použití Azure Key Vault k předání hodnotu zabezpečené parametru během nasazení](./resource-manager-keyvault-parameter.md).

Chcete-li spustit následující skript prostředí Azure PowerShell, vyberte **vyzkoušet** otevřete Azure Cloud Shell. Vložte skript, klikněte pravým tlačítkem na podokno prostředí a pak vyberte **vložte**.

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
> * Název skupiny prostředků je název projektu, ale s **rg** připojí k němu. Aby bylo snazší [vyčistila prostředky, které jste vytvořili v tomto kurzu](#clean-up-resources), stejném projektu a skupinou prostředků pojmenujte při použití můžete [nasadit další šablony](#deploy-the-template).
> * Výchozí název tajného kódu je **vmAdminPassword**. To je pevně zakódované v šabloně.
> * Chcete-li šablonu, kterou chcete získat tajný kód, je nutné povolit zásadu přístupu s názvem "Povolit přístup k Azure Resource Manageru pro nasazení šablony" pro trezor klíčů. V šabloně se povolí tyto zásady. Další informace o zásadách přístupu najdete v tématu [nasazení trezorů klíčů a tajných kódů](./resource-manager-keyvault-parameter.md#deploy-key-vaults-and-secrets).

Šablona má jednu hodnotu výstup, volá *keyVaultId*. Poznamenejte si hodnotu ID pro pozdější použití při nasazení virtuálního počítače. Formát ID prostředků je:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

Když zkopírujete a vložíte Identifikátor, může být rozdělené více řádků. Sloučí řádky a zkrátit nadbytečné mezery.

Pro ověření nasazení, spusťte následující příkaz Powershellu v podokně prostředí k načtení tajný klíč ve formátu prostého textu. Příkaz lze použít pouze ve stejné relaci prostředí, protože používá proměnnou *$keyVaultName*, který je definován v předchozím skript prostředí PowerShell.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Nyní jste připravili služby key vault a tajný klíč. Následující části ukazují, jak přizpůsobit existující šablonu k načtení tajný kód během nasazování.

## <a name="open-a-quickstart-template"></a>Otevření šablony rychlého startu

Šablony rychlý start Azure je úložiště šablon Resource Manageru. Místo vytvoření šablony úplně od začátku si můžete najít ukázkovou šablonu a přizpůsobit ji. Šablona, která se používá v tomto kurzu se nazývá [jednoduchý virtuální počítač s Windows nasadit](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. Ve Visual Studio Code, vyberte **souboru** > **otevřít soubor**.

1. V **název_souboru** vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Výběrem **Open** (Otevřít) soubor otevřete. Tento scénář je stejný jako ten, který se používá v [kurzu: Vytváření šablon Azure Resource Manageru s závislé prostředky](./resource-manager-tutorial-create-templates-with-dependent-resources.md).
   Šablona definuje pět zdrojů:

   * `Microsoft.Storage/storageAccounts`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

   To je užitečné mít seznámení se základními principy šablony dříve, než ji přizpůsobit.

1. Vyberte **souboru** > **uložit jako**a poté uložte kopii souboru do místního počítače s názvem *azuredeploy.json*.

1. Opakujte kroky 1 až 3 otevřít následující adresu URL a pak soubor uložte jako *azuredeploy.parameters.json*.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Úprava souboru parametrů

V souboru šablony není nutné provádět žádné změny.

1. V sadě Visual Studio Code otevřete *azuredeploy.parameters.json* Pokud ještě není otevřený.
1. Aktualizace `adminPassword` parametr:

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
    > Nahraďte hodnotu pro **id** s ID prostředku trezoru klíčů, který jste vytvořili v předchozím postupu.

    ![Integrace služby key vault a soubor parametrů nasazení virtuálních počítačů šablony Resource Manageru](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. Aktualizací následujících hodnot:

    * **adminUsername**: Název účtu správce virtuálního počítače.
    * **dnsLabelPrefix**: Název hodnoty dnsLabelPrefix.

    Příklady názvy najdete v článku na předchozím obrázku.

1. Uložte změny.

## <a name="deploy-the-template"></a>Nasazení šablony

Postupujte podle pokynů v [nasazení šablony](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Jak nahrát *azuredeploy.json* a *azuredeploy.parameters.json* Cloud Shell, a pak použijte následující příkaz powershellu k nasazení šablony:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

Při nasazení šablony použijte stejnou skupinu prostředků, který jste použili v trezoru klíčů. Tento přístup usnadňuje chcete vyčistit prostředky, protože je potřeba odstranit pouze jednu skupinu prostředků místo dvou.

## <a name="validate-the-deployment"></a>Ověření nasazení

Po úspěšném nasazení virtuálního počítače, otestujte přihlašovací údaje pomocí hesla, která je uložená ve službě key vault.

1. Otevřete web [Azure Portal](https://portal.azure.com).

1. Vyberte **skupiny prostředků** >  **\<*YourResourceGroupName*>**  > **simpleWinVM** .
1. Vyberte **připojení** v horní části.
1. Vyberte **stáhnout soubor RDP**a pak postupujte podle pokynů k přihlášení k virtuálnímu počítači pomocí hesla, která je uložená ve službě key vault.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky Azure, vyčistěte prostředky, které jste nasadili tak, že odstraníte skupinu prostředků.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Další postup

V tomto kurzu, který jste získali tajného klíče z trezoru klíčů Azure. Tajný kód se pak používá v nasazení šablony. Informace o vytváření propojených šablon najdete tady:

> [!div class="nextstepaction"]
> [Vytvoření propojených šablon](./resource-manager-tutorial-create-linked-templates.md)
