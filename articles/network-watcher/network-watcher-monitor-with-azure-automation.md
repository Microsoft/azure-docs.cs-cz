---
title: Monitorování s řešení potíží s sledovací proces sítě Azure VPN Gateway | Microsoft Docs
description: Tento článek popisuje, jak diagnostikovat místní připojení Azure Automation a sledovací proces sítě
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
ms.openlocfilehash: a102916bb0626f5b110fb134a8a25c902cfaefe7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31598128"
---
# <a name="monitor-vpn-gateways-with-network-watcher-troubleshooting"></a>Sledování brány sítě VPN s řešení potíží s sledovací proces sítě

Získání hlubšímu porozumění na výkonu sítě je důležité poskytují spolehlivé služby zákazníkům. Proto je důležité ke zjišťování síťové podmínky výpadku rychle a podniknout kroky ke zmírnění stav výpadku. Automatizace Azure umožňuje implementovat a spusťte úlohu programový způsobem prostřednictvím sady runbook. Pomocí Azure Automation vytvoří ideální recepturách pro provádění sítě nepřetržitý a Proaktivní monitorování a výstrahy.

## <a name="scenario"></a>Scénář

Scénář na následujícím obrázku je víceúrovňových aplikace, se na místní připojení navázat pomocí brány sítě VPN a tunelové propojení. Zajištění, že služby VPN Gateway je zapnutý a spuštění je důležité pro výkon aplikace.

Runbook se vytvoří pomocí skriptu zkontrolujte stav připojení tunelového propojení sítě VPN, pomocí rozhraní API řešení potíží s prostředků zkontrolujte stav tunelového připojení. Pokud stav není v pořádku, se správcům odešle aktivační procedury e-mailu.

![Příklad scénáře][scenario]

Tento scénář se:

- Vytvoření volání sady runbook `Start-AzureRmNetworkWatcherResourceTroubleshooting` rutiny, chcete-li vyřešit stav připojení
- Propojit plán s sady runbook

## <a name="before-you-begin"></a>Než začnete

Než začnete tento scénář, musíte mít následující požadavky:

- Účet Azure automation v Azure. Zajistěte, aby účet automation nejnovější modulů a také obsahuje modul AzureRM.Network. Modul AzureRM.Network je k dispozici v galerii modulů, pokud je potřeba ho přidat do vašeho účtu automation.
- Musíte mít sadu přihlašovacích údajů nakonfigurovat ve službě Azure Automation. Další informace na [zabezpečení Azure Automation.](../automation/automation-security-overview.md)
- Platný server SMTP (Office 365, místní e-mailu nebo jiné) a přihlašovací údaje definované ve službě Azure Automation
- Nakonfigurované virtuální bránu sítě v Azure.
- Stávající účet úložiště s existující kontejner k ukládání protokolů v.

> [!NOTE]
> Infrastruktury, které popsané v předchozí obrázek je pro účely obrázku a nejsou vytvořeny s kroky obsažené v tomto článku.

### <a name="create-the-runbook"></a>Vytvoření sady runbook

Prvním krokem k konfigurace v příkladu se k vytvoření sady runbook. Tento příklad používá účet Spustit jako. Další informace o účtech spustit jako najdete [ověření Runbooků pomocí účtu spustit v Azure jako](../automation/automation-create-runas-account.md)

### <a name="step-1"></a>Krok 1

Přejděte do Azure Automation v [portál Azure](https://portal.azure.com) a klikněte na tlačítko **sady Runbook**

![Přehled účtu Automation][1]

### <a name="step-2"></a>Krok 2

Klikněte na tlačítko **přidat runbook** ke spuštění procesu vytvoření sady runbook.

![okno sady runbook][2]

### <a name="step-3"></a>Krok 3

V části **rychle vytvořit**, klikněte na tlačítko **vytvořit nový runbook** k vytvoření sady runbook.

![runbook okně Přidat][3]

### <a name="step-4"></a>Krok 4

V tomto kroku jsme dejte runbooku název, v příkladu se označuje jako **Get-VPNGatewayStatus**. Je důležité si dejte runbooku popisný název a doporučené pod názvem, který následuje standardní standardy pro vytváření názvů prostředí PowerShell. Typ sady runbook v tomto příkladu je **prostředí PowerShell**, ostatní možnosti patří grafický pracovního postupu Powershellu a pracovní postup grafické prostředí PowerShell.

![okno sady runbook][4]

### <a name="step-5"></a>Krok 5

Následující příklad kódu v tomto kroku, který je sada runbook vytvořena poskytuje všechny potřebné pro tento příklad kódu. Položky v kódu, které obsahují \<hodnota\> je nutné nahradit s hodnotami ze svého předplatného.

Použít následující kód jako klikněte na tlačítko **uložit**

```PowerShell
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

Po uložení runbooku plánu musí být propojena k němu automatizovat spuštění sady runbook. Chcete-li spustit proces, klikněte na tlačítko **plán**.

![Krok 6][6]

## <a name="link-a-schedule-to-the-runbook"></a>Propojit plán s sady runbook

Je nutné vytvořit nový plán. Klikněte na tlačítko **propojit plán s runbookem**.

![Krok 7][7]

### <a name="step-1"></a>Krok 1

Na **plán** okně klikněte na tlačítko **vytvoření nového plánu**

![Krok 8][8]

### <a name="step-2"></a>Krok 2

Na **nový plán** výplně okno se informace o plánu. V následujícím seznamu jsou hodnoty, které lze nastavit:

- **Název** -popisný název plánu.
- **Popis** – popis plánu.
- **Spustí** – tato hodnota je kombinací datum, čas a časové pásmo, které tvoří časový plán aktivačních událostí.
- **Opakování** – tato hodnota určuje opakování plány.  Platné hodnoty jsou **jednou** nebo **opakovaná**.
- **Opakovat každý** – interval opakování plánu v hodiny, dny, týdny nebo měsíce.
- **Nastavit dobu platnosti** -hodnota určuje, pokud by měla vypršet podle plánu, nebo ne. Může být nastaven na **Ano** nebo **ne**. Pokud ano jste vybrali, musíte zadat jsou platné datum a čas.

> [!NOTE]
> Pokud je potřeba mít sady runbook spustit častěji než každou hodinu, je nutné vytvořit více plánů v různých intervalech (to znamená, 15, 30, 45 minut po hodině)

![Krok 9][9]

### <a name="step-3"></a>Krok 3

Kliknutím na Uložit o uložení plánu k sadě runbook.

![Krok 10][10]

## <a name="next-steps"></a>Další postup

Nyní když máte představu o tom, jak integrovat řešení potíží s sledovací proces sítě s Azure Automation, zjistěte, jak aktivovat zachycení paketů na výstrahy virtuálních počítačů tím, navštivte stránky [vytvořit zaznamenání výstrahy spouštěná paketů s sledovací proces sítě Azure](network-watcher-alert-triggered-packet-capture.md).

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
