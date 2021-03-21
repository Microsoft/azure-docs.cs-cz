---
title: Nainstalovat .NET v Azure Cloud Services (Classic) role | Microsoft Docs
description: Tento článek popisuje, jak ručně nainstalovat .NET Framework na webové role a role pracovních procesů cloudové služby.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: aa05fc9f02c26192762ed34db54b60b4760bf3bf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99061847"
---
# <a name="install-net-on-azure-cloud-services-classic-roles"></a>Instalace rolí .NET v Azure Cloud Services (Classic)

> [!IMPORTANT]
> [Azure Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md) je nový model nasazení založený na Azure Resource Manager pro produkt Azure Cloud Services.V důsledku této změny se Azure Cloud Services běžící na modelu nasazení založeném na Azure Service Manager přejmenovala jako Cloud Services (Classic) a všechna nová nasazení by měla používat [Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md).

Tento článek popisuje, jak nainstalovat verze .NET Framework, které nepřichází v hostovaném operačním systému Azure. .NET v hostovaném operačním systému můžete použít ke konfiguraci webových rolí a rolí pracovních procesů cloudové služby.

Můžete například nainstalovat .NET Framework 4.6.2 v hostovaném operačním systému řady 4, který nepochází z verze .NET Framework 4,6. (Řada hostovaných operačních systémů 5 se dodává s .NET Framework 4,6.) Nejnovější informace o vydáních hostovaného operačního systému Azure najdete v článku [novinky k vydání hostovaného operačního systému Azure](cloud-services-guestos-update-matrix.md). 

>[!IMPORTANT]
>Sada Azure SDK 2,9 obsahuje omezení na nasazení .NET Framework 4,6 na řadě operačních systémů hostovaného operačního systému 4 nebo starší verze. Oprava omezení je k dispozici na [Microsoft docs](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9) lokalitě.

Pokud chcete nainstalovat .NET na webové a pracovní role, přidejte do projektu cloudové služby webový instalační program .NET. Spusťte instalační program jako součást úloh po spuštění role. 

## <a name="add-the-net-installer-to-your-project"></a>Přidání instalačního programu .NET do projektu
Chcete-li stáhnout webový instalační program pro .NET Framework, vyberte verzi, kterou chcete nainstalovat:

* [Webová instalační služba .NET Framework 4,8](https://go.microsoft.com/fwlink/?LinkId=2150985)
* [Webová instalační služba .NET Framework 4.7.2](https://go.microsoft.com/fwlink/?LinkId=863262)
* [Webová instalační služba .NET Framework 4.6.2](https://www.microsoft.com/download/details.aspx?id=53345)

Přidání instalačního programu pro *webovou* roli:
  1. V **Průzkumník řešení** v části **role** v projektu cloudové služby klikněte pravým tlačítkem na *webovou* roli a vyberte **Přidat**  >  **novou složku**. Vytvořte složku s názvem **bin**.
  2. Klikněte pravým tlačítkem na složku bin a vyberte **Přidat**  >  **existující položku**. Vyberte instalační program .NET a přidejte ho do složky bin.
  
Postup přidání instalačního programu pro roli *pracovního procesu* :
* Klikněte pravým tlačítkem na roli *pracovního procesu* a vyberte **Přidat**  >  **existující položku**. Vyberte instalační program .NET a přidejte ho do role. 

Když se soubory přidávají tímto způsobem do složky obsahu role, automaticky se přidají do balíčku cloudové služby. Soubory se pak nasadí do konzistentního umístění na virtuálním počítači. Tento postup opakujte pro každou webovou roli a roli pracovního procesu v cloudové službě, aby všechny role měly kopii instalačního programu.

> [!NOTE]
> Do role cloudové služby byste měli nainstalovat .NET Framework 4.6.2, i když vaše aplikace cílí .NET Framework 4,6. Hostovaný operační systém zahrnuje aktualizaci znalostní báze [3098779](https://support.microsoft.com/kb/3098779) a [aktualizaci 3097997](https://support.microsoft.com/kb/3097997). Problémy se můžou vyskytnout, když spustíte aplikace .NET, pokud je na aktualizace znalostní báze nainstalovaná .NET Framework 4,6. Chcete-li se těmto problémům vyhnout, nainstalujte .NET Framework 4.6.2 místo verze 4,6. Další informace najdete v [článku znalostní báze Knowledge Base 3118750](https://support.microsoft.com/kb/3118750) a [4340191](https://support.microsoft.com/kb/4340191).
> 
> 

![Obsah role s instalačními soubory][1]

## <a name="define-startup-tasks-for-your-roles"></a>Definování úloh po spuštění pro vaše role
Úlohy po spuštění můžete použít k provádění operací před spuštěním role. Instalace .NET Framework jako součást úlohy po spuštění zajistí, že se rozhraní nainstaluje před spuštěním libovolného kódu aplikace. Další informace o úlohách po spuštění najdete v tématu [spuštění úloh po spuštění v Azure](cloud-services-startup-tasks.md). 

1. Přidejte následující obsah do souboru ServiceDefinition. csdef pod uzlem **webrole** nebo **role pracovního procesu** pro všechny role:
   
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
   
    Předchozí konfigurace spustí příkaz Console `install.cmd` s oprávněními správce k instalaci .NET Framework. Konfigurace také vytvoří element **localStorage** s názvem **NETFXInstall**. Spouštěcí skript nastaví dočasnou složku pro použití tohoto místního prostředku úložiště. 
    
    > [!IMPORTANT]
    > Chcete-li zajistit správnou instalaci rozhraní, nastavte velikost tohoto prostředku na alespoň 1 024 MB.
    
    Další informace o úlohách po spuštění najdete v tématu [běžné úlohy při spuštění služby Azure Cloud Services](cloud-services-startup-tasks-common.md).

2. Vytvořte soubor s názvem **install. cmd** a přidejte do souboru následující instalační skript.

   Skript zkontroluje, zda je v počítači již nainstalována zadaná verze .NET Framework pomocí dotazu do registru. Pokud není nainstalovaná verze .NET Framework, otevře se webová instalační služba .NET Framework. Aby bylo možné řešit případné problémy, skript zaznamená všechny aktivity do souboru startuptasklog-(aktuální datum a čas). txt, který je uložený v **InstallLogs** místním úložišti.
   
   > [!IMPORTANT]
   > Pomocí základního textového editoru, jako je Poznámkový blok systému Windows, vytvořte soubor Install. cmd. Pokud používáte sadu Visual Studio k vytvoření textového souboru a změnu rozšíření na. cmd, soubor může stále obsahovat značku pořadí bajtů UTF-8. Tato značka může způsobit chybu při spuštění prvního řádku skriptu. Chcete-li se této chybě vyhnout, udělejte první řádek příkazu Script a příkazu REM, který může být vynechán zpracováním pořadí bajtů. 
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

3. Pomocí příkazu **Přidat** existující položku v Průzkumník řešení přidejte do každé role soubor Install. cmd  >   , jak je popsáno dříve v tomto tématu.  

    Po dokončení tohoto kroku musí mít všechny role soubor Instalační služby .NET a soubor Install. cmd.

   ![Obsah role se všemi soubory][2]

## <a name="configure-diagnostics-to-transfer-startup-logs-to-blob-storage"></a>Konfigurace diagnostiky pro přenos protokolů spuštění do úložiště objektů BLOB
Pro zjednodušení potíží s instalací můžete nakonfigurovat Azure Diagnostics pro přenos všech souborů protokolu generovaných spouštěcím skriptem nebo instalačním programem .NET do úložiště objektů BLOB v Azure. Pomocí tohoto přístupu můžete zobrazit protokoly stažením souborů protokolu z úložiště objektů blob, nikoli pomocí vzdálené plochy do role.


Chcete-li konfigurovat diagnostiku, otevřete soubor Diagnostics. wadcfgx a přidejte do uzlu **adresáře** následující obsah: 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Tento kód XML provede konfiguraci diagnostiky pro přenos souborů v adresáři protokolu v prostředku **NETFXInstall** do účtu úložiště diagnostiky v kontejneru objektů BLOB **netfx-Install** .

## <a name="deploy-your-cloud-service"></a>Nasazení cloudové služby
Když nasadíte cloudovou službu, úlohy po spuštění nainstalují .NET Framework, pokud ještě není nainstalovaná. Role cloudové služby jsou po instalaci rozhraní v *zaneprázdněném* stavu. Pokud instalace rozhraní vyžaduje restart, můžou se taky restartovat role služby. 

## <a name="additional-resources"></a>Další zdroje informací
* [Instalace rozhraní .NET Framework][Installing the .NET Framework]
* [Určit, které verze .NET Framework jsou nainstalovány][How to: Determine Which .NET Framework Versions Are Installed]
* [Řešení potíží s .NET Framework instalací][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: /dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed
[Installing the .NET Framework]: /dotnet/framework/install/guide-for-developers
[Troubleshooting .NET Framework Installations]: /dotnet/framework/install/troubleshoot-blocked-installations-and-uninstallations

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png



