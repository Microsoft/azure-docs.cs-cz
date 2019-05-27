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
ms.openlocfilehash: 0d78e6eaca708073c3a216507b320fe8783a25b6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239246"
---
# <a name="tutorial-integrate-azure-key-vault-in-resource-manager-template-deployment"></a>Kurz: Integrace Azure Key Vault v nasazení šablony Resource Manageru

Zjistěte, jak načíst tajné kódy ze služby Azure Key Vault a tajné klíče předat jako parametry při nasazení podle modelu Resource Manager. Hodnota se nikdy vystavena, protože pouze odkazujete na jeho ID služby key vault. Další informace najdete v tématu [Použití služby Azure Key Vault k předávání hodnot zabezpečených parametrů během nasazení](./resource-manager-keyvault-parameter.md).

V [nastavení pořadí nasazení prostředků](./resource-manager-tutorial-create-templates-with-dependent-resources.md) kurzu, vytvořte virtuální počítač. Budete muset zadat uživatelské jméno správce virtuálního počítače a heslo. Místo zadání hesla, můžete předem ukládání hesel do služby Azure Key Vault a pak upravit šablonu, kterou chcete načíst heslo ze služby key vault během nasazení.

![Diagram integrace služby Key Vault šablony Resource Manageru](./media/resource-manager-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Příprava služby key vault
> * Otevření šablony pro rychlý start
> * Úprava souboru parametrů
> * Nasazení šablony
> * Ověření nasazení
> * Vyčištění prostředků

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* [Visual Studio Code](https://code.visualstudio.com/) s [rozšířením Nástroje Resource Manageru](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)
* Pro zlepšení zabezpečení použijte pro účet správce virtuálního počítače vygenerované heslo. Tady ukázka generování hesla:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Ověřte, že vygenerované heslo splňuje požadavky na hesla virtuálních počítačů. Každá služba Azure má specifické požadavky na hesla. Požadavky na hesla virtuálních počítačů najdete v části [Jaké jsou požadavky na heslo při vytváření virtuálního počítače?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)

## <a name="prepare-a-key-vault"></a>Příprava služby key vault

V této části vytvoříte trezor klíčů a přidání tajného klíče do trezoru klíčů, tak, aby při nasazování šablony můžete získat tajný kód. Existuje mnoho způsobů, jak vytvořit trezor klíčů. V tomto kurzu použijete Azure PowerShell k nasazení [šablony Resource Manageru](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json). Tato šablona:

* Vytvoření služby key vault pomocí `enabledForTemplateDeployment` povolí vlastnost. Tato vlastnost musí mít hodnotu true, předtím, než se proces nasazení šablony můžete přístup k tajným klíčům definované v tomto trezoru klíčů.
* Přidání tajného klíče do trezoru klíčů.  V tajném klíči je uložené heslo správce virtuálního počítače.

> [!NOTE]
> Pokud si (jako uživatel, který chcete nasadit šablonu virtuálního počítače) nejste vlastník nebo Přispěvatel trezoru klíčů, vlastník nebo Přispěvatel trezoru klíčů musí udělit vám přístup k Microsoft.KeyVault/vaults/deploy/action oprávnění pro key vault. Další informace najdete v tématu [Použití služby Azure Key Vault k předávání hodnot zabezpečených parametrů během nasazení](./resource-manager-keyvault-parameter.md).

Chcete-li spustit následující skript prostředí PowerShell, vyberte **vyzkoušet** a otevřete Cloud shell. Vložte skript, klikněte pravým tlačítkem na podokno prostředí a pak vyberte **vložte**.

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

Několik částí důležitých informací:

* Název skupiny prostředků je název projektu s **rg** připojí. Aby bylo snazší [vyčistit prostředky vytvořené v tomto kurzu](#clean-up-resources), stejném projektu a skupinou prostředků pojmenujte při použití můžete [nasadit další šablony](#deploy-the-template).
* Výchozí název pro název tajného kódu je **vmAdminPassword**. Je pevně kódovaný v šabloně.
* Aby bylo možné pro šablonu, kterou chcete získat tajný klíč, musíte povolit zásadu přístupu volá **povolit přístup k Azure Resource Manageru pro nasazení šablony** pro trezor klíčů. V šabloně se povolí tyto zásady. Další informace o těchto zásadách přístupu najdete v tématu [nasazení trezorů klíčů a tajných kódů](./resource-manager-keyvault-parameter.md#deploy-key-vaults-and-secrets).

Šablona má jeden výstupu hodnoty nazvané **keyVaultId**. Poznamenejte si hodnotu. Toto ID budete potřebovat k nasazení virtuálního počítače. Formát ID prostředku je:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

Když zkopírujete a vložíte Identifikátor, může být ID rozdělená do více řádků. Musíte sloučit řádky a zkrátit nadbytečné mezery.

Pro ověření nasazení, spusťte následující příkaz Powershellu v podokně prostředí k načtení tajný klíč ve formátu prostého textu. Příkaz lze použít pouze ve stejné relaci prostředí vzhledem k tomu používá proměnnou $keyVaultName definované v předchozí skript prostředí PowerShell.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Teď připravíte trezor klíčů a tajný klíč, následujících částech se dozvíte, jak přizpůsobit existující šablonu k načtení tajný kód během nasazování.

## <a name="open-a-quickstart-template"></a>Otevření šablony pro rychlý start

Šablony pro rychlý start Azure slouží jako úložiště šablon Resource Manageru. Místo vytvoření šablony úplně od začátku si můžete najít ukázkovou šablonu a přizpůsobit ji. Šablona používaná v tomto kurzu má název [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Nasazení jednoduchého virtuálního počítače s Windows).

1. V nástroji Visual Studio Code vyberte **File** (Soubor) >**Open File** (Otevřít soubor).
2. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. Výběrem **Open** (Otevřít) soubor otevřete. Jedná se o stejný scénář jako v [kurzu vytváření šablon Azure Resource Manageru se závislými prostředky](./resource-manager-tutorial-create-templates-with-dependent-resources.md).
4. Šablona definuje pět prostředků:

   * `Microsoft.Storage/storageAccounts`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     Než začnete šablonu přizpůsobovat, je vhodné se s ní nejprve trochu seznámit.
5. Vyberte **File** (Soubor) >**Save As** (Uložit jako) a soubor uložte na místní počítač pod názvem **azuredeploy.json**.
6. Zopakováním kroků 1 až 4 otevřete následující adresu URL a pak soubor uložte jako **azuredeploy.parameters.json**.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Úprava souboru parametrů

V souboru šablony není nutné provádět žádné změny.

1. Ve Visual Studio Code otevřete soubor **azuredeploy.parameters.json**, pokud ještě není otevřený.
2. Aktualizujte parametr **adminPassword**:

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
    > Nahraďte hodnotu **id** s ID prostředku trezoru klíčů, vytvořili v posledním postupu.

    ![Integrace služby Key Vault a nasazení virtuálního počítače šablony Resource Manageru – soubor parametrů](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. Zadejte hodnoty následujících parametrů:

    * **adminUsername:** název účtu správce virtuálního počítače
    * **dnsLabelPrefix:** název předpony názvu DNS

    Viz příklad v předchozím snímku obrazovky.

4. Uložte změny.

## <a name="deploy-the-template"></a>Nasazení šablony

Postupujte podle pokynů v tématu [Nasazení šablony](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) a nasaďte šablonu. Musíte nahrát obě **azuredeploy.json** a **azuredeploy.parameters.json** do Cloud shellu a pak použijte následující příkaz powershellu k nasazení šablony:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

Při nasazení šablony použijte stejnou skupinu prostředků jako služba key vault. Usnadníte si tím vyčištění prostředků. Místo dvou skupin prostředků vám bude stačit odstranit pouze jednu.

## <a name="validate-the-deployment"></a>Ověření nasazení

Po úspěšném nasazení virtuálního počítače otestujte přihlášení s použitím hesla uložená ve službě key vault.

1. Otevřete web [Azure Portal](https://portal.azure.com).
2. Vyberte **Skupiny prostředků**/ **<název_vaší_skupiny_prostředků>** /**simpleWinVM**.
3. V horní části vyberte **Připojit**.
4. Vyberte **stáhnout soubor RDP** a pak postupujte podle pokynů k přihlášení k virtuálnímu počítači pomocí hesla uložená ve službě key vault.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste ze služby Azure Key Vault načetli tajný klíč a použili jste ho v nasazení šablony.  Informace o vytváření propojených šablon najdete tady:

> [!div class="nextstepaction"]
> [Vytvoření propojených šablon](./resource-manager-tutorial-create-linked-templates.md)
