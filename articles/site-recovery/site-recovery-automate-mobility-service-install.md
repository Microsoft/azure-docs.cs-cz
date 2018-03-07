---
title: "Nasazení služby Mobility obnovení lokality pomocí Azure Automation DSC. | Microsoft Docs"
description: "Popisuje, jak automaticky nasadit služby Azure Site Recovery Mobility a Azure agent pro virtuální počítač VMware a fyzické serveru replikaci do Azure pomocí Azure Automation DSC."
services: site-recovery
documentationcenter: 
author: krnese
manager: lorenr
editor: 
ms.assetid: 1f8cd3ac-0522-48eb-a5f0-679ee9192ddb
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: krnese
ms.openlocfilehash: eb837858fe81fa1f2d4966051bd4b7b068defe8b
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2018
---
# <a name="deploy-the-mobility-service-with-azure-automation-dsc-for-replication-of-vm"></a>Nasazení služby Mobility pomocí Azure Automation DSC pro replikaci virtuálního počítače
V Operations Management Suite jsme poskytují komplexní zálohování a řešení pro zotavení po havárii, které můžete použít jako součást plánu pro kontinuitu podnikových.

Spuštění této cesty společně s technologií Hyper-V pomocí repliky technologie Hyper-V. Ale jsme mít rozšířenou o podporu heterogenní instalační program, protože zákazníci mají více hypervisorů a platformy v jejich cloudy.

Pokud používáte úlohy VMware nebo fyzických serverů dnes, server pro správu všech součástí Azure Site Recovery běží v prostředí pro zpracování komunikace a data replikace s Azure, když Azure je cíl.

## <a name="deploy-the-site-recovery-mobility-service-by-using-automation-dsc"></a>Nasazení služby Mobility obnovení lokality pomocí Automation DSC
Začněme tím, provádění rychlé rozpis jaké tento server pro správu.

Server pro správu spouští několik rolí serveru. Jedna z těchto rolí se *konfigurace*, který koordinuje komunikaci a spravuje procesy data replikace a obnovení.

Kromě toho *proces* role funguje jako replikační brána. Tato role přijímá data replikace z chráněných zdrojových počítačů, optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odešle ji do účtu úložiště Azure. Jednou z funkcí pro roli proces je také nabízená instalace služby Mobility na chráněné počítače a provádět automatického zjišťování virtuálních počítačů VMware.

Pokud dojde navrácení služeb po obnovení z Azure, *hlavní cíl* role bude zpracovávat data replikace v rámci této operace.

Pro chráněné počítače spoléháme na *služba Mobility*. Tato součást je nasazený na každý počítač (virtuálního počítače VMware nebo fyzických serverů), který chcete replikovat do Azure. Se zaznamenává datové zápisy na počítači a předává je na serveru pro správu (proces role).

V případě, že pracujete s kontinuity podnikových procesů, je důležité si uvědomit, úlohy, infrastruktury a související součásti. Potom můžete splňovat požadavky na cíli času obnovení (RTO) a cíl bodu obnovení (RPO). V tomto kontextu služba Mobility je klíčem k zajištění ochrany vašich úloh podle předpokladů.

Jak tedy, optimalizované způsobem, zajistíte, že máte spolehlivé chráněné instalace pomoci z některé součásti služby Operations Management Suite?

Tento článek obsahuje příklad použití Azure Automation požadovaného stavu konfigurace (DSC), společně s Site Recovery, abyste ověřili, že:

* Služba Mobility a agenta virtuálního počítače Azure se nasadí do počítače systému Windows, které chcete chránit.
* Služba Mobility a agenta virtuálního počítače Azure jsou vždy spuštěny Azure je cílem replikace.

## <a name="prerequisites"></a>Požadavky
* Úložiště pro ukládání požadované instalace
* Úložiště pro ukládání vyžaduje heslo pro registraci se serverem pro správu

  > [!NOTE]
  > Jedinečné heslo se vygeneruje pro každý server pro správu. Pokud se chystáte nasadit více serverů pro správu, musíte zajistit, že správné heslo je uloženo v souboru passphrase.txt.
  >
  >
* Windows Management Framework (WMF) 5.0 nainstalovaný na počítačích, které chcete povolit pro ochranu (požadavek pro Automation DSC)

  > [!NOTE]
  > Pokud chcete používat počítače DSC pro systém Windows, které mají nainstalovaný WMF 4.0, najdete v části [pomocí DSC v odpojených prostředích](## Use DSC in disconnected environments).
  

Služba Mobility lze nainstalovat pomocí příkazového řádku a několika argumenty. Proto je potřeba mít binárních souborů (po jejich extrahování z vašeho nastavení) a ukládat je na místě, kde je můžete obnovit pomocí konfigurace DSC.

## <a name="step-1-extract-binaries"></a>Krok 1: Extrahování binárních souborů
1. Pokud chcete extrahovat soubory, které potřebujete pro tento instalační program, přejděte na následující adresář na vašem serveru pro správu:

    **Recovery\home\svsystems\pushinstallsvc\repository \Microsoft azure lokality**

    V této složce měli byste vidět soubor MSI s názvem:

    **Microsoft-ASR_UA_version_Windows_GA_date_Release.exe**

    Použijte následující příkaz k extrakci instalační program:

    **.\Microsoft-ASR_UA_9.1.0.0_Windows_GA_02May2016_release.exe /q /x:C:\Users\Administrator\Desktop\Mobility_Service\Extract**
2. Vyberte všechny soubory a odeslat je do složky komprimované (ZIP).

Nyní máte binární soubory, které potřebujete k automatizaci instalace služby Mobility pomocí Automation DSC.

### <a name="passphrase"></a>Přístupové heslo
Dále musíte určit, kam chcete umístit tento komprimované složce. Účet úložiště Azure můžete použít, jak je znázorněno novější ukládat heslo, které potřebujete k instalaci. Agent bude potom proveďte registraci se serverem pro správu jako součást procesu.

Do textového souboru jako passphrase.txt lze uložit heslo, které jste získali při nasazení serveru pro správu.

Umístěte komprimované složce a heslo v vyhrazené kontejneru v účtu úložiště Azure.

![Umístění složky](./media/site-recovery-automate-mobilitysevice-install/folder-and-passphrase-location.png)

Pokud chcete zachovat tyto soubory ve sdílené složce v síti, můžete tak učinit. Potřebujete zkontrolujte, zda prostředek DSC, který budete používat později přístup a můžete získat nastavení a heslo.

## <a name="step-2-create-the-dsc-configuration"></a>Krok 2: Vytvoření konfigurace DSC
Instalace závisí na WMF 5.0. Pro počítač úspěšně použít konfiguraci prostřednictvím Automation DSC musí být k dispozici WMF 5.0.

Prostředí používá následující ukázková konfigurace DSC:

```powershell
configuration ASRMobilityService {

    $RemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    $RemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    $RemoteAzureAgent = 'http://go.microsoft.com/fwlink/p/?LinkId=394789'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node localhost {

        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
```
Konfigurace bude postupujte takto:

* Proměnné se v konfiguraci umožňují získat binární soubory pro tuto službu Mobility a agenta virtuálního počítače Azure, kde lze získat heslo a kam se mají ukládat výstup.
* Konfigurace naimportuje xPSDesiredStateConfiguration DSC prostředků, tak, aby můžete použít `xRemoteFile` ke stažení souborů z úložiště.
* Konfigurace vytvoří adresář, kam chcete uložit binární soubory.
* Archiv prostředků bude extrahujte soubory z komprimované složky.
* Z UNIFIEDAGENT nainstaluje balíček prostředků instalace služby Mobility. Instalační program EXE s konkrétní argumenty. (Proměnné, které vytvořit argumenty, které je nutné změnit tak, aby se zohlednilo vaše prostředí.)
* Balíček prostředků AzureAgent nainstaluje agenta virtuálního počítače Azure, který se doporučuje pro každý virtuální počítač, který běží v Azure. Agent virtuálního počítače Azure také umožňuje přidat rozšíření do virtuálního počítače po převzetí služeb při selhání.
* Prostředek služby nebo prostředky zajistí vždy spuštěn související služby Mobility a službami Azure.

Uložte konfiguraci jako **ASRMobilityService**.

> [!NOTE]
> Nezapomeňte nahradit CSIP ve vaší konfiguraci tak, aby odrážela samotnou správu serveru, tak, aby agent správně připojí a bude používat správné heslo.
>
>

## <a name="step-3-upload-to-automation-dsc"></a>Krok 3: Nahrát do Automation DSC
Vzhledem k tomu, že konfigurace DSC, které jste provedli importuje modul požadovaný prostředek DSC (xPSDesiredStateConfiguration), je třeba importovat Tenhle modul ve službě Automation před nahráním konfigurace DSC.

Přihlaste se k účtu Automation, přejděte na **prostředky** > **moduly**a klikněte na tlačítko **procházet galerii**.

Zde můžete vyhledat modul a naimportujte ho do vašeho účtu.

![Import modulu](./media/site-recovery-automate-mobilitysevice-install/search-and-import-module.png)

Když toto dokončíte, přejděte k počítači, kde máte nainstalované moduly Azure Resource Manager a přejít k importu nově vytvořený konfigurace DSC.

### <a name="import-cmdlets"></a>Rutiny importu
V prostředí PowerShell Přihlaste se k předplatnému Azure. Upravte rutiny zohlednilo vaše prostředí a zaznamenat informace o účtu Automation v proměnné:

```powershell
$AAAccount = Get-AzureRmAutomationAccount -ResourceGroupName 'KNOMS' -Name 'KNOMSAA'
```

Odešlete konfigurace do Automation DSC pomocí následující rutiny:

```powershell
$ImportArgs = @{
    SourcePath = 'C:\ASR\ASRMobilityService.ps1'
    Published = $true
    Description = 'DSC Config for Mobility Service'
}
$AAAccount | Import-AzureRmAutomationDscConfiguration @ImportArgs
```

### <a name="compile-the-configuration-in-automation-dsc"></a>Zkompilování konfigurace v Automation DSC
Dále musíte o zkompilování konfigurace v Automation DSC, takže můžete začít registrovat uzly k němu. Můžete toho dosáhnout spuštěním následující rutiny:

```powershell
$AAAccount | Start-AzureRmAutomationDscCompilationJob -ConfigurationName ASRMobilityService
```

Může to trvat několik minut, protože v podstatě nasazujete konfigurace hostované službě vyžádání DSC.

Po konfiguraci zkompilujete, můžete načíst informace o úlohách pomocí prostředí PowerShell (Get-AzureRmAutomationDscCompilationJob) nebo pomocí [portál Azure](https://portal.azure.com/).

![Načtení úlohy](./media/site-recovery-automate-mobilitysevice-install/retrieve-job.png)

Teď úspěšně publikována a konfiguraci DSC nahrán do Automation DSC.

## <a name="step-4-onboard-machines-to-automation-dsc"></a>Krok 4: Připojit počítače k Automation DSC
> [!NOTE]
> Jeden z požadovaných součástí pro dokončení tohoto scénáře je, že vaše počítače Windows jsou aktualizovány na nejnovější verzi WMF. Můžete stáhnout a nainstalovat správnou verzi pro vaši platformu z [Download Center](https://www.microsoft.com/download/details.aspx?id=50395).
>
>

Nyní vytvoříte metaconfig pro DSC, která budou platit pro uzly. Uspět s tím, budete muset získat adresu URL koncového bodu a primární klíč pro vybrané účtu Automation v Azure. Můžete najít tyto hodnoty v části **klíče** na **všechna nastavení** okno pro účet služby Automation.

![Hodnoty klíče](./media/site-recovery-automate-mobilitysevice-install/key-values.png)

V tomto příkladu máte fyzického serveru Windows Server 2012 R2, který chcete chránit pomocí Site Recovery.

### <a name="check-for-any-pending-file-rename-operations-in-the-registry"></a>Zkontrolujte všechny čekající operace přejmenování souboru v registru
Než začnete přidružení serveru s koncovým bodem Automation DSC, doporučujeme zkontrolovat pro všechny čekající operace přejmenování souboru v registru. Instalace se může mají zakázáno dokončení kvůli čeká na restartování.

Spusťte následující rutiny ověřte, zda je žádné čeká na restartování na serveru:

```powershell
Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\' | Select-Object -Property PendingFileRenameOperations
```
Pokud se zobrazí prázdné, jste na tlačítko OK. V opačném případě by to vyřešit tak, že restartování serveru během časového období údržby.

Použít konfiguraci na serveru, spusťte prostředí PowerShell integrovaném skriptovacím prostředí (ISE) a spusťte následující skript. Toto je v podstatě DSC místní konfiguraci, která bude určit, aby modul správce místní konfigurace zaregistrovat službu Automation DSC a načíst konkrétní konfiguraci (ASRMobilityService.localhost).

```powershell
[DSCLocalConfigurationManager()]
configuration metaconfig {
    param (
        $URL,
        $Key
    )
    node localhost {
        Settings {
            RefreshFrequencyMins = '30'
            RebootNodeIfNeeded = $true
            RefreshMode = 'PULL'
            ActionAfterReboot = 'ContinueConfiguration'
            ConfigurationMode = 'ApplyAndMonitor'
            AllowModuleOverwrite = $true
        }

        ResourceRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }

        ConfigurationRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
            ConfigurationNames = 'ASRMobilityService.localhost'
        }

        ReportServerWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }
    }
}
metaconfig -URL 'https://we-agentservice-prod-1.azure-automation.net/accounts/<YOURAAAccountID>' -Key '<YOURAAAccountKey>'

Set-DscLocalConfigurationManager .\metaconfig -Force -Verbose
```

Tato konfigurace způsobí, že modul správce místní konfigurace k registraci v Automation DSC. Také se určí, jak pracovat modul, jak ho postupovat, pokud je konfigurace odlišily (ApplyAndAutoCorrect) a jak by měla postupovat s konfigurací Pokud je vyžadován restart.

Po spuštění tohoto skriptu uzlu začněte registrace u služby Automation DSC.

![Registrace uzlu v průběhu](./media/site-recovery-automate-mobilitysevice-install/register-node.png)

Pokud přejdete zpět na portál Azure, uvidíte, že nově zaregistrovaný uzel má nyní zobrazovaly v portálu.

![Registrovaný uzlu na portálu](./media/site-recovery-automate-mobilitysevice-install/registered-node.png)

Na serveru můžete spustit následující rutiny prostředí PowerShell k ověření, že správně zaregistrovány uzlu:

```powershell
Get-DscLocalConfigurationManager
```

Po konfiguraci byl vyžádat a použít na server, můžete to ověřit spuštěním následující rutiny:

```powershell
Get-DscConfigurationStatus
```

Výstup ukazuje, že server úspěšně vyžádat jeho konfigurace:

![Výstup](./media/site-recovery-automate-mobilitysevice-install/successful-config.png)

Kromě toho instalace služby Mobility má svou vlastní protokolu, který se nachází v *SystemDrive*\ProgramData\ASRSetupLogs.

Je to. Teď úspěšně nasazen a registrované služby Mobility na počítači, který chcete chránit pomocí Site Recovery. DSC se ujistěte, že jsou vždy neběží požadované služby.

![Úspěšné nasazení](./media/site-recovery-automate-mobilitysevice-install/successful-install.png)

Po úspěšné nasazení zjistí, server pro správu, musíte nakonfigurovat ochranu a zapnout replikaci na počítači pomocí Site Recovery.

## <a name="use-dsc-in-disconnected-environments"></a>Použití DSC v odpojených prostředích
Pokud vaše počítače nejsou připojené k Internetu, můžete stále spolehnout na DSC k nasazení a konfiguraci služby Mobility na jiné úlohy, které chcete chránit.

Můžete vytvořit vlastní server DSC za instanci ve vašem prostředí a v podstatě poskytovat stejné funkce, které jste získali z Automation DSC. To znamená klienti načte konfiguraci (po dokončení registrace) ke koncovému bodu DSC. Další možností je ručně push konfigurace DSC na počítače, místně nebo vzdáleně.

Všimněte si, že v tomto příkladu je přidaný parametr pro název počítače. Vzdálené soubory, které jsou umístěné ve vzdálené sdílené složce, která musí být přístupný pomocí počítače, které chcete chránit. Konec skript představuje konfigurace a pak spustí použít konfigurace DSC k cílovému počítači.

### <a name="prerequisites"></a>Požadavky
Ujistěte se, že je nainstalován modul PowerShell xPSDesiredStateConfiguration. Pro počítače s Windows, kde je nainstalován WMF 5.0 můžete nainstalovat modul xPSDesiredStateConfiguration spuštěním následující rutiny na cílových počítačích:

```powershell
Find-Module -Name xPSDesiredStateConfiguration | Install-Module
```

Můžete také stáhnout a uložit v modulu v případě, že budete muset distribuovat do počítače s Windows, které mají WMF 4.0. Spusťte tuto rutinu na počítači, kde se nachází PowerShellGet (WMF 5.0):

```powershell
Save-Module -Name xPSDesiredStateConfiguration -Path <location>
```

Také pro WMF 4.0, zkontrolujte, zda [Windows 8.1 update KB2883200](https://www.microsoft.com/download/details.aspx?id=40749) je nainstalován na počítačích.

Následující konfigurace můžete vloží do počítače s Windows, které mají WMF 5.0 a WMF 4.0:

```powershell
configuration ASRMobilityService {
    param (
        [Parameter(Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [System.String] $ComputerName
    )

    $RemoteFile = '\\myfileserver\share\asr.zip'
    $RemotePassphrase = '\\myfileserver\share\passphrase.txt'
    $RemoteAzureAgent = '\\myfileserver\share\AzureVmAgent.msi'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node $ComputerName {      
        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
ASRMobilityService -ComputerName 'MyTargetComputerName'

Start-DscConfiguration .\ASRMobilityService -Wait -Force -Verbose
```

Pokud chcete vytvořit vlastní server DSC za ve vaší podnikové síti tak, aby napodoboval možností, které můžete získat z Automation DSC, přečtěte si téma [nastavení webového serveru vyžádané replikace DSC](https://msdn.microsoft.com/powershell/dsc/pullserver?f=255&MSPPError=-2147217396).

## <a name="optional-deploy-a-dsc-configuration-by-using-an-azure-resource-manager-template"></a>Volitelné: Konfigurace DSC nasazení pomocí šablony Azure Resource Manager
Tento článek se zaměřuje na tom, jak můžete vytvořit vlastní konfigurace DSC pro automatické nasazení služby Mobility a Agent virtuálního počítače Azure – a ujistěte se, která běží na počítačích, které chcete chránit. Máme také šablonu Azure Resource Manager, nasadíte tuto konfiguraci DSC na nový nebo existující účet automatizace Azure. Šablonu bude používat vstupních parametrů k vytvoření automatizace prostředky, které budou obsahovat proměnné prostředí.

Po nasazení šablony, jednoduše najdete ke kroku 4 v tomto průvodci zařadit do vašeho počítače.

Šablonu bude postupujte takto:

1. Použít existující účet Automation nebo vytvořit novou
2. Trvat vstupní parametry:
   * ASRRemoteFile – umístění pro uložení instalace služby Mobility
   * ASRPassphrase – umístění, kam jste uložili soubor passphrase.txt
   * ASRCSEndpoint – IP adresa serveru pro správu
3. Naimportujte modul Powershellu xPSDesiredStateConfiguration
4. Vytvoření a kompilaci konfigurace DSC

Aby mohl začít registrace vašich počítačů pro ochranu, stane se všechny předchozí kroky ve správném pořadí.

Šablona, s pokyny pro nasazení, se nachází na [Githubu](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/DSC).

Nasazení šablony pomocí prostředí PowerShell:

```powershell
$RGDeployArgs = @{
    Name = 'DSC3'
    ResourceGroupName = 'KNOMS'
    TemplateFile = 'https://raw.githubusercontent.com/krnese/AzureDeploy/master/OMS/MSOMS/DSC/azuredeploy.json'
    OMSAutomationAccountName = 'KNOMSAA'
    ASRRemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    ASRRemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    ASRCSEndpoint = '10.0.0.115'
    DSCJobGuid = [System.Guid]::NewGuid().ToString()
}
New-AzureRmResourceGroupDeployment @RGDeployArgs -Verbose
```

## <a name="next-steps"></a>Další postup
Poté, co nasadíte agenty služby Mobility, můžete [povolit replikaci](site-recovery-vmware-to-azure.md) pro virtuální počítače.
