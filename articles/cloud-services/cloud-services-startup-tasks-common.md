---
title: Běžné úlohy po spuštění pro Cloud Services | Dokumentace Microsoftu
description: Obsahuje některé příklady běžné úlohy po spuštění, které můžete provádět v cloud services webové role nebo role pracovního procesu.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: a7095dad-1ee7-4141-bc6a-ef19a6e570f1
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeconnoc
ms.openlocfilehash: 0737738bfd0ab27898631263f57302d15ee11d53
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006542"
---
# <a name="common-cloud-service-startup-tasks"></a>Běžné úlohy po spuštění cloudové služby
Tento článek obsahuje několik příkladů běžných úloh po spuštění, které můžete provádět v cloudové službě. Úlohy po spuštění můžete použít k provádění operací před zahájením roli. Operace, které můžete chtít provést zahrnout instalaci součásti, registrace komponent COM, nastavení klíče registru nebo spouští se dlouho běžící proces. 

Zobrazit [v tomto článku](cloud-services-startup-tasks.md) pochopit, jak fungují úlohy po spuštění a konkrétně jak vytvořit záznamy, které definují úlohy po spuštění.

> [!NOTE]
> Úlohy po spuštění se nedají použít k virtuálním počítačům, jenom pro cloudové služby Web a role pracovního procesu.
> 

## <a name="define-environment-variables-before-a-role-starts"></a>Definování proměnné prostředí před spuštěním role
Pokud potřebujete proměnné prostředí definované pro specifické úlohy, použijte [prostředí] element v rámci [Úkol] elementu.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
                <Environment>
                    <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
                </Environment>
            </Task>
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

Můžete také použít proměnné [platná hodnota Azure XPath](cloud-services-role-config-xpath.md) odkazovat něco o tomto nasazení. Namísto použití `value` atribut, definovat [RoleInstanceValue] podřízený element.

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## <a name="configure-iis-startup-with-appcmdexe"></a>Konfigurace spuštění služby IIS pomocí AppCmd.exe
[AppCmd.exe](https://technet.microsoft.com/library/jj635852.aspx) nástroj příkazového řádku můžete použít ke správě nastavení služby IIS při spuštění v Azure. *AppCmd.exe* poskytuje pohodlné, příkazového řádku přístup k nastavení konfigurace pro použití v úlohy po spuštění v Azure. Pomocí *AppCmd.exe*, nastavení webu lze přidat, upravit nebo odebrat aplikace a weby.

Existuje však několik věcí, které dávejte pozor na použití *AppCmd.exe* jako úloha po spuštění:

* Úlohy po spuštění můžete spustit více než jednou mezi jednotlivými restartováními. Například když role recykluje.
* Pokud *AppCmd.exe* akce se provádí více než jednou, to může způsobit chybu. Například pokus o přidání sekce pro *Web.config* dvakrát může způsobit chybu.
* Úlohy po spuštění selhat, pokud vrátí nenulový ukončovací kód nebo **errorlevel**. Například když *AppCmd.exe* dojde k chybě.

Je vhodné zkontrolovat **errorlevel** po volání *AppCmd.exe*, což je snadné dělat, když zabalte volání do *AppCmd.exe* s *.cmd* souboru. Pokud zjišťování známého **errorlevel** odpovědi, můžete ji ignorovat, nebo předávání zpátky.

Errorlevel vrácený *AppCmd.exe* jsou uvedené v souboru winerror.h, můžete zobrazit také na [MSDN](https://msdn.microsoft.com/library/windows/desktop/ms681382.aspx).

### <a name="example-of-managing-the-error-level"></a>Příklad správy úroveň chyby
V tomto příkladu přidá pro formát JSON do části komprese a komprese položku *Web.config* souboru, zpracování chyb a protokolování.

V příslušných oddílech [ServiceDefinition.csdef] souboru se tady zobrazí, mezi které patří nastavení [kontextu executionContext](https://msdn.microsoft.com/library/azure/gg557552.aspx#Task) atribut `elevated` poskytnout *AppCmd.exe* dostatečná oprávnění ke změně nastavení *Web.config* souboru:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

*Startup.cmd* batch používá soubor *AppCmd.exe* přidat oddíl komprese a komprese položka pro formát JSON k *Web.config* souboru. Očekávané **errorlevel** 183 se nastaví na nulovou pomocí ověřit. Program v příkazovém řádku souboru EXE. Neočekávané hodnoty parametru ERRORLEVEL při jsou protokolovány StartupErrorLog.txt.

```cmd
REM   *** Add a compression section to the Web.config file. ***
%windir%\system32\inetsrv\appcmd set config /section:urlCompression /doDynamicCompression:True /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1

REM   ERRORLEVEL 183 occurs when trying to add a section that already exists. This error is expected if this
REM   batch file were executed twice. This can occur and must be accounted for in a Azure startup
REM   task. To handle this situation, set the ERRORLEVEL to zero by using the Verify command. The Verify
REM   command will safely set the ERRORLEVEL to zero.
IF %ERRORLEVEL% EQU 183 DO VERIFY > NUL

REM   If the ERRORLEVEL is not zero at this point, some other error occurred.
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding a compression section to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   *** Add compression for json. ***
%windir%\system32\inetsrv\appcmd set config  -section:system.webServer/httpCompression /+"dynamicTypes.[mimeType='application/json; charset=utf-8',enabled='True']" /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
IF %ERRORLEVEL% EQU 183 VERIFY > NUL
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding the JSON compression type to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   *** Exit batch file. ***
EXIT /b 0

REM   *** Log error and exit ***
:ErrorExit
REM   Report the date, time, and ERRORLEVEL of the error.
DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
ECHO An error occurred during startup. ERRORLEVEL = %ERRORLEVEL% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT %ERRORLEVEL%
```

## <a name="add-firewall-rules"></a>Přidání pravidla brány firewall
V Azure jsou efektivně dvě brány firewall. První brány firewall řídí připojení mezi virtuálním počítačem a okolním světem. Tato brána firewall je řízen [koncové body] element v [ServiceDefinition.csdef] souboru.

Druhá brána firewall řídí připojení mezi virtuálním počítačem a procesy v rámci tohoto virtuálního počítače. Tato brána firewall mohou být řízena `netsh advfirewall firewall` nástroj příkazového řádku.

Azure vytvoří pravidla brány firewall pro procesy spuštěné v rámci své role. Například při spuštění služby nebo programu Azure automaticky vytvoří pravidla nezbytné firewallu povolující tuto službu ke komunikaci s Internetem. Nicméně pokud vytvoříte službu, která se spustí proces mimo vaše role (jako je služba COM + nebo naplánované úlohy Windows), musíte ručně vytvořit pravidlo brány firewall umožňující přístup k této službě. Pomocí úlohy po spuštění můžete vytvořit tato pravidla brány firewall.

Úlohy po spuštění, který vytvoří pravidlo brány firewall musí mít [kontextu executionContext][úkol] z **se zvýšenými oprávněními**. Následující úloha spuštění pro přidání [ServiceDefinition.csdef] souboru.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="AddFirewallRules.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

Chcete-li přidat pravidla brány firewall, je nutné použít odpovídající `netsh advfirewall firewall` příkazů v dávkovém souboru při spuštění. V tomto příkladu úloha po spuštění vyžaduje zabezpečení a šifrování pro TCP port 80.

```cmd
REM   Add a firewall rule in a startup task.

REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="block-a-specific-ip-address"></a>Blokovat konkrétní IP adresu
Můžete omezit přístup Azure web role na sadu ze zadaných IP adres tak, že upravíte vaší služby IIS **web.config** souboru. Budete také muset použít příkazový soubor, který odemyká **ipSecurity** část **ApplicationHost.config** souboru.

Odemknout **ipSecurity** část **ApplicationHost.config** soubor, vytvořit soubor příkazů, která se spouští na začátku roli. Vytvoření složky na kořenové úrovni vaši webovou roli s názvem **spuštění** a v této složce vytvořte dávkový soubor s názvem **startup.cmd**. Přidejte tento soubor do projektu sady Visual Studio a nastavte vlastnosti na **vždy Kopírovat** zajistit, že je součástí vašeho balíčku.

Následující úloha spuštění pro přidání [ServiceDefinition.csdef] souboru.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="startup.cmd" executionContext="elevated" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Přidejte tento příkaz **startup.cmd** souboru:

```cmd
@echo off
@echo Installing "IPv4 Address and Domain Restrictions" feature 
powershell -ExecutionPolicy Unrestricted -command "Install-WindowsFeature Web-IP-Security"
@echo Unlocking configuration for "IPv4 Address and Domain Restrictions" feature 
%windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity
```

Způsobí, že tato úloha **startup.cmd** dávkový soubor se spustí pokaždé, když je inicializován ve webové roli, zajistit, aby požadované **ipSecurity** části je odemknuté.

Nakonec upravte [oddíl system.webServer](http://www.iis.net/configreference/system.webserver/security/ipsecurity#005) webovou roli **web.config** soubor a přidejte seznam IP adres, které je udělen přístup, jak je znázorněno v následujícím příkladu:

Tato ukázka konfigurace **umožňuje** všechny IP adresy pro přístup k serveru s výjimkou dvě definice

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are granted access-->
    <ipSecurity>
        <!--The following IP addresses are denied access-->
        <add allowed="false" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="false" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

Tato ukázka konfigurace **zakazuje** všechny IP adresy v přístupu k serveru s výjimkou dvou definované.

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are denied access-->
    <ipSecurity allowUnlisted="false">
        <!--The following IP addresses are granted access-->
        <add allowed="true" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="true" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

## <a name="create-a-powershell-startup-task"></a>Vytvoření úlohy po spuštění Powershellu
Skripty prostředí Windows PowerShell nelze volat přímo z [ServiceDefinition.csdef] souboru, ale může být vyvolána z v rámci spuštění dávkového souboru.

PowerShell (ve výchozím nastavení) nejde spustit nepodepsané skripty. Pokud se váš skript, musíte nakonfigurovat prostředí PowerShell ke spouštění nepodepsaných skriptů. Ke spouštění nepodepsaných skriptů **ExecutionPolicy** musí být nastaveno na **Unrestricted**. **ExecutionPolicy** nastavení, které jste používání je založen na verzi prostředí Windows PowerShell.

```cmd
REM   Run an unsigned PowerShell script and log the output
PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

Při použití hostovaného operačního systému, který je spuštění PowerShell 2.0 nebo verze 2 pro spuštění můžete vynutit 1.0 a pokud není k dispozici, použijte verzi 1.

```cmd
REM   Attempt to set the execution policy by using PowerShell version 2.0 syntax.
PowerShell -Version 2.0 -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If PowerShell version 2.0 isn't available. Set the execution policy by using the PowerShell
IF %ERRORLEVEL% EQU -393216 (
   PowerShell -Command "Set-ExecutionPolicy Unrestricted" >> "%TEMP%\StartupLog.txt" 2>&1
   PowerShell .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
)

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="create-files-in-local-storage-from-a-startup-task"></a>Vytvoření souborů v místním úložišti z úlohy po spuštění
Prostředek místního úložiště můžete ukládat soubory vytvořené ve vaší úloze po spuštění, která se později využívají vaše aplikace.

Chcete-li vytvořit místní úložiště prostředků, přidejte [LocalResources] části [ServiceDefinition.csdef] a pak přidejte [LocalStorage] podřízený element. Zadejte místní úložiště prostředků jedinečný název a odpovídající velikost pro úkol při spuštění.

Použití místního úložiště prostředků ve vaší úloze po spuštění, budete muset vytvořit proměnné prostředí odkazovat na umístění prostředků místního úložiště. Po spuštění úlohy a aplikace budou moct číst a zapisovat soubory prostředků místní úložiště.

V příslušných oddílech **ServiceDefinition.csdef** souboru se tady zobrazí:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">
    ...

    <LocalResources>
      <LocalStorage name="StartupLocalStorage" sizeInMB="5"/>
    </LocalResources>

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="PathToStartupStorage">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
  </WorkerRole>
</ServiceDefinition>
```

Jako příklad to **Startup.cmd** batch používá soubor **PathToStartupStorage** proměnnou prostředí k vytvoření souboru **MyTest.txt** na místní úložiště.

```cmd
REM   Create a simple text file.

ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

REM   Exit the batch file with ERRORLEVEL 0.

EXIT /b 0
```

Místní úložiště složky můžete přistupovat ze sady Azure SDK pomocí [GetLocalResource](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) metody.

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTestFile.txt"));
```

## <a name="run-in-the-emulator-or-cloud"></a>Spustit v emulátoru nebo v cloudu
Vaše úloha po spuštění kroků různých při provozu v cloudu, když je v emulátoru služby compute v porovnání s může mít. Můžete například použít novou kopii dat SQL jenom při spouštění v emulátoru. Nebo můžete chtít provést některé optimalizace výkonu pro cloud, který není nutné provést při spouštění v emulátoru.

Tato schopnost provádět různé akce na emulátor služby výpočty a cloudy, které lze provést tak, že vytvoříte proměnnou prostředí v [ServiceDefinition.csdef] souboru. Tato proměnná prostředí pro hodnotu potom otestovat ve vaší úloze po spuštění.

Chcete-li vytvořit proměnnou prostředí, přidejte [Proměnná]/[RoleInstanceValue] elementu a vytvořit hodnotu XPath `/RoleEnvironment/Deployment/@emulated`. Hodnota **ComputeEmulatorRunning %** proměnná prostředí je `true` při spuštění na emulátoru služby compute, a `false` při spouštění v cloudu.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">

    ...

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>

  </WorkerRole>
</ServiceDefinition>
```

Nyní můžete zkontrolovat úlohy **ComputeEmulatorRunning %** proměnnou prostředí k provádění různých akcí založené na tom, jestli role běží v cloudu nebo v emulátoru. Tady je skript prostředí cmd, který kontroluje pro danou proměnnou prostředí.

```cmd
REM   Check if this task is running on the compute emulator.

IF "%ComputeEmulatorRunning%" == "true" (
    REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
) ELSE (
    REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
)
```


## <a name="detect-that-your-task-has-already-run"></a>Zjištění, že vaše úloha již byla spuštěna
Role může recyklovat bez restartování způsobí spuštění úkolů znovu spustit. Neexistuje žádný příznak označující, že úkol již spuštěn na hostování virtuálních počítačů. Může mít některé úlohy, ve kterém nezáleží na spuštění více než jednou. Však může narazíte na situace, kdy potřebujete zabránit úlohu ve spuštění více než jednou.

Nejjednodušší způsob, jak zjistit, že úkol již spuštěn, je vytvoření souboru v **% TEMP %** složky, pokud je úloha úspěšná a podívejte se na začátku úkolu. Tady je ukázkový skript prostředí příkazového řádku, které udělá za vás.

```cmd
REM   If Task1_Success.txt exists, then Application 1 is already installed.
IF EXIST "%RoleRoot%\Task1_Success.txt" (
  ECHO Application 1 is already installed. Exiting. >> "%TEMP%\StartupLog.txt" 2>&1
  GOTO Finish
)

REM   Run your real exe task
ECHO Running XYZ >> "%TEMP%\StartupLog.txt" 2>&1
"%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (
  REM   The application installed without error. Create a file to indicate that the task
  REM   does not need to be run again.

  ECHO This line will create a file to indicate that Application 1 installed correctly. > "%RoleRoot%\Task1_Success.txt"

) ELSE (
  REM   An error occurred. Log the error and exit with the error code.

  DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
  TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
  ECHO  An error occurred running task 1. Errorlevel = %ERRORLEVEL%. >> "%TEMP%\StartupLog.txt" 2>&1

  EXIT %ERRORLEVEL%
)

:Finish

REM   Exit normally.
EXIT /B 0
```

## <a name="task-best-practices"></a>Osvědčené postupy úloh
Tady jsou některé osvědčené postupy, které byste měli postupovat při konfiguraci úlohy pro webové nebo pracovní role.

### <a name="always-log-startup-activities"></a>Vždy protokolů aktivit při spouštění
Visual Studio neposkytuje ladicí program pro jednotlivé kroky v dávkových souborech, takže je vhodné získat co nejvíce data na operace dávkové soubory nejvíce. Protokolování výstupu dávkové soubory i **stdout** a **stderr**, může vám poskytnou důležité informace při pokusu o ladění a opravy dávkové soubory. Do protokolu i **stdout** a **stderr** k StartupLog.txt souboru v adresáři odkazuje **% TEMP %** proměnné prostředí, přidejte text `>>  "%TEMP%\\StartupLog.txt" 2>&1` na konec konkrétní řádky, které chcete se přihlásit. Například spusťte setup.exe v **PathToApp1Install %** adresáře:

    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

Pro zjednodušení soubor xml, můžete vytvořit obálku *cmd* soubor, který volá všechny vaše spuštění úlohy spolu s protokolování a zajišťuje každá podřízená úloha sdílí stejné proměnné prostředí.

Může být pro vás vám budou používat `>> "%TEMP%\StartupLog.txt" 2>&1` na konci každé úloze po spuštění. Protokolování úloh můžete vynutit tím, že vytvoříte obálku, která zpracovává protokolování za vás. Tato Obálka volání skutečné dávkový soubor, který chcete spustit. Všechny výstupy z dávkového souboru target, budete přesměrováni na *Startuplog.txt* souboru.

Následující příklad ukazuje, jak přesměrovat veškerý výstup z dávkového souboru při spuštění. V tomto příkladu, vytvoří soubor ServerDefinition.csdef úlohy po spuštění, která volá *logwrap.cmd*. *logwrap.cmd* volání *Startup2.cmd*, přesměrování všechny výstup do **% TEMP %\\StartupLog.txt**.

ServiceDefinition.cmd:

```xml
<Startup>
    <Task commandLine="logwrap.cmd startup2.cmd" executionContext="limited" taskType="simple" />
</Startup>
```

**logwrap.cmd:**

```cmd
@ECHO OFF

REM   logwrap.cmd calls passed in batch file, redirecting all output to the StartupLog.txt log file.

ECHO [%date% %time%] == START logwrap.cmd ============================================== >> "%TEMP%\StartupLog.txt" 2>&1
ECHO [%date% %time%] Running %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Call the child command batch file, redirecting all output to the StartupLog.txt log file.
START /B /WAIT %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Log the completion of child command.
ECHO [%date% %time%] Done >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (

   REM   No errors occurred. Exit logwrap.cmd normally.
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B 0

) ELSE (

   REM   Log the error.
   ECHO [%date% %time%] An error occurred. The ERRORLEVEL = %ERRORLEVEL%.  >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B %ERRORLEVEL%

)
```

**Startup2.cmd:**

```cmd
@ECHO OFF

REM   This is the batch file where the startup steps should be performed. Because of the
REM   way Startup2.cmd was called, all commands and their outputs will be stored in the
REM   StartupLog.txt file in the directory pointed to by the TEMP environment variable.

REM   If an error occurs, the following command will pass the ERRORLEVEL back to the
REM   calling batch file.

ECHO [%date% %time%] Some log information about this task
ECHO [%date% %time%] Some more log information about this task

EXIT %ERRORLEVEL%
```

Ukázkový výstup v **StartupLog.txt** souboru:

```txt
[Mon 10/17/2016 20:24:46.75] == START logwrap.cmd ============================================== 
[Mon 10/17/2016 20:24:46.75] Running command1.cmd 
[Mon 10/17/2016 20:24:46.77] Some log information about this task
[Mon 10/17/2016 20:24:46.77] Some more log information about this task
[Mon 10/17/2016 20:24:46.77] Done 
[Mon 10/17/2016 20:24:46.77] == END logwrap.cmd ================================================ 
```

> [!TIP]
> **StartupLog.txt** soubor se nachází v *C:\Resources\temp\\{identifikátor role} \RoleTemp* složky.
> 
> 

### <a name="set-executioncontext-appropriately-for-startup-tasks"></a>Nastavte kontextu executionContext odpovídajícím způsobem pro úlohy po spuštění
Nastavte oprávnění pro úlohy po spuštění. Někdy úlohy po spuštění musí spustit se zvýšenými oprávněními, i v případě, že role běží s normálními oprávněními.

[Kontextu executionContext][úkol] atribut nastaví oprávnění na úrovni úlohy po spuštění. Pomocí `executionContext="limited"` znamená, že úloha po spuštění bude mít stejnou úroveň oprávnění roli. Pomocí `executionContext="elevated"` znamená, že úloha po spuštění má oprávnění správce, která umožní úloze po spuštění k provedení úlohy správce bez oprávnění správce pro vaši roli.

Úlohy po spuštění, který vyžaduje zvýšená oprávnění příklad je úloha po spuštění, který používá **AppCmd.exe** můžete nakonfigurovat službu IIS. **AppCmd.exe** vyžaduje `executionContext="elevated"`.

### <a name="use-the-appropriate-tasktype"></a>Použít příslušné taskType
[TaskType][úkol] atribut určuje způsob, jakým úloha po spuštění je proveden. Existují tři hodnoty: **jednoduché**, **pozadí**, a **popředí**. Úlohy na pozadí a popředí se spouští asynchronně, a potom synchronně spuštění jednoduché úlohy postupně po jednom.

S **jednoduché** úlohy po spuštění, můžete nastavit pořadí, ve kterém je spuštěný úkoly podle pořadí, ve kterém jsou uvedeny úkoly v souboru ServiceDefinition.csdef. Pokud **jednoduché** skončí úkol nenulový ukončovací kód a potom postup zastaví spuštění a role se nespustí.

Rozdíl mezi **pozadí** úlohy po spuštění a **popředí** úlohy po spuštění je, že **popředí** úlohy zachovat spuštěný až do role  **popředí** úloha skončí. Zároveň to znamená, že pokud **popředí** úkol zablokuje nebo selže, role nebudou recyklovat až do **popředí** úkolu je nucen uzavřen. Z tohoto důvodu **pozadí** úlohy doporučují pro asynchronní spuštění úlohy, pokud potřebujete tuto funkci **popředí** úloh.

### <a name="end-batch-files-with-exit-b-0"></a>End dávkové soubory s UKONČOVACÍM /B 0
Role se spustí, jenom když **errorlevel** ze všech jednoduché spuštění úkolu je nula. Nastavte všechny programy **errorlevel** (ukončovací kód) správně, takže dávkový soubor by měl končit `EXIT /B 0` Pokud vše, co byl spuštěn správně.

Chybějící `EXIT /B 0` na konci dávky spuštění souboru je běžnou příčinou role, které nebudou spuštěny.

> [!NOTE]
> Všimli této vnořené dávkové soubory někdy přestane reagovat při použití `/B` parametru. Můžete chtít Ujistěte se, že tento problém zablokování neodehrává volá-li jinou dávkový soubor aktuální dávkového souboru, například, pokud použijete [obálky protokolu](#always-log-startup-activities). Můžete vynechat `/B` parametrů v tomto případě.
> 
> 

### <a name="expect-startup-tasks-to-run-more-than-once"></a>Očekávané úlohy po spuštění pro spuštění více než jednou.
Ne všechny recyklace rolí zahrnovat restartování, ale všechny recyklace rolí systémem všechny úlohy po spuštění. To znamená, že úlohy po spuštění musí být schopen mezi jednotlivými restartováními bez problémů spustit více než jednou. Tento postup je popsán v [předchozí části](#detect-that-your-task-has-already-run).

### <a name="use-local-storage-to-store-files-that-must-be-accessed-in-the-role"></a>Použít místní úložiště pro ukládání souborů, které musí být přístupné v roli
Pokud chcete zkopírovat nebo během spuštění úlohy, které je pak dostupné pro vaši roli vytvoří soubor, tento soubor musí být umístěn v místním úložišti. Zobrazit [předchozí části](#create-files-in-local-storage-from-a-startup-task).

## <a name="next-steps"></a>Další postup
Zkontrolujte cloudu [služby balíček a model](cloud-services-model-and-package.md)

Další informace o tom [úlohy](cloud-services-startup-tasks.md) fungovat.

[Vytvoření a nasazení](cloud-services-how-to-create-deploy-portal.md) váš balíček cloudové služby.

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Úkol]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Prostředí]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Proměnná]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
[Koncové body]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Endpoints
[LocalStorage]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalStorage
[LocalResources]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalResources
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
