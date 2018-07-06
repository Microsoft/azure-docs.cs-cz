---
title: Začínáme se službou Azure Cloud Services a Pythonem| Dokumentace Microsoftu
description: Přehled vytváření cloudových služeb Azure včetně webových rolí a rolí pracovního procesu pomocí nástrojů Python Tools pro Visual Studio
services: cloud-services
documentationcenter: python
author: thraka
manager: timlt
editor: ''
ms.assetid: 5489405d-6fa9-4b11-a161-609103cbdc18
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: hero-article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: 7465d355b31c495049167c652a2a7e1512827d8d
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37111231"
---
# <a name="python-web-and-worker-roles-with-python-tools-for-visual-studio"></a>Webové role a role pracovních procesů Pythonu při použití nástrojů Python Tools for Visual Studio

Tento článek obsahuje přehled používání webových rolí a rolí pracovních procesů Python pomocí nástrojů [Python Tools for Visual Studio][Python Tools for Visual Studio]. Dozvíte se, jak použít službu Visual Studio k vytvoření a nasazení základní cloudové služby, která používá Python.

## <a name="prerequisites"></a>Požadavky
* [Visual Studio 2013, 2015 nebo 2017](https://www.visualstudio.com/)
* [Python Tools for Visual Studio][Python Tools for Visual Studio] (PTVS)
* [Azure SDK Tools for VS 2013][Azure SDK Tools for VS 2013] nebo  
[Azure SDK Tools for VS 2015][Azure SDK Tools for VS 2015] nebo  
[Azure SDK Tools for VS 2017][Azure SDK Tools for VS 2017]
* [Python 2.7 (32bitová verze)][Python 2.7 32-bit] nebo [Python 3.5 (32bitová verze)][Python 3.5 32-bit]

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## <a name="what-are-python-web-and-worker-roles"></a>Co jsou webové role a role pracovních procesů Pythonu?
Azure nabízí tři počítačové modely pro spouštění aplikací: [funkci Web Apps v Azure App Service][execution model-web sites], [Azure Virtual Machines][execution model-vms] a [Azure Cloud Services][execution model-cloud services]. Všechny tři modely podporují Python. Služby Cloud Services, které obsahují webové role a role pracovních procesů, zajišťují *PaaS (Platform as a Service)*. Webová role v rámci cloudové služby poskytuje vyhrazený webový server Internetové informační služby (IIS) pro hostování front-endových webových aplikací, zatímco role pracovních procesů dokážou spouštět asynchronní, dlouhotrvající nebo trvalé úlohy, které jsou nezávislé na vstupu nebo interakci uživatelů.

Další informace najdete v článku [Co je cloudová služba?].

> [!NOTE]
> *Chcete si vytvořit jednoduchý web?*
> Pokud váš scénář zahrnuje jen jednoduchý front-endový web, zvažte použití odlehčené funkce Web Apps v rámci Azure App Service. V případě potřeby budete moct snadno upgradovat na Cloud Service, až se váš web rozroste a vaše požadavky se změní. Ve <a href="/develop/python/">středisku pro vývojáře programující v Pythonu</a> najdete články věnované vývoji funkce Web Apps v rámci Azure App Service.
> <br />
> 
> 

## <a name="project-creation"></a>Vytvoření projektu
V sadě Visual Studio můžete v dialogovém okně **Nový projekt** v části **Python** vybrat **Cloudová služba Azure**.

![Dialogové okno Nový projekt](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

V průvodci cloudovou službou Azure můžete vytvořit novou webovou roli a roli pracovního procesu.

![Dialog Cloudová služba Azure](./media/cloud-services-python-ptvs/new-service-wizard.png)

Součástí šablony role pracovního procesu je často používaný kód k připojení k účtu služeb Azure Storage nebo Azure Service Bus.

![Řešení cloudových služeb](./media/cloud-services-python-ptvs/worker.png)

Do existující cloudové služby můžete kdykoli přidat webovou roli nebo roli pracovního procesu.  Můžete je buď přidat do existujících projektů ve vašem řešení, nebo můžete vytvořit nové.

![Příkaz pro přidání role](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

Cloudové služby můžou obsahovat role implementované v různých jazycích.  Můžete mít například webovou roli Pythonu implementovanou pomocí rolí pracovního procesu Django, Python nebo C#.  Mezi svými rolemi můžete snadno komunikovat pomocí front služeb Storage nebo Service Bus.

## <a name="install-python-on-the-cloud-service"></a>Instalace Pythonu v cloudové službě
> [!WARNING]
> Skripty pro nastavení, které jsou nainstalovány se službou Visual Studio (v době poslední instalace tohoto článku), nefungují. Tato část popisuje alternativní řešení.
> 
> 

Hlavní problém se skripty pro nastavení spočívá v tom, že neinstalují Python. Nejprve definujte dvě [počáteční úlohy](cloud-services-startup-tasks.md) v souboru [ServiceDefinition.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef). První úloha (**PrepPython.ps1**) stáhne a nainstaluje modul runtime Pythonu. Druhá úloha (**PipInstaller.ps1**) spustí program pip, který nainstaluje všechny případné závislosti.

Následující skripty byly napsány pro Python 3.5. Pokud chcete použít verzi 2.x Pythonu, nastavte soubor proměnné **PYTHON2** na hodnotu **on** pro obě počáteční úlohy spuštění a úlohu runtime: `<Variable name="PYTHON2" value="<mark>on</mark>" />`.

```xml
<Startup>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>
  </Task>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>

  </Task>

</Startup>
```

Proměnné **PYTHON2** a **PYPATH** je třeba přidat do počáteční úlohy pracovního procesu. Proměnná **PYPATH** se použije pouze v případě, že je proměnná **PYTHON2** nastavena na hodnotu **on**.

```xml
<Runtime>
  <Environment>
    <Variable name="EMULATED">
      <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
    </Variable>
    <Variable name="PYTHON2" value="off" />
    <Variable name="PYPATH" value="%SystemDrive%\Python27" />
  </Environment>
  <EntryPoint>
    <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
  </EntryPoint>
</Runtime>
```

#### <a name="sample-servicedefinitioncsdef"></a>Ukázkový soubor ServiceDefinition.csdef
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicePython" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
      <Setting name="Python2" />
    </ConfigurationSettings>
    <Startup>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="EMULATED">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
        </Variable>
        <Variable name="PYTHON2" value="off" />
        <Variable name="PYPATH" value="%SystemDrive%\Python27" />
      </Environment>
      <EntryPoint>
        <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
      </EntryPoint>
    </Runtime>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
  </WorkerRole>
</ServiceDefinition>
```



Dále vytvořte soubory **PrepPython.ps1** a **PipInstaller.ps1** ve složce **./bin** vaší role.

#### <a name="preppythonps1"></a>PrepPython.ps1
Tento skript nainstaluje Python. Pokud je proměnná prostředí **PYTHON2** nastavená na hodnotu **on**, nainstaluje se Python 2.7; v opačném případě se nainstaluje Python 3.5.

```powershell
[Net.ServicePointManager]::SecurityProtocol = "tls12, tls11, tls"
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if python is installed...$nl"
    if ($is_python2) {
        & "${env:SystemDrive}\Python27\python.exe"  -V | Out-Null
    }
    else {
        py -V | Out-Null
    }

    if (-not $?) {

        $url = "https://www.python.org/ftp/python/3.5.2/python-3.5.2-amd64.exe"
        $outFile = "${env:TEMP}\python-3.5.2-amd64.exe"

        if ($is_python2) {
            $url = "https://www.python.org/ftp/python/2.7.12/python-2.7.12.amd64.msi"
            $outFile = "${env:TEMP}\python-2.7.12.amd64.msi"
        }

        Write-Output "Not found, downloading $url to $outFile$nl"
        Invoke-WebRequest $url -OutFile $outFile
        Write-Output "Installing$nl"

        if ($is_python2) {
            Start-Process msiexec.exe -ArgumentList "/q", "/i", "$outFile", "ALLUSERS=1" -Wait
        }
        else {
            Start-Process "$outFile" -ArgumentList "/quiet", "InstallAllUsers=1" -Wait
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Already installed"
    }
}
```

#### <a name="pipinstallerps1"></a>PipInstaller.ps1
Tento skript volá program pip a instaluje všechny závislosti v souboru **requirements.txt**. Pokud je proměnná prostředí **PYTHON2** nastavená na hodnotu **on**, použije se Python 2.7; v opačném případě se použije Python 3.5.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if requirements.txt exists$nl"
    if (Test-Path ..\requirements.txt) {
        Write-Output "Found. Processing pip$nl"

        if ($is_python2) {
            & "${env:SystemDrive}\Python27\python.exe" -m pip install -r ..\requirements.txt
        }
        else {
            py -m pip install -r ..\requirements.txt
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Not found$nl"
    }
}
```

#### <a name="modify-launchworkerps1"></a>Úprava souboru LaunchWorker.ps1
> [!NOTE]
> V případě projektu **role pracovního procesu** je ke spuštění spouštěcího souboru vyžadován soubor **LauncherWorker.ps1**. U projektu **webové role** je spouštěcí soubor definován ve vlastnostech projektu.
> 
> 

Soubor **bin\LaunchWorker.ps1** byl původně vytvořen pro větší množství přípravných prací, ve skutečnosti však tento cíl neplní. Nahraďte obsah daného souboru následujícím skriptem.

Tento skript volá soubor **worker.py** z projektu v Pythonu. Pokud je proměnná prostředí **PYTHON2** nastavená na hodnotu **on**, použije se Python 2.7; v opačném případě se použije Python 3.5.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated)
{
    Write-Output "Running worker.py$nl"

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
else
{
    Write-Output "Running (EMULATED) worker.py$nl"

    # Customize to your local dev environment

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
```

#### <a name="pscmd"></a>ps.cmd
Šablony služby Visual Studio měly vytvořit soubor **ps.cmd** ve složce **./bin**. Tento skript prostředí volá obálkové skripty prostředí PowerShell uvedené výše a zajišťuje protokolování na základě názvu volaného obálkového skriptu prostředí PowerShell. Pokud tento soubor nebyl vytvořen, zde je uveden jeho očekávaný obsah. 

```bat
@echo off

cd /D %~dp0

if not exist "%DiagnosticStore%\LogFiles" mkdir "%DiagnosticStore%\LogFiles"
%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe -ExecutionPolicy Unrestricted -File %* >> "%DiagnosticStore%\LogFiles\%~n1.txt" 2>> "%DiagnosticStore%\LogFiles\%~n1.err.txt"
```



## <a name="run-locally"></a>Spuštění v místním prostředí
Pokud si projekt cloudové služby nastavíte jako projekt po spuštění a stisknete F5, spustí se cloudová služba v emulátoru místního prostředí Azure.

Nástroje PTVS sice podporují spouštění v emulátoru, nebude ale fungovat ladění (například zarážky).

Když budete chtít webové role a role pracovních procesů ladit, můžete jako projekt po spuštění nastavit projekt role a ladit místo toho ten.  Můžete také nastavit více projektů po spuštění.  Klikněte pravým tlačítkem myši na řešení a pak vyberte **Nastavit projekty po spuštění**.

![Vlastnosti projektu po spuštění pro řešení](./media/cloud-services-python-ptvs/startup.png)

## <a name="publish-to-azure"></a>Publikování aplikací do Azure
Když budete chtít aplikaci publikovat, klikněte pravým tlačítkem na projekt cloudové služby v řešení a pak vyberte **Publikovat**.

![Přihlášení pro publikování v Microsoft Azure](./media/cloud-services-python-ptvs/publish-sign-in.png)

Postupujte podle pokynů průvodce. V případě potřeby povolte vzdálenou plochu. Vzdálená plocha je užitečné, když potřebujete něco ladit.

Po dokončení nastavení konfigurace klikněte na **Publikovat**.

V okně výstupu uvidíte průběh a pak se zobrazí okno Protokoly aktivit Microsoft Azure.

![Okno Protokoly aktivit Microsoft Azure](./media/cloud-services-python-ptvs/publish-activity-log.png)

Pár minut bude probíhat nasazování a pak už vám na Azure začne běžet webová role a role pracovního procesu.

### <a name="investigate-logs"></a>Prozkoumání protokolů
Po spuštění virtuálního počítače cloudové služby a instalaci Pythonu si můžete prohlédnou protokoly a hledat případné zprávy o neúspěchu. Tyto protokoly jsou umístěné ve složce **C:\Resources\Directory\\{role}\LogFiles**. Soubor **PrepPython.err.txt** obsahuje alespoň jednu chybu, protože se skript pokusil zjistit, zda je nainstalován Python, a soubor **PipInstaller.err.txt** může obsahovat zprávu ohledně zastaralé verze programu pip.

## <a name="next-steps"></a>Další kroky
Další podrobné informace o práci s webovými rolemi a rolemi pracovních procesů v nástrojích Python Tools for Visual Studio najdete v dokumentaci k těmto nástrojům:

* [Projekty cloudových služeb][Cloud Service Projects]

Další podrobnosti o používání služeb Azure z vaší webové role a role pracovního procesu, například pomocí služeb Azure Storage nebo Azure Service Bus, najdete v následujících článcích:

* [Blob Service][Blob Service]
* [Služba Table service][Table Service]
* [Služba front][Queue Service]
* [Fronty služby Service Bus][Service Bus Queues]
* [Témata služby Service Bus][Service Bus Topics]

<!--Link references-->

[Co je cloudová služba?]: cloud-services-choose-me.md
[execution model-web sites]: ../app-service/app-service-web-overview.md
[execution model-vms]:../virtual-machines/windows/overview.md
[execution model-cloud services]: cloud-services-choose-me.md
[Python Developer Center]: /develop/python/

[Blob Service]:../storage/blobs/storage-python-how-to-use-blob-storage.md
[Queue Service]: ../storage/queues/storage-python-how-to-use-queue-storage.md
[Table Service]:../cosmos-db/table-storage-how-to-use-python.md
[Service Bus Queues]: ../service-bus-messaging/service-bus-python-how-to-use-queues.md
[Service Bus Topics]: ../service-bus-messaging/service-bus-python-how-to-use-topics-subscriptions.md


<!--External Link references-->

[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools for Visual Studio Documentation]: http://aka.ms/ptvsdocs
[Cloud Service Projects]: https://docs.microsoft.com/visualstudio/python/python-azure-cloud-service-project-template
[Azure SDK Tools for VS 2013]: http://go.microsoft.com/fwlink/?LinkId=746482
[Azure SDK Tools for VS 2015]: http://go.microsoft.com/fwlink/?LinkId=746481
[Azure SDK Tools for VS 2017]: http://go.microsoft.com/fwlink/?LinkId=746483
[Python 2.7 32-bit]: https://www.python.org/downloads/
[Python 3.5 32-bit]: https://www.python.org/downloads/
