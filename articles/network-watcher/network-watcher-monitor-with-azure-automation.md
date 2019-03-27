---
title: Monitorování bran VPN s řešením potíží Azure Network Watcher | Dokumentace Microsoftu
description: Tento článek popisuje, jak Diagnostika místního připojení s Azure Automation a Network Watcher
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 4995d7ae846652c374a289603f29f88f6f56dfef
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2019
ms.locfileid: "58485489"
---
# <a name="monitor-vpn-gateways-with-network-watcher-troubleshooting"></a>Monitorování bran VPN s řešením potíží Network Watcher

Získání podrobné přehledy o výkonu vaší sítě je velmi důležité zákazníkům poskytovat modelu reliable services. Proto je důležité, rychle zjišťovat stavy výpadek sítě a provést opravné akce ke zmírnění stav výpadku. Azure Automation umožňuje implementovat a spouštět úlohy prostřednictvím kódu programu může prostřednictvím sady runbook. Pomocí Azure Automation vytváří ideální předpisu pro provádění průběžné a proaktivní sítě monitorování a upozorňování.

## <a name="scenario"></a>Scénář

Scénář na následujícím obrázku je vícevrstvé aplikace, se na připojení navázáno s použitím brány VPN Gateway a tunelové propojení. Zajistit, že je brána sítě VPN v provozu a spuštění je důležité pro výkon aplikací.

Sady runbook se vytvoří pomocí skriptu zkontrolujte stav připojení tunelu VPN, pomocí rozhraní API prostředku řešení potíží zkontrolujte stav připojení tunelového propojení. Pokud stav není v pořádku, aktivační událost e-mailu odesílat správcům.

![Příklad scénáře][scenario]

Tento scénář bude:

- Vytvoření volání sady runbook `Start-AzureRmNetworkWatcherResourceTroubleshooting` rutiny pro řešení potíží se stavem připojení
- Připojení plánu k runbooku

## <a name="before-you-begin"></a>Před zahájením

Než začnete tento scénář, musíte mít následující předpoklady:

- Účet Azure automation v Azure. Ujistěte se, že účet služby automation má nejnovější moduly a také AzureRM.Network modulu. AzureRM.Network modulu je k dispozici v galerii modulů, pokud je potřeba přidat do vašeho účtu automation.
- Musíte mít sadu přihlašovacích údajů nakonfigurovat ve službě Azure Automation. Další informace najdete na [zabezpečení služby Azure Automation](../automation/automation-security-overview.md)
- Platný název serveru SMTP (Office 365, e-mailu v místním nebo jiný) a přihlašovací údaje definované ve službě Azure Automation
- Nakonfigurované brány virtuální sítě v Azure.
- Existující účet úložiště se existující kontejner pro ukládání protokolů v.

> [!NOTE]
> Infrastruktura znázorněno na předchozím obrázku je pro ilustraci a nejsou vytvořeny s kroky obsažené v tomto článku.

### <a name="create-the-runbook"></a>Vytvoření sady runbook

Prvním krokem při konfiguraci v příkladu se k vytvoření sady runbook. Tento příklad používá účet Spustit jako. Další informace o účtech spustit jako najdete v tématu [ověření Runbooků pomocí účtu spustit jako pro Azure](../automation/automation-create-runas-account.md)

### <a name="step-1"></a>Krok 1

Přejděte do služby Azure Automation v [webu Azure portal](https://portal.azure.com) a klikněte na tlačítko **sady Runbook**

![Přehled účtu Automation][1]

### <a name="step-2"></a>Krok 2

Klikněte na tlačítko **přidat runbook** zahájíte proces vytváření sady runbook.

![okno sady runbook][2]

### <a name="step-3"></a>Krok 3

V části **rychlé vytvoření**, klikněte na tlačítko **vytvořit nový runbook** k vytvoření sady runbook.

![Přidat okno runbooku][3]

### <a name="step-4"></a>Krok 4

V tomto kroku jsme dejte runbooku název, v příkladu je název **Get-VPNGatewayStatus**. Je potřeba dát popisný název sady runbook a doporučené zadání názvu, který následuje standardní standardy pro vytváření názvů prostředí PowerShell. Typ runbooku v tomto příkladu je **Powershellu**, další možnosti jsou grafický pracovního postupu Powershellu a grafický Powershellový pracovní postup.

![okno runbooku][4]

### <a name="step-5"></a>Krok 5

Následující příklad kódu v tomto kroku vytvoření sady runbook, poskytuje všechny potřebné pro tento příklad kódu. Položky v kódu, které obsahují \<hodnotu\> muset nahradit hodnotami ze svého předplatného.

Pomocí následujícího kódu jako kliknutím **uložit**

```powershell
# Set these variables to the proper values for your environment
$o365AutomationCredential = "<Office 365 account>"
$fromEmail = "<from email address>"
$toEmail = "<to email address>"
$smtpServer = "<smtp.office365.com>"
$smtpPort = 587
$runAsConnectionName = "<AzureRunAsConnection>"
$subscriptionId = "<subscription id>"
$region = "<Azure region>"
$vpnConnectionName = "<vpn connection name>"
$vpnConnectionResourceGroup = "<resource group name>"
$storageAccountName = "<storage account name>"
$storageAccountResourceGroup = "<resource group name>"
$storageAccountContainer = "<container name>"

# Get credentials for Office 365 account
$cred = Get-AutomationPSCredential -Name $o365AutomationCredential

# Get the connection "AzureRunAsConnection "
$servicePrincipalConnection=Get-AutomationConnection -Name $runAsConnectionName

"Logging in to Azure..."
Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $servicePrincipalConnection.TenantId `
    -ApplicationId $servicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
"Setting context to a specific subscription"
Set-AzureRmContext -SubscriptionId $subscriptionId

$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $region }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name $vpnConnectionName -ResourceGroupName $vpnConnectionResourceGroup
$sa = Get-AzureRmStorageAccount -Name $storageAccountName -ResourceGroupName $storageAccountResourceGroup 
$storagePath = "$($sa.PrimaryEndpoints.Blob)$($storageAccountContainer)"
$result = Start-AzureRmNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath $storagePath

if($result.code -ne "Healthy")
    {
        $body = "Connection for $($connection.name) is: $($result.code) `n$($result.results[0].summary) `nView the logs at $($storagePath) to learn more."
        Write-Output $body
        $subject = "$($connection.name) Status"
        Send-MailMessage `
        -To $toEmail `
        -Subject $subject `
        -Body $body `
        -UseSsl `
        -Port $smtpPort `
        -SmtpServer $smtpServer `
        -From $fromEmail `
        -BodyAsHtml `
        -Credential $cred
    }
else
    {
    Write-Output ("Connection Status is: $($result.code)")
    }
```

### <a name="step-6"></a>Krok 6

Po uložení runbooku plánu je potřeba propojit ji k automatizaci spuštění sady runbook. Chcete-li zahájit proces, klikněte na tlačítko **plán**.

![Krok 6][6]

## <a name="link-a-schedule-to-the-runbook"></a>Připojení plánu k runbooku

Je nutné vytvořit nový plán. Klikněte na tlačítko **připojení plánu k runbooku**.

![Krok 7][7]

### <a name="step-1"></a>Krok 1

Na **plán** okna, klikněte na tlačítko **vytvořit nový plán**

![Krok 8][8]

### <a name="step-2"></a>Krok 2

Na **nový plán** okně zadejte informace o plánu. Jsou hodnoty, které lze nastavit v následujícím seznamu:

- **Název** – popisný název plánu.
- **Popis** – popis plánu.
- **Spustí** – tato hodnota je kombinací datum, čas a časové pásmo, které společně tvoří čas aktivačních událostech plánovače.
- **Opakování** – tato hodnota určuje plánů opakování.  Platné hodnoty jsou **jednou** nebo **periodický**.
- **Opakovat každý** – interval opakování plánu v hodinách, dnech, týdnech nebo měsících.
- **Nastavit dobu platnosti** – hodnota určuje, že pokud plán by měla vypršet platnost nebo ne. Je možné nastavit **Ano** nebo **ne**. Platné datum a čas mají být zadán, pokud ano je vybrán.

> [!NOTE]
> Pokud je potřeba mít sady runbook spouštět častěji než každou hodinu, je nutné vytvořit více plánů v různých intervalech (to znamená, 15, 30, 45 minut za hodinu)

![Krok 9][9]

### <a name="step-3"></a>Krok 3

Klikněte na tlačítko Uložit uložte plánu k sadě runbook.

![Krok 10][10]

## <a name="next-steps"></a>Další postup

Teď, když máte představu o tom, jak integrovat Network Watcher řešení potíží s Azure Automation, zjistěte, jak aktivovat zachytávání paketů na virtuálním počítači výstrahy návštěvou [vytvořit zachytávání paketů upozornění aktivovaných pomocí služby Azure Network Watcher](network-watcher-alert-triggered-packet-capture.md).

<!-- images -->
[scenario]: ./media/network-watcher-monitor-with-azure-automation/scenario.png
[1]: ./media/network-watcher-monitor-with-azure-automation/figure1.png
[2]: ./media/network-watcher-monitor-with-azure-automation/figure2.png
[3]: ./media/network-watcher-monitor-with-azure-automation/figure3.png
[4]: ./media/network-watcher-monitor-with-azure-automation/figure4.png
[5]: ./media/network-watcher-monitor-with-azure-automation/figure5.png
[6]: ./media/network-watcher-monitor-with-azure-automation/figure6.png
[7]: ./media/network-watcher-monitor-with-azure-automation/figure7.png
[8]: ./media/network-watcher-monitor-with-azure-automation/figure8.png
[9]: ./media/network-watcher-monitor-with-azure-automation/figure9.png
[10]: ./media/network-watcher-monitor-with-azure-automation/figure10.png
