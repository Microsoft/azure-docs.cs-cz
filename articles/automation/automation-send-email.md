---
title: Odeslání e-mailu z runbooku Azure Automation
description: Přečtěte si, jak pomocí SendGrid odeslat e-mail z runbooku.
services: automation
ms.subservice: process-automation
ms.date: 07/15/2019
ms.topic: tutorial
ms.openlocfilehash: 332d7e4cae3b65d794d48d208b54db3809b78a8f
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632366"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>Kurz: Odeslání e-mailu z runbooku Azure Automation

Pomocí PowerShellu můžete odeslat e-mail z runbooku pomocí [SendGrid.](https://sendgrid.com/solutions) Tento kurz vám ukáže, jak vytvořit opakovaně použitelný runbook, který odešle e-mail pomocí klíče rozhraní API uloženého v [Azure KeyVault](/azure/key-vault/).

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Vytvoření Azure KeyVault
> * Uložení klíče rozhraní API SendGrid do úložiště KeyVault
> * Vytvoření runbooku, který načte klíč rozhraní API a odešle e-mail

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující:

* Předplatné Azure: Pokud ještě nemáte, můžete [aktivovat výhody předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Vytvořte účet SendGrid](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Účet automatizace](automation-offering-get-started.md) s moduly **Az** a [spustit jako připojení](automation-create-runas-account.md), ukládat a spouštět runbook.

## <a name="create-an-azure-keyvault"></a>Vytvoření Azure KeyVault

Azure KeyVault můžete vytvořit pomocí následujícího skriptu Prostředí PowerShell. Nahraďte hodnoty proměnných hodnotami specifickými pro vaše prostředí. Pomocí vloženého prostředí Azure Cloud Shell použijte pomocí tlačítka <kbd>Try It,</kbd> které se nachází v pravém horním rohu bloku kódu. Kód můžete také zkopírovat a spustit místně, pokud máte na místním počítači nainstalovaný [modul Azure PowerShell.](/powershell/azure/install-az-ps)

> [!NOTE]
> Chcete-li načíst klíč rozhraní API, použijte kroky nalezené v části [Najít klíč rozhraní API SendGrid](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key).

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

Další způsoby vytvoření azure keyvault a uložení tajného klíče naleznete v [tématu KeyVault Quickstarts](/azure/key-vault/).

## <a name="import-required-modules-to-your-automation-account"></a>Import požadovaných modulů do účtu Automation

Chcete-li používat Azure KeyVault v rámci sady Runbook, váš účet Automation bude potřebovat následující moduly:

* [Az.Profil](https://www.powershellgallery.com/packages/Az.Profile).
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault).

Klikněte <kbd>na Nasadit do Azure Automation</kbd> na kartě Azure Automation v části Možnosti instalace. Tato akce otevře portál Azure. Na stránce Import vyberte účet automatizace a klepněte na tlačítko <kbd>OK</kbd>.

Další metody pro přidání požadovaných modulů naleznete [v tématu Import Modules](/azure/automation/shared-resources/modules#import-modules).

## <a name="create-the-runbook-to-send-an-email"></a>Vytvoření runbooku pro odeslání e-mailu

Po vytvoření keyvault a uloženy SendGrid API klíč, je čas vytvořit runbook, který načte klíč rozhraní API a odeslat e-mail.

Tento runbook používá účet AzureRunAsConnection [Run As](automation-create-runas-account.md) k ověření pomocí Azure k načtení tajného klíče z Azure KeyVault.

Tento příklad slouží k vytvoření runbooku s názvem **Send-GridMailMessage**. Můžete upravit skript Prostředí PowerShell a znovu jej použít pro různé scénáře.

1. Přejděte na svůj účet Azure Automation.
2. V části **Automatizace procesů**vyberte **Runbook**.
3. V horní části seznamu runbooků vyberte **+ Vytvořit runbook**.
4. Na stránce **Přidat runbook** zadejte pro název runbooku **send-gridmailmessage.** Pro typ runbooku vyberte **PowerShell**. Potom vyberte **Create** (Vytvořit).
   ![Vytvořit runbook](./media/automation-send-email/automation-send-email-runbook.png)
5. Runbook se vytvoří a otevře se stránka **Upravit powershellový runbook**.
   ![Úprava runbooku](./media/automation-send-email/automation-send-email-edit.png)
6. Zkopírujte následující příklad PowerShellu na stránku **Úpravy.** Ujistěte `$VaultName` se, že je název, který jste zadali při vytváření KeyVault.

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

7. Vyberte **Publikovat,** chcete-li soubor Runbook uložit a publikovat.

Chcete-li ověřit, zda se runbook úspěšně spustí, můžete postupovat podle kroků v části [Testovat runbook](manage-runbooks.md#testing-a-runbook) nebo [Spustit runbook](start-runbooks.md).
Pokud se testovací e-mail zpočátku nezobrazují, zkontrolujte složky **Nevyžádaná pošta** a **Spam.**

## <a name="clean-up"></a>Vyčištění

Pokud už runbook nepotřebujete, odstraňte ho. Provedete to tak, že v seznamu runbooků vyberete příslušný runbook a kliknete na **Odstranit**.

Odstraňte trezor klíčů pomocí rutiny [Remove-AzureRMKeyVault.](/powershell/module/azurerm.keyvault/remove-azurermkeyvault?view=azurermps)

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Další kroky

* Problémy s vytvářením nebo spuštěním sady Runbook najdete v [tématu Poradce při potížích se sadou Runbook](./troubleshoot/runbooks.md).
* Pokud chcete aktualizovat moduly v účtu Automation, přečtěte si informace [o tom, jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md)].
* Pokud chcete sledovat spouštění runbooků, přečtěte si informace [o stavu úlohy předávání a datových proudech úloh z protokolů Automatizace do Azure Monitoru](automation-manage-send-joblogs-log-analytics.md).
* Pokud chcete spustit runbook pomocí výstrahy, [přečtěte si informace o spuštění runbooku Azure Automation pomocí výstrahy.](automation-create-alert-triggered-runbook.md)
