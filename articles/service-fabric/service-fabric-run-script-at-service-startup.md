---
title: Spuštění skriptu při spuštění služby Azure Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat zásady pro Service Fabric vstupního bodu nastavení služby a spuštění skriptu na doba spuštění služby.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: caca931806aed7e1868c126d4629073bcea4b900
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55098608"
---
# <a name="run-a-service-startup-script-as-a-local-user-or-system-account"></a>Spuštění skriptu spuštění služby jako místní uživatel nebo systémový účet
Před spuštění spustitelného souboru služby Service Fabric může být nutné spustit úkony konfigurace nebo nastavení.  Například konfigurace proměnných prostředí. Můžete určit skript běžet před spuštěním spustitelného souboru služby v manifestu služby pro službu. Tím, že nakonfigurujete zásadu RunAs vstupního bodu nastavení služby, že které účet můžete změnit instalačního programu běží pod.  Vstupní bod samostatný instalační program umožňuje spustit vysokými konfigurace na krátkou dobu, tak spustitelný soubor hostitele služby nemusí spouštět s vysokou úrovní oprávnění pro dlouhou dobu.

Vstupní bod nastavení (**SetupEntryPoint** v [manifestu služby](service-fabric-application-and-service-manifests.md)) je privilegovaných vstupní bod, který ve výchozím nastavení běží se stejnými přihlašovacími údaji jako Service Fabric (obvykle  *NetworkService* účtu) před všechny ostatní vstupní bod. Spustitelný soubor, který je určen **EntryPoint** je obvykle dlouhotrvající hostitele služby. **EntryPoint** spuštění spustitelného souboru **SetupEntryPoint** spustitelný soubor skončí úspěšně. Výsledný proces je monitorovat a restartuje a znovu začíná **SetupEntryPoint** Pokud někdy ukončí nebo dojde k chybě. 

## <a name="configure-the-service-setup-entry-point"></a>Konfigurace vstupního bodu nastavení služby
Následující je příklad jednoduchou službu manifestu pro bezstavovou službu, která určuje instalační skript *MySetup.bat* ve službě **SetupEntryPoint**.  **Argumenty** slouží k předávání argumentů do skriptu při spuštění.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyStatelessServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest.</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyStatelessServiceType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <Arguments>MyValue</Arguments>
        <WorkingFolder>Work</WorkingFolder>        
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyStatelessService.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```
## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>Konfigurace zásad pro vstupního bodu nastavení služby
Spustitelný soubor vstupního bodu služby Instalační program ve výchozím nastavení spouští pod stejnými přihlašovacími údaji jako Service Fabric (obvykle *NetworkService* účtu).  V manifestu aplikace můžete změnit oprávnění zabezpečení pro spuštění skriptu spuštění pod účtem místního systému nebo účtem správce.

### <a name="configure-the-policy-by-using-a-local-system-account"></a>Konfigurace zásad s použitím místní systémový účet
V následujícím příkladu manifestu aplikace ukazuje, jak nakonfigurovat vstupního bodu nastavení služby pro spuštění uživatelského účtu správce (SetupAdminUser).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

Nejprve vytvořte **objekty zabezpečení** část s uživatelským jménem, například SetupAdminUser. Uživatelský účet SetupAdminUser je členem skupiny Administrators systému.

Části **ServiceManifestImport** nakonfigurujte zásadu, která platí tento instanční objekt k **SetupEntryPoint**. Tato zásada říká Service Fabric, že **MySetup.bat** se spustí soubor by měl spustit jako SetupAdminUser (s oprávněními správce). Protože máte *není* použít zásadu pro hlavní vstupní bod kódu v **MyServiceHost.exe** běží v rámci systému **NetworkService** účtu. Toto je výchozí účet, který všechny vstupní body služby se spustit jako.

### <a name="configure-the-policy-by-using-local-system-accounts"></a>Konfigurace zásad s použitím místní systémové účty
Často je vhodnější pro spuštění po spuštění skriptu s použitím místního systémového účtu než účtu správce. Spuštění zásad RunAs jako člen skupiny Administrators obvykle nebude fungovat, protože počítače mají přístup k řízení Uživatelských účtů ve výchozím nastavení povolená. V takovém případě doporučujeme spustit SetupEntryPoint pod účtem LocalSystem, nikoli jako místní uživatel přidán do skupiny Správci. Následující příklad ukazuje nastavení SetupEntryPoint, aby byl spuštěn:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
```

> [!NOTE]
> U Linuxových clusterů pro spuštění služby nebo nastavení vstupní bod jako **kořenové**, můžete zadat **AccountType** jako **LocalSystem**.

## <a name="run-a-script-from-the-setup-entry-point"></a>Spusťte skript ze vstupního bodu nastavení
Teď přidejte do projektu spuštět s oprávněními správce po spuštění skriptu. 

V sadě Visual Studio, klikněte pravým tlačítkem na projekt služby a přidejte nový soubor s názvem *MySetup.bat*.

Dále, ujistěte se, že *MySetup.bat* souboru je zahrnuta v balíčku služby. Ve výchozím nastavení není. Vyberte soubor, klikněte pravým tlačítkem na získání kontextovou nabídku a zvolte **vlastnosti**. V dialogovém okně Vlastnosti, ujistěte se, že **kopírovat do výstupního adresáře** je nastavena na **kopírovat, pokud je novější**. Viz následující snímek obrazovky.

![Visual Studio CopyToOutput pro SetupEntryPoint dávkového souboru][image1]

Nyní upravit *MySetup.bat* a přidejte následující příkazy nastavit proměnné prostředí systému a výstup do textového souboru:

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable %*
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

V dalším kroku sestavení a nasazení řešení do místního vývojového clusteru. Po spuštění služby, jak je znázorněno v [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), uvidíte, že soubor MySetup.bat úspěšné dvěma způsoby. Otevřete příkazový řádek Powershellu a zadejte:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Poznamenejte si název uzlu, kde se služba nasazení a spuštění v [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md). Například uzel 2. Dále přejděte do složky aplikace instanci pracovní out.txt soubor, který zobrazuje hodnota **TestVariable**. Například pokud je tato služba byla nasazena na uzlu 2, pak můžete přejít na této cestě **MyApplicationType**:

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

## <a name="run-powershell-commands-from-a-setup-entry-point"></a>Spusťte příkazy Powershellu ze vstupního bodu nastavení
Chcete spustit PowerShell z **SetupEntryPoint** bodu, můžete spustit **PowerShell.exe** v dávkovém souboru, který odkazuje na soubor Powershellu. Nejprve přidejte soubor Powershellu projekt služby – například **MySetup.ps1**. Nezapomeňte nastavit *kopírovat, pokud je novější* vlastnost tak, že soubor je také součástí balíčku služby. Následující příklad ukazuje dávkový soubor, který se spustí soubor prostředí PowerShell s názvem MySetup.ps1, který nastaví proměnnou prostředí systému s názvem **TestVariable**.

MySetup.bat spustit soubor prostředí PowerShell:

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

V prostředí PowerShell souboru přidejte následující k nastavení proměnné prostředí systému:

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> Ve výchozím nastavení, když dávkový soubor se spustí, dohlíží na aplikace složku s názvem **fungovat** pro soubory. V takovém případě při spuštění MySetup.bat chceme, aby pro vyhledání souboru MySetup.ps1 ve stejné složce, což je aplikace **balíček kódu** složky. Chcete-li změnit tato složka, nastavte pracovní složky:
> 
> 

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
```

## <a name="debug-a-startup-script-locally-using-console-redirection"></a>Ladění spouštěcí skript místně pomocí přesměrování konzoly
V některých případech je užitečné pro ladění účely zobrazíte výstup konzoly z spouští se instalační skript. Zásady přesměrování konzoly můžete nastavit v nastavení vstupní bod v manifestu služby, který zapíše výstup do souboru. Výstupní soubor je zapsána do aplikace složku s názvem **protokolu** na uzlu clusteru, kde nasazení a spuštění aplikace. 

> [!WARNING]
> Nikdy nepoužívejte zásady přesměrování konzoly v aplikaci, která je nasazená v produkčním prostředí, protože to může mít vliv převzetí služeb při selhání aplikace. *Pouze* používá se pro místní vývoj a ladění.  
> 
> 

Následující příklad manifestu služby ukazuje nastavení přesměrování konzoly s hodnotou FileRetentionCount:

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

Pokud změníte nyní MySetup.ps1 soubor pro zápis **Echo** příkazu, to bude zapisovat do výstupního souboru pro účely ladění:

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

> [!WARNING]
> Po ladění skriptu, okamžitě odeberou tyto zásady přesměrování konzoly.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další postup
* [Další informace o aplikaci a služby zabezpečení](service-fabric-application-and-service-security.md)
* [Pochopení aplikačního modelu](service-fabric-application-model.md)
* [Zadání prostředků v manifestu služby](service-fabric-service-manifest-resources.md)
* [Nasazení aplikace](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
