---
title: Instalace rozhraní .NET na role Cloudových služeb Azure | Dokumenty společnosti Microsoft
description: Tento článek popisuje ruční instalaci rozhraní .NET Framework na webové a pracovní role cloudové služby
services: cloud-services
documentationcenter: .net
author: tgore03
manager: carmonm
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 06/22/2018
ms.author: tagore
ms.openlocfilehash: c830dc0ee38ad808579a62274e3db87d0696e099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214720"
---
# <a name="install-net-on-azure-cloud-services-roles"></a>Instalace rozhraní .NET na role Azure cloudových služeb
Tento článek popisuje, jak nainstalovat verze rozhraní .NET Framework, které nejsou dodávány s hostama Azure OS. Pomocí rozhraní .NET v hostovaném ošetřovaném zařízení můžete nakonfigurovat webové a pracovní role cloudové služby.

Například můžete nainstalovat rozhraní .NET Framework 4.6.2 na rodině hostovaného operačního systému 4, který není dodáván s žádnou verzí rozhraní .NET Framework 4.6. (Rodina hostovaného operačního programu 5 přichází s rozhraním .NET Framework 4.6.) Nejnovější informace o verzích hostovaného operačního systému Azure najdete v [tématu verze vydání hosta operačního systému Azure](cloud-services-guestos-update-matrix.md). 

>[!IMPORTANT]
>Sada Azure SDK 2.9 obsahuje omezení nasazení rozhraní .NET Framework 4.6 v rodině hostovaného operačního serveru 4 nebo starší. Oprava omezení je k dispozici na webu [Microsoft Docs.](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9)

Chcete-li nainstalovat rozhraní .NET na webové a pracovní role, zahrňte do projektu cloudové služby instalační program .NET. Spusťte instalační program jako součást úloh spuštění role. 

## <a name="add-the-net-installer-to-your-project"></a>Přidání instalačního programu .NET do projektu
Chcete-li stáhnout webovou instalační službu pro rozhraní .NET Framework, zvolte verzi, kterou chcete nainstalovat:

* [Webový instalační program rozhraní .NET Framework 4.8](https://dotnet.microsoft.com/download/thank-you/net48)
* [Instalační program rozhraní .NET Framework 4.7.2](https://go.microsoft.com/fwlink/?LinkId=863262)
* [Instalační program rozhraní .NET Framework 4.6.2](https://www.microsoft.com/download/details.aspx?id=53345)

Přidání instalačního programu pro *webovou* roli:
  1. V **Průzkumníku řešení** **klikněte** v části Role v projektu cloudové služby pravým tlačítkem myši na *webovou* roli a vyberte **Přidat** > **novou složku**. Vytvořte složku s názvem **bin**.
  2. Klepněte pravým tlačítkem myši na složku přihrádky a vyberte **přidat** > **existující položku**. Vyberte instalační program .NET a přidejte ho do složky přihrádky.
  
Přidání instalačního programu pro *roli pracovního procesu:*
* Klikněte pravým tlačítkem myši na roli *pracovního procesu* a vyberte **přidat** > **existující položku**. Vyberte instalační program .NET a přidejte jej do role. 

Když jsou soubory přidány tímto způsobem do složky obsahu role, automaticky se přidají do balíčku cloudových služeb. Soubory jsou pak nasazeny do konzistentního umístění ve virtuálním počítači. Tento postup opakujte pro každou roli webu a pracovního procesu v cloudové službě, aby všechny role měly kopii instalačního programu.

> [!NOTE]
> Rozhraní .NET Framework 4.6.2 byste měli nainstalovat do role cloudové služby i v případě, že vaše aplikace cílí na rozhraní .NET Framework 4.6. Hostovaný operační systém obsahuje aktualizaci znalostní báze [3098779](https://support.microsoft.com/kb/3098779) a [aktualizaci 3097997](https://support.microsoft.com/kb/3097997). Pokud je rozhraní .NET Framework 4.6 nainstalováno nad aktualizacemi znalostní báze Knowledge Base, může dojít k problémům při spuštění aplikací .NET. Chcete-li se těmto problémům vyhnout, nainstalujte rozhraní .NET Framework 4.6.2 spíše než verzi 4.6. Další informace naleznete v [článku 3118750](https://support.microsoft.com/kb/3118750) a [4340191](https://support.microsoft.com/kb/4340191)znalostní báze Knowledge Base .
> 
> 

![Obsah role se soubory instalačního programu][1]

## <a name="define-startup-tasks-for-your-roles"></a>Definování úloh při spuštění pro role
Úlohy spuštění můžete použít k provádění operací před spuštěním role. Instalace rozhraní .NET Framework jako součást úlohy při spuštění zajišťuje, že je rozhraní nainstalováno před spuštěním kódu aplikace. Další informace o úlohách při spuštění najdete v tématu [Spouštění úloh při spuštění v Azure](cloud-services-startup-tasks.md). 

1. Přidejte následující obsah do souboru ServiceDefinition.csdef v uzlu **WebRole** nebo **WorkerRole** pro všechny role:
   
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
   
    Předchozí konfigurace spustí příkaz `install.cmd` konzoly s oprávněními správce k instalaci rozhraní .NET Framework. Konfigurace také vytvoří prvek **LocalStorage** s názvem **NETFXInstall**. Spouštěcí skript nastaví dočasnou složku tak, aby používala tento prostředek místního úložiště. 
    
    > [!IMPORTANT]
    > Chcete-li zajistit správnou instalaci rozhraní, nastavte velikost tohoto prostředku alespoň 1 024 MB.
    
    Další informace o úlohách při spuštění najdete [v tématu Běžné úlohy spuštění cloudových služeb Azure](cloud-services-startup-tasks-common.md).

2. Vytvořte soubor s názvem **install.cmd** a přidejte do něj následující instalační skript.

   Skript zkontroluje, zda je zadaná verze rozhraní .NET Framework již v počítači nainstalována dotazováním na registr. Pokud není nainstalována verze rozhraní .NET Framework, bude otevřena webová instalační služba rozhraní .NET Framework. Chcete-li pomoci vyřešit případné problémy, skript protokoluje veškerou aktivitu do souboru startuptasklog-(aktuální datum a čas).txt, který je uložen v místním úložišti **InstallLogs.**
   
   > [!IMPORTANT]
   > K vytvoření souboru install.cmd použijte základní textový editor, jako je Windows Notepad. Pokud použijete Visual Studio k vytvoření textového souboru a změnit příponu na CMD, soubor může stále obsahovat značku pořadí bajtů UTF-8. Tato značka může způsobit chybu při spuštění prvního řádku skriptu. Chcete-li se této chybě vyhnout, vytvořte první řádek skriptu příkazEM REM, který lze přeskočit zpracováním pořadí bajtů. 
   > 
   >
   
   ```cmd
   REM Set the value of netfx to install appropriate .NET Framework. 
   REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
   REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
   REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" ***** https://go.microsoft.com/fwlink/?LinkId=671729
   REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" ***** https://www.microsoft.com/download/details.aspx?id=53345
   REM ***** To install .NET 4.7 set the variable netfx to "NDP47" ***** 
   REM ***** To install .NET 4.7.1 set the variable netfx to "NDP471" ***** https://go.microsoft.com/fwlink/?LinkId=852095
   REM ***** To install .NET 4.7.2 set the variable netfx to "NDP472" ***** https://go.microsoft.com/fwlink/?LinkId=863262
   set netfx="NDP472"
   REM ***** To install .NET 4.8 set the variable netfx to "NDP48" ***** https://dotnet.microsoft.com/download/thank-you/net48
      
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
       goto exit
       
   :restart
   echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
   shutdown.exe /r /t 5 /c "Installed .NET framework" /f /d p:2:4
   
   :installed
   echo .NET (%netfx%) is installed >> %startuptasklog%
   
   :end
   echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
   
   :exit
   EXIT /B 0
   ```

3. Přidejte soubor install.cmd do každé role pomocí **funkce Přidat** > **existující položku** v **Průzkumníku řešení,** jak je popsáno výše v tomto tématu. 

    Po dokončení tohoto kroku by všechny role měly mít instalační soubor .NET a soubor install.cmd.

   ![Obsah role se všemi soubory][2]

## <a name="configure-diagnostics-to-transfer-startup-logs-to-blob-storage"></a>Konfigurace diagnostiky pro přenos spouštěcích protokolů do úložiště objektů Blob
Chcete-li zjednodušit problémy s instalací, můžete nakonfigurovat Diagnostika Azure tak, aby přenášela všechny soubory protokolu generované spouštěcím skriptem nebo instalačním programem .NET do úložiště objektů Blob Azure. Pomocí tohoto přístupu můžete zobrazit protokoly stažením souborů protokolu z úložiště objektů Blob, nikoli nutností vzdálené plochy do role.


Chcete-li nakonfigurovat diagnostiku, otevřete soubor diagnostics.wadcfgx a do uzlu **Adresáře** přidejte následující obsah: 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Tento jazyk XML konfiguruje diagnostiku pro přenos souborů v adresáři protokolu v prostředku **NETFXInstall** do účtu úložiště Diagnostika v kontejneru objektů blob **instalace netfx.**

## <a name="deploy-your-cloud-service"></a>Nasazení cloudové služby
Při nasazení cloudové služby nainstalují úlohy spuštění rozhraní .NET Framework, pokud ještě není nainstalována. Role cloudové služby jsou v *zaneprázdněném* stavu, zatímco se provádí instalace architektury. Pokud instalace rozhraní vyžaduje restartování, role služby může také restartovat. 

## <a name="additional-resources"></a>Další zdroje
* [Instalace rozhraní .NET Framework][Installing the .NET Framework]
* [Určení nainstalovaných verzí rozhraní .NET Framework][How to: Determine Which .NET Framework Versions Are Installed]
* [Poradce při potížích s instalacemi rozhraní .NET Framework][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: /dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed
[Installing the .NET Framework]: /dotnet/framework/install/guide-for-developers
[Troubleshooting .NET Framework Installations]: /dotnet/framework/install/troubleshoot-blocked-installations-and-uninstallations

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png



