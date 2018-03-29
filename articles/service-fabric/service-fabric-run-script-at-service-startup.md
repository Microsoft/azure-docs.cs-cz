---
title: Spustit skript při spuštění služby Azure Service Fabric | Microsoft Docs
description: Zjistěte, jak nakonfigurovat zásadu pro vstupní bod instalační program služby Service Fabric a spusťte skript v doba spuštění služby.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: bd2bb0d05029237242b42225a2c846c78a7c6de9
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="run-a-service-startup-script-as-a-local-user-or-system-account"></a>Spustit skript spuštění služby jako účet místního uživatele nebo systému
Před spuštění spustitelného souboru služby Service Fabric může být nutné spustit pracovní některé konfigurace nebo instalační program.  Například konfigurace proměnné prostředí. Můžete určit skript běžet před spustitelný soubor služby spuštění v service manifest pro službu. Tím, že nakonfigurujete zásadu RunAs pro instalační program Vstupní bod služby, že můžete změnit účet, který instalačního programu běží pod.  Vstupní bod samostatného instalačního umožňuje spustit vysokou privilged konfigurace na krátkou dobu, spustitelný soubor hostitele služby nemusí spustit s vysokou úrovní oprávnění pro dlouhou dobu.

Instalační program vstupního bodu (**SetupEntryPoint** v [service manifest](service-fabric-application-and-service-manifests.md)) je privilegované vstupního bodu, který ve výchozím nastavení běží se stejnými pověřeními, jako Service Fabric (obvykle  *NetworkService* účtu) před další vstupní bod. Spustitelný soubor, který je zadán **EntryPoint** je obvykle dlouho běžící hostitele služby. **EntryPoint** po je spustit spustitelný soubor **SetupEntryPoint** spustitelný soubor ukončí úspěšně. Výsledný proces monitorovat a restartuje a znovu začíná **SetupEntryPoint** Pokud někdy ukončí nebo dojde k chybě. 

## <a name="configure-the-service-setup-entry-point"></a>Konfigurace služby instalace vstupního bodu
Následující je příklad manifestu jednoduché služby pro bezstavové služby, který určuje skript instalace *MySetup.bat* ve službě **SetupEntryPoint**.  **Argumenty** slouží k předání argumentů do skriptu, když je spuštěna.

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
## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>Konfigurace zásad pro bod služby instalační položka
Ve výchozím nastavení spouští spustitelný soubor služby instalace vstupní bod v rámci stejné přihlašovací údaje jako Service Fabric (obvykle *NetworkService* účtu).  V manifestu aplikace můžete změnit oprávnění zabezpečení pro spuštění skriptu spuštění pod účtem místního systému nebo účtem správce.

### <a name="configure-the-policy-by-using-a-local-system-account"></a>Nakonfigurujte zásady pomocí účtu místního systému
Následující příklad manifestu aplikace ukazuje, jak nakonfigurovat službu instalace vstupní bod pro spuštění pod účtem uživatele správce (SetupAdminUser).

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

Nejprve vytvořte **objekty** část s uživatelským jménem, jako je například SetupAdminUser. SetupAdminUser uživatelský účet je členem skupiny Administrators systému.

Dále v části **ServiceManifestImport** nakonfigurujte zásadu použít tento objekt zabezpečení na **SetupEntryPoint**. Tato zásada informuje Service Fabric, který po **MySetup.bat** spuštění souboru má být spuštěn jako SetupAdminUser (s oprávněními správce). Vzhledem k tomu, že máte *není* použít zásadu na hlavní vstupní bod, kód v **MyServiceHost.exe** běží v rámci systému **NetworkService** účtu. Toto je výchozí účet, který všechny vstupní body služby jsou spustit jako.

### <a name="configure-the-policy-by-using-local-system-accounts"></a>Nakonfigurujte zásady pomocí místní systémové účty
Často je vhodnější pro spuštění skriptu spuštění pomocí účtu local system, nikoli účet správce. Spuštění zásad RunAs jako člen skupiny Administrators obvykle nefunguje správně, protože počítače mají přístup k řízení Uživatelských účtů ve výchozím nastavení povolené. V takových případech doporučuje se ke spuštění SetupEntryPoint pod účtem LocalSystem, ne jako místní uživatel přidán do skupiny Administrators. Následující příklad ukazuje nastavení SetupEntryPoint běží pod účtem LocalSystem:

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
> Pro clustery Linux ke spuštění služby nebo nastavení vstupního bodu jako **kořenové**, můžete zadat **AccountType** jako **LocalSystem**.

## <a name="run-a-script-from-the-setup-entry-point"></a>Spusťte skript ze vstupního bodu instalační program
Nyní přidejte po spuštění skriptu do projektu běh oprávnění správce. 

V sadě Visual Studio, klikněte pravým tlačítkem na projekt služby a přidejte nový soubor s názvem *MySetup.bat*.

Dále zkontrolujte, zda *MySetup.bat* souboru je zahrnutý v balíčku služby. Ve výchozím nastavení není. Vyberte soubor, klikněte pravým tlačítkem na získat v místní nabídce a zvolte **vlastnosti**. V dialogovém okně Vlastnosti ověřte, že **kopírovat do výstupního adresáře** je nastaven na **kopírovat, pokud je novější**. Viz následující snímek obrazovky.

![Visual Studio CopyToOutput pro SetupEntryPoint dávkového souboru][image1]

Nyní upravit *MySetup.bat* souboru a přidejte následující příkazy nastavit proměnnou prostředí systému a výstup do textového souboru:

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable %*
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

V dalším kroku sestavení a nasadit řešení místního vývojového clusteru. Po spuštění služby, jak je znázorněno v [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), uvidíte, že MySetup.bat souboru bylo úspěšné dvěma způsoby. Otevřete příkazový řádek prostředí PowerShell a zadejte:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Poznamenejte si název uzlu, kde byla službu nasadit a spustit [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md). Například uzlu 2. Dále přejděte do složky pracovní instance aplikace out.txt soubor, který se zobrazuje hodnota **TestVariable**. Například pokud tato služba byla nasazena na uzlu 2, potom můžete přejít na tuto cestu pro **MyApplicationType**:

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

## <a name="run-powershell-commands-from-a-setup-entry-point"></a>Spouštění příkazů prostředí PowerShell ze vstupního bodu instalační program
Spustit prostředí PowerShell z **SetupEntryPoint** bodu, můžete spustit **PowerShell.exe** v dávkovém souboru, který odkazuje na soubor prostředí PowerShell. Nejprve přidejte soubor prostředí PowerShell služby projektu – například **MySetup.ps1**. Nezapomeňte nastavit *kopírovat, pokud je novější* vlastnost tak, aby soubor jsou také obsaženy v balíčku služby. Následující příklad ukazuje dávkový soubor začínající soubor prostředí PowerShell s názvem MySetup.ps1, která nastavuje proměnnou prostředí systému s názvem **TestVariable**.

MySetup.bat spustit soubor prostředí PowerShell:

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

V prostředí PowerShell souboru přidejte následující nastavení proměnné prostředí systému:

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> Ve výchozím nastavení, když dávkový soubor spouští, vypadá to, v aplikaci složku s názvem **pracovní** pro soubory. V takovém případě při spuštění MySetup.bat chceme najít soubor MySetup.ps1 ve stejné složce, která je aplikace **balíček kódu** složky. Chcete-li změnit tato složka, nastavte pracovní složku:
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

## <a name="debug-a-startup-script-locally-using-console-redirection"></a>Ladění skriptu spuštění místně pomocí přesměrování konzoly
V některých případech je užitečná pro účely, chcete-li zobrazit výstup konzoly z spuštění skriptu instalace ladění. Můžete nastavit zásadu konzoly přesměrování na instalační program Vstupní bod v manifestu služby, který zapíše výstup do souboru. Soubor výstup zapsán do aplikace složku s názvem **protokolu** na uzlu clusteru, kde je aplikace nasazena a spustit. 

> [!WARNING]
> Nikdy nepoužívejte konzolu Zásady přesměrování v aplikaci, která je nasazena v produkčním prostředí, protože to může mít vliv převzetí služeb při selhání aplikaci. *Pouze* používejte pro místní vývoj a účely ladění.  
> 
> 

V následujícím příkladu manifestu služby ukazuje nastavení přesměrování konzoly s hodnotou FileRetentionCount:

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

Pokud změníte teď MySetup.ps1 souboru pro zápis **Echo** příkaz, to bude zapisovat do výstupního souboru pro účely ladění:

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

> [!WARNING]
> Po ladění skriptu, okamžitě odeberte tuto zásadu přesměrování konzoly.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další postup
* [Další informace o zabezpečení služby a aplikace](service-fabric-application-and-service-security.md)
* [Pochopení aplikačního modelu](service-fabric-application-model.md)
* [Zadejte prostředky v service manifest](service-fabric-service-manifest-resources.md)
* [Nasazení aplikace](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
