---
title: Běžné úlohy po spuštění pro Cloud Services (Classic) | Microsoft Docs
description: V této části najdete několik příkladů běžných úloh po spuštění, které můžete chtít provést v rámci webové role nebo role pracovního procesu Cloud Services.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: f55b225e615a3e7a5fbcf56b405054883d3b5413
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98741192"
---
# <a name="common-cloud-service-classic-startup-tasks"></a>Běžné úlohy po spuštění služby Cloud Service (Classic)

> [!IMPORTANT]
> [Azure Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md) je nový model nasazení založený na Azure Resource Manager pro produkt Azure Cloud Services.V důsledku této změny se Azure Cloud Services běžící na modelu nasazení založeném na Azure Service Manager přejmenovala jako Cloud Services (Classic) a všechna nová nasazení by měla používat [Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md).

Tento článek popisuje několik příkladů běžných úloh po spuštění, které můžete chtít provést ve své cloudové službě. Úlohy po spuštění můžete použít k provádění operací před spuštěním role. Operace, které můžete chtít provést, zahrnují instalaci komponenty, registraci komponent modelu COM, nastavení klíčů registru nebo spuštění dlouhotrvajícího procesu. 

V [tomto článku](cloud-services-startup-tasks.md) se seznámíte s tím, jak fungují úlohy při spouštění, a konkrétně jak vytvořit položky definující úlohu po spuštění.

> [!NOTE]
> Úlohy po spuštění se nevztahují na Virtual Machines, a to pouze na webové role a role pracovních procesů cloudové služby.
> 

## <a name="define-environment-variables-before-a-role-starts"></a>Definování proměnných prostředí před spuštěním role
Pokud potřebujete proměnné prostředí definované pro konkrétní úlohu, použijte prvek [prostředí] uvnitř elementu [Task] .

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

Proměnné můžou použít taky [platnou hodnotu XPath Azure](cloud-services-role-config-xpath.md) pro odkazování na něco o nasazení. Namísto použití `value` atributu definujte podřízený element [RoleInstanceValue] .

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## <a name="configure-iis-startup-with-appcmdexe"></a>Konfigurace spouštění služby IIS pomocí AppCmd.exe
Nástroj příkazového řádku [AppCmd.exe](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj635852(v=ws.11)) můžete použít ke správě nastavení IIS při spuštění v Azure. *AppCmd.exe* poskytuje pohodlný přístup k nastavení konfigurace pro použití v úlohách po spuštění v Azure, a to prostřednictvím příkazového řádku. Pomocí *AppCmd.exe* můžete přidat, upravit nebo odebrat nastavení webu pro aplikace a weby.

K dispozici je ale několik věcí, které můžete využít při použití *AppCmd.exe* jako úlohy po spuštění:

* Úlohy po spuštění lze spustit více než jednou mezi restartováními. Například při recyklaci role.
* Pokud je akce *AppCmd.exe* provedena více než jednou, může dojít k chybě. Například pokus o přidání oddílu *Web.config* dvakrát může vygenerovat chybu.
* Úlohy po spuštění selžou, pokud vrátí nenulový ukončovací kód nebo hodnotu **errorlevel**. Například když *AppCmd.exe* vygeneruje chybu.

Je vhodné kontrolovat hodnotu **errorlevel** po volání *AppCmd.exe*, což je snadné, pokud zabalíte volání *AppCmd.exe* se souborem *. cmd* . Pokud zjistíte známou odpověď **errorlevel** , můžete ji ignorovat nebo ji předat zpátky.

Hodnota ERRORLEVEL vrácená funkcí *AppCmd.exe* je uvedena v souboru WinError. h a lze ji také zobrazit na [webu MSDN](/windows/desktop/Debug/system-error-codes--0-499-).

### <a name="example-of-managing-the-error-level"></a>Příklad správy úrovně chyby
Tento příklad přidá kompresní oddíl a položku komprese pro JSON do souboru *Web.config* s chybami zpracování a protokolování.

Zde jsou uvedeny relevantní oddíly souboru [ServiceDefinition. csdef] , které zahrnují nastavení atributu [executionContext](/previous-versions/azure/reference/gg557552(v=azure.100)#task) pro `elevated` udělení *AppCmd.exe* dostatečná oprávnění ke změně nastavení v souboru *Web.config* :

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

*Spouštěcí soubor. cmd* batch používá *AppCmd.exe* k přidání kompresního oddílu a položky komprese pro JSON do souboru *Web.config* . Očekávaná hodnota **parametru errorlevel** 183 je nastavena na hodnotu nula pomocí VERIFY.EXE programu příkazového řádku. K StartupErrorLog.txt jsou protokolovány neočekávané ERRORLEVEL.

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

## <a name="add-firewall-rules"></a>Přidat pravidla brány firewall
V Azure existují efektivní dvě brány firewall. První brána firewall řídí připojení mezi virtuálním počítačem a vnějším světem. Tato brána firewall je řízena elementem [koncových bodů] v souboru [ServiceDefinition. csdef] .

Druhá brána firewall řídí připojení mezi virtuálním počítačem a procesy v rámci tohoto virtuálního počítače. Tuto bránu firewall lze ovládat pomocí `netsh advfirewall firewall` nástroje příkazového řádku.

Azure vytvoří pravidla brány firewall pro procesy spuštěné v rámci vašich rolí. Když třeba spustíte službu nebo program, Azure automaticky vytvoří potřebná pravidla firewallu, která této službě umožní komunikovat s internetem. Pokud ale vytvoříte službu, kterou spustí proces mimo vaši roli (třeba službu COM+ nebo plánovanou úlohu Windows), musíte ručně vytvořit pravidlo brány firewall, které umožní přístup k této službě. Tato pravidla brány firewall lze vytvořit pomocí úlohy po spuštění.

Úloha po spuštění, která vytvoří pravidlo brány firewall, musí mít [ExecutionContext][úlohu] se **zvýšenými oprávněními**. Přidejte následující úlohu po spuštění do souboru [ServiceDefinition. csdef] .

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

Chcete-li přidat pravidlo brány firewall, je nutné použít příslušné `netsh advfirewall firewall` příkazy ve spouštěcím dávkovém souboru. V tomto příkladu úloha po spuštění vyžaduje zabezpečení a šifrování pro port TCP 80.

```cmd
REM   Add a firewall rule in a startup task.

REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="block-a-specific-ip-address"></a>Blokování konkrétní IP adresy
Úpravou souboru **web.config** služby IIS můžete omezit přístup webové role Azure na sadu zadaných IP adres. Také je nutné použít soubor příkazů, který odemkne část **IPSecurity** souboru **ApplicationHost.config** .

Chcete-li odemknout část **IPSecurity** souboru **ApplicationHost.config** , vytvořte soubor příkazů, který se spouští při spuštění role. Vytvořte složku na kořenové úrovni webové role s názvem **Startup** a v rámci této složky vytvořte dávkový soubor s názvem **Startup. cmd**. Přidejte tento soubor do projektu sady Visual Studio a nastavte vlastnosti na **Kopírovat vždy** , abyste měli jistotu, že je zahrnutý do balíčku.

Přidejte následující úlohu po spuštění do souboru [ServiceDefinition. csdef] .

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

Přidejte tento příkaz do souboru **Startup. cmd** :

```cmd
@echo off
@echo Installing "IPv4 Address and Domain Restrictions" feature 
powershell -ExecutionPolicy Unrestricted -command "Install-WindowsFeature Web-IP-Security"
@echo Unlocking configuration for "IPv4 Address and Domain Restrictions" feature 
%windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity
```

Tato úloha způsobí, že se **spouštěcí soubor Startup. cmd** spustí pokaždé, když se inicializuje webová role, a zajistí odemčení požadovaného oddílu **IPSecurity** .

Nakonec upravte [oddíl System. webserver](https://www.iis.net/configreference/system.webserver/security/ipsecurity#005) **web.config** souboru webové role a přidejte seznam IP adres, kterým je udělen přístup, jak je znázorněno v následujícím příkladu:

Tato ukázková konfigurace **umožňuje** všem IP adresám přístup k serveru s výjimkou definovaných dvou.

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

Tato ukázková konfigurace **odepírá** všem IP adresám přístup k serveru s výjimkou definovaných dvou.

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

## <a name="create-a-powershell-startup-task"></a>Vytvoření spouštěcí úlohy PowerShellu
Skripty prostředí Windows PowerShell nelze volat přímo ze souboru [ServiceDefinition. csdef] , ale mohou být vyvolány v rámci spouštěcího dávkového souboru.

PowerShell (ve výchozím nastavení) nespouští nepodepsané skripty. Pokud váš skript nepodepisujete, budete muset nakonfigurovat PowerShell tak, aby spouštěl nepodepsané skripty. Aby bylo možné spouštět nepodepsané skripty, musí být **ExecutionPolicy** nastaven na **neomezený**. Používané nastavení **ExecutionPolicy** je založené na verzi Windows PowerShellu.

```cmd
REM   Run an unsigned PowerShell script and log the output
PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

Pokud používáte hostovaný operační systém, na kterém běží PowerShell 2,0 nebo 1,0, můžete vynutit spuštění verze 2 a pokud není k dispozici, použijte verzi 1.

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
Prostředek místního úložiště můžete použít k ukládání souborů vytvořených spouštěcí úlohou, ke kterým později přistupovala vaše aplikace.

Chcete-li vytvořit prostředek místního úložiště, přidejte do souboru [ServiceDefinition. csdef] oddíl [LocalResources] a přidejte do něj podřízený element [localStorage] . Poskytněte místnímu prostředku úložiště jedinečný název a odpovídající velikost spouštěcí úlohy.

Pokud chcete použít prostředek místního úložiště v úloze po spuštění, musíte vytvořit proměnnou prostředí, která bude odkazovat na umístění prostředku místního úložiště. Pak bude spouštěcí úkol a aplikace moci číst a zapisovat soubory do místního prostředku úložiště.

Relevantní části souboru **ServiceDefinition. csdef** jsou uvedeny zde:

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

Příklad: Tento spouštěcí dávkový soubor **. cmd** používá proměnnou prostředí **PathToStartupStorage** k vytvoření souboru **MyTest.txt** v umístění místního úložiště.

```cmd
REM   Create a simple text file.

ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

REM   Exit the batch file with ERRORLEVEL 0.

EXIT /b 0
```

Pomocí metody [GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) můžete získat přístup ke složce místního úložiště ze sady Azure SDK.

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTestFile.txt"));
```

## <a name="run-in-the-emulator-or-cloud"></a>Spuštění v emulátoru nebo v cloudu
Můžete mít spouštěcí úlohu, pokud je v cloudu v porovnání s tím, kdy se nachází v emulátoru služby COMPUTE, provádět různé kroky. Například můžete chtít použít novou kopii dat SQL pouze v případě, že je spuštěna v emulátoru. Nebo možná budete chtít provést některé optimalizace výkonu pro Cloud, které nemusíte dělat při spuštění v emulátoru.

Tato možnost provádění různých akcí s emulátorem výpočtů a cloudem je možné dosáhnout vytvořením proměnné prostředí v souboru [ServiceDefinition. csdef] . Pak otestujete proměnnou prostředí pro hodnotu v úloze po spuštění.

Chcete-li vytvořit proměnnou prostředí, přidejte element [Variable] / [RoleInstanceValue] a vytvořte hodnotu XPath `/RoleEnvironment/Deployment/@emulated` . Hodnota proměnné prostředí **% ComputeEmulatorRunning%** je `true` spuštěna v emulátoru služby COMPUTE a `false` při spuštění v cloudu.

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

Úloha teď může zkontrolovat proměnnou prostředí **% ComputeEmulatorRunning%** , aby prováděla různé akce na základě toho, jestli je role spuštěná v cloudu, nebo v emulátoru. Tady je skript prostředí. cmd, který kontroluje tuto proměnnou prostředí.

```cmd
REM   Check if this task is running on the compute emulator.

IF "%ComputeEmulatorRunning%" == "true" (
    REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
) ELSE (
    REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
)
```


## <a name="detect-that-your-task-has-already-run"></a>Zjištění, že váš úkol již běžel
Role se může recyklovat bez restartování, což způsobí, že se úlohy po spuštění znovu spustí. Není k dispozici žádný příznak, který označuje, že úloha již byla spuštěna na hostitelském virtuálním počítači. Můžete mít některé úkoly, které nezáleží na tom, že se spouštějí několikrát. Můžete ale spustit v situaci, kdy potřebujete zabránit spuštění úlohy více než jednou.

Nejjednodušší způsob, jak zjistit, že úloha už je spuštěná, je vytvořit soubor ve složce **% TEMP%** , když je úloha úspěšná, a najít ji na začátku úkolu. Tady je ukázkový skript CMD Shell, který to dělá za vás.

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

## <a name="task-best-practices"></a>Doporučené postupy pro úlohy
Tady jsou některé osvědčené postupy, které byste měli dodržovat při konfiguraci úlohy pro webovou roli nebo roli pracovního procesu.

### <a name="always-log-startup-activities"></a>Vždy protokolovat aktivity při spuštění
Visual Studio neposkytuje ladicí program pro procházení souborů Batch, takže je dobré získat co nejvíce dat o fungování dávkových souborů. Protokolování výstupu dávkových souborů, **stdout** a **stderr**, vám může poskytnout důležité informace při pokusu o ladění a opravě dávkových souborů. Pokud chcete protokolovat **stdout** a **stderr** do souboru StartupLog.txt v adresáři, na který odkazovala proměnná prostředí **% TEMP%** , přidejte text `>>  "%TEMP%\\StartupLog.txt" 2>&1` na konec konkrétních řádků, které chcete protokolovat. Chcete-li například provést setup.exe v adresáři **% PathToApp1Install%** : `"%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1`

Chcete-li zjednodušit XML, můžete vytvořit soubor *cmd cmd* , který volá všechny úlohy po spuštění společně s protokolováním a zajišťuje, že každá podřízená úloha sdílí stejné proměnné prostředí.

Může se stát, že na `>> "%TEMP%\StartupLog.txt" 2>&1` konci každé úlohy po spuštění bude aplikace nepříjemné. Protokolování úloh můžete vynutil vytvořit obálku, která vám zpracuje protokolování. Tato obálka volá skutečný dávkový soubor, který chcete spustit. Libovolný výstup z cílového dávkového souboru bude přesměrován do souboru *Startuplog.txt* .

Následující příklad ukazuje, jak přesměrovat všechny výstupy z spouštěcího dávkového souboru. V tomto příkladu vytvoří soubor ServerDefinition. csdef úlohu po spuštění, která volá *logwrap. cmd*. *logwrap. cmd* volá *Startup2. cmd* a přesměruje veškerý výstup do **% TEMP% \\StartupLog.txt**.

ServiceDefinition. cmd:

```xml
<Startup>
    <Task commandLine="logwrap.cmd startup2.cmd" executionContext="limited" taskType="simple" />
</Startup>
```

**logwrap. cmd:**

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

**Startup2. cmd:**

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

Ukázkový výstup v souboru **StartupLog.txt** :

```txt
[Mon 10/17/2016 20:24:46.75] == START logwrap.cmd ============================================== 
[Mon 10/17/2016 20:24:46.75] Running command1.cmd 
[Mon 10/17/2016 20:24:46.77] Some log information about this task
[Mon 10/17/2016 20:24:46.77] Some more log information about this task
[Mon 10/17/2016 20:24:46.77] Done 
[Mon 10/17/2016 20:24:46.77] == END logwrap.cmd ================================================ 
```

> [!TIP]
> Soubor **StartupLog.txt** je umístěný ve složce *C:\Resources\temp \\ {role} \RoleTemp* .
> 
> 

### <a name="set-executioncontext-appropriately-for-startup-tasks"></a>Nastavte executionContext správně pro úlohy po spuštění.
Nastavte patřičná oprávnění pro úlohu po spuštění. Někdy se úlohy po spuštění musí spouštět se zvýšenými oprávněními, i když role běží s normálními oprávněními.

Atribut [][Task] ExecutionContext nastaví úroveň oprávnění pro úlohu po spuštění. Použití `executionContext="limited"` znamená, že úloha po spuštění má stejnou úroveň oprávnění jako role. Použití `executionContext="elevated"` znamená, že úloha po spuštění má oprávnění správce, což umožňuje, aby úloha po spuštění prováděla úlohy správce bez udělení oprávnění správce vaší roli.

Příkladem úlohy po spuštění, která vyžaduje zvýšená oprávnění, je úloha po spuštění, která používá **AppCmd.exe** ke konfiguraci služby IIS. **AppCmd.exe** vyžaduje `executionContext="elevated"` .

### <a name="use-the-appropriate-tasktype"></a>Použijte odpovídající taskType
Atribut [][úlohy] taskType určuje způsob, jakým je spouštěn úkol při spuštění. Existují tři hodnoty: **jednoduché**, **pozadí** a **popředí**. Úlohy na pozadí a na popředí jsou spouštěny asynchronně a jednoduché úkoly jsou spouštěny synchronně po jednom.

Pomocí **jednoduchých** úloh po spuštění můžete nastavit pořadí, ve kterém jsou úlohy spouštěny v pořadí, ve kterém jsou úlohy uvedeny v souboru ServiceDefinition. csdef. Pokud **Jednoduchá** úloha končí nenulovým ukončovacím kódem, pak se proces spuštění zastaví a role se nespustí.

Rozdíl mezi úlohami při spuštění na **pozadí** a úlohami spouštěnými v **popředí** je, že úlohy na **popředí** udržují roli spuštěnou až do ukončení úlohy na **popředí** . To také znamená, že pokud úloha na **popředí** přestane reagovat nebo dojde k selhání, nebude role recyklovat, dokud není úloha na **popředí** vynucena zavřena. Z tohoto důvodu se doporučuje úlohy na **pozadí** pro asynchronní úlohy po spuštění, pokud tuto funkci úlohy na **popředí** nepotřebujete.

### <a name="end-batch-files-with-exit-b-0"></a>Ukončit dávkové soubory s UKONČENÍm/B 0
Role se spustí jenom v případě, že se hodnota **errorlevel** z každého jednoduchého spouštěcího úkolu rovná nule. Ne všechny programy nastavily správně **errorlevel** (ukončovací kód), takže dávkový soubor by měl končit na, `EXIT /B 0` Pokud všechno běželo správně.

Chybějící `EXIT /B 0` na konci spouštěcího dávkového souboru je běžnou příčinou rolí, které se nespouštějí.

> [!NOTE]
> Zjistili jsme, že vnořené dávkové soubory někdy při použití parametru přestanou reagovat `/B` . Možná budete chtít zajistit, aby k tomuto problému nedocházelo, pokud jiný dávkový soubor volá aktuální dávkový soubor, jako kdybyste použili [obálku protokolu](#always-log-startup-activities). `/B`V tomto případě můžete parametr vynechat.
> 
> 

### <a name="expect-startup-tasks-to-run-more-than-once"></a>Očekávat spouštění úloh po spuštění více než jednou
Ne všechny recyklace rolí zahrnují restartování, ale všechny recykly role obsahují spuštěné všechny úlohy po spuštění. To znamená, že úlohy po spuštění musí být možné spouštět několikrát mezi restartováními bez problémů. Tento popis je popsán v [předchozí části](#detect-that-your-task-has-already-run).

### <a name="use-local-storage-to-store-files-that-must-be-accessed-in-the-role"></a>Použití místního úložiště k ukládání souborů, ke kterým je potřeba mít v roli pøístup
Pokud chcete kopírovat nebo vytvořit soubor během úlohy po spuštění, která je pak přístupná pro vaši roli, musí být tento soubor umístěný v místním úložišti. Projděte si [předchozí část](#create-files-in-local-storage-from-a-startup-task).

## <a name="next-steps"></a>Další kroky
Kontrola [modelu a balíčku](cloud-services-model-and-package.md) cloudové služby

Přečtěte si další informace o práci s [úkoly](cloud-services-startup-tasks.md) .

[Vytvořte a nasaďte](cloud-services-how-to-create-deploy-portal.md) balíček cloudové služby.

[ServiceDefinition. csdef]: cloud-services-model-and-package.md#csdef
[Úkol]: /previous-versions/azure/reference/gg557552(v=azure.100)#Task
[Startup]: /previous-versions/azure/reference/gg557552(v=azure.100)#Startup
[Runtime]: /previous-versions/azure/reference/gg557552(v=azure.100)#Runtime
[Prostředí]: /previous-versions/azure/reference/gg557552(v=azure.100)#Environment
[Proměnná]: /previous-versions/azure/reference/gg557552(v=azure.100)#Variable
[RoleInstanceValue]: /previous-versions/azure/reference/gg557552(v=azure.100)#RoleInstanceValue
[RoleEnvironment]: /previous-versions/azure/reference/ee773173(v=azure.100)
[Koncové body]: /previous-versions/azure/reference/gg557552(v=azure.100)#Endpoints
[LocalStorage]: /previous-versions/azure/reference/gg557552(v=azure.100)#LocalStorage
[LocalResources]: /previous-versions/azure/reference/gg557552(v=azure.100)#LocalResources
[RoleInstanceValue]: /previous-versions/azure/reference/gg557552(v=azure.100)#RoleInstanceValue