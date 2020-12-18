---
title: Použití skriptů nasazení šablony | Microsoft Docs
description: Naučte se používat skripty pro nasazení v šablonách Azure Resource Manager (šablony ARM).
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 12/14/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7352bde887648918cbfd2a9ebeaae83cddefc61e
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673274"
---
# <a name="tutorial-use-deployment-scripts-to-create-a-self-signed-certificate"></a>Kurz: vytvoření certifikátu podepsaného svým držitelem pomocí skriptů pro nasazení

Naučte se používat skripty pro nasazení v šablonách Azure Resource Manager (šablony ARM). Skripty pro nasazení lze použít k provedení vlastních kroků, které nelze provést pomocí šablon ARM. Například vytvoření certifikátu podepsaného svým držitelem. V tomto kurzu vytvoříte šablonu pro nasazení trezoru klíčů Azure a pak pomocí `Microsoft.Resources/deploymentScripts` prostředku ve stejné šabloně vytvoříte certifikát a pak certifikát přidáte do trezoru klíčů. Další informace o skriptu nasazení najdete v tématu [použití skriptů nasazení v šablonách ARM](./deployment-script-template.md).

> [!IMPORTANT]
> Dva prostředky skriptu nasazení, účet úložiště a instance kontejneru, se vytvoří ve stejné skupině prostředků ke spuštění skriptu a odstraňování potíží. Tyto prostředky obvykle odstraní služba skriptu, když se spuštění skriptu dostane do stavu terminálu. Budou se vám účtovat prostředky, dokud se prostředky neodstraní. Další informace najdete v tématu [vyčištění prostředků skriptu nasazení](./deployment-script-template.md#clean-up-deployment-script-resources).

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Otevření šablony rychlého startu
> * Úprava šablony
> * Nasazení šablony
> * Ladění neúspěšného skriptu
> * Vyčištění prostředků

Microsoft Learn modul, který pokrývá skripty nasazení, najdete v tématu věnovaném [rozšiřování šablon ARM pomocí skriptů nasazení](/learn/modules/extend-resource-manager-template-deployment-scripts/).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* **[Visual Studio Code](https://code.visualstudio.com/) s rozšířením nástroje Správce prostředků Tools**. Další informace najdete v tématu [rychlý Start: vytvoření šablon ARM pomocí Visual Studio Code](./quickstart-create-templates-use-visual-studio-code.md).

* **Spravovaná identita přiřazená uživatelem s rolí přispěvatele na úrovni předplatného**. Tato identita se používá ke spouštění skriptů nasazení. Pokud ho chcete vytvořit, přečtěte si téma [spravovaná identita přiřazená uživatelem](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). ID identity budete potřebovat při nasazení šablony. Formát identity je:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Pomocí následujícího skriptu CLI Získejte ID zadáním názvu skupiny prostředků a názvu identity.

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  az identity list -g $resourceGroupName
  ```

## <a name="open-a-quickstart-template"></a>Otevření šablony pro rychlý start

Šablonu nemusíte vytvářet od začátku, ale můžete místo toho otevřít některou [šablonu pro rychlý start Azure](https://azure.microsoft.com/resources/templates/). Šablony pro rychlý Start Azure jsou úložiště pro šablony ARM.

Šablona použitá v tomto rychlém startu se nazývá [vytvořit Azure Key Vault a tajný klíč](https://azure.microsoft.com/resources/templates/101-key-vault-create/). Šablona vytvoří Trezor klíčů a potom do trezoru klíčů přidá tajný klíč.

1. Z Visual Studio Code vyberte **soubor**  >  **otevřít soubor**.
2. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-key-vault-create/azuredeploy.json
    ```

3. Výběrem **Open** (Otevřít) soubor otevřete.
4. Vyberte **soubor**  >  **Uložit jako** a uložte soubor jako _azuredeploy.js_ do svého místního počítače.

## <a name="edit-the-template"></a>Úprava šablony

Proveďte následující změny šablony:

### <a name="clean-up-the-template-optional"></a>Vyčištění šablony (volitelné)

Původní šablona přidá do trezoru klíčů tajný klíč. Pro zjednodušení tohoto kurzu odeberte následující prostředek:

* `Microsoft.KeyVault/vaults/secrets`

Odeberte následující definice dvou parametrů:

* `secretName`
* `secretValue`

Pokud se rozhodnete tyto definice neodebírat, je nutné zadat hodnoty parametrů během nasazování.

### <a name="configure-the-key-vault-access-policies"></a>Konfigurace zásad přístupu trezoru klíčů

Skript nasazení přidá certifikát do trezoru klíčů. Konfigurace zásad přístupu trezoru klíčů pro udělení oprávnění spravované identitě:

1. Přidejte parametr pro získání ID spravované identity:

    ```json
    "identityId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the ID of the user-assigned managed identity."
      }
    },
    ```

    > [!NOTE]
    > Rozšíření Správce prostředků šablon Visual Studio Code nemůže zatím naformátovat skripty nasazení. Nepoužívejte SHIFT + ALT + F k naformátování `deploymentScripts` prostředků, jako je třeba následující.

1. Přidejte parametr pro konfiguraci zásad přístupu trezoru klíčů tak, aby spravovaná identita mohla přidávat certifikáty do trezoru klíčů:

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

1. Aktualizujte stávající zásady přístupu trezoru klíčů na:

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

    Jsou definovány dvě zásady, jeden pro přihlášeného uživatele a druhý pro spravovanou identitu. Přihlášený uživatel potřebuje jenom oprávnění *seznam* pro ověření nasazení. Pro zjednodušení tohoto kurzu se ke spravované identitě a přihlášeným uživatelům přiřadí stejný certifikát.

### <a name="add-the-deployment-script"></a>Přidání skriptu nasazení

1. Přidejte tři parametry, které používá skript nasazení:

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

1. Přidat `deploymentScripts` prostředek:

    > [!NOTE]
    > Vzhledem k tomu, že vložené skripty pro nasazení jsou uzavřeny do dvojitých uvozovek, řetězce v skriptech nasazení musí být místo toho uzavřeny v jednoduchých uvozovkách. [Řídicí znak prostředí PowerShell je zpětný](/powershell/module/microsoft.powershell.core/about/about_quoting_rules#single-and-double-quoted-strings) impuls ( `` ` `` ).

    ```json
    {
      "type": "Microsoft.Resources/deploymentScripts",
      "apiVersion": "2020-10-01",
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

    `deploymentScripts`Prostředek závisí na prostředku trezoru klíčů a prostředku přiřazení role. Má tyto vlastnosti:

    * `identity`: Skript nasazení používá ke spouštění skriptů spravovanou identitu přiřazenou uživatelem.
    * `kind`: Zadejte typ skriptu. V současné době jsou podporovány pouze skripty prostředí PowerShell.
    * `forceUpdateTag`: Určete, jestli se má skript nasazení spustit i v případě, že se nezměnil zdroj skriptu. Může to být aktuální časové razítko nebo identifikátor GUID. Další informace najdete v tématu [spuštění skriptu více než jednou](./deployment-script-template.md#run-script-more-than-once).
    * `azPowerShellVersion`: Určuje verzi modulu Azure PowerShell, která se má použít. Skript nasazení v současné době podporuje verze 2.7.0, 2.8.0 a 3.0.0.
    * `timeout`: Zadejte maximální povolenou dobu spuštění skriptu určenou ve [formátu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Výchozí hodnota je **P1D**.
    * `arguments`: Zadejte hodnoty parametrů. Hodnoty jsou oddělené mezerami.
    * `scriptContent`: Zadejte obsah skriptu. Pokud chcete spustit externí skript, použijte `primaryScriptURI` místo toho. Další informace najdete v tématu [použití externího skriptu](./deployment-script-template.md#use-external-scripts).
        Deklarace `$DeploymentScriptOutputs` se vyžaduje jenom při testování skriptu na místním počítači. Deklarování proměnné umožňuje spustit skript v místním počítači a v `deploymentScript` prostředku bez nutnosti provádět změny. Hodnota přiřazená k `$DeploymentScriptOutputs` je k dispozici jako výstupy v nasazeních. Další informace najdete v tématu [práce s výstupy ze skriptů nasazení PowerShellu](./deployment-script-template.md#work-with-outputs-from-powershell-script) nebo [práce s výstupy ze skriptů nasazení CLI](./deployment-script-template.md#work-with-outputs-from-cli-script).
    * `cleanupPreference`: Zadejte prioritu při odstraňování prostředků skriptu nasazení. Výchozí hodnota je **vždycky**, což znamená, že prostředky skriptu nasazení se odstraní navzdory stavu terminálu (úspěšné, neúspěšné, zrušené). V tomto kurzu se používá **úspěch** , abyste získali možnost Zobrazit výsledky spuštění skriptu.
    * `retentionInterval`: Zadejte interval, po který služba uchovává prostředky skriptu po dosažení stavu terminálu. Prostředky budou odstraněny po uplynutí této doby trvání. Doba trvání vychází ze vzoru ISO 8601. V tomto kurzu se používá **P1D**, což znamená jeden den. Tato vlastnost se používá v případě, že `cleanupPreference` je nastavena na hodnotu- **vypršení platnosti**. Tato vlastnost není aktuálně povolena.

    Skript nasazení používá tři parametry: `keyVaultName` , `certificateName` a `subjectName` . Vytvoří certifikát a pak certifikát přidá do trezoru klíčů.

    `$DeploymentScriptOutputs` slouží k uložení výstupní hodnoty. Další informace najdete v tématu [práce s výstupy ze skriptů nasazení PowerShellu](./deployment-script-template.md#work-with-outputs-from-powershell-script) nebo [práce s výstupy ze skriptů nasazení CLI](./deployment-script-template.md#work-with-outputs-from-cli-script).

    Dokončenou šablonu najdete [tady](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json).

1. Chcete-li zobrazit proces ladění, vložte chybu do kódu přidáním následujícího řádku do skriptu nasazení:

    ```powershell
    Write-Output1 $keyVaultName
    ```

    Správný příkaz je `Write-Output` místo `Write-Output1` .

1. Vyberte **soubor**  >  **Uložit** a soubor uložte.

## <a name="deploy-the-template"></a>Nasazení šablony

1. Přihlaste se k [Azure Cloud Shell](https://shell.azure.com)

1. Vyberte prostředí **PowerShell** nebo **bash** (pro CLI) v levém horním rohu a zvolte preferované prostředí. Po přepnutí se vyžaduje restartování prostředí.

    ![Azure Portal Cloud Shell nahrát soubor](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Vyberte **Nahrát nebo stáhnout soubory** a potom vyberte **Nahrát**. Viz předchozí snímek obrazovky.  Vyberte soubor, který jste uložili v předchozí části. Po nahrání souboru můžete pomocí `ls` příkazu a `cat` příkazu ověřit, jestli se soubor úspěšně nahrál.

1. Spuštěním následujícího skriptu PowerShellu nasaďte šablonu.

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

    Služba skriptu nasazení potřebuje vytvořit další prostředky skriptu nasazení pro provedení skriptu. Příprava a proces čištění může trvat až jednu minutu, než se dokončí Skutečná doba spuštění skriptu.

    Nasazení se nezdařilo, protože `Write-Output1` ve skriptu je použit neplatný příkaz. Zobrazí se chybová zpráva:

    ```error
    The term 'Write-Output1' is not recognized as the name of a cmdlet, function, script file, or operable
    program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.
    ```

    Výsledek spuštění skriptu nasazení je uložen do prostředků skriptu nasazení pro účely řešení potíží.

## <a name="debug-the-failed-script"></a>Ladění neúspěšného skriptu

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Otevřete skupinu prostředků. Je to název projektu s připojením **RG** . Ve skupině prostředků se zobrazí dva další prostředky. Tyto prostředky se označují jako *prostředky skriptu nasazení*.

    ![Prostředky skriptu pro nasazení Správce prostředků šablon](./media/template-tutorial-deployment-script/resource-manager-template-deployment-script-resources.png)

    Oba soubory mají příponu **azscripts** . Jedním z nich je účet úložiště a druhý je instance kontejneru.

    Vyberte **Zobrazit skryté typy** a seznam `deploymentScripts` prostředku.

1. Vyberte účet úložiště s příponou **azscripts** .
1. Vyberte dlaždici **sdílení souborů** . Zobrazí se složka **azscripts** . Složka obsahuje spouštěcí soubory skriptu nasazení.
1. Vyberte **azscripts**. Zobrazí se dvě složky **azscriptinput** a **azscriptoutput**. Vstupní složka obsahuje systémový soubor skriptu PowerShellu a soubory skriptu nasazení uživatele. Výstupní složka obsahuje _executionresult.js_ a výstupní soubor skriptu. V _executionresult.js_ se zobrazí chybová zpráva. Výstupní soubor není k dispozici, protože spuštění selhalo.

Odeberte `Write-Output1` řádek a znovu nasaďte šablonu.

Po úspěšném spuštění druhého nasazení služba skriptu odebere prostředky skriptu nasazení, protože `cleanupPreference` vlastnost je nastavena na hodnotu **úspěch**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  V rámci skupiny prostředků se zobrazí celkem šest prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak používat skript nasazení v šablonách ARM. Informace o tom, jak nasazovat prostředky Azure na základě podmínek, najdete v tomto tématu:

> [!div class="nextstepaction"]
> [Použití podmínek](./template-tutorial-use-conditions.md)
