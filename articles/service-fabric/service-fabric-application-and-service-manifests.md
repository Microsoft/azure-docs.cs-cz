---
title: Popisující Azure Service Fabric aplikace a služby | Microsoft Docs
description: Popisuje, jak se manifesty používají k popisu služby a aplikace Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: ryanwi
ms.openlocfilehash: b79206b9d456226d14984e8a1c1002c07c4f626a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208467"
---
# <a name="service-fabric-application-and-service-manifests"></a>Manifestů služby a aplikace Service Fabric
Tento článek popisuje, jak Service Fabric aplikace a služby jsou určené a verzí pomocí ApplicationManifest.xml a ServiceManifest.xml souborů.  Schéma XML pro tyto soubory manifestu je popsána v [dokumentace schématu ServiceFabricServiceModel.xsd](service-fabric-service-model-schema.md).

## <a name="describe-a-service-in-servicemanifestxml"></a>Popis služby v ServiceManifest.xml
Manifest služby deklarativně definuje typ služby a verze. Určuje metadata služby, jako je například typ služby, Vlastnosti stavu, metriky Vyrovnávání zatížení, binární soubory služby a konfigurační soubory.  Jinými slovy, popisuje kód, konfigurace a data balíčky, které tvoří balíček služby pro podporu jeden nebo více typů služeb. Manifest služby může obsahovat více kód, konfigurace a data balíčků, které může být verzí nezávisle. Tady je service manifest pro ASP.NET Core front-endu webové služby [hlasování ukázkovou aplikaci](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart):

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

**Verze** atributy jsou nestrukturovaných řetězce a analyzovat není v systému. Verze atributy se používá k verze jednotlivých součástí pro upgrade.

**ServiceTypes** deklaruje, jaké typy služby podporuje **CodePackages** v tomto manifestu. Při vytváření instance služby pro jeden z těchto typů služeb aktivují se všechny balíčky kódu, které jsou deklarované v manifestu tento spuštěním jejich vstupní body. Výsledný procesy se očekává registrace podporovaných typů služeb v době běhu. Typů služeb jsou deklarované v manifestu úroveň a úroveň typu balíček kódu. Takže pokud je více balíčků kódu, všechny aktivují se vždy, když systém hledá pro každý typ deklarované služby.

Spustitelný soubor určený podle **EntryPoint** je obvykle dlouho běžící hostitele služby. **SetupEntryPoint** je privilegované vstupního bodu, který běží se stejnými pověřeními, jako Service Fabric (obvykle *LocalSystem* účtu) před další vstupní bod.  Přítomnost vstupní bod samostatného instalačního zabraňuje nutnosti spuštění hostitele služby s vysokou úrovní oprávnění pro dlouhou dobu. Spustitelný soubor určený podle **EntryPoint** běží **SetupEntryPoint** ukončí úspěšně. Pokud proces se někdy ukončí nebo dojde k chybě, výsledná proces monitorovat a restartuje (počínaje znovu **SetupEntryPoint**).  

Typické scénáře použití **SetupEntryPoint** jsou při spustit spustitelný soubor před spuštěním služby nebo k provedení operace se zvýšenými oprávněními. Příklad:

* Nastavení a inicializace proměnných prostředí, které potřebuje spustitelný soubor služby. Toto není omezen na napsané pomocí Service Fabric programovací modely pouze spustitelné soubory. Například npm.exe musí některé proměnné prostředí nakonfigurované pro nasazení aplikace node.js.
* Nastavení řízení přístupu nainstalováním certifikáty zabezpečení.

Další informace o tom, jak nakonfigurovat **SetupEntryPoint**, najdete v části [nakonfigurovat zásady pro bod služby instalační položka](service-fabric-application-runas-security.md)

**EnvironmentVariables** (není nastaven v předchozím příkladu) poskytuje seznam proměnných prostředí, které jsou nastavené pro tento balíček kódu. Proměnné prostředí může být přepsána nastaveními v `ApplicationManifest.xml` zajistit různé hodnoty pro instance různé služby. 

**DataPackage** deklaruje (není nastaven v předchozím příkladu) do složky s názvem podle **název** atribut, který obsahuje libovolné statických dat, který se má používat v procesu v době běhu.

**ConfigPackage** deklaruje do složky s názvem podle **název** atribut, který obsahuje *souborech Settings.xml* souboru. Soubor nastavení obsahuje oddíly pár definovaný uživatelem, klíč hodnota nastavení, která čte proces zpět za běhu. Během upgradu, pokud je to pouze **ConfigPackage** **verze** došlo ke změně, pak není běžící proces restartovat. Místo toho zpětné volání upozorní proces, který se změnila nastavení konfigurace, může být dynamicky znovu. Tady je příklad *souborech Settings.xml* souboru:

```xml
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

**Prostředky**, jako je například koncových bodů, používané službou být deklarován či změnit beze změny zkompilovaný kód.  Přístup k prostředkům, které jsou určené v service manifest se dá řídit přes **objektu SecurityGroup** v manifestu aplikace.  Když **koncový bod** prostředek je definován v service manifest, Service Fabric přiřazuje porty z rozsahu portů vyhrazené aplikace, pokud není port určen explicitně.  Další informace o [zadání nebo přepsání koncový bod prostředků](service-fabric-service-manifest-resources.md).


<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application-in-applicationmanifestxml"></a>Popis aplikace v ApplicationManifest.xml
Manifest aplikace deklarativně popisuje typ aplikace a verze. Určuje metadata služby složení například stabilní názvy, vytváření oddílů schématu, faktor počet/replikace instance, zásady zabezpečení nebo izolace, omezení umístění, konfigurace přepsání a typů základní služby. Vyrovnávání zatížení domény, do které se umístí aplikace jsou také popsány.

Manifest aplikace proto popisuje elementy na úrovni aplikace a odkazuje na jeden nebo více manifesty služby k vytváření typ aplikace. Tady je manifest aplikace pro [hlasování ukázkovou aplikaci](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart):

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

Jako služba manifesty **verze** atributy jsou nestrukturovaných řetězce a nejsou analyzovat v systému. Verze atributy jsou také použít k verze jednotlivých součástí pro upgrade.

**Parametry** definuje parametry používaných v celém manifest aplikace. Hodnoty těchto parametrů můžete zadat, když je aplikace instatiated a můžete přepsat nastavení aplikace nebo služby konfigurace.  Pokud není hodnota změněna během vytváření instancí aplikace, použije se výchozí hodnota parametru. Zjistěte, jak udržovat různé aplikace a služby parametry pro jednotlivá prostředí, najdete v tématu [Správa parametry aplikace pro prostředí s více](service-fabric-manage-multiple-environment-app-configuration.md).

**ServiceManifestImport** obsahuje odkazy na manifesty služby, které tvoří tento typ aplikace. Manifest aplikace může obsahovat více služby manifestu importy, každé z nich může být verzí nezávisle. Manifesty importované služba zjistit, jaké typy služby jsou platné v rámci tento typ aplikace. V rámci ServiceManifestImport můžete přepsat hodnoty konfigurace v souborech Settings.xml a prostředí proměnné v ServiceManifest.xml soubory. **Zásady** (není nastavena v předchozím příkladu) pro vazbu koncový bod, zabezpečení a přístup a balíček sdílení lze nastavit u manifesty importované služby.  Další informace najdete v tématu [konfigurovat zásady zabezpečení pro vaši aplikaci](service-fabric-application-runas-security.md).

**DefaultServices** deklaruje instance služby, které se automaticky vytvoří vždy, když aplikace je vytvořena instance pro tento typ aplikace. Výchozí služby jsou pouze pro vaše pohodlí a chovají se jako normální služby, každý po jejich vytvoření. Tyto jsou upgradovány spolu s jinými službami v instanci aplikace a může být odebrán také. Manifest aplikace může obsahovat několik výchozích služeb.

**Certifikáty** (není nastaven v předchozím příkladu) deklaruje certifikátů používaných pro [nastavit koncové body HTTPS](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) nebo [šifrování tajných klíčů v manifestu aplikace](service-fabric-application-secret-management.md).

**Zásady** (není nastaven v předchozím příkladu) Popisuje kolekci protokolu [výchozí spustit jako](service-fabric-application-runas-security.md), [stavu](service-fabric-health-introduction.md#health-policies), a [zabezpečení přístupu](service-fabric-application-runas-security.md) zásady, které chcete nastavit úrovni aplikace.

**Objekty** (není nastavena v předchozím příkladu) popisují objekty zabezpečení (uživatele nebo skupiny), potřeba [spuštění služby a prostředky zabezpečené služby](service-fabric-application-runas-security.md).  Objekty odkazují **zásady** oddíly.





<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>Další postup
- [Balíček aplikace](service-fabric-package-apps.md) a nastavit jej jako připraveny k nasazení.
- [Nasazení a odebírat aplikace](service-fabric-deploy-remove-applications.md).
- [Konfigurace parametrů a proměnných prostředí pro jinou aplikaci instance](service-fabric-manage-multiple-environment-app-configuration.md).
- [Konfigurovat zásady zabezpečení pro vaši aplikaci](service-fabric-application-runas-security.md).
- [Nastavení koncových bodů HTTPS](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service).
- [Šifrování tajných klíčů v manifestu aplikace](service-fabric-application-secret-management.md)

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png



