---
title: Vytváření šablon Azure Resource Manageru se závislými prostředky | Microsoft Docs
description: Zjistěte, jak vytvořit šablonu Azure Resource Manageru s více prostředky a jak ji nasadit pomocí webu Azure Portal
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/09/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 50f1c81f08787181de2fe3a9f6fb97a96a2bd882
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114308"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>Kurz: Vytváření šablon Azure Resource Manageru se závislými prostředky

Zjistěte, jak vytvořit šablonu Azure Resource Manageru a nasadit více prostředků.  Po vytvoření šablonu nasadíte pomocí Cloud Shellu z webu Azure Portal.

V tomto kurzu vytvoříte účet úložiště, virtuální počítač, virtuální síť a několik dalších závislých prostředků. Některé prostředky se nedají nasadit, dokud bude existovat jiný prostředek. Nemůžete třeba vytvořit virtuální počítač, dokud bude existovat jeho účet úložiště a síťové rozhraní. Tento vztah se definuje tím, že jeden prostředek označíte jako závislý na jiných prostředcích. Resource Manager vyhodnocuje závislosti mezi prostředky a provádí nasazení v závislém pořadí. Pokud na sobě prostředky nezávisí, Resource Manager je nasadí paralelně. Další informace najdete v článku [Určení pořadí pro nasazení prostředků v šablonách Azure Resource Manageru](./resource-group-define-dependencies.md).

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Příprava služby Key Vault
> * Otevření šablony rychlého startu
> * Prozkoumání šablony
> * Úprava souboru parametrů
> * Nasazení šablony

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* [Visual Studio Code](https://code.visualstudio.com/) s rozšířením Nástroje Resource Manageru  Přečtěte si, [jak toto rozšíření nainstalovat](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="prepare-key-vault"></a>Příprava služby Key Vault

Aby se zabránilo útokům password spray, doporučuje se pro účet správce virtuálního počítače použít automaticky vygenerované heslo a uložit ho ve službě Key Vault. Následujícím postupem se vytvoří služba Key Vault a tajný klíč pro uložení hesla. Také se nakonfigurují potřebná oprávnění, která povolí nasazení šablony přístup k tajnému klíči uloženému ve službě Key Vault. Pokud se služba Key Vault nachází v jiném předplatném Azure, jsou potřeba další zásady přístupu. Podrobnosti najdete v tématu [Použití služby Azure Key Vault k předávání hodnot zabezpečených parametrů během nasazení](./resource-manager-keyvault-parameter.md).

1. Přihlaste se do služby [Azure Cloud Shell](https://shell.azure.com).
2. Výběrem **PowerShellu** nebo **Bash** v levém horním rohu přepněte do oblíbeného prostředí.
3. Spusťte následující příkaz Azure PowerShellu nebo Azure CLI.  

    ```azurecli-interactive
    keyVaultName='<your-unique-vault-name>'
    resourceGroupName='<your-resource-group-name>'
    location='Central US'
    userPrincipalName='<your-email-address-associated-with-your-subscription>'
    
    # Create a resource group
    az group create --name $resourceGroupName --location $location
    
    # Create a Key Vault
    keyVault=$(az keyvault create \
      --name $keyVaultName \
      --resource-group $resourceGroupName \
      --location $location \
      --enabled-for-template-deployment true)
    keyVaultId=$(echo $keyVault | jq -r '.id')
    az keyvault set-policy --upn $userPrincipalName --name $keyVaultName --secret-permissions set delete get list

    # Create a secret
    password=$(openssl rand -base64 32)
    az keyvault secret set --vault-name $keyVaultName --name 'vmAdminPassword' --value $password
    
    # Print the useful property values
    echo "You need the following values for the virtual machine deployment:"
    echo "Resource group name is: $resourceGroupName."
    echo "The admin password is: $password."
    echo "The Key Vault resource ID is: $keyVaultId."
    ```

    ```azurepowershell-interactive
    $keyVaultName = "<your-unique-vault-name>"
    $resourceGroupName="<your-resource-group-name>"
    $location='Central US'
    $userPrincipalName="<your-email-address-associated-with-your-subscription>"
    
    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
        
    # Create a Key Vault
    $keyVault = New-AzureRmKeyVault `
      -VaultName $keyVaultName `
      -resourceGroupName $resourceGroupName `
      -Location $location `
      -EnabledForTemplateDeployment
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userPrincipalName -PermissionsToSecrets set,delete,get,list
      
    # Create a secret
    $password = openssl rand -base64 32
    
    $secretValue = ConvertTo-SecureString $password -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name "vmAdminPassword" -SecretValue $secretValue
    
    # Print the useful property values
    echo "You need the following values for the virtual machine deployment:"
    echo "Resource group name is: $resourceGroupName."
    echo "The admin password is: $password."
    echo "The Key Vault resource ID is: " $keyVault.ResourceID
    ```
4. Poznamenejte si výstupní hodnoty. Budete je potřebovat v pozdější části kurzu.

> [!NOTE]
> Každá služba Azure má specifické požadavky na hesla. Například požadavky virtuálních počítačů Azure najdete v tématu Jaké jsou požadavky na heslo při vytváření virtuálního počítače?

## <a name="open-a-quickstart-template"></a>Otevření šablony pro rychlý start

Šablony pro rychlý start Azure slouží jako úložiště šablon Resource Manageru. Místo vytvoření šablony úplně od začátku si můžete najít ukázkovou šablonu a přizpůsobit ji. Šablona používaná v tomto kurzu má název [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Nasazení jednoduchého virtuálního počítače s Windows).

1. V nástroji Visual Studio Code vyberte **File** (Soubor) >**Open File** (Otevřít soubor).
2. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Výběrem **Open** (Otevřít) soubor otevřete.
4. Vyberte **File** (Soubor) >**Save As** (Uložit jako) a soubor uložte na místní počítač pod názvem **azuredeploy.json**.
5. Zopakováním kroků 1 až 4 otevřete soubor **https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json** a pak soubor uložte jako **azuredeploy.parameters.json**.

## <a name="explore-the-template"></a>Prozkoumání šablony

Při zkoumání šablony v této části zkuste zodpovědět tyto otázky:

- Kolik prostředků Azure se v této šabloně definuje?
- Jedním z prostředků je účet úložiště Azure.  Vypadá jeho definice jako ta, kterou jsme použili v posledním kurzu?
- Najdete referenční informace k šablonám pro prostředky definované v této šabloně?
- Najdete závislosti těchto prostředků?

1. V nástroji Visual Studio Code sbalte elementy tak, abyste viděli jenom elementy první úrovně a u položky **resources** elementy druhé úrovně:

    ![Šablony Azure Resource Manageru ve Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    Šablona definuje pět prostředků.
2. Rozbalte první prostředek. Jedná se o účet úložiště. Jeho definice by měla být stejná jako ta, kterou jsme použili na začátku posledního kurzu.

    ![Šablony Azure Resource Manageru ve Visual Studio Code – definice účtu úložiště](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

3. Rozbalte druhý prostředek. Typ tohoto prostředku je **Microsoft.Network/publicIPAddresses**. Pokud chcete najít referenční informace k šablonám, přejděte na stránku s [referenčními informacemi k šablonám](https://docs.microsoft.com/azure/templates/) a do pole **Filtrovat podle názvu** zadejte **veřejná IP adresa** nebo **veřejné IP adresy**. Porovnejte definici prostředku s referenčními informacemi k šablonám.

    ![Šablony Azure Resource Manageru ve Visual Studio Code – definice veřejné IP adresy](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)
4. Zopakujte poslední krok a vyhledejte referenční informace k šablonám pro ostatní prostředky definované v této šabloně.  Porovnejte definice prostředků s referenčními informacemi.
5. Rozbalte čtvrtý prostředek:

    ![Šablony Azure Resource Manageru ve Visual Studio Code – dependson](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    Element DependsOn umožňuje definovat jeden prostředek jako závislý na jednom nebo více prostředcích. V tomto příkladu se jedná o prostředek networkInterface.  Ten závisí na dvou dalších prostředcích:

    * publicIPAddress
    * virtualNetwork

6. Rozbalte pátý prostředek. Tento prostředek je virtuální počítač. Ten závisí na dvou dalších prostředcích:

    * storageAccount
    * networkInterface

Následující diagram znázorňuje prostředky a informace o závislostech pro tuto šablonu:

![Šablony Azure Resource Manageru ve Visual Studio Code – diagram](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

Určení závislostí umožňuje Resource Manageru účinně nasadit řešení. Paralelně nasadí účet úložiště, veřejnou IP adresu a virtuální síť, protože tyto prostředky nemají žádné závislosti. Po nasazení veřejné IP adresy a virtuální sítě se vytvoří síťové rozhraní. Po nasazení všech ostatních prostředků Resource Manager nasadí virtuální počítač.

## <a name="edit-the-parameters-file"></a>Úprava souboru parametrů

V souboru šablony není nutné provádět žádné změny. Je však potřeba upravit soubor parametrů, aby heslo správce načítal ze služby Key Vault.

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
    Nahraďte **id** za ID prostředku vaší služby Key Vault vytvořené předchozím postupem. Je jedním z výstupů. 

    ![Integrace služby Key Vault a nasazení virtuálního počítače šablony Resource Manageru – soubor parametrů](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. Zadejte hodnoty následujících parametrů:

    - **adminUsername:** název účtu správce virtuálního počítače
    - **dnsLabelPrefix:** název předpony názvu DNS
4. Uložte změny.

## <a name="deploy-the-template"></a>Nasazení šablony

Šablony můžete nasadit mnoha způsoby.  V tomto kurzu použijete Cloud Shell z webu Azure Portal.

1. Přihlaste se do služby [Cloud Shell](https://shell.azure.com). Můžete se také přihlásit k webu [Azure Portal](https://portal.azure.com) a pak v pravém horním rohu vybrat **Cloud Shell**, jak je znázorněno na tomto obrázku:

    ![Cloud Shell na portálu Azure Portal](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell.png)
2. V levém horním rohu služby Cloud Shell vyberte **PowerShell** a pak **Potvrdit**.  V tomto kurzu použijete prostředí PowerShell.
3. V Cloud Shellu vyberte **Nahrát soubor**:

    ![Nahrání souboru v Cloud Shellu na portálu Azure Portal](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
4. Vyberte soubory, které jste si v rámci tohoto kurzu uložili. Výchozí názvy jsou **azuredeploy.json** a **azuredeploy.paraemters.json**.  Pokud máte soubory se stejnými názvy, staré soubory se bez upozornění přepíší.
5. V Cloud Shellu spusťte následující příkaz, který ověří úspěšné nahrání souboru. 

    ```bash
    ls
    ```

    ![Zobrazení souboru v Cloud Shellu na portálu Azure Portal](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-list-file.png)

    Název souboru na snímku obrazovky je azuredeploy.json.

6. V Cloud Shellu spusťte následující příkaz, který ověří obsah souboru JSON:

    ```bash
    cat azuredeploy.json
    cat azuredeploy.parameters.json
    ```
7. Ve službě Cloud Shell spusťte následující příkazy PowerShellu. Ukázkový skript používá stejnou skupinu prostředků, který se vytvořila pro službu Key Vault. Použití stejné skupiny prostředků usnadňuje vyčištění prostředků.

    ```powershell
    $resourceGroupName = "<Enter the resource group name>"
    $deploymentName = "<Enter a deployment name>"

    New-AzureRmResourceGroupDeployment -Name $deploymentName `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile azuredeploy.json `
        -TemplateparameterFile azuredeploy.parameters.json
    ```
8. Spuštěním následujícího příkazu PowerShellu zobrazíte nově vytvořený virtuální počítač:

    ```powershell
    Get-AzureRmVM -Name SimpleWinVM -ResourceGroupName $resourceGroupName
    ```

    V šabloně je pevně zakódovaný název virtuálního počítače **SimpleWinVM**.

9. Přihlaste se k virtuálnímu počítači, abyste otestovali přihlašovací údaje správce. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků uvidíte celkem šest prostředků.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu vyvinete a nasadíte šablonu pro vytvoření virtuálního počítače, virtuální sítě a závislých prostředků. Informace o tom, jak nasazovat prostředky Azure na základě podmínek, najdete v tomto tématu:


> [!div class="nextstepaction"]
> [Použití podmínek](./resource-manager-tutorial-use-conditions.md)

