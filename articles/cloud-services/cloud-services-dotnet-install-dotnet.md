---
title: Instalace rozhraní .NET pro role Azure Cloud Services | Dokumentace Microsoftu
description: Tento článek popisuje, jak ručně nainstalovat rozhraní .NET Framework na vaše cloudové služby webových a pracovních rolí
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 8d1243dc-879c-4d1f-9ed0-eecd1f6a6653
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/22/2018
ms.author: jeconnoc
ms.openlocfilehash: 698cae13b9e78de6318c28bde998e195540c513c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51256795"
---
# <a name="install-net-on-azure-cloud-services-roles"></a>Instalace rozhraní .NET pro role Azure Cloud Services
Tento článek popisuje, jak nainstalovat verze rozhraní .NET Framework, které nejsou součástí hostovaného operačního systému Azure. .NET na hostovaný operační systém můžete nakonfigurovat role cloudové služby webové a pracovní proces.

Můžete například nainstalovat hostovaného operačního systému řady 4, který není součástí žádné verze rozhraní .NET 4.6 .NET 4.6.2. (Hostovaného operačního systému řady 5 jsou součástí rozhraní .NET 4.6). Nejnovější informace o vydaných verzích hostovaného operačního systému Azure, najdete v článku [příspěvky k vydání hostovaného operačního systému Azure](cloud-services-guestos-update-matrix.md). 

>[!IMPORTANT]
>Azure SDK 2.9 obsahuje omezení o nasazení rozhraní .NET 4.6 v hostovaného operačního systému řady 4 nebo dřívější. Oprava pro omezení je k dispozici na [Microsoft Docs](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9) lokality.

Instalace rozhraní .NET pro webové a pracovní role, zahrňte projekt cloudové služby v rámci instalačního programu webové rozhraní .NET. Spusťte instalační program jako součást úlohy po spuštění této role. 

## <a name="add-the-net-installer-to-your-project"></a>Přidání instalačního programu .NET do projektu
Pokud chcete stáhnout webovou Instalační službu pro rozhraní .NET Framework, zvolte verzi, kterou chcete nainstalovat:

* [.NET 4.7.2 Webová instalační služba](https://go.microsoft.com/fwlink/?LinkId=863262)
* [.NET 4.6.2 Webová instalační služba](https://www.microsoft.com/download/details.aspx?id=53345)

Chcete-li přidat instalační program *webové* role:
  1. V **Průzkumníka řešení**v části **role** v projekt cloudové služby, klikněte pravým tlačítkem na váš *webové* roli a vyberte **přidat**  >  **Novou složku**. Vytvořte složku s názvem **bin**.
  2. Klikněte pravým tlačítkem na složku bin a vyberte **přidat** > **existující položku**. Vyberte instalační program rozhraní .NET a přidejte ho do složky bin.
  
Chcete-li přidat instalační program *pracovního procesu* role:
* Klikněte pravým tlačítkem na váš *pracovního procesu* roli a vyberte **přidat** > **existující položku**. Vyberte instalační program rozhraní .NET a přidat k roli. 

Když se přidají soubory do složky obsahu role tímto způsobem, automaticky se přidá do vašeho balíček cloudové služby. Soubory se pak nasadí do umístění konzistentní vzhledem k aplikacím na virtuálním počítači. Tento postup opakujte pro všechny webové a pracovní role v cloudové službě, tak, aby všechny role obsahují kopii instalačního programu.

> [!NOTE]
> .NET 4.6.2 byste měli nainstalovat na vaše role cloudové služby i v případě, že vaše aplikace cílí na .NET 4.6. Hostovaný operační systém zahrnuje znalostní báze [aktualizovat 3098779](https://support.microsoft.com/kb/3098779) a [aktualizovat 3097997](https://support.microsoft.com/kb/3097997). Můžou nastat problémy při spuštění aplikace technologie .NET, pokud rozhraní .NET 4.6 se nainstalovat navíc k samotnému znalostní báze aktualizace. Chcete-li se těmto problémům, nainstalujte .NET 4.6.2 spíše než verze 4.6. Další informace najdete v tématu [článku znalostní báze 3118750](https://support.microsoft.com/kb/3118750) a [4340191](https://support.microsoft.com/kb/4340191).
> 
> 

![Obsah role s soubory Instalační služby systému][1]

## <a name="define-startup-tasks-for-your-roles"></a>Definujte úlohy po spuštění pro vaše role
Úlohy po spuštění můžete použít k provádění operací před zahájením roli. Instalace rozhraní .NET Framework jako součást úlohy po spuštění se zajistí, že je nainstalováno rozhraní framework, před spuštěním jakéhokoli kódu aplikace. Další informace o úkolech po spuštění najdete v tématu [spouštění úloh po spuštění v Azure](cloud-services-startup-tasks.md). 

1. Přidejte následující obsah v souboru ServiceDefinition.csdef **WebRole** nebo **WorkerRole** uzel pro všechny role:
   
    ```xml
    <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
   
    Předchozí konfigurace spustí příkaz konzoly `install.cmd` s oprávněními správce pro instalaci rozhraní .NET Framework. Také vytvoří v konfiguraci **LocalStorage** element s názvem **NETFXInstall**. Spouštěcí skript nastaví dočasnou složku používat tento prostředek místního úložiště. 
    
    > [!IMPORTANT]
    > Aby se zajistilo správné instalaci rozhraní framework, nastavte velikost tohoto prostředku na alespoň 1 024 MB.
    
    Další informace o úkolech po spuštění najdete v tématu [úlohy při spuštění běžných Azure Cloud Services](cloud-services-startup-tasks-common.md).

2. Vytvořte soubor s názvem **soubor install.cmd** a přidejte následující skript nainstalovat do souboru.

   Tento skript ověřuje, jestli zadaná verze rozhraní .NET Framework je už nainstalovaná na počítači dotazem na registr. Pokud není nainstalovaná verze rozhraní .NET, se otevře webová instalační služba rozhraní .NET. Mohli snadněji řešit jakékoli problémy, skriptu zaznamená veškerou aktivitu do souboru startuptasklog-(aktuální datum a čas) txt, který je uložený v **InstallLogs** místního úložiště.
   
   > [!IMPORTANT]
   > Můžete vytvořit soubor install.cmd základního textového editoru, například Poznámkový blok Windows. Pokud používáte sadu Visual Studio vytvořte textový soubor a změňte jeho příponu na cmd, soubor může obsahovat stále značku pořadí bajtů kódování UTF-8. Toto označení může způsobit chybu při spuštění první řádek skriptu. K této chybě předejít, proveďte první řádek souboru, který REM – příkaz, který může vynecháno zpracování pořadí bajtů. 
   > 
   >
   
   ```cmd
   REM Set the value of netfx to install appropriate .NET Framework. 
   REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
   REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
   REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" ***** http://go.microsoft.com/fwlink/?LinkId=671729
   REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" ***** https://www.microsoft.com/download/details.aspx?id=53345
   REM ***** To install .NET 4.7 set the variable netfx to "NDP47" ***** 
   REM ***** To install .NET 4.7.1 set the variable netfx to "NDP471" ***** http://go.microsoft.com/fwlink/?LinkId=852095
   REM ***** To install .NET 4.7.2 set the variable netfx to "NDP472" ***** http://go.microsoft.com/fwlink/?LinkId=863262
   set netfx="NDP472"
   
   REM ***** Set script start timestamp *****
   set timehour=%time:~0,2%
   set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
   set "log=install.cmd started %timestamp%."
   
   REM ***** Exit script if running in Emulator *****
   if "%ComputeEmulatorRunning%"=="true" goto exit
   
   REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
   set TMP=%PathToNETFXInstall%
   set TEMP=%PathToNETFXInstall%
   
   REM ***** Setup .NET filenames and registry keys *****
   if %netfx%=="NDP472" goto NDP472
   if %netfx%=="NDP471" goto NDP471
   if %netfx%=="NDP47" goto NDP47
   if %netfx%=="NDP462" goto NDP462
   if %netfx%=="NDP461" goto NDP461
   if %netfx%=="NDP46" goto NDP46
   
   set "netfxinstallfile=NDP452-KB2901954-Web.exe"
   set netfxregkey="0x5cbf5"
   goto logtimestamp
   
   :NDP46
   set "netfxinstallfile=NDP46-KB3045560-Web.exe"
   set netfxregkey="0x6004f"
   goto logtimestamp
   
   :NDP461
   set "netfxinstallfile=NDP461-KB3102438-Web.exe"
   set netfxregkey="0x6040e"
   goto logtimestamp
   
   :NDP462
   set "netfxinstallfile=NDP462-KB3151802-Web.exe"
   set netfxregkey="0x60632"
   goto logtimestamp
   
   :NDP47
   set "netfxinstallfile=NDP47-KB3186500-Web.exe"
   set netfxregkey="0x707FE"
   goto logtimestamp
   
   :NDP471
   set "netfxinstallfile=NDP471-KB4033344-Web.exe"
   set netfxregkey="0x709fc"
   goto logtimestamp
   
   :NDP472
   set "netfxinstallfile=NDP472-KB4054531-Web.exe"
   set netfxregkey="0x70BF6"
   goto logtimestamp
   
   :logtimestamp
   REM ***** Setup LogFile with timestamp *****
   md "%PathToNETFXInstall%\log"
   set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
   set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
   echo %log% >> %startuptasklog%
   echo Logfile generated at: %startuptasklog% >> %startuptasklog%
   echo TMP set to: %TMP% >> %startuptasklog%
   echo TEMP set to: %TEMP% >> %startuptasklog%
   
   REM ***** Check if .NET is installed *****
   echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
   set /A netfxregkeydecimal=%netfxregkey%
   set foundkey=0
   FOR /F "usebackq skip=2 tokens=1,2*" %%A in (`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release 2^>nul`) do @set /A foundkey=%%C
   echo Minimum required key: %netfxregkeydecimal% -- found key: %foundkey% >> %startuptasklog%
   if %foundkey% GEQ %netfxregkeydecimal% goto installed
   
   REM ***** Installing .NET *****
   echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
   start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
   if %ERRORLEVEL%== 0 goto installed
       echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%    
       if %ERRORLEVEL%== 3010 goto restart
       if %ERRORLEVEL%== 1641 goto restart
       echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%
   
   :restart
   echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
   EXIT /B %ERRORLEVEL%
   
   :installed
   echo .NET (%netfx%) is installed >> %startuptasklog%
   
   :end
   echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
   
   :exit
   EXIT /B 0
   ```

3. Přidat soubor install.cmd ke každé roli pomocí **přidat** > **existující položku** v **Průzkumníka řešení** jak je popsáno výše v tomto tématu. 

    Po dokončení tohohle kroku by měl mít všechny role .NET instalační soubor a soubor install.cmd.

   ![Obsah role se všechny soubory][2]

## <a name="configure-diagnostics-to-transfer-startup-logs-to-blob-storage"></a>Konfigurovat diagnostiku spouštění protokolů přenosu do úložiště objektů Blob
Pro zjednodušení instalace problémů, můžete nakonfigurovat diagnostiky Azure pro přenos všechny soubory protokolů generované spouštěcí skript nebo instalačního programu .NET do Azure Blob storage. Pomocí tohoto přístupu můžete zobrazit protokoly si stáhnete soubory protokolů z úložiště objektů Blob, spíše než by bylo nutné vzdálené plochy do role.


Ke konfiguraci diagnostiky, otevřete soubor diagnostics.wadcfgx a přidejte následující obsah v části **adresáře** uzlu: 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Tato konfigurace XML nakonfiguruje diagnostiky pro přenos souborů v adresáři protokolu v **NETFXInstall** prostředek, který se účet úložiště diagnostiky v **instalace nástrojů netfx** kontejner objektů blob.

## <a name="deploy-your-cloud-service"></a>Nasazení cloudové služby
Při nasazení cloudové služby, úlohy po spuštění instalace rozhraní .NET Framework, pokud ještě není nainstalovaná. Role cloudové služby jsou v *zaneprázdněný* stavu během instalace rozhraní framework. Pokud instalace rozhraní framework vyžaduje restartování, může také restartovat službu role. 

## <a name="additional-resources"></a>Další zdroje informací:
* [Instalace rozhraní .NET Framework][Installing the .NET Framework]
* [Zjištění nainstalovaných verzí rozhraní .NET Framework][How to: Determine Which .NET Framework Versions Are Installed]
* [Řešení potíží s instalací rozhraní .NET Framework][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: /dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed
[Installing the .NET Framework]: /dotnet/framework/install/guide-for-developers
[Troubleshooting .NET Framework Installations]: /dotnet/framework/install/troubleshoot-blocked-installations-and-uninstallations

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png
