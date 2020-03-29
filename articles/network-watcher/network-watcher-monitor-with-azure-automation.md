---
title: Poradce při potížích a monitorování bran VPN – Azure Automation
titleSuffix: Azure Network Watcher
description: Tento článek popisuje, jak diagnostikovat místní připojení s Azure Automation a Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 74c9f44ff5fbbbb50bba1594d371633fd49857eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845039"
---
# <a name="monitor-vpn-gateways-with-network-watcher-troubleshooting"></a>Sledování bran VPN pomocí odstraňování potíží s sledováním sítě

Získání podrobného přehledu o výkonu sítě je důležité pro poskytování spolehlivých služeb zákazníkům. Je proto důležité rychle zjistit podmínky výpadku sítě a přijmout nápravná opatření ke zmírnění stavu výpadku. Azure Automation umožňuje implementovat a spustit úlohu programmaticky prostřednictvím runbooků. Pomocí Azure Automation vytvoří dokonalý recept pro provádění nepřetržitého a proaktivního monitorování sítě a upozorňování.

## <a name="scenario"></a>Scénář

Scénář v následující bitové kopii je vícevrstvá aplikace, s místní připojení vytvořené pomocí brány VPN a tunelového propojení. Zajištění provozu brány VPN je důležité pro výkon aplikací.

Runbook je vytvořen se skriptem pro kontrolu stavu připojení tunelového propojení VPN pomocí rozhraní RESOURCE Troubleshooting API ke kontrole stavu tunelového propojení připojení. Pokud stav není v pořádku, je správcům odeslána aktivační událost e-mailu.

![Příklad scénáře][scenario]

Tento scénář bude:

- Vytvoření runbooku `Start-AzureRmNetworkWatcherResourceTroubleshooting` volajícího rutinu k řešení potíží se stavem připojení
- Propojení plánu s runbookem

## <a name="before-you-begin"></a>Než začnete

Před zahájením tohoto scénáře musíte mít následující předpoklady:

- Účet azure automatizace v Azure. Ujistěte se, že účet automatizace má nejnovější moduly a má také modul AzureRM.Network. Modul AzureRM.Network je k dispozici v galerii modulů, pokud ho potřebujete přidat do svého účtu automatizace.
- Musíte mít sadu přihlašovacích údajů nakonfigurovat v Azure Automation. Další informace o [zabezpečení Azure Automation](../automation/automation-security-overview.md)
- Platný server SMTP (Office 365, místní e-mail nebo jiný) a přihlašovací údaje definované v Azure Automation
- Nakonfigurovaná brána virtuální sítě v Azure.
- Existující účet úložiště s existujícím kontejnerem pro uložení protokolů.

> [!NOTE]
> Infrastruktura zobrazená na předchozím obrázku je pro ilustrační účely a nejsou vytvořeny pomocí kroků obsažených v tomto článku.

### <a name="create-the-runbook"></a>Vytvoření runbooku

Prvním krokem ke konfiguraci příkladu je vytvoření runbooku. Tento příklad používá účet run-as. Další informace o účtech run-as najdete v části [Ověření runbooků s účtem Azure Run As.](../automation/automation-create-runas-account.md)

### <a name="step-1"></a>Krok 1

Přejděte na Azure Automation na [portálu Azure](https://portal.azure.com) a klikněte na **Runbooky.**

![přehled účtu automatizace][1]

### <a name="step-2"></a>Krok 2

Kliknutím **na Přidat runbook** spusťte proces vytváření runbooku.

![runbookblade][2]

### <a name="step-3"></a>Krok 3

V části **Rychlé vytvoření**klikněte na Vytvořit **novou runbook** a vytvořte runbook.

![přidání okna runbooku][3]

### <a name="step-4"></a>Krok 4

V tomto kroku dáváme runbook jméno, v příkladu se nazývá **Get-VPNGatewayStatus**. Je důležité dát runbookpopisný název a doporučuje dát mu název, který se řídí standardními standardy pojmenování Prostředí PowerShell. Typ sady Runbook pro tento příklad je **PowerShell**, další možnosti jsou Graphical, Pracovní postup prostředí PowerShell a Grafický pracovní postup Prostředí PowerShell.

![runbook blade][4]

### <a name="step-5"></a>Krok 5

V tomto kroku je vytvořen runbook, následující příklad kódu obsahuje veškerý kód potřebný pro příklad. Položky v kódu, \<\> které obsahují hodnotu je třeba nahradit hodnoty z vašeho předplatného.

Pomocí následujícího kódu klikněte na **Uložit.**

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

Po uložení runbooku musí být propojován plán, aby bylo možné automatizovat spuštění runbooku. Proces zahájíte klepnutím na tlačítko **Naplánovat**.

![Krok 6][6]

## <a name="link-a-schedule-to-the-runbook"></a>Propojení plánu s runbookem

Musí být vytvořen nový plán. Klikněte **na Propojit plán se souborem Runbook**.

![Krok 7][7]

### <a name="step-1"></a>Krok 1

V okně **Plán** klikněte na **Vytvořit nový plán.**

![Krok 8][8]

### <a name="step-2"></a>Krok 2

V okně **Nový plán** vyplňte informace o plánu. Hodnoty, které lze nastavit, jsou v následujícím seznamu:

- **Název** - Popisný název plánu.
- **Popis** - Popis plánu.
- **Spustí** - Tato hodnota je kombinací data, času a časového pásma, které tvoří čas, který plán aktivuje.
- **Opakování** - Tato hodnota určuje opakování plánů.  Platné hodnoty jsou **Once** nebo **Recurring**.
- **Opakování každé** – interval opakování plánu v hodinách, dnech, týdnech nebo měsících.
- **Nastavit vypršení platnosti** - Hodnota určuje, zda má platnost plánu vypršet nebo ne. Lze nastavit na **ano** nebo **ne**. Pokud je zvoleno ano, je třeba uvést platné datum a čas.

> [!NOTE]
> Pokud potřebujete spustit runbook častěji než každou hodinu, musí být v různých intervalech vytvořeno více plánů (to znamená 15, 30, 45 minut po hodině)

![Krok 9][9]

### <a name="step-3"></a>Krok 3

Kliknutím na Uložit plán uložíte do runbooku.

![Krok 10][10]

## <a name="next-steps"></a>Další kroky

Teď, když máte pochopení, jak integrovat řešení potíží sledování sítě s Azure Automation, zjistěte, jak aktivovat zachycení paketů na výstrahách virtuálních počítačí, a to tak, že najdete v [přehledu vytvoření výstrahy s aktivovaného paketu pomocí služby Azure Network Watcher](network-watcher-alert-triggered-packet-capture.md).

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
