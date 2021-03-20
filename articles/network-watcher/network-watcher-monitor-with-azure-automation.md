---
title: Řešení potíží a monitorování bran sítě VPN – Azure Automation
titleSuffix: Azure Network Watcher
description: Tento článek popisuje, jak diagnostikovat místní připojení pomocí Azure Automation a Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: af671996722524de9af1a90ae8dfde27f814c8c2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011808"
---
# <a name="monitor-vpn-gateways-with-network-watcher-troubleshooting"></a>Monitorování bran VPN pomocí Network Watcher řešení potíží

Získání podrobných přehledů o výkonu vaší sítě je důležité pro poskytování spolehlivých služeb zákazníkům. Proto je důležité zjistit stav výpadku sítě rychle a provést nápravná opatření ke zmírnění stavu výpadku. Azure Automation umožňuje implementovat a spustit úlohu programovým způsobem prostřednictvím runbooků. Použití Azure Automation vytvoří dokonalý recept pro průběžné a proaktivní monitorování sítě a výstrahy.

## <a name="scenario"></a>Scenario

Scénář na následujícím obrázku je Vícevrstvá aplikace s místním připojením vytvořeným pomocí VPN Gateway a tunelového propojení. Zajištění, že je VPN Gateway v provozu, je pro výkon aplikací zásadní.

Vytvoří se sada Runbook se skriptem, který kontroluje stav připojení tunelu VPN, a to pomocí rozhraní API pro řešení potíží s prostředky pro kontrolu stavu tunelového připojení. Pokud stav není v pořádku, pošle se správci e-mail Trigger.

![Příklad scénáře][scenario]

Tento scénář bude:

- Vytvoření Runbooku, který volá `Start-AzureRmNetworkWatcherResourceTroubleshooting` rutinu pro řešení potíží se stavem připojení
- Propojit plán s runbookm

## <a name="before-you-begin"></a>Než začnete

Než začnete tento scénář, musíte mít následující požadavky:

- Účet Azure Automation v Azure. Ujistěte se, že účet Automation má nejnovější moduly a má také modul AzureRM. Network. Modul AzureRM. Network je dostupný v galerii modulů, pokud ho potřebujete přidat do svého účtu Automation.
- Musíte mít v Azure Automation nakonfigurovanou sadu přihlašovacích údajů. Další informace o [Azure Automation zabezpečení](../automation/automation-security-overview.md)
- Platný server SMTP (Microsoft 365, místní e-mail nebo jiný) a přihlašovací údaje definované v Azure Automation
- Nakonfigurovaná Virtual Network brána v Azure.
- Existující účet úložiště s existujícím kontejnerem pro ukládání protokolů.

> [!NOTE]
> Infrastruktura znázorněná na předchozím obrázku je určena pro ilustraci a není vytvořena postupem uvedeným v tomto článku.

### <a name="create-the-runbook"></a>Vytvoření Runbooku

Prvním krokem ke konfiguraci příkladu je vytvoření Runbooku. V tomto příkladu se používá účet Spustit jako. Další informace o účtech Spustit jako najdete v webu [ověřování runbooků pomocí účtu Spustit jako pro Azure](../automation/manage-runas-account.md) .

### <a name="step-1"></a>Krok 1

V [Azure Portal](https://portal.azure.com) přejděte na Azure Automation a klikněte na **Runbooky** .

![Přehled účtu Automation][1]

### <a name="step-2"></a>Krok 2

Kliknutím na **Přidat Runbook** zahajte proces vytváření sady Runbook.

![okno Runbooky][2]

### <a name="step-3"></a>Krok 3

V části **rychlé vytvoření** klikněte na **vytvořit novou sadu Runbook** a vytvořte sadu Runbook.

![Přidat okno Runbooku][3]

### <a name="step-4"></a>Krok 4

V tomto kroku poskytneme Runbooku název, v příkladu se nazývá **Get-VPNGatewayStatus**. Je důležité poskytnout Runbooku popisný název a doporučuje se mu dát název, který bude postupovat podle standardních standardů pojmenovávání PowerShellu. Typ Runbooku pro tento příklad je **PowerShell**, ostatní možnosti jsou grafické, powershellový pracovní postup a grafický pracovní postup PowerShellu.

![okno Runbooku][4]

### <a name="step-5"></a>Krok 5

V tomto kroku je vytvořen Runbook, následující příklad kódu poskytuje veškerý kód potřebný pro příklad. Položky v kódu, které obsahují, je \<value\> nutné nahradit hodnotami z vašeho předplatného.

Jako tlačítko **Uložit** použijte následující kód.

```powershell
# Set these variables to the proper values for your environment
$automationCredential = "<work or school account>"
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

# Get credentials for work or school account
$cred = Get-AutomationPSCredential -Name $automationCredential

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

Po uložení Runbooku se na něj musí navzájem propojit plán, který automatizuje spuštění Runbooku. Chcete-li spustit proces, klikněte na tlačítko **naplánovat**.

![Krok 6][6]

## <a name="link-a-schedule-to-the-runbook"></a>Propojit plán s runbookm

Je nutné vytvořit nový plán. Klikněte na **připojit plán k sadě Runbook**.

![Krok 7][7]

### <a name="step-1"></a>Krok 1

V okně **plán** klikněte na **vytvořit nový plán** .

![Krok 8][8]

### <a name="step-2"></a>Krok 2

V okně **Nový plán** vyplňte informace o plánu. Hodnoty, které lze nastavit, jsou uvedeny v následujícím seznamu:

- **Název** – popisný název plánu.
- **Popis** – popis plánu.
- **Start** – tato hodnota je kombinací data, času a časového pásma, které tvoří čas triggeru plánu.
- **Opakování** – tato hodnota určuje opakování plánu.  Platné hodnoty jsou **jednou** nebo **opakované**.
- **Opakuje každý** – interval opakování plánu v hodinách, dnech, týdnech nebo měsících.
- **Nastavit vypršení platnosti** – hodnota určuje, jestli má plán vypršet, nebo ne. Dá se nastavit na **Ano** nebo **ne**. Pokud je zvolena možnost Ano, je nutné zadat platné datum a čas.

> [!NOTE]
> Pokud potřebujete, aby sada Runbook běžela častěji než každou hodinu, je třeba vytvořit více plánů v různých intervalech (tj. 15, 30 až 45 minut po hodině).

![Krok 9][9]

### <a name="step-3"></a>Krok 3

Kliknutím na Uložit uložte plán do Runbooku.

![Krok 10][10]

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s tím, jak integrovat Network Watcher řešení potíží s Azure Automation, se naučíte, jak aktivovat zachycení paketů při výstrahách virtuálních počítačů návštěvou [Vytvoření výstrahy aktivované zachycením paketů s Azure Network Watcher](network-watcher-alert-triggered-packet-capture.md).

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