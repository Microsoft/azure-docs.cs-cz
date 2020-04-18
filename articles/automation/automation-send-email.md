---
title: Odeslání e-mailu z runbooku Azure Automation
description: Přečtěte si, jak pomocí SendGrid odeslat e-mail z runbooku.
services: automation
ms.subservice: process-automation
ms.date: 07/15/2019
ms.topic: tutorial
ms.openlocfilehash: d4b35458c76da82b33dfcb530cfdc71ee3da3bb6
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604784"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>Kurz: Odeslání e-mailu z runbooku Azure Automation

Pomocí PowerShellu můžete odeslat e-mail z runbooku pomocí [SendGrid.](https://sendgrid.com/solutions) V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Vytvořte trezor klíčů Azure.
> * Uložte `SendGrid` klíč rozhraní API do trezoru klíčů.
> * Vytvořte opakovaně použitelný runbook, který načte klíč rozhraní API a odešle e-mail pomocí klíče rozhraní API uloženého v [úložišti klíčů Azure](/azure/key-vault/).

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující:

* Předplatné Azure: Pokud ještě nemáte, můžete [aktivovat výhody předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Vytvořte účet SendGrid](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Účet automatizace](automation-offering-get-started.md) s moduly **Az** a [spustit jako připojení](automation-create-runas-account.md), ukládat a spouštět runbook.

## <a name="create-an-azure-key-vault"></a>Vytvoření služby Azure Key Vault

Trezor klíčů Azure můžete vytvořit pomocí následujícího skriptu Prostředí PowerShell. Nahraďte hodnoty proměnných hodnotami specifickými pro vaše prostředí. Pomocí vloženého prostředí Azure Cloud Shell použijte pomocí tlačítka **Try It,** které se nachází v pravém horním rohu bloku kódu. Kód můžete také zkopírovat a spustit místně, pokud máte na místním počítači nainstalovaný [modul Azure PowerShell.](/powershell/azure/install-az-ps)

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

# Grant access to the Key Vault to the Automation Run As account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

Další způsoby vytvoření trezoru klíčů Azure a uložení tajného klíče naleznete v [tématu Key Vault Quickstarts](/azure/key-vault/).

## <a name="import-required-modules-to-your-automation-account"></a>Import požadovaných modulů do účtu Automation

Chcete-li používat Azure Key Vault v rámci sady Runbook, váš účet automatizace potřebuje následující moduly:

* [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile)
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)

Klikněte **na Nasadit do Azure Automation** na kartě Azure Automation v části **Možnosti instalace**. Tato akce otevře portál Azure. Na stránce Import vyberte účet Automatizace a klepněte na tlačítko **OK**.

Další metody pro přidání požadovaných modulů naleznete [v tématu Import Modules](/azure/automation/shared-resources/modules#importing-modules).

## <a name="create-the-runbook-to-send-an-email"></a>Vytvoření runbooku pro odeslání e-mailu

Po vytvoření trezoru klíčů a `SendGrid` uložení klíče rozhraní API je čas vytvořit runbook, který načte klíč rozhraní API a odešle e-mail.

Tento runbook `AzureRunAsConnection` používá jako [účet Spustit jako](automation-create-runas-account.md) k ověření pomocí Azure k načtení tajného klíče z Azure Key Vault.

Tento příklad slouží k vytvoření runbooku s názvem **Send-GridMailMessage**. Můžete upravit skript Prostředí PowerShell a znovu jej použít pro různé scénáře.

1. Přejděte na svůj účet Azure Automation.
2. V části **Automatizace procesů**vyberte **Runbook**.
3. V horní části seznamu runbooků vyberte **+ Vytvořit runbook**.
4. Na stránce **Přidat runbook** zadejte pro název runbooku **send-gridmailmessage.** Pro typ runbooku vyberte **PowerShell**. Potom vyberte **Create** (Vytvořit).
   ![Vytvořit runbook](./media/automation-send-email/automation-send-email-runbook.png)
5. Runbook se vytvoří a otevře se stránka **Upravit powershellový runbook**.
   ![Úprava runbooku](./media/automation-send-email/automation-send-email-edit.png)
6. Zkopírujte následující příklad PowerShellu na stránku **Úpravy.** Ujistěte `$VaultName` se, že je název, který jste zadali při vytváření trezoru klíčů.

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

Odstraňte trezor klíčů pomocí rutiny [Remove-AzKeyVault.](https://docs.microsoft.com/powershell/module/az.keyvault/remove-azkeyvault?view=azps-3.7.0)

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Další kroky

* Problémy s vytvářením nebo spuštěním sady Runbook najdete v [tématu Poradce při potížích se sadou Runbook](./troubleshoot/runbooks.md).
* Pokud chcete aktualizovat moduly v účtu Automation, přečtěte si informace [o tom, jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md)].
* Pokud chcete sledovat spouštění runbooků, přečtěte si informace [o stavu úlohy předávání a datových proudech úloh z protokolů Automatizace do Azure Monitoru](automation-manage-send-joblogs-log-analytics.md).
* Pokud chcete spustit runbook pomocí výstrahy, [přečtěte si informace o spuštění runbooku Azure Automation pomocí výstrahy.](automation-create-alert-triggered-runbook.md)
