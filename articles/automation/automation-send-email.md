---
title: Odeslání e-mailu z Azure Automation Runbooku
description: Naučte se používat SendGrid k posílání e-mailů v rámci Runbooku.
services: automation
ms.subservice: process-automation
ms.date: 07/15/2019
ms.topic: tutorial
ms.openlocfilehash: d4b35458c76da82b33dfcb530cfdc71ee3da3bb6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81604784"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>Kurz: odeslání e-mailu z Azure Automation Runbook

Pomocí prostředí PowerShell můžete odeslat e-mail z Runbooku pomocí [SendGrid](https://sendgrid.com/solutions) . V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Vytvořte Azure Key Vault.
> * Klíč `SendGrid` rozhraní API uložte do trezoru klíčů.
> * Vytvořte opakovaně použitelný Runbook, který načte klíč rozhraní API a pošle e-mail pomocí klíče rozhraní API uloženého v [Azure Key Vault](/azure/key-vault/).

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci nástroje AZ Module Hybrid Runbook Worker najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pro váš účet Automation můžete aktualizovat moduly na nejnovější verzi pomocí [postupu aktualizace modulů Azure PowerShell v Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující:

* Předplatné Azure: Pokud ho ještě nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Vytvořte účet SendGrid](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Účet služby Automation](automation-offering-get-started.md) pomocí příkazu **AZ** modules a [Spustit jako připojení](automation-create-runas-account.md)pro uložení a spuštění sady Runbook.

## <a name="create-an-azure-key-vault"></a>Vytvoření služby Azure Key Vault

Azure Key Vault můžete vytvořit pomocí následujícího skriptu PowerShellu. Nahraďte hodnoty proměnných hodnotami, které jsou specifické pro vaše prostředí. Použijte vložený Azure Cloud Shell přes tlačítko **vyzkoušet** , které se nachází v pravém horním rohu bloku kódu. Kód můžete také zkopírovat a spustit místně, pokud je v místním počítači nainstalován [modul Azure PowerShell](/powershell/azure/install-az-ps) .

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

# Grant access to the Key Vault to the Automation Run As account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

Další způsoby vytvoření Azure Key Vault a uložení tajného klíče najdete v tématu [Key Vault rychlé starty](/azure/key-vault/).

## <a name="import-required-modules-to-your-automation-account"></a>Importujte požadované moduly do svého účtu Automation.

Pokud chcete použít Azure Key Vault v rámci sady Runbook, váš účet Automation potřebuje následující moduly:

* [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile)
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)

Klikněte na **nasadit a Azure Automation** na kartě Azure Automation v části **Možnosti instalace**. Tato akce otevře Azure Portal. Na stránce Import vyberte svůj účet Automation a klikněte na **OK**.

Další metody pro přidání požadovaných modulů naleznete v tématu [Import modulů](/azure/automation/shared-resources/modules#importing-modules).

## <a name="create-the-runbook-to-send-an-email"></a>Vytvoření Runbooku k odeslání e-mailu

Po vytvoření trezoru klíčů a uložení klíče `SendGrid` rozhraní API je čas vytvořit Runbook, který načte klíč rozhraní API a pošle e-mail.

Tato sada Runbook `AzureRunAsConnection` používá jako [účet Spustit jako](automation-create-runas-account.md) k ověření pomocí Azure pro načtení tajného klíče z Azure Key Vault.

Tento příklad slouží k vytvoření Runbooku s názvem **Send-GridMailMessage**. Můžete upravit skript prostředí PowerShell a znovu ho použít pro různé scénáře.

1. Přejít na účet Azure Automation.
2. V části **Automatizace procesu**vyberte **Runbooky**.
3. V horní části seznamu sad Runbook vyberte **+ vytvořit Runbook**.
4. Na stránce **Přidat sadu Runbook** zadejte do pole název sady Runbook **odeslání-GridMailMessage** . Jako typ Runbooku vyberte **PowerShell**. Potom vyberte **Create** (Vytvořit).
   ![Vytvořit Runbook](./media/automation-send-email/automation-send-email-runbook.png)
5. Runbook se vytvoří a otevře se stránka **Upravit powershellový runbook**.
   ![Upravit Runbook](./media/automation-send-email/automation-send-email-edit.png)
6. Zkopírujte následující příklad PowerShellu do stránky pro **Úpravy** . Ujistěte se, `$VaultName` že je název, který jste zadali při vytváření trezoru klíčů.

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

Chcete-li ověřit, zda se sada Runbook úspěšně spustí, můžete postupovat podle kroků v části [test sady Runbook](manage-runbooks.md#testing-a-runbook) nebo [spuštění sady Runbook](start-runbooks.md).
Pokud se vám v úvodním e-mailu nezobrazuje testovací e-mail, zkontrolujte složky **nevyžádanou** **poštou a spam** .

## <a name="clean-up"></a>Vyčištění

Pokud už runbook nepotřebujete, odstraňte ho. Provedete to tak, že v seznamu runbooků vyberete příslušný runbook a kliknete na **Odstranit**.

Odstraňte Trezor klíčů pomocí rutiny [Remove-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/remove-azkeyvault?view=azps-3.7.0) .

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Další kroky

* Problémy při vytváření nebo spouštění Runbooku najdete v tématu [řešení chyb pomocí runbooků](./troubleshoot/runbooks.md).
* Pokud chcete aktualizovat moduly v účtu Automation, přečtěte si téma [jak aktualizovat Azure PowerShell moduly v Azure Automation](automation-update-azure-modules.md)].
* Pokud chcete monitorovat provádění sady Runbook, přečtěte si téma [o stavu úlohy a datových proudech úloh od automatizace po Azure monitor protokolů](automation-manage-send-joblogs-log-analytics.md).
* Postup aktivace sady Runbook pomocí výstrahy naleznete v tématu [použití výstrahy ke spuštění sady runbook Azure Automation](automation-create-alert-triggered-runbook.md).
