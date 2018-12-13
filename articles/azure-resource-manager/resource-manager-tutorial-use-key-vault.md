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
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 3a84f9ed35bac7f56d4a6aa2af94d1c28e335b74
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53093195"
---
# <a name="tutorial-integrate-azure-key-vault-in-resource-manager-template-deployment"></a>Kurz: Integrace služby Azure Key Vault v nasazení šablony Resource Manageru

Zjistěte, jak načítat tajné hodnoty ze služby Azure Key Vault a předávat je jako parametry během nasazení Resource Manageru. Hodnota se nikdy neodhaluje, protože odkazujete pouze na příslušné ID služby Key Vault. Další informace najdete v tématu [Použití služby Azure Key Vault k předávání hodnot zabezpečených parametrů během nasazení](./resource-manager-keyvault-parameter.md).

V kurzu [Nastavení pořadí nasazování prostředků](./resource-manager-tutorial-create-templates-with-dependent-resources.md) vytvoříte virtuální počítač, virtuální síť a několik dalších závislých prostředků. V tomto kurzu přizpůsobíte šablonu tak, aby načetla heslo správce virtuálního počítače ze služby Azure Key Vault.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Příprava služby Key Vault
> * Otevření šablony pro rychlý start
> * Úprava souboru parametrů
> * Nasazení šablony
> * Ověření nasazení
> * Vyčištění prostředků

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* [Visual Studio Code](https://code.visualstudio.com/) s [rozšířením Nástroje Resource Manageru](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)
* Pro zlepšení zabezpečení použijte pro účet správce virtuálního počítače vygenerované heslo. Tady ukázka generování hesla:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Služba Azure Key Vault je určená k ochraně kryptografických klíčů a dalších tajných klíčů. Další informace najdete v [kurzu integrace služby Azure Key Vault v nasazení šablony Resource Manageru](./resource-manager-tutorial-use-key-vault.md). Zároveň doporučujeme heslo každé tři měsíce aktualizovat.

## <a name="prepare-the-key-vault"></a>Příprava služby Key Vault

V této části pomocí šablony Resource Manageru vytvoříte službu Key Vault a tajný klíč. Tato šablona:

* Vytvoří službu Key Vault s povolenou vlastností `enabledForTemplateDeployment`. Tato vlastnost musí mít hodnotu true, aby v procesu nasazení šablony bylo možné přistupovat k tajným klíčům definovaným v této službě Key Vault.
* Přidá do služby Key Vault tajný klíč.  V tajném klíči je uložené heslo správce virtuálního počítače.

Pokud jako uživatel, který nasazuje šablonu virtuálního počítače, nejste vlastníkem ani přispěvatelem příslušné služby Key Vault, vlastník nebo přispěvatel služby Key Vault vám musí udělit oprávnění Microsoft.KeyVault/vaults/deploy/action ke službě Key Vault. Další informace najdete v tématu [Použití služby Azure Key Vault k předávání hodnot zabezpečených parametrů během nasazení](./resource-manager-keyvault-parameter.md).

Šablona potřebuje ke konfiguraci oprávnění vaše ID objektu uživatele Azure AD. Následující procedura získá ID objektu (GUID) a vygeneruje heslo správce. Aby se zabránilo útoku password spray, doporučuje se používat vygenerovaná hesla.

1. Spusťte následující příkaz Azure PowerShellu nebo Azure CLI.  

    ```azurecli-interactive
    echo "Enter your email address that is associated with your Azure subscription):" &&
    read upn &&
    az ad user show --upn-or-object-id $upn --query "objectId" &&
    openssl rand -base64 32
    ```
    ```azurepowershell-interactive
    $upn = Read-Host -Prompt "Input your user principal name (email address) used to sign in to Azure"
    (Get-AzureADUser -ObjectId $upn).ObjectId
    openssl rand -base64 32
    ```
2. Poznamenejte si ID objektu a vygenerované heslo. Budete je potřebovat později.
3. Ověřte, že vygenerované heslo splňuje požadavky na hesla virtuálních počítačů. Každá služba Azure má specifické požadavky na hesla. Požadavky na hesla virtuálních počítačů najdete v části [Jaké jsou požadavky na heslo při vytváření virtuálního počítače?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)

Vytvoření služby Key Vault:

1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří službu Key Vault a tajný kód služby Key Vault.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Farmtutorials.blob.core.windows.net%2Fcreatekeyvault%2FCreateKeyVault.json"><img src="./media/resource-manager-tutorial-use-key-vault/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Vyberte nebo zadejte následující hodnoty.  Po zadání hodnot nevybírejte **Koupit**.

    ![Integrace služby Key Vault v šabloně Resource Manageru – portál pro nasazení](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-key-vault-portal.png)

    * **Předplatné:** Vyberte předplatné Azure.
    * **Skupina prostředků:** Přiřaďte jedinečný název. Tento název si poznamenejte – stejnou skupinu prostředků použijete k nasazení virtuálního počítače v další relaci. Umístěním služby Key Vault i virtuálního počítače do stejné skupiny prostředků se usnadní vyčištění prostředků na konci kurzu.
    * **Umístění:** Vyberte prosím umístění.  Výchozí umístění je **USA – střed**.
    * **Název služby Key Vault:** Přiřaďte jedinečný název. 
    * **ID tenanta:** Funkce šablony automaticky načte ID vašeho tenanta.  Výchozí hodnotu neměňte.
    * **ID uživatele AD:** Zadejte své ID objektu uživatele Azure AD, které jste získali předchozím postupem.
    * **Název tajného kódu**: výchozí název je **vmAdminPassword**. Pokud tady změníte název tajného klíče, bude potřeba aktualizovat název tajného klíče i při nasazování virtuálního počítače.
    * **Hodnota tajného klíče:** Zadejte svůj tajný klíč.  Tajný klíč je heslo pro přihlášení k virtuálnímu počítači. Doporučuje se použít vygenerované heslo, které jste vytvořili předchozím postupem.
    * **Souhlasím s podmínkami a ujednáními uvedenými nahoře:** Toto políčko zaškrtněte.
3. V horní části vyberte **Upravit parametry** a prohlédněte si šablonu.
4. V souboru JSON šablony přejděte na řádek 28. Toto je definice prostředku služby Key Vault.
5. Přejděte na řádek 35:

    ```json
    "enabledForTemplateDeployment": true,
    ```
    `enabledForTemplateDeployment` je vlastnost služby Key Vault. Tato vlastnost musí mít hodnotu true, abyste z této služby Key Vault mohli načíst tajné klíče během nasazení.
6. Přejděte na řádek 89. Toto je definice tajného klíče služby Key Vault.
7. V dolní části stránky vyberte **Zahodit**. Neprovedli jste žádné změny.
8. Zkontrolujte, že jste zadali všechny hodnoty, jak je vidět na předchozím snímku obrazovky, a pak v dolní části stránky klikněte na **Koupit**.
9. Výběrem ikony zvonku (oznámení) v horní části stránky otevřete podokno **Oznámení**. Počkejte na úspěšné nasazení prostředku.
10. V podokně **Oznámení** vyberte **Přejít ke skupině prostředků**. 
11. Výběrem názvu služby Key Vault ji otevřete.
12. V levém podokně vyberte **Zásady přístupu**. Měl by se zobrazit váš název (Active Directory, jinak nemáte oprávnění k přístupu k trezoru klíčů.
13. Vyberte **Kliknutím zobrazíte pokročilé zásady přístupu**. Všimněte si, že je vybraná možnost **Povolit přístup k Azure Resource Manageru pro nasazení šablony**. Toto je další podmínka k fungování integrace se službou Key Vault.

    ![Integrace služby Key Vault v šabloně Resource Manageru – zásady přístupu](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-key-vault-access-policies.png)
14. V levém podokně vyberte **Vlastnosti**.
15. Zkopírujte si **ID prostředku**. Toto ID budete potřebovat k nasazení virtuálního počítače.  Formát ID prostředku je:

    ```json
    /subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
    ```

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
    Nahraďte **id** za ID prostředku vaší služby Key Vault vytvořené předchozím postupem.  

    ![Integrace služby Key Vault a nasazení virtuálního počítače šablony Resource Manageru – soubor parametrů](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. Zadejte hodnoty následujících parametrů:

    * **adminUsername:** název účtu správce virtuálního počítače
    * **dnsLabelPrefix:** název předpony názvu DNS
4. Uložte změny.

## <a name="deploy-the-template"></a>Nasazení šablony

Postupujte podle pokynů v tématu [Nasazení šablony](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) a nasaďte šablonu. Do služby Cloud Shell je potřeba nahrát soubory **azuredeploy.json** a **azuredeploy.parameters.json** a pak šablonu nasadit pomocí následujícího skriptu PowerShellu:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name of the Key Vault"
$deploymentName = Read-Host -Prompt "Enter the name for this deployment"
New-AzureRmResourceGroupDeployment -Name $deploymentName -ResourceGroupName $resourceGroupName `
    -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
```

Při nasazování šablony použijte stejnou skupinu prostředků jako pro službu Key Vault. Usnadníte si tím vyčištění prostředků. Místo dvou skupin prostředků vám bude stačit odstranit pouze jednu.

## <a name="valid-the-deployment"></a>Ověření nasazení

Po úspěšném nasazení virtuálního počítače otestujte přihlášení s použitím hesla uloženého ve službě Key Vault.

1. Otevřete web [Azure Portal](https://portal.azure.com).
2. Vyberte **Skupiny prostředků**/**<název_vaší_skupiny_prostředků>**/**simpleWinVM**.
3. V horní části vyberte **Připojit**.
4. Vyberte **Stáhnout soubor RDP**, pak postupujte podle pokynů a přihlaste se k virtuálnímu počítači s použitím hesla uloženého ve službě Key Vault.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků uvidíte celkem šest prostředků.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste ze služby Azure Key Vault načetli tajný klíč a použili jste ho v nasazení šablony.  Informace o vytváření propojených šablon najdete tady:

> [!div class="nextstepaction"]
> [Vytvoření propojených šablon](./resource-manager-tutorial-create-linked-templates.md)
