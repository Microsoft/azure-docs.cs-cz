---
title: Použití skriptů pro nasazení šablony | Dokumenty společnosti Microsoft
description: Přečtěte si, jak používat skripty nasazení v šablonách Azure Resource Manageru.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: carmonm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 94b351ddb18ca596f47e8ef40cff8229c838d7bd
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239209"
---
# <a name="tutorial-use-deployment-scripts-to-create-a-self-signed-certificate-preview"></a>Kurz: Vytvoření certifikátu podepsaného svým držitelem (Preview) pomocí skriptů nasazení

Zjistěte, jak používat skripty nasazení v šablonách Azure Resource Manage (ARM). Skripty nasazení lze použít k provedení vlastníkroky, které nelze provést pomocí šablony ARM. Například vytvoření certifikátu podepsaného svým držitelem.  V tomto kurzu vytvoříte šablonu pro nasazení trezoru `Microsoft.Resources/deploymentScripts` klíčů Azure a pak pomocí prostředku ve stejné šabloně vytvořte certifikát a pak přidáte certifikát do trezoru klíčů. Další informace o skriptu nasazení najdete [v tématu Použití skriptů nasazení v šablonách ARM](./deployment-script-template.md).

> [!IMPORTANT]
> Dva prostředky skriptu nasazení, účet úložiště a instance kontejneru, jsou vytvořeny ve stejné skupině prostředků pro spuštění skriptu a řešení potíží. Tyto prostředky jsou obvykle odstraněny službou skriptu při spuštění skriptu dostane do terminálového stavu. Prostředky se vám budou účtovat, dokud nebudou prostředky odstraněny. Další informace naleznete v [tématu Vyčištění prostředků skriptu nasazení](./deployment-script-template.md#clean-up-deployment-script-resources).

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Otevření šablony pro rychlý start
> * Úprava šablony
> * Nasazení šablony
> * Ladění neúspěšného skriptu
> * Vyčištění prostředků

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* ** [Visual Studio Kód](https://code.visualstudio.com/) s rozšířením Nástroje Správce prostředků**. Viz [Použití kódu Visual Studia k vytvoření šablon ARM](./use-vs-code-to-create-template.md).

* **Uživatelem přiřazená spravovaná identita s rolí přispěvatele na úrovni předplatného**. Tato identita se používá ke spuštění skriptů nasazení. Pokud chcete ji vytvořit, přečtěte si informace o [spravované identitě přiřazené uživateli](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity). Při nasazování šablony potřebujete ID identity. Formát identity je:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Pomocí následujícího skriptu prostředí PowerShell získáte ID zadáním názvu skupiny prostředků a názvu identity.

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  $id = (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name idName).Id
  ```

## <a name="open-a-quickstart-template"></a>Otevření šablony pro rychlý start

Šablonu nemusíte vytvářet od začátku, ale můžete místo toho otevřít některou [šablonu pro rychlý start Azure](https://azure.microsoft.com/resources/templates/). Azure Quickstart Templates je úložiště pro šablony ARM.

Šablona použitá v tomto rychlém startu se nazývá [Vytvoření trezoru klíčů Azure a tajný klíč](https://azure.microsoft.com/resources/templates/101-key-vault-create/). Šablona vytvoří trezor klíčů a přidá tajný klíč do trezoru klíčů.

1. V kódu sady Visual Studio vyberte **Soubor**>**otevřít soubor**.
2. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-key-vault-create/azuredeploy.json
    ```

3. Výběrem **Open** (Otevřít) soubor otevřete.
4. Vyberte **Soubor**>**Uložit jako,** chcete-li soubor uložit jako **azuredeploy.json** do místního počítače.

## <a name="edit-the-template"></a>Úprava šablony

Proveďte v šabloně následující změny:

### <a name="clean-up-the-template-optional"></a>Vyčištění šablony (volitelné)

Původní šablona přidá tajný klíč do trezoru klíčů.  Chcete-li kurz zjednodušit, odeberte následující zdroj:

* **Microsoft.KeyVault/trezory/tajné klíče**

Odeberte následující dvě definice parametrů:

* **tajný_název**
* **secretValue**

Pokud se rozhodnete tyto definice neodebírat, je třeba během nasazení zadat hodnoty parametrů.

### <a name="configure-the-key-vault-access-policies"></a>Konfigurace zásad přístupu trezoru klíčů

Skript nasazení přidá certifikát do trezoru klíčů. Nakonfigurujte zásady přístupu trezoru klíčů tak, aby udělit oprávnění ke spravované identity:

1. Přidáním parametru získáte ID spravované identity:

    ```json
    "identityId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the ID of the user-assigned managed identity."
      }
    },
    ```

    > [!NOTE]
    > Rozšíření šablony Správce prostředků kódu sady Visual Studio ještě není schopno formátovat skripty nasazení. Nepoužívejte [SHIFT]+[ALT]+F k formátování prostředků deploymentScripts, jako je následující.

1. Přidejte parametr pro konfiguraci zásad přístupu trezoru klíčů tak, aby spravovaná identita mohla přidávat certifikáty do trezoru klíčů.

    ```json
    "certificatesPermissions": {
      "type": "array",
      "defaultValue": [
        "get",
        "list",
        "update",
        "create"
      ],
      "metadata": {
      "description": "Specifies the permissions to certificates in the vault. Valid values are: all, get, list, update, create, import, delete, recover, backup, restore, manage contacts, manage certificate authorities, get certificate authorities, list certificate authorities, set certificate authorities, delete certificate authorities."
      }
    }
    ```

1. Aktualizujte existující zásady přístupu k trezoru klíčů na:

    ```json
    "accessPolicies": [
      {
        "objectId": "[parameters('objectId')]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      },
      {
        "objectId": "[reference(parameters('identityId'), '2018-11-30').principalId]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      }
    ],
    ```

    Existují dvě zásady definované, jeden pro přihlášený uživatel a druhý je pro spravovanou identitu.  Přihlášený uživatel potřebuje pouze oprávnění *k ověření* nasazení.  Pro zjednodušení kurzu je stejný certifikát přiřazen jak spravované identitě, tak přihlášeným uživatelům.

### <a name="add-the-deployment-script"></a>Přidání skriptu nasazení

1. Přidejte tři parametry, které jsou používány skriptnasazení.

    ```json
    "certificateName": {
      "type": "string",
      "defaultValue": "DeploymentScripts2019"
    },
    "subjectName": {
      "type": "string",
      "defaultValue": "CN=contoso.com"
    },
    "utcValue": {
      "type": "string",
      "defaultValue": "[utcNow()]"
    }
    ```

1. Přidejte prostředek deploymentScripts:

    > [!NOTE]
    > Vzhledem k tomu, že skripty vsazení jsou uzavřeny v uvozovkách, řetězce uvnitř skriptů nasazení musí být místo toho uzavřeny v jednoduchých uvozovkách. Řídicí znak pro prostředí PowerShell je **&#92;**.

    ```json
    {
      "type": "Microsoft.Resources/deploymentScripts",
      "apiVersion": "2019-10-01-preview",
      "name": "createAddCertificate",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
      ],
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[parameters('identityId')]": {
          }
        }
      },
      "kind": "AzurePowerShell",
      "properties": {
        "forceUpdateTag": "[parameters('utcValue')]",
        "azPowerShellVersion": "3.0",
        "timeout": "PT30M",
        "arguments": "[format(' -vaultName {0} -certificateName {1} -subjectName {2}', parameters('keyVaultName'), parameters('certificateName'), parameters('subjectName'))]", // can pass an argument string, double quotes must be escaped
        "scriptContent": "
          param(
            [string] [Parameter(Mandatory=$true)] $vaultName,
            [string] [Parameter(Mandatory=$true)] $certificateName,
            [string] [Parameter(Mandatory=$true)] $subjectName
          )

          $ErrorActionPreference = 'Stop'
          $DeploymentScriptOutputs = @{}

          $existingCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

          if ($existingCert -and $existingCert.Certificate.Subject -eq $subjectName) {

            Write-Host 'Certificate $certificateName in vault $vaultName is already present.'

            $DeploymentScriptOutputs['certThumbprint'] = $existingCert.Thumbprint
            $existingCert | Out-String
          }
          else {
            $policy = New-AzKeyVaultCertificatePolicy -SubjectName $subjectName -IssuerName Self -ValidityInMonths 12 -Verbose

            # private key is added as a secret that can be retrieved in the Resource Manager template
            Add-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName -CertificatePolicy $policy -Verbose

            $newCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

            # it takes a few seconds for KeyVault to finish
            $tries = 0
            do {
              Write-Host 'Waiting for certificate creation completion...'
              Start-Sleep -Seconds 10
              $operation = Get-AzKeyVaultCertificateOperation -VaultName $vaultName -Name $certificateName
              $tries++

              if ($operation.Status -eq 'failed')
              {
                throw 'Creating certificate $certificateName in vault $vaultName failed with error $($operation.ErrorMessage)'
              }

              if ($tries -gt 120)
              {
                throw 'Timed out waiting for creation of certificate $certificateName in vault $vaultName'
              }
            } while ($operation.Status -ne 'completed')

            $DeploymentScriptOutputs['certThumbprint'] = $newCert.Thumbprint
            $newCert | Out-String
          }
        ",
        "cleanupPreference": "OnSuccess",
        "retentionInterval": "P1D"
      }
    }
    ```

    Zdroj `deploymentScripts` závisí na prostředku trezoru klíčů a zdroji přiřazení role.  Má tyto vlastnosti:

    * **identita**: Skript nasazení používá k spuštění skriptů uživatelem přiřazenou spravovanou identitu.
    * **druh**: Zadejte typ skriptu. V současné době je podporován pouze skript prostředí PowerShell.
    * **forceUpdateTag**: Zjistěte, zda má být skript nasazení spuštěn i v případě, že se nezměnil zdroj skriptu. Může být aktuální časové razítko nebo IDENTIFIKÁTOR GUID. Další informace naleznete v [tématu Spuštění skriptu více než jednou](./deployment-script-template.md#run-script-more-than-once).
    * **azPowerShellVersion**: Určuje verzi modulu Azure PowerShell, která má být použita. V současné době podporuje skript nasazení verze 2.7.0, 2.8.0 a 3.0.0.
    * **Časový limit**: Zadejte maximální povolenou dobu spuštění skriptu zadanou ve [formátu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Výchozí hodnota je **P1D**.
    * **argumenty**: Zadejte hodnoty parametrů. Hodnoty jsou odděleny mezerami.
    * **scriptContent**: Zadejte obsah skriptu. Chcete-li spustit externí skript, použijte místo toho **primární jazykScriptURI.** Další informace naleznete v [tématu Použití externího skriptu](./deployment-script-template.md#use-external-scripts).
        Deklarování **$DeploymentScriptOutputs** je vyžadováno pouze při testování skriptu v místním počítači. Deklarování proměnné umožňuje spuštění skriptu v místním počítači a v prostředku deploymentScript bez nutnosti provádění změn. Hodnota přiřazená $DeploymentScriptOutputs je k dispozici jako výstupy v nasazeních. Další informace naleznete v [tématu Práce s výstupy ze skriptů nasazení prostředí PowerShell](./deployment-script-template.md#work-with-outputs-from-powershell-script) nebo [Práce s výstupy ze skriptů pro nasazení cli](./deployment-script-template.md#work-with-outputs-from-cli-script).
    * **cleanupPreference**: Zadejte předvolbu, kdy se mají odstranit prostředky skriptu nasazení.  Výchozí hodnota je **Always**, což znamená, že prostředky skriptu nasazení jsou odstraněny i přes stav terminálu (Proběhlo úspěšně, Nezdařilo se, zrušeno). V tomto kurzu **OnSuccess** se používá tak, že máte možnost zobrazit výsledky spuštění skriptu.
    * **retentionInterval**: Zadejte interval, pro který služba zachová prostředky skriptu po dosažení terminálového stavu. Prostředky budou po vypršení této doby trvání odstraněny. Doba trvání je založena na vzoru ISO 8601. Tento kurz používá P1D, což znamená jeden den.  Tato vlastnost se používá při **cleanupPreference** je nastavena **na OnExpiration**. Tato vlastnost není aktuálně povolena.

    Skript nasazení má tři parametry: název trezoru klíčů, název certifikátu a název subjektu.  Vytvoří certifikát a potom přidá certifikát do trezoru klíčů.

    **$DeploymentScriptOutputs** se používá k ukládání výstupní hodnoty.  Další informace najdete v [tématu Práce s výstupy ze skriptů nasazení prostředí PowerShell](./deployment-script-template.md#work-with-outputs-from-powershell-script) nebo [Práce s výstupy ze skriptů pro nasazení cli](./deployment-script-template.md#work-with-outputs-from-cli-script).

    Vyplněnou šablonu naleznete [zde](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json).

1. Chcete-li zobrazit proces ladění, umístěte chybu v kódu přidáním následujícího řádku do skriptu nasazení:

    ```powershell
    Write-Output1 $keyVaultName
    ```

    Správný příkaz je **Zápis-Výstup** namísto **zápisu-výstup1**.

1. Chcete-li soubor uložit, vyberte **Uložit**>**Save** soubor.

## <a name="deploy-the-template"></a>Nasazení šablony

Podívejte se na [část Nasazení šablony](./quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template) v rychlém startu kódu visual studia pro otevření prostředí Cloud a nahrání souboru šablony do prostředí. A pak spusťte následující skript Prostředí PowerShell:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
$identityId = Read-Host -Prompt "Enter the user-assigned managed identity ID"

$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$resourceGroupName = "${projectName}rg"
$keyVaultName = "${projectName}kv"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -identityId $identityId -keyVaultName $keyVaultName -objectId $adUserId

Write-Host "Press [ENTER] to continue ..."
```

Služba skriptu nasazení musí vytvořit další prostředky skriptu nasazení pro spuštění skriptu. Příprava a proces čištění může trvat až jednu minutu k dokončení kromě skutečného času spuštění skriptu.

Nasazení se nezdařilo z důvodu neplatného **příkazu, zápis-výstup1** se používá ve skriptu. Dostanete chybu, která říká:

```error
The term 'Write-Output1' is not recognized as the name of a cmdlet, function, script file, or operable
program.\nCheck the spelling of the name, or if a path was included, verify that the path is correct and try again.\n
```

Výsledek spuštění skriptu nasazení je uložen v prostředcích skriptu nasazení pro účely řešení potíží.

## <a name="debug-the-failed-script"></a>Ladění neúspěšného skriptu

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Otevřete skupinu prostředků. Jedná se o název projektu s **rg** připojen. Zobrazí se dva další prostředky ve skupině prostředků. Tyto prostředky jsou označovány jako *prostředky skriptu nasazení*.

    ![Prostředky skriptu nasazení šablony Správce prostředků Správce prostředků](./media/template-tutorial-deployment-script/resource-manager-template-deployment-script-resources.png)

    Oba soubory mají příponu **azscripts.** Jeden je účet úložiště a druhý je instance kontejneru.

    Výběrem **možnosti Zobrazit skryté typy** zobrazíte seznam prostředků jazyka deploymentScripts.

1. Vyberte účet úložiště s příponou **azscripts.**
1. Vyberte dlaždici **Sdílení souborů.** Zobrazí se složka **azscripts.**  Složka obsahuje soubory spuštění skriptu nasazení.
1. Vyberte **azscripts**. Zobrazí se dvě složkyL **azscriptinput** a **azscriptoutput**.  Vstupní složka obsahuje soubor skriptu systému PowerShell a soubory skriptu pro nasazení uživatele. Výstupní složka obsahuje **executionresult.json** a výstupní soubor skriptu. Zobrazí se chybová zpráva v **executionresult.json**. Výstupní soubor není k dispozici, protože spuštění se nezdařilo.

Odeberte řádek **Write-Output1** a znovu nasadit šablonu.

Při úspěšném spuštění druhého nasazení musí být prostředky skriptu nasazení odebrány službou skriptu, protože vlastnost **cleanupPreference** je nastavena **na hodnotu OnSuccess**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure vyberte **skupinu prostředků** z levé nabídky.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků uvidíte celkem šest prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků.**

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili používat skript nasazení v šablonách ARM. Informace o tom, jak nasazovat prostředky Azure na základě podmínek, najdete v tomto tématu:

> [!div class="nextstepaction"]
> [Použití podmínek](./template-tutorial-use-conditions.md)
