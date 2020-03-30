---
title: Spuštění skriptu při spuštění služby Azure Service Fabric
description: Přečtěte si, jak nakonfigurovat zásady pro vstupní bod nastavení služby Service Fabric a spustit skript v době spuštění služby.
author: athinanthny
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: atsenthi
ms.openlocfilehash: a25f16f08ab8ae9564363f179d19d4b30c5315fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464287"
---
# <a name="run-a-service-startup-script-as-a-local-user-or-system-account"></a>Spuštění skriptu spuštění služby jako místní uživatel nebo systémový účet
Před spuštěním spustitelného souboru služby Service Fabric může být nutné spustit některé konfigurace nebo instalační práce.  Například konfigurace proměnných prostředí. Můžete zadat skript, který se má spustit před spuštěním spustitelného souboru služby v manifestu služby pro službu. Konfigurací zásad runas pro vstupní bod nastavení služby můžete změnit, pod kterým účtem spustitelný soubor instalace je spuštěn.  Samostatný vstupní bod nastavení umožňuje spustit konfiguraci s vysokými oprávněními na krátkou dobu, takže spustitelný soubor hostitele služby nemusí běžet s vysokými oprávněními po delší dobu.

Vstupní bod instalace **(SetupEntryPoint** v [manifestu služby](service-fabric-application-and-service-manifests.md)) je privilegovaný vstupní bod, který ve výchozím nastavení běží se stejnými pověřeními jako Service Fabric (obvykle účet *NetworkService)* před jakýmkoli jiným vstupním bodem. Spustitelný soubor určený **entrypointem** je obvykle dlouhotrvající hostitel služby. Spustitelný soubor **EntryPoint** je spuštěn po úspěšném ukončení spustitelného souboru **SetupEntryPoint.** Výsledný proces je sledován a restartován a začíná znovu **s SetupEntryPoint,** pokud někdy ukončí nebo dojde k chybě. 

## <a name="configure-the-service-setup-entry-point"></a>Konfigurace vstupního bodu nastavení služby
Následuje jednoduchý příklad manifestu služby pro bezstavovou službu, která určuje instalační skript *MySetup.bat* ve službě **SetupEntryPoint**.  **Argumenty** se používá k předání argumentů do skriptu při jeho spuštění.

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
Ve výchozím nastavení je spustitelný soubor vstupního bodu služby spuštěn pod stejnými pověřeními jako Service Fabric (obvykle účet *NetworkService).*  V manifestu aplikace můžete změnit oprávnění zabezpečení pro spuštění spouštěcího skriptu pod místním systémovým účtem nebo účtem správce.

### <a name="configure-the-policy-by-using-a-local-system-account"></a>Konfigurace zásad pomocí místního systémového účtu
Následující příklad manifestu aplikace ukazuje, jak nakonfigurovat vstupní bod nastavení služby pro spuštění pod účtem správce uživatele (SetupAdminUser).

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

Nejprve vytvořte oddíl **Objekty zabezpečení** s uživatelským jménem, například SetupAdminUser. Uživatelský účet SetupAdminUser je členem systémové skupiny Administrators.

Dále v části **ServiceManifestImport** nakonfigurujte zásadu pro použití tohoto objektu zabezpečení pro **SetupEntryPoint**. Tato zásada informuje service fabric, že při spuštění souboru **MySetup.bat** by měl být spuštěn jako SetupAdminUser (s oprávněními správce). Vzhledem k tomu, že jste *nepoužili* zásadu na hlavní vstupní bod, kód v **myServiceHost.exe** běží pod systémem **NetworkService** účtu. Toto je výchozí účet, za kterým jsou spuštěny všechny vstupní body servisu.

### <a name="configure-the-policy-by-using-local-system-accounts"></a>Konfigurace zásad pomocí místních systémových účtů
Často je vhodnější spustit spouštěcí skript pomocí místního systémového účtu, nikoli pomocí účtu správce. Spuštění zásadrun jako člen skupiny Administrators obvykle nefunguje dobře, protože počítače mají ve výchozím nastavení povoleno řízení přístupu uživatelů (UAC). V takových případech je doporučení mů e-li spustit setupentrypoint jako LocalSystem, nikoli jako místní uživatel přidaný do skupiny Administrators. Následující příklad ukazuje nastavení nastavení SetupEntryPoint spustit jako LocalSystem:

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
> Chcete-li pro clustery Linux spustit službu nebo vstupní bod nastavení jako **root**, můžete zadat **AccountType** jako **LocalSystem**.

## <a name="run-a-script-from-the-setup-entry-point"></a>Spuštění skriptu z vstupního bodu nastavení
Nyní přidejte skript pro spuštění projektu, který bude spuštěn pod oprávněními správce. 

V sadě Visual Studio klepněte pravým tlačítkem myši na projekt služby a přidejte nový soubor s názvem *MySetup.bat*.

Dále se ujistěte, že soubor *MySetup.bat* je součástí balíčku služby. Ve výchozím nastavení není. Vyberte soubor, klepnutím pravým tlačítkem myši získáte místní nabídku a zvolte **Vlastnosti**. V dialogovém okně Vlastnosti zkontrolujte, zda je **možnost Kopírovat do výstupního adresáře** nastavena na **kopírovat, pokud**je novější . Viz následující snímek obrazovky.

![Dávkový soubor Visual Studio CopyToOutput for SetupEntryPoint][image1]

Nyní upravte soubor *MySetup.bat* a přidejte následující příkazy, nastavte proměnnou prostředí systému a výstup textového souboru:

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable %*
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

Dále sestavte a nasaďte řešení do clusteru místního vývoje. Po spuštění služby, jak je znázorněno v [aplikaci Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), můžete vidět, že soubor MySetup.bat byl úspěšný dvěma způsoby. Otevřete příkazový řádek Prostředí PowerShell a zadejte:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Potom poznamenejte si název uzlu, ve kterém byla služba nasazena a spuštěna v [aplikaci Service Fabric Explorer](service-fabric-visualizing-your-cluster.md). Například uzel 2. Dále přejděte do pracovní složky instance aplikace a vyhledejte soubor out.txt, který zobrazuje hodnotu **TestVariable**. Například pokud tato služba byla nasazena na uzel 2, pak můžete přejít na tuto cestu pro **MyApplicationType**:

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

## <a name="run-powershell-commands-from-a-setup-entry-point"></a>Spuštění příkazů Prostředí PowerShell z vstupního bodu nastavení
Chcete-li spustit prostředí PowerShell z bodu **SetupEntryPoint,** můžete spustit **program PowerShell.exe** v dávkovém souboru, který odkazuje na soubor prostředí PowerShell. Nejprve přidejte soubor prostředí PowerShell do projektu služby– například **MySetup.ps1**. Nezapomeňte nastavit *Copy, pokud novější* vlastnost tak, aby soubor je také součástí balíčku služby. Následující příklad ukazuje ukázkový dávkový soubor, který spouští soubor prostředí PowerShell s názvem MySetup.ps1, který nastavuje proměnnou systémového prostředí nazvanou **TestVariable**.

MySetup.bat pro spuštění souboru PowerShellu:

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

V souboru PowerShellu přidejte následující nastavení proměnné systémového prostředí:

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> Ve výchozím nastavení se při spuštění dávkového souboru podívá na složku aplikace nazvanou **práce** pro soubory. V tomto případě při spuštění souboru MySetup.bat chceme, aby byl soubor MySetup.ps1 vyhledání ve stejné složce, což je složka **balíčku kódu** aplikace. Chcete-li tuto složku změnit, nastavte pracovní složku:
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
V některých případě je užitečné pro účely ladění zobrazit výstup konzoly ze spuštění instalačního skriptu. Můžete nastavit zásady přesměrování konzoly v vstupním bodě instalace v manifestu služby, který zapíše výstup do souboru. Výstup souboru se zapíše do složky aplikace s názvem **protokol** v uzlu clusteru, kde je aplikace nasazena a spuštěna. 

> [!WARNING]
> Nikdy nepoužívejte zásady přesměrování konzoly v aplikaci, která je nasazena v produkčním prostředí, protože to může ovlivnit převzetí služeb při selhání aplikace. Používejte *pouze* pro účely místního vývoje a ladění.  
> 
> 

Následující příklad manifestu služby ukazuje nastavení přesměrování konzoly pomocí hodnoty FileRetentionCount:

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

Pokud nyní změníte soubor MySetup.ps1 na příkaz **Echo,** zapíše se do výstupního souboru pro účely ladění:

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

> [!WARNING]
> Po ladění skriptu okamžitě odeberte tuto zásadu přesměrování konzoly.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další kroky
* [Informace o zabezpečení aplikací a služeb](service-fabric-application-and-service-security.md)
* [Principy aplikačního modelu](service-fabric-application-model.md)
* [Určení prostředků v manifestu služby](service-fabric-service-manifest-resources.md)
* [Nasazení aplikace](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
