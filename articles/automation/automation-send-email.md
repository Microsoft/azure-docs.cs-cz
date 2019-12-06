---
title: Odeslání e-mailu z Azure Automation Runbooku
description: Naučte se používat SendGrid k posílání e-mailů v rámci Runbooku.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 07/15/2019
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 8550635b581eb944719c39cc8c195859a2c9e868
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849611"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>Kurz: odeslání e-mailu z Azure Automation Runbook

Pomocí prostředí PowerShell můžete odeslat e-mail z Runbooku pomocí [SendGrid](https://sendgrid.com/solutions) . V tomto kurzu se dozvíte, jak vytvořit opakovaně použitelný Runbook, který odešle e-mail pomocí klíče rozhraní API uloženého v [úložišti klíčů Azure](/azure/key-vault/).

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Vytvoření trezoru klíčů Azure
> * Uložení klíče rozhraní API SendGrid do trezoru klíčů
> * Vytvoření Runbooku, který načte klíč rozhraní API a pošle e-mail

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete následující:

* Předplatné Azure: Pokud ho ještě nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Vytvořte účet SendGrid](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Účet služby Automation](automation-offering-get-started.md) pomocí příkazu **AZ** modules a [Spustit jako připojení](automation-create-runas-account.md)pro uložení a spuštění sady Runbook.

## <a name="create-an-azure-keyvault"></a>Vytvoření trezoru klíčů Azure

Pomocí následujícího skriptu PowerShellu můžete vytvořit Trezor klíčů Azure. Nahraďte hodnoty proměnných hodnotami, které jsou specifické pro vaše prostředí. Použijte vložený Azure Cloud Shell přes tlačítko <kbd>vyzkoušet</kbd> , které se nachází v pravém horním rohu bloku kódu. Kód můžete také zkopírovat a spustit místně, pokud je v místním počítači nainstalován [modul Azure PowerShell](/powershell/azure/install-az-ps) .

> [!NOTE]
> Pokud chcete načíst svůj klíč rozhraní API, použijte postup uvedený v části [vyhledání klíče rozhraní API SendGrid](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key).

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

Další způsoby vytvoření trezoru klíčů Azure a uložení tajného klíče najdete v tématu [rychlý Start k trezoru](/azure/key-vault/)klíčů.

## <a name="import-required-modules-to-your-automation-account"></a>Importujte požadované moduly do svého účtu Automation.

Pokud chcete používat Azure webtrezor v rámci sady Runbook, váš účet Automation bude potřebovat následující moduly:

* [AZ. Profile](https://www.powershellgallery.com/packages/Az.Profile).
* [AZ. klíčů trezor](https://www.powershellgallery.com/packages/Az.KeyVault).

Klikněte na <kbd>nasadit a Azure Automation</kbd> na kartě Azure Automation v části Možnosti instalace. Tato akce otevře Azure Portal. Na stránce Import vyberte svůj účet Automation a klikněte na <kbd>OK</kbd>.

Další metody pro přidání požadovaných modulů naleznete v tématu [Import modulů](/azure/automation/shared-resources/modules#import-modules).

## <a name="create-the-runbook-to-send-an-email"></a>Vytvoření Runbooku k odeslání e-mailu

Po vytvoření trezoru klíčů a uložení klíče rozhraní API SendGrid je čas vytvořit Runbook, který načte klíč rozhraní API a pošle e-mail.

Tato sada Runbook používá [účet Spustit jako](automation-create-runas-account.md) AzureRunAsConnection k ověřování pomocí Azure pro načtení tajného klíče z Azure webtrezoru.

Tento příklad slouží k vytvoření Runbooku s názvem **Send-GridMailMessage**. Můžete upravit skript prostředí PowerShell a znovu ho použít pro různé scénáře.

1. Přejít na účet Azure Automation.
2. V části **Automatizace procesu**vyberte **Runbooky**.
3. V horní části seznamu sad Runbook vyberte **+ vytvořit Runbook**.
4. Na stránce **Přidat sadu Runbook** zadejte do pole název sady Runbook **odeslání-GridMailMessage** . Jako typ Runbooku vyberte **PowerShell**. Potom vyberte **Create** (Vytvořit).
   ![vytvoření sady Runbook](./media/automation-send-email/automation-send-email-runbook.png)
5. Runbook se vytvoří a otevře se stránka **Upravit powershellový runbook**.
   ![upravit sadu Runbook](./media/automation-send-email/automation-send-email-edit.png)
6. Zkopírujte následující příklad PowerShellu do stránky pro **Úpravy** . Ujistěte se, že `$VaultName` je název, který jste zadali při vytváření trezoru klíčů.

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

7. Vyberte **publikovat** a Runbook uložte a publikujte.

Chcete-li ověřit, zda se sada Runbook úspěšně spustí, můžete postupovat podle kroků v části [test sady Runbook](manage-runbooks.md#test-a-runbook) nebo [spuštění sady Runbook](start-runbooks.md).
Pokud se vám v úvodním e-mailu nezobrazuje testovací e-mail, zkontrolujte složky **nevyžádanou** **poštou a spam** .

## <a name="clean-up"></a>Vyčistit

Pokud už runbook nepotřebujete, odstraňte ho. Provedete to tak, že v seznamu runbooků vyberete příslušný runbook a kliknete na **Odstranit**.

Odstraňte Trezor klíčů pomocí rutiny [Remove-AzureRMKeyVault](/powershell/module/azurerm.keyvault/remove-azurermkeyvault?view=azurermps) .

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Další kroky

* Problémy při vytváření nebo spouštění Runbooku najdete v tématu [řešení chyb pomocí runbooků](./troubleshoot/runbooks.md).
* Pokud chcete aktualizovat moduly v účtu Automation, přečtěte si téma [jak aktualizovat Azure PowerShell moduly v Azure Automation](automation-update-azure-modules.md)].
* Pokud chcete monitorovat provádění sady Runbook, přečtěte si téma [o stavu úlohy a datových proudech úloh od automatizace po Azure monitor protokolů](automation-manage-send-joblogs-log-analytics.md).
* Postup aktivace sady Runbook pomocí výstrahy naleznete v tématu [použití výstrahy ke spuštění sady runbook Azure Automation](automation-create-alert-triggered-runbook.md).
