---
title: Běžné úlohy spuštění pro cloudové služby | Microsoft Docs
description: Obsahuje některé příklady běžných úloh spuštění, které můžete chtít provést ve vaší cloudové služby webovou roli nebo role pracovního procesu.
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
ms.assetid: a7095dad-1ee7-4141-bc6a-ef19a6e570f1
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: cee23da5b089b02bfc0ef10afd60f0f2272585b1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23843697"
---
# <a name="common-cloud-service-startup-tasks"></a>Běžné úlohy spuštění cloudové služby
Tento článek obsahuje některé příklady běžných úloh spuštění, který chcete provést v rámci cloudové služby. Spuštění úlohy můžete použít k provádění operací před zahájením roli. Operace, které můžete chtít provést zahrnovat instalaci komponenty, registraci komponenty modelu COM, nastavení klíče registru nebo dlouhotrvající proces. 

V tématu [v tomto článku](cloud-services-startup-tasks.md) pochopit, jak fungují spuštění úlohy a konkrétně postup vytvoření položky, které definují úloha spuštění.

> [!NOTE]
> Spuštění úlohy se nedají použít k virtuálním počítačům, pouze pro webové služby Cloud a rolí pracovního procesu.
> 

## <a name="define-environment-variables-before-a-role-starts"></a>Definování proměnné prostředí před spuštěním roli
Pokud potřebujete proměnné definované pro konkrétní úkol, použijte [prostředí] element uvnitř [úloh] element.

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

Můžete také použít proměnné [platnou hodnotu Azure XPath](cloud-services-role-config-xpath.md) tak, aby odkazovaly něco o nasazení. Místo použití `value` atribut, definování [RoleInstanceValue] podřízený element.

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## <a name="configure-iis-startup-with-appcmdexe"></a>Konfigurace spuštění služby IIS s AppCmd.exe
[AppCmd.exe](https://technet.microsoft.com/library/jj635852.aspx) nástroj příkazového řádku lze použít ke správě nastavení služby IIS na spuštění v Azure. *AppCmd.exe* poskytuje praktické, příkazového řádku přístup k nastavení konfigurace pro použití při spuštění úlohy v Azure. Pomocí *AppCmd.exe*, nastavení webu můžete přidat, změnit ani odebrat, pro aplikace a weby.

Existuje však několik postupů k dávejte pozor na použití *AppCmd.exe* jako úloha spuštění:

* Spuštění úlohy mohou být spouštěny více než jednou mezi jednotlivými restartováními. Například když roli recyklování.
* Pokud *AppCmd.exe* akce se provádí více než jednou, to může generovat chybu. Například se pokouší přidat oddíl na *Web.config* dvakrát může způsobit chybu.
* Spuštění úlohy nezdaří, pokud se vrátí nenulový ukončovací kód nebo **errorlevel**. Například když *AppCmd.exe* , vygeneruje se chyba.

Je dobrým zvykem zkontrolovat **errorlevel** po volání *AppCmd.exe*, které lze snadno dělat, když zabalení volání *AppCmd.exe* s *.cmd* souboru. Pokud zjistíte známá **errorlevel** odpovědi, můžete ho ignorovat nebo předejte ji zpět.

Errorlevel vrácený *AppCmd.exe* jsou uvedeny v souboru winerror.h a můžete také zobrazit na [MSDN](https://msdn.microsoft.com/library/windows/desktop/ms681382.aspx).

### <a name="example-of-managing-the-error-level"></a>Příklad správy úroveň chyb
Tento příklad přidá části komprese a položku komprese pro formát JSON na *Web.config* soubor s chybou zpracování a protokolování.

V příslušných částech [ServiceDefinition.csdef] souboru zde se zobrazují, mezi které patří nastavení [executionContext](https://msdn.microsoft.com/library/azure/gg557552.aspx#Task) atribut `elevated` umožnit *AppCmd.exe* dostatečná oprávnění pro změnu nastavení v *Web.config* souboru:

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

*Startup.cmd* batch používá soubor *AppCmd.exe* přidat oddíl komprese a komprese záznam pro formát JSON na *Web.config* souboru. Očekávané **errorlevel** z 183 nastavena na nulové pomocí ověřit. EXE příkazového řádku programu. Neočekávané hodnoty parametru ERRORLEVEL při jsou protokolovány StartupErrorLog.txt.

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

## <a name="add-firewall-rules"></a>Přidat pravidla brány firewall
V Azure jsou efektivně dvě brány firewall. První brána firewall řídí připojení mezi virtuálním počítačem a vnějším světem. Tato brána firewall řídí [koncové body] element v [ServiceDefinition.csdef] souboru.

Druhá brána firewall řídí připojení mezi virtuálním počítačem a procesy v tomto virtuálním počítači. Tato brána firewall je řízena `netsh advfirewall firewall` nástroj příkazového řádku.

Azure vytvoří pravidla brány firewall pro procesy spuštěné v rámci role. Například při spuštění služby nebo aplikace, Azure automaticky vytvoří pravidla brány firewall nutné povolit tuto službu ke komunikaci s Internetem. Ale pokud vytvoříte službu, která se spustí proces mimo vaši roli (např. Windows naplánované úlohy nebo služby COM +), budete muset ručně vytvořit pravidlo brány firewall pro povolení přístupu k této službě. Tato pravidla brány firewall můžete vytvořit pomocí úloha spuštění.

Úloha spuštění, který vytvoří pravidlo brány firewall musí mít [executionContext][úloh] z **zvýšenými**. Přidejte následující úloha spuštění na [ServiceDefinition.csdef] souboru.

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

Chcete-li přidat pravidlo brány firewall, je nutné použít na příslušné `netsh advfirewall firewall` příkazy do spuštění dávkového souboru. V tomto příkladu úloha spuštění vyžaduje zabezpečení a šifrování pro TCP port 80.

```cmd
REM   Add a firewall rule in a startup task.

REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="block-a-specific-ip-address"></a>Blokovat konkrétní IP adresu
Můžete omezit přístup službě Azure web role na sadu ze zadaných IP adres změnou vaší služby IIS **web.config** souboru. Budete také muset použít soubor příkazů, které odemyká **ipSecurity** části **ApplicationHost.config** souboru.

K odemknutí **ipSecurity** části **ApplicationHost.config** souboru, vytvořte soubor příkaz, který se spustí na spuštění role. Vytvořte složku na kořenové úrovni webové role názvem **spuštění** a v této složce vytvořte dávkový soubor s názvem **startup.cmd**. Tento soubor přidat do projektu sady Visual Studio a nastavte vlastnosti na **kopie vždy** zajistit, že je součástí vašeho balíčku.

Přidejte následující úloha spuštění na [ServiceDefinition.csdef] souboru.

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

Způsobí, že tato úloha **startup.cmd** dávkového souboru ke spuštění pokaždé, když je inicializovat webovou roli, zajištění, že požadovaná **ipSecurity** pořizování.

Nakonec upravte [oddíl system.webServer](http://www.iis.net/configreference/system.webserver/security/ipsecurity#005) webovou roli **web.config** přidejte seznam IP adres, které mají udělen přístup, jak je znázorněno v následujícím příkladu:

Tato ukázka konfigurace **umožňuje** všechny IP adresy pro přístup k serveru s výjimkou dva definované

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

Tato ukázka konfigurace **odmítne** všechny IP adresy v přístupu k serveru s výjimkou dva definované.

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

## <a name="create-a-powershell-startup-task"></a>Vytvoření úlohy spuštění prostředí PowerShell
Skripty prostředí Windows PowerShell nelze volat přímo z [ServiceDefinition.csdef] souboru, ale můžete vyvolat z v dávkovém souboru spuštění.

Prostředí PowerShell (ve výchozím nastavení) nelze spustit nepodepsané skripty. Pokud váš skript, musíte nakonfigurovat prostředí PowerShell ke spouštění nepodepsaných skriptů. Ke spouštění nepodepsaných skriptů **ExecutionPolicy** musí být nastavena na **bez omezení**. **ExecutionPolicy** nastavení použití je založená na verzi prostředí Windows PowerShell.

```cmd
REM   Run an unsigned PowerShell script and log the output
PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

Pokud používáte hostovaného operačního systému, který je běží PowerShell 2.0 nebo 1.0 můžete vynutit verze 2, pokud chcete spustit a pokud není k dispozici, použijte verze 1.

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

## <a name="create-files-in-local-storage-from-a-startup-task"></a>Vytvoření souborů v místním úložišti z spuštění úlohy
Prostředek Místní úložiště můžete ukládat soubory vytvořené serverem vaše úloha spuštění, které je přístupné později vaše aplikace.

Chcete-li vytvořit prostředek Místní úložiště, přidejte [LocalResources] části k [ServiceDefinition.csdef] souboru a poté přidejte [LocalStorage] podřízený element. Poskytují prostředků místní úložiště pro vaše úloha spuštění jedinečný název a správnou velikost.

V úkolu spuštění použít místní úložiště prostředků, musíte vytvořit proměnnou prostředí tak, aby odkazovaly umístění prostředků místní úložiště. Spuštění úlohy a aplikace budou moci číst a zapisovat soubory do místního úložiště prostředků.

V příslušných částech **ServiceDefinition.csdef** zde se zobrazují souboru:

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

Jako příklad to **Startup.cmd** batch používá soubor **PathToStartupStorage** proměnnou prostředí k vytvoření souboru **MyTest.txt** v umístění místní úložiště.

```cmd
REM   Create a simple text file.

ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

REM   Exit the batch file with ERRORLEVEL 0.

EXIT /b 0
```

Místní úložiště složky můžete přístup ze sady SDK Azure pomocí [GetLocalResource](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) metoda.

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTestFile.txt"));
```

## <a name="run-in-the-emulator-or-cloud"></a>Spustit v emulátoru nebo v cloudu
Může mít vaše úloha spuštění provést různé kroky v případě, že ho pracuje v porovnání s, pokud je v emulátoru služby výpočty v cloudu. Můžete například použít novou kopii dat SQL jenom v případě, že je spuštěna v emulátoru. Nebo můžete chtít provést některých optimalizací výkonu pro cloud, který je nemusíte dělat při spuštění v emulátoru.

Tato schopnost provádět různé akce v emulátoru služby výpočty v a cloudu, můžete provést vytvoření proměnné prostředí v [ServiceDefinition.csdef] souboru. Potom otestovat tuto proměnnou prostředí pro hodnotu ve spuštění úkolu.

Chcete-li vytvořit proměnnou prostředí, přidejte [proměnné]/[RoleInstanceValue] element a vytvořit má hodnotu XPath `/RoleEnvironment/Deployment/@emulated`. Hodnota **ComputeEmulatorRunning %** proměnná prostředí je `true` při spuštění v emulátoru služby výpočty v, a `false` při spuštění v cloudu.

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

Nyní můžete zkontrolovat úlohy **ComputeEmulatorRunning %** proměnnou prostředí k provádění různých akcí založené na roli, zda je spuštěný v cloudu, nebo emulátor. Zde je skript prostředí .cmd, který kontroluje pro tuto proměnnou prostředí.

```cmd
REM   Check if this task is running on the compute emulator.

IF "%ComputeEmulatorRunning%" == "true" (
    REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
) ELSE (
    REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
)
```


## <a name="detect-that-your-task-has-already-run"></a>Zjistit, že úlohu již spuštěn
Role může recyklujte bez restartování způsobí spuštění úkoly spustit znovu. Neexistuje žádné příznak indikující, že úloha již spuštěn na hostování virtuálních počítačů. Může mít některé úlohy, kde nezáleží na spuštění více než jednou. Je však může spustit do situace, kdy potřebujete zabránit úlohu ve spuštění více než jednou.

Nejjednodušší způsob, jak zjistit, jestli se úloha spustila již je k vytvoření souboru v **% TEMP %** složku, když je úloha úspěšná a podívejte se na začátku úlohy. Tady je ukázkový skript prostředí cmd, který pro sebe.

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
Tady jsou některé z osvědčených postupů, které byste měli postupovat při konfiguraci úloh pro váš web nebo worker roli.

### <a name="always-log-startup-activities"></a>Vždy protokolu spuštění aktivit
Visual Studio neposkytuje ladicí program ke krokování dávkové soubory, proto je vhodné k získání tolik dat na operace dávkové soubory nejdříve. Protokolování výstupu dávkové soubory i **stdout** a **stderr**, vám může důležité informace při pokusu o ladění a opravte dávkové soubory. Do protokolu obě **stdout** a **stderr** k StartupLog.txt soubor v adresáři na kterou odkazuje **% TEMP %** proměnné prostředí, přidejte text `>>  "%TEMP%\\StartupLog.txt" 2>&1` na konec konkrétní řádky, které chcete protokolovat. Například pro spuštění setup.exe v **PathToApp1Install %** directory:

    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

Pro zjednodušení soubor xml, můžete vytvořit obálku *cmd* soubor, který volá všechny vaše spuštění úloh společně s protokolování a zajistí každé podřízené úlohy sdílí stejné proměnné prostředí.

Možná bude nepříjemných když chcete použít `>> "%TEMP%\StartupLog.txt" 2>&1` na konci každé spuštění úlohy. Protokolování úloh můžete vynutit tím, že vytvoříte obálku, která zpracovává protokolování pro vás. Tuto obálku volá skutečné dávkový soubor, který chcete spustit. Všechny výstupy z dávkového souboru cíl bude přesměrován na *Startuplog.txt* souboru.

Následující příklad ukazuje, jak přesměrovat veškerý výstup z dávkového souboru spuštění. V tomto příkladu se vytvoří soubor ServerDefinition.csdef spuštění úlohu, která volá *logwrap.cmd*. *logwrap.cmd* volání *Startup2.cmd*, přesměrování veškerý výstup do **% TEMP %\\StartupLog.txt**.

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

Ukázka výstupu v **StartupLog.txt** souboru:

```txt
[Mon 10/17/2016 20:24:46.75] == START logwrap.cmd ============================================== 
[Mon 10/17/2016 20:24:46.75] Running command1.cmd 
[Mon 10/17/2016 20:24:46.77] Some log information about this task
[Mon 10/17/2016 20:24:46.77] Some more log information about this task
[Mon 10/17/2016 20:24:46.77] Done 
[Mon 10/17/2016 20:24:46.77] == END logwrap.cmd ================================================ 
```

> [!TIP]
> **StartupLog.txt** soubor je umístěný ve *C:\Resources\temp\\{identifikátor role} \RoleTemp* složky.
> 
> 

### <a name="set-executioncontext-appropriately-for-startup-tasks"></a>Nastavit executionContext pro spuštění úlohy
Nastavte oprávnění pro spuštění úlohy. Spuštění úlohy někdy musí spusťte s vyššími oprávněními, i když role běží s normálními oprávněními.

[ExecutionContext][úloh] atribut nastaví úroveň oprávnění při spuštění úlohy. Pomocí `executionContext="limited"` znamená úloha spuštění má stejnou úroveň oprávnění jako roli. Pomocí `executionContext="elevated"` znamená úloha spuštění má oprávnění správce, který umožňuje spuštění úloh k provádění Správce úloh bez nutnosti poskytnutí oprávnění správce pro vaši roli.

Úloha spuštění, který používá je například spuštění úlohu, která vyžaduje zvýšená oprávnění **AppCmd.exe** konfiguraci služby IIS. **AppCmd.exe** vyžaduje `executionContext="elevated"`.

### <a name="use-the-appropriate-tasktype"></a>Použít příslušné taskType
[TaskType][úloh] atribut určuje způsob spuštění úlohy se spustí. Existují tři hodnoty: **jednoduché**, **pozadí**, a **popředí**. Pozadí a popředí úlohy se spouštějí asynchronně a potom synchronně spuštěn jednoduché úlohy po jednom.

S **jednoduché** spuštění úlohy, můžete nastavit pořadí, ve kterém úlohy spouštět podle pořadí, ve kterém jsou uvedeny úlohy v souboru ServiceDefinition.csdef. Pokud **jednoduché** úloh končí nenulový ukončovací kód a potom se nespustí zastaví postup spuštění a roli.

Rozdíl mezi **pozadí** spuštění úlohy a **popředí** spuštění úlohy je, že **popředí** úlohy zachovat roli systémem až **popředí** úkolů elementy end. To také znamená, že pokud **popředí** úloh přestane reagovat nebo dojde k chybě, dokud nebude recyklovat roli **popředí** úloh je nucen uzavřen. Z tohoto důvodu **pozadí** úlohy se doporučují pro asynchronní spuštění úloh, pokud je třeba tuto součást **popředí** úloh.

### <a name="end-batch-files-with-exit-b-0"></a>End dávkové soubory s UKONČOVACÍM /B 0
Role se spustí, pouze pokud **errorlevel** z každé vaší jednoduché spuštění úloh je nulová. Ne všechny programy nastavit **errorlevel** (ukončovací kód) správně, takže dávkový soubor musí končit `EXIT /B 0` Pokud se vše správně spustil.

Chybějící `EXIT /B 0` na konci dávky spuštění souboru je obvyklou příčinou rolí, které nebudou spuštěny.

> [!NOTE]
> I jste si všimli této vnořené dávkové soubory někdy zablokování, když pomocí `/B` parametr. Můžete chtít zajistěte, aby tento problém zablokování neodehrává volá-li jiný dávkový soubor váš aktuální dávkový soubor, třeba Pokud použijete [obálku protokolu](#always-log-startup-activities). Můžete vynechat `/B` parametr v tomto případě.
> 
> 

### <a name="expect-startup-tasks-to-run-more-than-once"></a>Očekávat spuštění úlohy ke spuštění více než jednou.
Ne všechny role recykluje zahrnovat restart, ale všechny role recykluje zahrnují spuštění všech spuštění úloh. To znamená, že spuštění úlohy musí být možné spustit několikrát mezi jednotlivými restartováními bez problémů. To je podrobněji [předcházející části](#detect-that-your-task-has-already-run).

### <a name="use-local-storage-to-store-files-that-must-be-accessed-in-the-role"></a>Použít místní úložiště pro ukládání souborů, které musí být přístupné v roli
Pokud chcete zkopírovat nebo vytvořte soubor během vaší spuštění úlohy, který je pak přístupný pro vaši roli, že soubor musí být umístěn v místním úložišti. Najdete v článku [předcházející části](#create-files-in-local-storage-from-a-startup-task).

## <a name="next-steps"></a>Další kroky
Zkontrolujte cloudu [služby modelu a balíčku](cloud-services-model-and-package.md)

Další informace o [úlohy](cloud-services-startup-tasks.md) fungovat.

[Vytvoření a nasazení](cloud-services-how-to-create-deploy-portal.md) balíček vaše cloudové služby.

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[úloh]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[prostředí]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[proměnné]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
[Koncové body]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Endpoints
[LocalStorage]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalStorage
[LocalResources]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalResources
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
