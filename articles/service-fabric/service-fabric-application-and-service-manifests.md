---
title: Aplikace Azure Service Fabric a služby | Dokumentace Microsoftu
description: Popisuje, jak manifesty se používají k popisu aplikace Service Fabric a služeb.
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
ms.date: 12/19/2018
ms.author: ryanwi
ms.openlocfilehash: 3e390763255878384e7a767158210d0515b09958
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2018
ms.locfileid: "53653540"
---
# <a name="service-fabric-application-and-service-manifests"></a>Manifesty služby a aplikace Service Fabric
Tento článek popisuje, jak aplikace Service Fabric a služby jsou definované a verzovaným pomocí souboru ApplicationManifest.xml a ServiceManifest.xml.  Podrobnější příklady najdete v článku [aplikací a službou manifest příklady](service-fabric-manifest-examples.md).  Schéma XML pro tyto soubory manifestu jsou uvedené v [dokumentace schématu ServiceFabricServiceModel.xsd](service-fabric-service-model-schema.md).

> [!WARNING]
> Soubor schématu manifestu XML vynucuje správné řazení podřízených elementů.  Jako částečné alternativní řešení otevřete v sadě Visual Studio při vytváření nebo úprava všech manifestů Service Fabric "C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd". To vám umožní zkontrolovat řazení podřízené prvky a poskytuje práci.

## <a name="describe-a-service-in-servicemanifestxml"></a>Popis služby ve službě ServiceManifest.xml
Manifest služby definuje deklarativně, typ služby a její verzi. Určuje metadata služby, např. typ služby, Vlastnosti stavu, Vyrovnávání zatížení metriku, binární soubory služby a konfigurační soubory.  Jinými slovy, popisuje balíčky kódu, konfigurace a data, které tvoří službu balíček pro podporu jeden nebo více typů služeb. Manifest služby může obsahovat více kódu, konfigurace a data balíčky, které může být označené verzí nezávisle na sobě. Tady je manifest služby pro ASP.NET Core webová front-end služba nástroje [ukázkovou aplikaci Voting](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (a tady jsou některé [podrobnější příklady](service-fabric-manifest-examples.md)):

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

**Verze** atributy jsou řetězce, nestrukturovaná a nebyly analyzovány pomocí systému. Verze atributy se používají na verzi jednotlivých komponent pro upgrade.

**ServiceType** deklaruje, jaké služby jsou podporovány **CodePackages** v manifestu. Při vytváření instance služby s některou z těchto typů služeb, aktivují se všechny balíčky kódu, které jsou deklarované v manifestu spuštěním jejich vstupní body. Výsledný procesy, které se očekává, že registrace typů podporovaných služeb v době běhu. Typy služeb jsou deklarované v manifestu úroveň a úroveň typu balíček kódu. Takže pokud existuje víc balíčků kódu, všechny aktivují se pokaždé, když se systém, hledá pro každý z typy deklarované služby.

Spustitelný soubor určený **EntryPoint** je obvykle dlouhotrvající hostitele služby. **SetupEntryPoint** je privilegovaných vstupní bod, který běží se stejnými přihlašovacími údaji jako Service Fabric (obvykle *LocalSystem* účtu) před všechny ostatní vstupní bod.  Přítomnost vstupní bod samostatného instalačního díky tomu není nutné ke spuštění hostitele služby s vysokou úrovní oprávnění pro dlouhou dobu. Spustitelný soubor určený **EntryPoint** běží **SetupEntryPoint** skončí úspěšně. Pokud proces neustále skončí nebo dojde k chybě, je výsledný proces monitorovat a restartování (počínaje znovu **SetupEntryPoint**).  

Typické scénáře použití **SetupEntryPoint** jsou při spuštění spustitelného souboru před spuštěním služby nebo k provedení operace se zvýšenými oprávněními. Příklad:

* Nastavení a inicializace proměnných prostředí, které musí spustitelný soubor služby. Toto není omezena pouze spustitelné programy napsané pomocí programovacích modelů Service Fabric. Například npm.exe potřebuje některé proměnné prostředí nakonfigurované pro nasazení aplikace node.js.
* Nastavení řízení přístupu při instalaci certifikátů zabezpečení.

Další informace o tom, jak nakonfigurovat SetupEntryPoint najdete v tématu [nakonfigurovat zásady pro vstupního bodu nastavení služby](service-fabric-application-runas-security.md)

**EnvironmentVariables** (není nastaveno v předchozím příkladu) obsahuje seznam proměnných prostředí, které jsou nastavené pro tento balíček kódu. Proměnné prostředí se dá přepsat v `ApplicationManifest.xml` poskytující různé hodnoty pro instance různé služby. 

**DataPackage** deklaruje (není nastaveno v předchozím příkladu) složky s názvem podle **název** atribut, který obsahuje libovolného statických dat pro proces v době běhu.

**Složce ConfigPackage** deklaruje složku s názvem podle **název** atribut, který obsahuje *Settings.xml* souboru. V souboru nastavení obsahuje oddíly pár definovaný uživatelem, klíč hodnota nastavení, která čte procesu zpět v době běhu. Během upgradu, pokud je to jenom **složce ConfigPackage** **verze** došlo ke změně, pak není spuštěný proces restartovat. Místo toho zpětné volání upozornění procesu tak, že je možné znovu zavést dynamicky se nezměnila konfigurační nastavení. Tady je příklad *Settings.xml* souboru:

```xml
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

**Prostředky**, jako jsou koncové body, používané službou být deklarován nebo upravené beze změny zkompilovaný kód.  Přístup k prostředkům, které jsou určené v manifestu služby se dá řídit přes **skupiny SecurityGroup** v manifestu aplikace.  Když **koncový bod** prostředek je definován v manifestu služby, Service Fabric přiřazuje porty z rozsahu portů aplikace rezervovaných a není explicitně zadán port.  Další informace o [zadání nebo přepisující prostředky. koncový bod](service-fabric-service-manifest-resources.md).


<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application-in-applicationmanifestxml"></a>Popis aplikace v souboru ApplicationManifest.xml
Manifest aplikace deklarativně popisuje typ aplikace a verze. Určuje službu sestavení metadat – například stabilní názvy dělení schéma, faktor počet/replikace instance, zásady zabezpečení a izolace, omezení umístění, přepsání konfigurace a typů základních služeb. Vyrovnávání zatížení domény, do které aplikace umístěna, jsou rovněž popsány.

Proto manifest aplikace obsahuje popis elementů na úrovni aplikace a odkazuje na jeden nebo více manifestů služby k vytvoření aplikace typu. Tady je manifest aplikace pro [ukázkovou aplikaci Voting](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (a tady jsou některé [podrobnější příklady](service-fabric-manifest-examples.md)):

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

Manifesty služby, jako jsou **verze** atributy jsou řetězce, nestrukturovaná a nejsou analyzovat v systému. Verze atributy jsou také používá verzi jednotlivých komponent pro upgrade.

**Parametry** definuje parametry použité v manifestu aplikace. Hodnoty těchto parametrů lze zadat, když je vytvořena instance aplikace a můžete přepsat aplikace nebo nastavení konfigurace služby.  Pokud se hodnota nezmění během vytvoření instance aplikace, použije se výchozí hodnota parametru. Zjistěte, jak udržovat různé aplikace a služby parametry pro jednotlivá prostředí, najdete v článku [Správa parametrů aplikací pro víc prostředí](service-fabric-manage-multiple-environment-app-configuration.md).

**ServiceManifestImport** obsahuje odkazy na manifestů služby, které tvoří tento typ aplikace. Manifest aplikace může obsahovat několik importů manifestu služby, každý z nich může být označené verzí nezávisle na sobě. Manifesty služby importované určit, jaké služby jsou platné v rámci tohoto typu aplikace. V rámci ServiceManifestImport přepíšete konfigurační hodnoty v proměnné Settings.xml a prostředí v souborech ServiceManifest.xml. **Zásady** (není nastavený v předchozím příkladu) pro vazbu koncový bod, zabezpečení a přístupu a balíček sdílení lze nastavit pro manifesty importované služby.  Další informace najdete v tématu [konfigurovat zásady zabezpečení pro vaši aplikaci](service-fabric-application-runas-security.md).

**DefaultServices** deklaruje instancí služby, které se automaticky vytvoří pokaždé, když se aplikace je vytvořena instance pro tento typ aplikace. Výchozí služby jsou pouze v zájmu usnadnění a chovají se jako normální služeb ve všech ohledech po jejich vytvoření. Tyto jsou upgradovány společně s dalšími službami v instanci aplikace a můžete také odebrat. Manifest aplikace může obsahovat několik výchozích služeb.

**Certifikáty** (není nastaveno v předchozím příkladu) deklaruje certifikátů používaných pro [nastavení koncových bodů HTTPS](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) nebo [šifrování tajných kódů v manifestu aplikace](service-fabric-application-secret-management.md).

**Zásady** (není nastaveno v předchozím příkladu) popisuje shromažďování protokolů [výchozí spustit jako](service-fabric-application-runas-security.md), [stavu](service-fabric-health-introduction.md#health-policies), a [zabezpečení přístupu](service-fabric-application-runas-security.md) zásady, které chcete nastavit úrovni aplikace.

**Objekty zabezpečení** (není nastavený v předchozím příkladu) popisují objekty zabezpečení (uživatele nebo skupiny), potřeba [spuštění služby a prostředky služeb zabezpečené](service-fabric-application-runas-security.md).  Objekty odkazují **zásady** oddíly.





<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>Další postup
- [Balení aplikace](service-fabric-package-apps.md) a nastavte ji připraven k nasazení.
- [Nasazení a odebírat aplikace](service-fabric-deploy-remove-applications.md).
- [Konfigurace parametrů a proměnných prostředí pro různé instance aplikace](service-fabric-manage-multiple-environment-app-configuration.md).
- [Konfigurovat zásady zabezpečení pro vaši aplikaci](service-fabric-application-runas-security.md).
- [Nastavení koncových bodů HTTPS](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service).
- [Šifrování tajných kódů v manifestu aplikace](service-fabric-application-secret-management.md)

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png



