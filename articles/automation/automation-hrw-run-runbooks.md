---
title: Spuštění sad runbook pro Azure Automation Hybrid Runbook Worker.
description: Tento článek obsahuje informace o spuštění sady runbook na počítačích ve vaší místní datacenter nebo poskytovatele cloudových služeb s rolí hybridní pracovní proces Runbooku.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 4a334b63856a348838cbe61dfc0ab724f58100fb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Spuštěné sady runbook pro Hybrid Runbook Worker. 
Není žádný rozdíl ve struktuře sad runbook, které běží v Azure Automation a ty, které běží na hybridní pracovní proces Runbooku. Sady Runbook, které můžete použít pro každý s největší pravděpodobností se podstatně liší ale vzhledem k tomu obvykle cílení hybridní pracovní proces Runbooku sady runbook spravovat prostředky v místním počítači sám sebe nebo s prostředky v místním prostředí, kde je nasazen, při sady runbook v Služby Azure Automation obvykle spravovat prostředky v cloudu Azure.

Můžete upravit sady runbook pro hybridní pracovní proces Runbooku ve službě Azure Automation, ale mohou mít problémy, pokud se pokusíte o test runbooku se v editoru.  Moduly prostředí PowerShell, které přístup k místním prostředkům pravděpodobně nainstalována ve vašem prostředí Azure Automation. v takovém případě, test se nezdaří.  Pokud nainstalujete požadované moduly, spusťte sadu runbook, ale nebude mít přístup k místním prostředkům pro dokončení testu.

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Spuštění sady runbook pro Hybrid Runbook Worker.
[Spuštění sady Runbook ve službě Azure Automation](automation-starting-a-runbook.md) popisuje různé metody pro spuštění sady runbook.  Přidá hybridní pracovní proces Runbooku **RunOn** možnost, kde můžete zadat název skupiny hybridní Runbook Worker.  Pokud je skupina zadaná, sada runbook je načíst a spustit pracovníků v této skupině.  Pokud není tato možnost zadána, pak spuštění ve službě Azure Automation jako normální.

Při spuštění sady runbook na portálu Azure, se zobrazí **spustit na** možnost, kde můžete vybrat **Azure** nebo **hybridní pracovní proces**.  Pokud vyberete **hybridní pracovní proces**, pak můžete vybrat skupiny z rozevírací seznam.

Použití **RunOn** parametr.  Můžete následující příkaz pro spuštění sady runbook s názvem Test-Runbook na hybridní skupina pracovního procesu Runbook s názvem MyHybridGroup pomocí prostředí Windows PowerShell.

    Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"

> [!NOTE]
> **RunOn** parametr byl přidán do **Start-AzureAutomationRunbook** rutiny ve verzi 0.9.1 Microsoft Azure PowerShell.  Měli byste [stáhnout nejnovější verzi](https://azure.microsoft.com/downloads/) Pokud máte některou z dřívějších nainstalována.  Stačí tuto verzi nainstalovat na pracovní stanici, kde spouštíte sadu runbook z prostředí Windows PowerShell.  Nemusíte jej nainstalovat na počítače pracovní, pokud máte v úmyslu spouštění sad runbook z tohoto počítače.  Nelze spustit aktuálně sady runbook na hybridní pracovní proces Runbooku z jiného runbooku vzhledem k tomu, že bude vyžadovat nejnovější verzi prostředí Azure Powershell má být nainstalován do vašeho účtu Automation.  Nejnovější verze je automaticky aktualizovat ve službě Azure Automation a automaticky instaluje zaměstnancům brzy k dispozici.
>
>

## <a name="runbook-permissions"></a>Oprávnění sady Runbook
Runbook spuštěných v hybridní pracovní proces Runbooku nelze použít stejnou metodu, která se obvykle používá pro runbooky ověřované pro prostředky Azure, protože jejich přístupu k prostředkům mimo Azure.  Runbook můžete buď zadat vlastní ověřování k místním prostředkům, nebo můžete zadat účet RunAs zajistit kontext uživatele pro všechny sady runbook.

### <a name="runbook-authentication"></a>Ověření sady Runbook
Ve výchozím nastavení sady runbook poběží v kontextu účtu místního systému na místním počítači, musí zadat své vlastní ověřování na prostředky, které budou přistupovat k.  

Můžete použít [pověření](http://msdn.microsoft.com/library/dn940015.aspx) a [certifikát](http://msdn.microsoft.com/library/dn940013.aspx) prostředky ve vašem runbooku pomocí rutiny, které vám umožní zadat přihlašovací údaje, můžete provést ověření na různých prostředků.  Následující příklad ukazuje část sady runbook, který restartuje počítač.  Načte přihlašovací údaje z asset přihlašovacích údajů a názvu počítače z variabilní prostředek a potom tyto hodnoty používá pomocí rutiny Restart-Computer.

    $Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -Name "MyCredential"
    $Computer = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" -Name  "ComputerName"

    Restart-Computer -ComputerName $Computer -Credential $Cred

Můžete také využít [InlineScript](automation-powershell-workflow.md#inlinescript), která umožňuje spuštění bloky kódu na jiném počítači s přihlašovacími údaji určeného [PSCredential společný parametr](http://technet.microsoft.com/library/jj129719.aspx).

### <a name="runas-account"></a>Účet Spustit jako
Namísto s runbooky své vlastní ověřování k místním prostředkům, můžete zadat **RunAs** účet pro skupinu hybridních pracovních procesů.  Zadáte [asset přihlašovacích údajů](automation-credentials.md) který má přístup k místním prostředkům a všechny sady runbook spouštět tyto přihlašovací údaje při spuštění v hybridní pracovní proces Runbooku ve skupině.  

Uživatelské jméno pro přihlašovací údaje musí být v jednom z následujících formátů:

* domain\username
* username@domain
* uživatelské jméno (pro účty místní na místním počítači)

Následující postup použijte k určení účtu RunAs pro skupinu hybridních pracovních procesů:

1. Vytvoření [asset přihlašovacích údajů](automation-credentials.md) přístup k místním prostředkům.
2. Otevřete účet Automation na portálu Azure.
3. Vyberte **skupinám Hybrid Worker** dlaždici a pak vyberte skupinu.
4. Vyberte **všechna nastavení** a potom **nastavení skupiny hybridních pracovních**.
5. Změna **spustit jako** z **výchozí** k **vlastní**.
6. Vyberte přihlašovací údaje a klikněte na **Uložit**.

### <a name="automation-run-as-account"></a>Účet Spustit jako automatizace
Jako součást procesu automatizované sestavení pro nasazení prostředků v Azure možná budete potřebovat přístup k místním systémy pro podporu ve vašem nasazení pořadí úloh nebo sadu kroků.  Pro podporu ověřování proti Azure pomocí účtu spustit jako, musíte nainstalovat certifikát účtu spustit jako.  

Následující Powershellový runbook *Export RunAsCertificateToHybridWorker*, exportuje certifikát spustit jako z vašeho účtu Azure Automation a soubory ke stažení a naimportuje ho do úložiště certifikátů místního počítače v hybridním pracovní připojený k stejný účet.  Po dokončení tohoto kroku, tak ověří, že pracovní proces může úspěšně ověřit do Azure pomocí účtu spustit jako.

    <#PSScriptInfo
    .VERSION 1.0
    .GUID 3a796b9a-623d-499d-86c8-c249f10a6986
    .AUTHOR Azure Automation Team
    .COMPANYNAME Microsoft
    .COPYRIGHT 
    .TAGS Azure Automation 
    .LICENSEURI 
    .PROJECTURI 
    .ICONURI 
    .EXTERNALMODULEDEPENDENCIES 
    .REQUIREDSCRIPTS 
    .EXTERNALSCRIPTDEPENDENCIES 
    .RELEASENOTES
    #>

    <#  
    .SYNOPSIS  
    Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. 
  
    .DESCRIPTION  
    This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.
    Run this runbook in the hybrid worker where you want the certificate installed.
    This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running in the hybrid worker.

    .EXAMPLE
    .\Export-RunAsCertificateToHybridWorker

    .NOTES
    AUTHOR: Azure Automation Team 
    LASTEDIT: 2016.10.13
    #>

    [OutputType([string])] 

    # Generate the password used for this certificate
    Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
    $Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)
    
    # Stop on errors
    $ErrorActionPreference = 'stop'

    # Get the management certificate that will be used to make calls into Azure Service Management resources
    $RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"
       
    # location to store temporary certificate in the Automation service host
    $CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"
   
    # Save the certificate
    $Cert = $RunAsCert.Export("pfx",$Password)
    Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose 

    Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
    $SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
    Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

    # Test that authentication to Azure Resource Manager is working
    $RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection" 
    
    Add-AzureRmAccount `
      -ServicePrincipal `
      -TenantId $RunAsConnection.TenantId `
      -ApplicationId $RunAsConnection.ApplicationId `
      -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

    Set-AzureRmContext -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

    # List automation accounts to confirm Azure Resource Manager calls are working
    Get-AzureRmAutomationAccount | Select-Object AutomationAccountName

Uložit *Export RunAsCertificateToHybridWorker* runbook do počítače s `.ps1` rozšíření.  Importujte ho do vašeho účtu Automation a upravovat sady runbook, změně hodnoty proměnné `$Password` s vlastní heslo.  Publikování a znovu spusťte sady runbook cílené na skupinu hybridních pracovních procesů, které spustit a ověření runbooků pomocí účtu spustit jako.  Stream úloh sestavy pokus o import certifikátu do úložiště místního počítače a odpovídá s více řádky v závislosti na tom, kolik účty Automation jsou definovány v rámci vašeho předplatného, a pokud je ověření úspěšné.  

## <a name="troubleshooting-runbooks-on-hybrid-runbook-worker"></a>Řešení potíží s sady runbook pro Hybrid Runbook Worker.
Protokoly se ukládají místně na každém hybridní pracovní proces na C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes.  Hybridní pracovní proces taky zaznamenává chyby a události v protokolu událostí systému Windows v rámci **aplikace a služby Logs\Microsoft-SMA\Operational**.  Události souvisejících se sadami runbook spustit v pracovním procesu se zapisují do **aplikace a služby Logs\Microsoft-Automation\Operational**.  **Microsoft SMA** protokolu zahrnuje mnoho další události související s úloha sady runbook nabídnutých do pracovního procesu a zpracování sady runbook.  Když **automatizace Microsoft** protokolu událostí s podrobnostmi o pomoc při řešení potíží s spuštění sady runbook nemá mnoho událostí, zjistíte alespoň výsledky úlohy runbooku.  

[Runbook výstup a zprávy](automation-runbook-output-and-messages.md) odešlou do Azure Automation z hybridní pracovní procesy stejně jako úlohy sady runbook běží v cloudu.  Můžete také povolit podrobné a průběh datové proudy stejným způsobem jako pro ostatní sady runbook.  

Pokud vaše sady runbook nejsou úspěšném dokončení a Souhrn úlohy zobrazuje stav **pozastaveno**, přečtěte si článek o odstraňování potíží [Hybrid Runbook Worker: ukončí úlohy runbooku se stavem pozastaveno ](automation-troubleshooting-hybrid-runbook-worker.md#a-runbook-job-terminates-with-a-status-of-suspended).   

## <a name="next-steps"></a>Další postup
* Další informace o různých metod, které můžete použít ke spuštění sady runbook najdete v tématu [spuštění sady Runbook ve službě Azure Automation](automation-starting-a-runbook.md).  
* Chcete-li pochopit různé postupy pro práci se sadami runbook Powershellu a pracovní postup prostředí PowerShell ve službě Azure Automation pomocí textový editor, přečtěte si téma [úpravy sady Runbook ve službě Azure Automation](automation-edit-textual-runbook.md)
