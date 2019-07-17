---
title: Odeslání e-mailu z runbooku Azure Automation
description: Zjistěte, jak SendGrid použít k odesílání e-mailu z v rámci sady runbook.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 07/15/2019
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: ce05aadb53cc3ad24ed65ea139594010e1aef047
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234980"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>Kurz: Odeslání e-mailu z runbooku Azure Automation

Odešlete e-mailu ze sady runbook s [SendGrid](https://sendgrid.com/solutions) pomocí Powershellu. Tomto kurzu se dozvíte, jak vytvořit opakovaně použitelné sady runbook, která odešle e-mail pomocí klíče rozhraní API, která jsou uložená v [Azure Key Vaultu](/azure/key-vault/).

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Vytvoření Azure KeyVault
> * Store svůj klíč rozhraní API SendGrid v trezoru klíčů
> * Vytvoření sady runbook, který načte svůj klíč rozhraní API a odešle e-mailu

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující:

* Předplatné Azure: Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Vytvoření účtu SendGrid](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Účet Automation](automation-offering-get-started.md) s **Az** modulů, a [připojení spustit jako](automation-create-runas-account.md), abyste mohli uložit a spustit sadu runbook.

## <a name="create-an-azure-keyvault"></a>Vytvoření Azure KeyVault

Můžete vytvořit Azure Key Vaultu, pomocí následujícího skriptu prostředí PowerShell. Hodnoty proměnné nahraďte hodnotami, které jsou specifické pro vaše prostředí. Použití vložených Azure Cloud Shell pomocí <kbd>vyzkoušet</kbd> tlačítko, které se nachází v pravém horním rohu bloku kódu. Můžete také zkopírovat a spouštění kódu místně, pokud máte [modul Azure PowerShell](/powershell/azure/install-az-ps) nainstalované na místním počítači.

> [!NOTE]
> Načíst klíč rozhraní API, použijte postup v [najít svůj klíč rozhraní API SendGrid](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key).

```azurepowershell-interactive
$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
# If you omit the SubscriptionId parameter, the default subscription is selected.
Connect-AzAccount -SubscriptionId $SubscriptionId

# Use Get-AzLocation to see your available locations.
$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "<Enter a universally unique vault name>"
$SendGridAPIKey = "<SendGrid API key>"
$AutomationAccountName = "testaa"

# Create new Resource Group, or omit this step if you already have a resource group.
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region
$resourceId = $newKeyVault.ResourceId

# Convert the SendGrid API key into a SecureString
$Secret = ConvertTo-SecureString -String $SendGridAPIKey -AsPlainText -Force
Set-AzKeyVaultSecret -VaultName $VaultName -Name 'SendGridAPIKey' -SecretValue $Secret

# Grant access to the KeyVault to the Automation RunAs account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

Další možnosti pro vytvoření Azure Key Vaultu a uložíte tajný klíč, najdete v článku [rychlých startů KeyVault](/azure/key-vault/).

## <a name="import-required-modules-to-your-automation-account"></a>Importujte požadované moduly do vašeho účtu Automation

Použití Azure Key Vaultu v rámci sady runbook, bude nutné vašeho účtu Automation následující moduly:

* [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile).
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault).

Klikněte na tlačítko <kbd>nasadit do Azure Automation</kbd> na kartě Azure Automation v části Možnosti instalace. Tato akce otevře Azure Portal. Na stránce Import vyberte svůj účet Automation a klikněte na tlačítko <kbd>OK</kbd>.

Další metody pro přidávání požadované moduly, naleznete v tématu [importovat moduly](/azure/automation/shared-resources/modules#import-modules).

## <a name="create-the-runbook-to-send-an-email"></a>Vytvoření runbooku k odesílání e-mailu

Poté, co jste vytvořili trezor klíčů a uložili svůj klíč rozhraní API SendGrid, je čas vytvořit runbook, který načte klíč rozhraní API a odeslat e-mailu.

Tato sada runbook používá AzureRunAsConnection [účet Spustit jako](automation-create-runas-account.md) k ověřování pomocí Azure k načtení tajného klíče z Azure Key Vaultu.

Použijte tento příklad k vytvoření sady runbook volá **odeslat GridMailMessage**. Můžete upravit skript prostředí PowerShell a znovu ho použít pro různé scénáře.

1. Přejděte ke svému účtu Azure Automation.
2. V části **automatizace procesů**vyberte **sady Runbook**.
3. V horní části seznamu sad runbook, vyberte **+ vytvořit runbook**.
4. Na **přidat Runbook** zadejte **odeslat GridMailMessage** pro název sady runbook. Typ runbooku, vyberte **Powershellu**. Potom vyberte **Create** (Vytvořit).
   ![Vytvoření Runbooku](./media/automation-send-email/automation-send-email-runbook.png)
5. Runbook se vytvoří a otevře se stránka **Upravit powershellový runbook**.
   ![Upravit sadu Runbook](./media/automation-send-email/automation-send-email-edit.png)
6. Zkopírujte následující příklad Powershellu do **upravit** stránky. Ujistěte se, `$VaultName` je název, který jste zadali při vytváření vašeho trezoru klíčů.

    ```powershell-interactive
    Param(
      [Parameter(Mandatory=$True)]
      [String] $destEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $fromEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $subject,
      [Parameter(Mandatory=$True)]
      [String] $content
    )

    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Out-Null
    $VaultName = "<Enter your vault name>"
    $SENDGRID_API_KEY = (Get-AzKeyVaultSecret -VaultName $VaultName -Name "SendGridAPIKey").SecretValueText
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer " + $SENDGRID_API_KEY)
    $headers.Add("Content-Type", "application/json")

    $body = @{
    personalizations = @(
        @{
            to = @(
                    @{
                        email = $destEmailAddress
                    }
            )
        }
    )
    from = @{
        email = $fromEmailAddress
    }
    subject = $subject
    content = @(
        @{
            type = "text/plain"
            value = $content
        }
    )
    }

    $bodyJson = $body | ConvertTo-Json -Depth 4

    $response = Invoke-RestMethod -Uri https://api.sendgrid.com/v3/mail/send -Method Post -Headers $headers -Body $bodyJson
    ```

7. Vyberte **publikovat** uložte a publikujte runbook.

Chcete-li ověřit, že se runbook úspěšně spustí provedením kroků v části [otestování sady runbook](manage-runbooks.md#test-a-runbook) nebo [spuštění sady runbook](start-runbooks.md).
Pokud se zpočátku nezobrazí testovací e-mail, zkontrolujte vaši **nevyžádané** a **nevyžádané pošty** složek.

## <a name="clean-up"></a>Vyčistit

Pokud už runbook nepotřebujete, odstraňte ho. Provedete to tak, že v seznamu runbooků vyberete příslušný runbook a kliknete na **Odstranit**.

Odstranění trezoru klíčů pomocí [Remove-AzureRMKeyVault](/powershell/module/azurerm.keyvault/remove-azurermkeyvault?view=azurermps) rutiny.

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Další postup

* Problémy s vytvořením nebo spuštění runbooku, naleznete v tématu [řešení potíží se sadami runbook](./troubleshoot/runbooks.md).
* Aktualizace modulů ve vašem účtu Automation, najdete v článku [aktualizace modulů Azure Powershellu ve službě Azure Automation](automation-update-azure-modules.md)].
* Monitorování spuštění sady runbook najdete v tématu [dál stavu úlohy a datové proudy úlohy ze služby Automation na protokoly Azure monitoru](automation-manage-send-joblogs-log-analytics.md).
* K aktivaci sady runbook pomocí upozornění, najdete v článku [pomocí upozornění můžete aktivovat runbooku Azure Automation](automation-create-alert-triggered-runbook.md).
