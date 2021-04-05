---
title: Po spuštění služby Azure Service Fabric spustit skript
description: Naučte se konfigurovat zásady pro vstupní bod nastavení služby Service Fabric a spustit skript při spuštění služby.
author: athinanthny
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: atsenthi
ms.openlocfilehash: a25f16f08ab8ae9564363f179d19d4b30c5315fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96012523"
---
# <a name="run-a-service-startup-script-as-a-local-user-or-system-account"></a>Spuštění skriptu spuštění služby jako místní uživatel nebo systémový účet
Předtím, než se spustí spustitelný soubor služby Service Fabric, může být nutné spustit určitou konfiguraci nebo nastavení práce.  Například konfigurace proměnných prostředí. Můžete určit skript, který se spustí před spuštěním spustitelného souboru služby v manifestu služby pro danou službu. Konfigurací zásad RunAs pro vstupní bod nastavení služby můžete změnit účet, pod kterým se spouští instalační program.  Samostatný vstupní bod instalace vám umožní spustit konfiguraci s vysokou úrovní oprávnění po krátkou dobu, takže spustitelný soubor hostitele služby nemusí být spuštěn s vysokými oprávněními pro delší časová období.

Vstupní bod nastavení (**SetupEntryPoint** v [manifestu služby](service-fabric-application-and-service-manifests.md)) je privilegovaný vstupní bod, který ve výchozím nastavení používá stejné přihlašovací údaje jako Service Fabric (obvykle účet *NetworkService* ) před jakýmkoli jiným vstupním bodem. Spustitelným serverem, který je určen **parametrem EntryPoint** , je obvykle dlouhotrvající hostitel služby. Spustitelný soubor **EntryPoint** se spustí po úspěšném ukončení spustitelného souboru **SetupEntryPoint** . Výsledný proces se monitoruje a restartuje a začne znovu s **SetupEntryPoint** , pokud se někdy ukončí nebo dojde k chybě. 

## <a name="configure-the-service-setup-entry-point"></a>Konfigurace vstupního bodu nastavení služby
Následuje příklad jednoduchého manifestu služby pro bezstavovou službu, která určuje instalační skript *MySetup.bat* ve službě **SetupEntryPoint**.  **Argumenty** se používají k předávání argumentů skriptu při jeho spuštění.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyStatelessServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
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
## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>Konfigurace zásad pro vstupní bod nastavení služby
Ve výchozím nastavení se spustitelný soubor vstupního bodu nastavení služby spouští pod stejnými přihlašovacími údaji jako Service Fabric (obvykle se jedná o účet *NetworkService* ).  V manifestu aplikace můžete změnit oprávnění zabezpečení pro spuštění spouštěcího skriptu pod účtem místního systému nebo účtem správce.

### <a name="configure-the-policy-by-using-a-local-system-account"></a>Konfigurace zásad pomocí účtu místního systému
Následující příklad manifestu aplikace ukazuje, jak nakonfigurovat vstupní bod pro nastavení služby tak, aby běžel pod účtem správce uživatele (SetupAdminUser).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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

Nejprve vytvořte oddíl **objektů zabezpečení** s uživatelským jménem, jako je například SetupAdminUser. Uživatelský účet SetupAdminUser je členem systémové skupiny Administrators.

Potom v části **ServiceManifestImport** nakonfigurujte zásadu, která použije tento objekt zabezpečení na **SetupEntryPoint**. Tato zásada oznamuje Service Fabric, že při spuštění souboru **MySetup.bat** by měl běžet jako SetupAdminUser (s oprávněními správce). Vzhledem k tomu, *že jste nepoužívali* zásadu na hlavní vstupní bod, kód v **MyServiceHost.exe** spustí v rámci účtu systému **Network NetworkService** . Toto je výchozí účet, pod kterým se spouštějí všechny vstupní body služby.

### <a name="configure-the-policy-by-using-local-system-accounts"></a>Konfigurace zásad pomocí místních systémových účtů
Často je vhodnější spouštět spouštěcí skripty pomocí místního systémového účtu, nikoli účtu správce. Spuštění zásad RunAs jako člen skupiny Administrators obvykle nefunguje dobře, protože počítače mají ve výchozím nastavení povolený uživatelský Access Control (UAC). V takových případech doporučujeme spustit SetupEntryPoint jako LocalSystem, nikoli jako místní uživatel přidaný do skupiny Administrators. Následující příklad ukazuje nastavení SetupEntryPoint spustit jako LocalSystem:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
> U clusterů se systémem Linux můžete spustit službu nebo vstupní bod instalace jako **kořenový adresář**, a to tak, že zadáte  **AccountType** jako **LocalSystem**.

## <a name="run-a-script-from-the-setup-entry-point"></a>Spuštění skriptu z vstupního bodu instalace
Teď do projektu přidejte spouštěcí skript, který se spustí s oprávněními správce. 

V aplikaci Visual Studio klikněte pravým tlačítkem myši na projekt služby a přidejte nový soubor s názvem *MySetup.bat*.

Potom zajistěte, aby byl soubor *MySetup.bat* zahrnutý v balíčku služby. Ve výchozím nastavení to není. Vyberte soubor, klikněte pravým tlačítkem myši a zobrazte kontextovou nabídku a zvolte možnost **vlastnosti**. V dialogovém okně Vlastnosti zajistěte, aby byl příkaz **Kopírovat do výstupního adresáře** nastaven na hodnotu **Kopírovat, pokud je novější**. Viz následující snímek obrazovky.

![Dávkový soubor pro Visual Studio CopyToOutput for SetupEntryPoint][image1]

Nyní upravte soubor *MySetup.bat* a přidejte následující příkazy nastavte proměnnou prostředí systému a výstup textového souboru:

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable %*
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

V dalším kroku Sestavte a nasaďte řešení do místního vývojového clusteru. Po spuštění služby, jak je znázorněno v [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), vidíte, že MySetup.bat soubor byl úspěšný dvěma způsoby. Otevřete příkazový řádek prostředí PowerShell a zadejte:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Pak si poznamenejte název uzlu, ve kterém byla služba nasazená a spuštěná v [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md). Například Node 2. Potom přejděte do pracovní složky instance aplikace a vyhledejte soubor out.txt, který zobrazuje hodnotu **souboru TestVariable**. Pokud jste například tuto službu nasadili na uzel 2, můžete přejít do této cesty pro **MyApplicationType**:

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

## <a name="run-powershell-commands-from-a-setup-entry-point"></a>Spuštění příkazů PowerShellu z vstupního bodu instalace
Pokud chcete spustit PowerShell z **SetupEntryPoint** bodu, můžete spustit **PowerShell.exe** v dávkovém souboru, který odkazuje na soubor PowerShellu. Nejdřív přidejte soubor PowerShellu do projektu služby – například **MySetup.ps1**. Nezapomeňte nastavit vlastnost *Kopírovat, pokud je novější* , aby byl soubor také zahrnut do balíčku služby. Následující příklad ukazuje ukázkový dávkový soubor, který spustí soubor PowerShellu s názvem MySetup.ps1, který nastaví proměnnou prostředí systému s názvem **souboru TestVariable**.

MySetup.bat spustit soubor PowerShellu:

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

Do souboru PowerShellu přidejte následující příkaz a nastavte proměnnou prostředí systému:

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> Ve výchozím nastavení se při spuštění dávkového souboru podívá do složky aplikace s názvem **Work** for Files. V takovém případě chceme, aby při MySetup.bat běžel soubor MySetup.ps1 ve stejné složce, což je složka **balíčku kódu** aplikace. Chcete-li změnit tuto složku, nastavte pracovní složku:
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

## <a name="debug-a-startup-script-locally-using-console-redirection"></a>Místní ladění spouštěcího skriptu pomocí přesměrování konzoly
V některých případech je užitečné pro účely ladění, aby se zobrazil výstup konzoly pro spuštění instalačního skriptu. Zásady přesměrování konzoly můžete nastavit na vstupním bodu instalace v manifestu služby, který zapisuje výstup do souboru. Výstup souboru se zapisuje do složky aplikace s názvem **log (protokol** ) na uzlu clusteru, kde je aplikace nasazená a spuštěná. 

> [!WARNING]
> Nikdy nepoužívejte zásady přesměrování konzoly v aplikaci, která je nasazená v produkčním prostředí, protože to může mít vliv na převzetí služeb při selhání aplikace. Tato operace se používá *pouze* pro účely místního vývoje a ladění.  
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

Pokud teď změníte soubor MySetup.ps1 pro zápis příkazu **echo** , zapíše se do výstupního souboru pro účely ladění:

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

> [!WARNING]
> Po ladění skriptu okamžitě odeberte tuto zásadu přesměrování konzoly.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další kroky
* [Další informace o zabezpečení aplikací a služeb](service-fabric-application-and-service-security.md)
* [Pochopení aplikačního modelu](service-fabric-application-model.md)
* [Určení prostředků v manifestu služby](service-fabric-service-manifest-resources.md)
* [Nasazení aplikace](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
