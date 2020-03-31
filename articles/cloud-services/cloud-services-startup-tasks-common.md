---
title: Běžné úlohy spuštění cloudových služeb | Dokumenty společnosti Microsoft
description: Obsahuje některé příklady běžných úloh při spuštění, které můžete chtít provést ve webové roli cloudových služeb nebo roli pracovního procesu.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: 4fe1ee3ccf2849943959889838ba0f22fb64bb9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273056"
---
# <a name="common-cloud-service-startup-tasks"></a>Běžné úlohy spuštění cloudové služby
Tento článek obsahuje některé příklady běžných úloh při spuštění, které můžete chtít provést v cloudové službě. Úlohy spuštění můžete použít k provádění operací před spuštěním role. Mezi operace, které můžete chtít provést, patří instalace součásti, registrace součástí modelu COM, nastavení klíčů registru nebo spuštění dlouho běžícího procesu. 

V [tomto článku](cloud-services-startup-tasks.md) zjistíte, jak úlohy při spuštění fungují, a konkrétně jak vytvořit položky, které definují úlohu při spuštění.

> [!NOTE]
> Úlohy při spuštění se nevztahují na virtuální počítače, ale pouze na webové role cloudových služeb a role pracovních dělníků.
> 

## <a name="define-environment-variables-before-a-role-starts"></a>Definovat proměnné prostředí před spuštěním role
Pokud potřebujete proměnné prostředí definované pro konkrétní úkol, použijte prvek [Prostředí] uvnitř [task] elementu.

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

Proměnné můžete také použít [platnou hodnotu Azure XPath](cloud-services-role-config-xpath.md) odkazovat na něco o nasazení. Namísto použití `value` atributu definujte podřízený prvek [RoleInstanceValue.]

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## <a name="configure-iis-startup-with-appcmdexe"></a>Konfigurace spuštění služby IIS pomocí programu AppCmd.exe
Nástroj příkazového řádku [AppCmd.exe](https://technet.microsoft.com/library/jj635852.aspx) lze použít ke správě nastavení služby IIS při spuštění v Azure. *AppCmd.exe* poskytuje pohodlný přístup k nastavení konfigurace příkazového řádku pro použití při spouštění úloh v Azure. Pomocí *programu AppCmd.exe*lze nastavení webu přidat, upravit nebo odebrat pro aplikace a weby.

Existuje však několik věcí, na které je třeba si dávat pozor při používání *programu AppCmd.exe* jako úlohy při spuštění:

* Úlohy při spuštění lze spustit více než jednou mezi restartováním počítače. Například při recyklaci role.
* Pokud *akce AppCmd.exe* se provádí více než jednou, může generovat chybu. Například pokus o přidání oddílu do *souboru Web.config* dvakrát může generovat chybu.
* Úlohy při spuštění se nezdaří, pokud vrátí nenulový ukončovací kód nebo **úroveň chyby**. Například když *AppCmd.exe* generuje chybu.

Je vhodné zkontrolovat **errorlevel** po volání *AppCmd.exe*, což je snadné provést, pokud zabalíte volání *AppCmd.exe* se souborem *CMD.* Pokud zjistíte známou odpověď **na úrovni chyby,** můžete ji ignorovat nebo ji předat zpět.

Errorlevel vrácené *AppCmd.exe* jsou uvedeny v souboru winerror.h a lze také vidět na [MSDN](/windows/desktop/Debug/system-error-codes--0-499-).

### <a name="example-of-managing-the-error-level"></a>Příklad správy úrovně chyby
Tento příklad přidá do souboru *Web.config* oddíl komprese a položku komprese pro json se zpracováním chyb a protokolováním.

Zde jsou zobrazeny příslušné části souboru [ServiceDefinition.csdef,] které `elevated` zahrnují nastavení atributu [executionContext](/previous-versions/azure/reference/gg557552(v=azure.100)#task) tak, aby soubor *AppCmd.exe* měl dostatečná oprávnění ke změně nastavení v souboru *Web.config:*

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

Dávkový soubor *Startup.cmd* používá *soubor AppCmd.exe* k přidání oddílu komprese a položky komprese pro JSON do souboru *Web.config.* Očekávaná **úroveň chyb** 183 je nastavena na nulu pomocí VERIFY. EXE program příkazového řádku. Na soubor StartupErrorLog.txt jsou zaznamenány neočekávané úrovně chyb.

```cmd
REM   *** Add a compression section to the Web.config file. ***
%windir%\system32\inetsrv\appcmd set config /section:urlCompression /doDynamicCompression:True /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1

REM   ERRORLEVEL 183 occurs when trying to add a section that already exists. This error is expected if this
REM   batch file were executed twice. This can occur and must be accounted for in an Azure startup
REM   task. To handle this situation, set the ERRORLEVEL to zero by using the Verify command. The Verify
REM   command will safely set the ERRORLEVEL to zero.
IF %ERRORLEVEL% EQU 183 VERIFY > NUL

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

## <a name="add-firewall-rules"></a>Přidání pravidel brány firewall
V Azure jsou efektivně dvě brány firewall. První brána firewall řídí připojení mezi virtuálním počítačem a vnějším světem. Tato brána firewall je řízena elementem [EndPoints] v souboru [ServiceDefinition.csdef.]

Druhá brána firewall řídí připojení mezi virtuálním počítačem a procesy v rámci tohoto virtuálního počítače. Tuto bránu `netsh advfirewall firewall` firewall lze ovládat pomocí nástroje příkazového řádku.

Azure vytvoří pravidla brány firewall pro procesy spuštěné ve vašich rolích. Například při spuštění služby nebo programu Azure automaticky vytvoří potřebná pravidla brány firewall, aby tato služba mohla komunikovat s Internetem. Pokud však vytvoříte službu, která je spuštěna procesem mimo vaši roli (například službu COM+ nebo naplánovanou úlohu systému Windows), je třeba ručně vytvořit pravidlo brány firewall, které umožní přístup k této službě. Tato pravidla brány firewall lze vytvořit pomocí úlohy po spuštění.

Úloha při spuštění, která vytvoří pravidlo brány firewall, musí mít [executionContext][Task] **elevated**. Přidejte následující úlohu spuštění do souboru [ServiceDefinition.csdef.]

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

Chcete-li přidat pravidlo brány firewall, musíte použít příslušné `netsh advfirewall firewall` příkazy v spouštěcím dávkovém souboru. V tomto příkladu vyžaduje úloha spuštění zabezpečení a šifrování pro port TCP 80.

```cmd
REM   Add a firewall rule in a startup task.

REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="block-a-specific-ip-address"></a>Blokování určité IP adresy
Přístup k webové roli Azure můžete omezit na sadu zadaných IP adres úpravou **souboru web.config** služby IIS. Musíte také použít příkazový soubor, který odemkne část **ipSecurity** souboru **ApplicationHost.config.**

Chcete-li odemknout část **ipSecurity** souboru **ApplicationHost.config,** vytvořte soubor příkazů, který se spustí při spuštění role. Vytvořte složku na kořenové úrovni webové role s názvem **spuštění** a v rámci této složky vytvořte dávkový soubor nazvaný **startup.cmd**. Přidejte tento soubor do projektu sady Visual Studio a nastavte vlastnosti na **Kopírovat vždy,** abyste zajistili, že je součástí balíčku.

Přidejte následující úlohu spuštění do souboru [ServiceDefinition.csdef.]

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

Přidejte tento příkaz do souboru **startup.cmd:**

```cmd
@echo off
@echo Installing "IPv4 Address and Domain Restrictions" feature 
powershell -ExecutionPolicy Unrestricted -command "Install-WindowsFeature Web-IP-Security"
@echo Unlocking configuration for "IPv4 Address and Domain Restrictions" feature 
%windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity
```

Tato úloha způsobí spuštění dávkového souboru **startup.cmd** při každém inicializování webové role a zajistí, že požadovaná část **ipSecurity** bude odemčena.

Nakonec upravte [oddíl system.webServer](https://www.iis.net/configreference/system.webserver/security/ipsecurity#005) soubor **webová** role webová role webová konfigurace a přidejte seznam adres IP, kterým je udělen přístup, jak je znázorněno v následujícím příkladu:

Tato ukázková konfigurace **umožňuje** všem IP adresy přístup k serveru s výjimkou dvou definovaných

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

Tato ukázková konfigurace **odepře** přístup všech IP serverů k serveru s výjimkou dvou definovaných.

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

## <a name="create-a-powershell-startup-task"></a>Vytvoření úlohy spuštění Prostředí PowerShell
Skripty prostředí Windows PowerShell nelze volat přímo ze souboru [ServiceDefinition.csdef,] ale lze je vyvolat ze spouštěcího dávkového souboru.

Prostředí PowerShell (ve výchozím nastavení) nespouštějí nepodepsané skripty. Pokud skript nepodepíšete, je třeba nakonfigurovat prostředí PowerShell tak, aby spouštěl nepodepsané skripty. Chcete-li spustit nepodepsané skripty, musí být **zásady spuštění** **nastaveny**na možnost Bez omezení . Nastavení **ExecutionPolicy,** které používáte, je založeno na verzi prostředí Windows PowerShell.

```cmd
REM   Run an unsigned PowerShell script and log the output
PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

Pokud používáte hostovaný operační systém, který používá PowerShell 2.0 nebo 1.0, můžete vynutit spuštění verze 2 a pokud není k dispozici, použijte verzi 1.

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

## <a name="create-files-in-local-storage-from-a-startup-task"></a>Vytvoření souborů v místním úložišti z úlohy při spuštění
Prostředek místního úložiště můžete použít k ukládání souborů vytvořených úlohou při spuštění, ke kterému bude aplikace později přistupovat.

Chcete-li vytvořit prostředek místního úložiště, přidejte oddíl [LocalResources] do souboru [ServiceDefinition.csdef] a přidejte podřízený prvek [LocalStorage.] Pojmenujte prostředek místního úložiště jedinečný název a odpovídající velikost pro úlohu při spuštění.

Chcete-li použít prostředek místního úložiště v spouštěcí úloze, musíte vytvořit proměnnou prostředí, která bude odkazovat na umístění prostředků místního úložiště. Potom úloha při spuštění a aplikace jsou schopny číst a zapisovat soubory do místního prostředku úložiště.

Příslušné části souboru **ServiceDefinition.csdef** jsou uvedeny zde:

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

Jako příklad tento dávkový soubor **Startup.cmd** používá proměnnou prostředí **PathToStartupStorage** k vytvoření souboru **MyTest.txt** v místním úložišti.

```cmd
REM   Create a simple text file.

ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

REM   Exit the batch file with ERRORLEVEL 0.

EXIT /b 0
```

Ke složce místního úložiště můžete přistupovat ze sady Azure SDK pomocí metody [GetLocalResource.](/previous-versions/azure/reference/ee772845(v=azure.100))

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTestFile.txt"));
```

## <a name="run-in-the-emulator-or-cloud"></a>Spustit v emulátoru nebo cloudu
Můžete mít úlohu spuštění provádět různé kroky, když pracuje v cloudu ve srovnání s když je v emulátoru výpočetního prostředí. Například můžete chtít použít novou kopii dat SQL pouze při spuštění v emulátoru. Nebo můžete chtít provést některé optimalizace výkonu pro cloud, který nemusíte dělat při spuštění v emulátoru.

Tuto schopnost provádět různé akce na výpočetní emulátor a cloud lze provést vytvořením proměnné prostředí v souboru [ServiceDefinition.csdef.] Potom otestujte tuto proměnnou prostředí pro hodnotu v spouštěcí úloze.

Chcete-li vytvořit proměnnou prostředí, přidejte element [Variable]/[RoleInstanceValue] a vytvořte hodnotu XPath aplikace `/RoleEnvironment/Deployment/@emulated`. Hodnota proměnné prostředí **%ComputeEmulatorRunning%** `true` je při spuštění v emulátoru výpočetního prostředí a `false` při spuštění v cloudu.

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

Úloha nyní můžete zkontrolovat **%ComputeEmulatorRunning%** proměnné prostředí provádět různé akce na základě toho, zda je role spuštěna v cloudu nebo emulátoru. Zde je skript prostředí CMD, který kontroluje tuto proměnnou prostředí.

```cmd
REM   Check if this task is running on the compute emulator.

IF "%ComputeEmulatorRunning%" == "true" (
    REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
) ELSE (
    REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
)
```


## <a name="detect-that-your-task-has-already-run"></a>Zjištění, že úloha již byla spuštěna
Role může recyklovat bez restartování způsobuje spuštění úlohy znovu spustit. Neexistuje žádný příznak označující, že úloha již byla spuštěna na hostitelském virtuálním počítači. Můžete mít některé úkoly, kde nezáleží na tom, že běží vícekrát. Můžete však narazit na situaci, kdy je třeba zabránit spuštění úlohy více než jednou.

Nejjednodušší způsob, jak zjistit, že úloha již byla spuštěna, je vytvořit soubor ve složce **%TEMP%** v případě, že je úloha úspěšná, a vyhledejte jej na začátku úkolu. Zde je ukázka cmd shell skript, který dělá, že pro vás.

```cmd
REM   If Task1_Success.txt exists, then Application 1 is already installed.
IF EXIST "%PathToApp1Install%\Task1_Success.txt" (
  ECHO Application 1 is already installed. Exiting. >> "%TEMP%\StartupLog.txt" 2>&1
  GOTO Finish
)

REM   Run your real exe task
ECHO Running XYZ >> "%TEMP%\StartupLog.txt" 2>&1
"%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (
  REM   The application installed without error. Create a file to indicate that the task
  REM   does not need to be run again.

  ECHO This line will create a file to indicate that Application 1 installed correctly. > "%PathToApp1Install%\Task1_Success.txt"

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
Zde jsou některé doporučené postupy, které byste měli dodržovat při konfiguraci úlohy pro roli webu nebo pracovního procesu.

### <a name="always-log-startup-activities"></a>Vždy zaznamenávat aktivity při spuštění
Visual Studio neposkytuje ladicí program krokovat dávkové soubory, takže je vhodné získat co nejvíce dat o provozu dávkových souborů, jak je to možné. Protokolování výstupu dávkových souborů, **stdout** i **stderr**, vám může poskytnout důležité informace při pokusu o ladění a opravu dávkových souborů. Chcete-li přihlásit **stdout** a **stderr** do souboru StartupLog.txt v adresáři, `>>  "%TEMP%\\StartupLog.txt" 2>&1` na který je odkazováno proměnnou prostředí **%TEMP%,** přidejte text na konec určitých řádků, které chcete protokolovat. Chcete-li například spustit program setup.exe v adresáři **%PathToApp1Install%:**

    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

Chcete-li zjednodušit xml, můžete vytvořit soubor *cmd* obálky, který volá všechny úlohy při spuštění spolu s protokolováním a zajišťuje, že každá podřízená úloha sdílí stejné proměnné prostředí.

Možná zjistíte, že `>> "%TEMP%\StartupLog.txt" 2>&1` nepříjemné i když použít na konci každého spuštění úkolu. Protokolování úkolů můžete vynutit vytvořením obálky, která zpracovává protokolování za vás. Tento obálka volá skutečný dávkový soubor, který chcete spustit. Veškerý výstup z cílového dávkového souboru bude přesměrován do souboru *Startuplog.txt.*

Následující příklad ukazuje, jak přesměrovat veškerý výstup ze spouštěcího dávkového souboru. V tomto příkladu soubor ServerDefinition.csdef vytvoří úlohu po spuštění, která volá *logwrap.cmd*. *logwrap.cmd* volá *Startup2.cmd*a přesměrovává veškerý výstup na **%TEMP%\\StartupLog.txt**.

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

Ukázkový výstup v souboru **StartupLog.txt:**

```txt
[Mon 10/17/2016 20:24:46.75] == START logwrap.cmd ============================================== 
[Mon 10/17/2016 20:24:46.75] Running command1.cmd 
[Mon 10/17/2016 20:24:46.77] Some log information about this task
[Mon 10/17/2016 20:24:46.77] Some more log information about this task
[Mon 10/17/2016 20:24:46.77] Done 
[Mon 10/17/2016 20:24:46.77] == END logwrap.cmd ================================================ 
```

> [!TIP]
> Soubor **StartupLog.txt** je umístěn ve složce *C:\Resources\temp\\{role identifier}\RoleTemp.*
> 
> 

### <a name="set-executioncontext-appropriately-for-startup-tasks"></a>Nastavit executionContext vhodně pro úlohy při spuštění
Nastavte oprávnění odpovídajícím způsobem pro úlohu při spuštění. Někdy spouštěcí úlohy musí být spuštěny se zvýšenými oprávněními, i když role běží s normálními oprávněními.

Atribut [executionContext][Task] nastavuje úroveň oprávnění úlohy při spuštění. Použití `executionContext="limited"` znamená, že úloha při spuštění má stejnou úroveň oprávnění jako role. Použití `executionContext="elevated"` znamená, že úloha při spuštění má oprávnění správce, což umožňuje úlohám při spuštění provádět úlohy správce, aniž by oprávnění správce vaší roli.

Příkladem úlohy při spuštění, která vyžaduje zvýšená oprávnění, je úloha při spuštění, která ke konfiguraci služby IIS používá **nástroj AppCmd.exe.** **AppCmd.exe** `executionContext="elevated"`vyžaduje .

### <a name="use-the-appropriate-tasktype"></a>Použít příslušný taskType
Atribut [taskType][Task] určuje způsob spuštění úlohy. Existují tři hodnoty: **jednoduché**, **pozadí**a **popředí**. Úlohy na pozadí a popředí jsou spouštěny asynchronně a pak jednoduché úlohy jsou prováděny synchronně jeden po druhém.

Pomocí **jednoduchých** úloh při spuštění můžete nastavit pořadí, ve kterém jsou úlohy spuštěny podle pořadí, ve kterém jsou úkoly uvedeny v souboru ServiceDefinition.csdef. Pokud **jednoduchý** úkol končí nenulovým ukončovacím kódem, postup spuštění se zastaví a role se nespustí.

Rozdíl mezi úlohami při spuštění **na pozadí** a **úlohami** při spuštění v popředí spoje v tom, že úlohy **v popředí** udržují roli spuštěnou, dokud úloha v **popředí** neskončí. To také znamená, že pokud úloha **v popředí** přestane reagovat nebo dojde k chybě, role nebude recyklovat, dokud úloha **v popředí** je vynuceno uzavřena. Z tohoto důvodu jsou úlohy **na pozadí** doporučeny pro úlohy asynchronního spuštění, pokud nepotřebujete tuto funkci úlohy **v popředí.**

### <a name="end-batch-files-with-exit-b-0"></a>Ukončit dávkové soubory s exit /B 0
Role se spustí pouze v případě, že **úroveň chyby** z každé z vašich jednoduchých úloh při spuštění je nula. Ne všechny programy nastavit **errorlevel** (ukončovací kód) správně, takže dávkový soubor by měl skončit `EXIT /B 0` s if vše běželo správně.

Chybějící `EXIT /B 0` na konci spouštěcího dávkového souboru je běžnou příčinou rolí, které se nespustí.

> [!NOTE]
> Všiml jsem si, že vnořené `/B` dávkové soubory někdy visí při použití parametru. Můžete se ujistit, že k tomuto problému zablokování nedojde, pokud jiný dávkový soubor volá aktuální dávkový soubor, například pokud použijete [obálku protokolu](#always-log-startup-activities). `/B` V tomto případě můžete parametr vynechat.
> 
> 

### <a name="expect-startup-tasks-to-run-more-than-once"></a>Očekávejte spuštění úloh při spuštění více než jednou
Ne všechny recykluje role zahrnují restartování, ale všechny recykluje role zahrnují spuštění všech úloh při spuštění. To znamená, že úlohy při spuštění musí být možné spustit vícekrát mezi restartováním bez problémů. To je popsáno v [předchozí části](#detect-that-your-task-has-already-run).

### <a name="use-local-storage-to-store-files-that-must-be-accessed-in-the-role"></a>Použití místního úložiště k ukládání souborů, ke kterým je třeba přistupovat v roli
Pokud chcete zkopírovat nebo vytvořit soubor během úlohy při spuštění, který je pak přístupný pro vaši roli, musí být tento soubor umístěn do místního úložiště. Viz [předchozí část](#create-files-in-local-storage-from-a-startup-task).

## <a name="next-steps"></a>Další kroky
Kontrola modelu cloudové [služby a balíčku](cloud-services-model-and-package.md)

Přečtěte si další informace o tom, jak [úkoly](cloud-services-startup-tasks.md) fungují.

[Vytvořte a nasaďte](cloud-services-how-to-create-deploy-portal.md) balíček cloudových služeb.

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Úkol]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Prostředí]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Proměnná]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[Hodnota instance role]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
[Koncové body]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Endpoints
[Localstorage]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalStorage
[Místní zdroje]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalResources
[Hodnota instance role]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue



