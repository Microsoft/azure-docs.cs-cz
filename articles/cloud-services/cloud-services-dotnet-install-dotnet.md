---
title: Instalace rozhraní .NET pro role Azure Cloud Services | Microsoft Docs
description: Tento článek popisuje, jak ručně nainstalovat rozhraní .NET Framework na vaše cloudové služby webových a pracovních rolí
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: ''
ms.assetid: 8d1243dc-879c-4d1f-9ed0-eecd1f6a6653
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/10/2018
ms.author: adegeo
ms.openlocfilehash: 02e778154662c28b78b284fccb3bd9b9420a8bec
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34608436"
---
# <a name="install-net-on-azure-cloud-services-roles"></a>Instalace rozhraní .NET pro role Azure Cloud Services
Tento článek popisuje postup instalace verze rozhraní .NET Framework, které nejsou součástí hostovaného operačního systému Azure. .NET na hostovaného operačního systému vám pomůže nakonfigurovat role cloudové služby webové a pracovní.

Například můžete nainstalovat rozhraní .NET 4.6.1 v hostovaného operačního systému řady 4, který není součástí žádné verzi rozhraní .NET 4.6. (Hostovaného operačního systému rodiny 5 se dodávají s .NET 4.6.) Nejnovější informace o verzích hostovaného operačního systému Azure, najdete v článku [Azure hostovaného operačního systému verze zprávy](cloud-services-guestos-update-matrix.md). 

>[!IMPORTANT]
>Azure SDK 2.9 obsahuje omezení nasazení .NET 4.6 na hostovaného operačního systému rodiny 4 nebo dřívější. Oprava pro omezení je k dispozici na [Microsoft Docs](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9) lokality.

Na webových a pracovních rolí nainstalovat rozhraní .NET, patří instalační program webové rozhraní .NET v rámci projekt cloudové služby. Spusťte instalační program jako součást role spuštění úlohy. 

## <a name="add-the-net-installer-to-your-project"></a>Do projektu přidejte instalační program rozhraní .NET
Chcete-li stáhnout instalační program webové rozhraní .NET Framework, vyberte verzi, která chcete nainstalovat:

* [Instalační program webové rozhraní .NET 4.7.2](http://go.microsoft.com/fwlink/?LinkId=863262)
* [Instalační program webové rozhraní .NET 4.6.1](http://go.microsoft.com/fwlink/?LinkId=671729)

Chcete-li přidat instalační program *webové* role:
  1. V **Průzkumníku řešení**v části **role** v projekt cloudové služby, klikněte pravým tlačítkem na vaše *webové* roli a vyberte **přidat**  >  **Novou složku**. Vytvořte složku s názvem **bin**.
  2. Klikněte pravým tlačítkem na složku Koš a vyberte **přidat** > **existující položka**. Vyberte instalační program rozhraní .NET a přidejte ji do složky bin.
  
Chcete-li přidat instalační program *pracovní* role:
* Klikněte pravým tlačítkem na vaše *pracovní* roli a vyberte **přidat** > **existující položka**. Vyberte instalační program rozhraní .NET a přidejte ji do role. 

Když soubory jsou přidány do složky obsahu role tímto způsobem, se automaticky přidá do vašeho balíčku cloudové služby. Soubory se pak nasadí do konzistentní umístění na virtuálním počítači. Tento postup opakujte pro každý web a pracovní role v rámci cloudové služby tak, aby všechny role kopii Instalační služby.

> [!NOTE]
> Musíte nainstalovat rozhraní .NET 4.6.1 na vaše cloudové služby role i v případě, že vaše aplikace zaměřuje .NET 4.6. Hostovaného operačního systému zahrnuje znalostní báze [aktualizace 3098779](https://support.microsoft.com/kb/3098779) a [aktualizace 3097997](https://support.microsoft.com/kb/3097997). Problémy se můžou objevit při spuštění aplikace rozhraní .NET, pokud je nainstalován .NET 4.6 nad aktualizace znalostní báze Knowledge Base. Abyste se těmto problémům, nainstalujte rozhraní .NET 4.6.1 spíše než verze 4.6. Další informace najdete v tématu [článku znalostní báze 3118750](https://support.microsoft.com/kb/3118750).
> 
> 

![Obsah role s soubory Instalační služby systému][1]

## <a name="define-startup-tasks-for-your-roles"></a>Definování spuštění úloh pro své role
Spuštění úlohy můžete použít k provádění operací před zahájením roli. Instalace rozhraní .NET Framework jako součást spuštění úloh zajistí, že rozhraní je nainstalovat před spuštěním jakékoli kódu aplikace. Další informace o spuštění úlohy najdete v tématu [spouštět úlohy spuštění v Azure](cloud-services-startup-tasks.md). 

1. Přidejte následující obsah do souboru ServiceDefinition.csdef pod **WebRole** nebo **WorkerRole** uzlu u všech rolí:
   
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
   
    Předchozí konfiguraci spouští příkaz konzoly `install.cmd` s oprávněním správce pro instalaci rozhraní .NET Framework. Vytvoří také konfigurace **LocalStorage** element s názvem **NETFXInstall**. Spuštění skriptu Nastaví dočasnou složku používat tento prostředek Místní úložiště. 
    
    > [!IMPORTANT]
    > Aby se zajistilo správné instalaci rozhraní, nastavte velikost tohoto prostředku na alespoň 1 024 MB.
    
    Další informace o spuštění úlohy najdete v tématu [běžné Azure Cloud Services spuštění úlohy](cloud-services-startup-tasks-common.md).

2. Vytvořte soubor s názvem **soubor install.cmd** a přidejte následující nainstalujete skript do souboru.

    Skript ověřuje, zda zadaná verze rozhraní .NET Framework je již nainstalována v počítači pomocí dotazu na registru. Pokud není nainstalovaná verze rozhraní .NET, je otevřít web instalační program rozhraní .NET. Chcete-li vyřešit jakékoliv problémy, skript protokoluje všechny aktivity do souboru startuptasklog-(aktuální datum a čas) .txt, který je uložen v **InstallLogs** místní úložiště.
  
    > [!IMPORTANT]
    > K vytvoření souboru soubor install.cmd použijte základní textový editor, například Poznámkový blok systému Windows. Pokud používáte Visual Studio k vytvoření textového souboru a změňte příponu na .cmd, soubor může obsahovat stále značka pořadí bajtů ve formátu UTF-8. Tato značka může způsobit chybu při spuštění první řádek skriptu. Chcete-li se vyhnout této chybě, zkontrolujte první řádek skriptu REM příkaz, který může být vynecháno zpracování pořadí bajtů. 
    > 
    >
   
    ```cmd
    REM Set the value of netfx to install appropriate .NET Framework. 
    REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
    REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
    REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" ***** http://go.microsoft.com/fwlink/?LinkId=671729
    REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" *****
    REM ***** To install .NET 4.7 set the variable netfx to "NDP47" ***** 
    REM ***** To install .NET 4.7.1 set the variable netfx to "NDP471" ***** http://go.microsoft.com/fwlink/?LinkId=852095
    REM ***** To install .NET 4.7.2 set the variable netfx to "NDP472" ***** http://go.microsoft.com/fwlink/?LinkId=863262
    set netfx="NDP472"
    
    REM ***** Set script start timestamp *****
    set timehour=%time:~0,2%
    set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
    set "log=install.cmd started %timestamp%."
    
    REM ***** Exit script if running in Emulator *****
    if %ComputeEmulatorRunning%=="true" goto exit
    
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

3. Přidejte soubor install.cmd ke každé roli pomocí **přidat** > **existující položka** v **Průzkumníku řešení** jak je popsáno výše v tomto tématu. 

    Po dokončení tohoto kroku se všechny role měli soubor Instalační služby rozhraní .NET a soubor install.cmd.

   ![Obsah role se všechny soubory][2]

## <a name="configure-diagnostics-to-transfer-startup-logs-to-blob-storage"></a>Konfiguraci diagnostiky protokoly spuštění přenést do úložiště objektů Blob
Pro zjednodušení odstraňování problémů s instalací, můžete nakonfigurovat Azure Diagnostics pro přenos všechny soubory protokolů generovaných spouštěcí skript nebo instalačního programu .NET do úložiště objektů Blob Azure. Když použijete tuto metodu, můžete zobrazit protokoly stahování souborů protokolu z úložiště objektů Blob, namísto nutnosti vzdálenou plochu do role.

Ke konfiguraci diagnostiky, otevřete soubor diagnostics.wadcfgx a přidejte do něj následující obsah v části **adresáře** uzlu: 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Tato konfigurace XML nakonfiguruje diagnostiky pro přenos souborů v adresáři protokolu v **NETFXInstall** prostředek pro účet úložiště diagnostiky **netfx instalace** kontejner objektů blob.

## <a name="deploy-your-cloud-service"></a>Nasazení cloudové služby
Při nasazení cloudové služby, spuštění úlohy instalaci rozhraní .NET Framework, pokud je ještě není nainstalován. Role cloudové služby jsou v *zaneprázdněn* stav rozhraní je během instalace. Pokud framework instalace vyžaduje restartování, může také restartovat službu role. 

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
