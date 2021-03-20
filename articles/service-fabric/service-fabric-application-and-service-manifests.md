---
title: Popisující aplikace a služby Azure Service Fabric
description: Popisuje, jak se používají manifesty k popisu Service Fabric aplikací a služeb.
ms.topic: conceptual
ms.date: 8/12/2019
ms.openlocfilehash: fcf4c7611f0a6f52c28b234717b9244ac58ad2d4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86248216"
---
# <a name="service-fabric-application-and-service-manifests"></a>Service Fabric manifestů aplikací a služeb
Tento článek popisuje, jak se Service Fabric aplikace a služby definují a používají ApplicationManifest.xml a ServiceManifest.xml soubory.  Podrobnější příklady najdete v tématu [Příklady manifestu aplikace a služby](service-fabric-manifest-examples.md).  Schéma XML pro tyto soubory manifestu je dokumentováno v [dokumentaci ke schématu ServiceFabricServiceModel. xsd](service-fabric-service-model-schema.md).

> [!WARNING]
> Schéma souboru XML manifestu vynutilo správné pořadí podřízených elementů.  V případě částečného alternativního řešení otevřete při vytváření nebo úpravách Service Fabric manifestů "C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd" v aplikaci Visual Studio. To vám umožní kontrolovat pořadí podřízených elementů a poskytuje práci.

## <a name="describe-a-service-in-servicemanifestxml"></a>Popište službu v ServiceManifest.xml
Manifest služby deklarativně definuje typ a verzi služby. Určuje metadata služby, jako je typ služby, vlastnosti stavu, metriky vyrovnávání zatížení, binární soubory služby a konfigurační soubory.  Jiným způsobem, popisuje kód, konfiguraci a balíčky dat, které tvoří balíček služby pro podporu jednoho nebo více typů služeb. Manifest služby může obsahovat více balíčků kódu, konfigurací a dat, které mohou být nezávislé na verzi. Tady je manifest služby pro ASP.NET Core webovou front-end službu pro [hlasovací ukázkovou aplikaci](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (a tady jsou některé [podrobnější příklady](service-fabric-manifest-examples.md)):

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
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

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
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

Atributy **verze** jsou nestrukturované řetězce a nejsou analyzovány systémem. Atributy verze se používají ke každé součásti pro upgradování.

**ServiceTypes** deklaruje, jaké typy služeb jsou podporovány **CodePackages** v tomto manifestu. Při vytváření instance služby proti jednomu z těchto typů služeb jsou všechny balíčky kódu deklarované v tomto manifestu aktivovány spuštěním jejich vstupních bodů. U výsledných procesů se očekává, že se v době běhu zaregistrují podporované typy služeb. Typy služeb jsou deklarovány na úrovni manifestu a nikoli na úrovni balíčku kódu. Takže pokud je k dispozici více balíčků kódu, jsou všechny aktivovány vždy, když systém vyhledá některý z deklarovaných typů služeb.

Spustitelný soubor určený **parametrem EntryPoint** je obvykle dlouhodobě běžící hostitel služby. **SetupEntryPoint** je privilegovaný vstupní bod, který se spouští se stejnými přihlašovacími údaji jako Service Fabric (obvykle účet *LocalSystem* ) před jakýmkoli jiným vstupním bodem.  Přítomnost samostatného vstupního bodu instalace zabrání nutnosti spustit hostitele služby s vysokými oprávněními pro delší časová období. Spustitelný soubor určený **parametrem EntryPoint** je spuštěn po úspěšném ukončení **SetupEntryPoint** . Pokud se proces někdy ukončí nebo dojde k chybě, výsledný proces se monitoruje a restartuje (začíná znovu s **SetupEntryPoint**).  

Typické scénáře použití **SetupEntryPoint** jsou při spuštění spustitelného souboru před spuštěním služby nebo provedením operace se zvýšenými oprávněními. Například:

* Nastavení a inicializace proměnných prostředí, které vyžaduje spustitelný soubor služby. To není omezeno pouze na spustitelné soubory napsané prostřednictvím Service Fabric programovacích modelů. npm.exe například potřebuje některé proměnné prostředí nakonfigurované pro nasazení node.js aplikace.
* Nastavení řízení přístupu instalací certifikátů zabezpečení.

Další informace o tom, jak nakonfigurovat SetupEntryPoint, najdete v tématu [Konfigurace zásad pro vstupní bod nastavení služby](service-fabric-application-runas-security.md) .

**EnvironmentVariables** (není nastavené v předchozím příkladu) poskytuje seznam proměnných prostředí, které jsou nastavené pro tento balíček kódu. Proměnné prostředí lze přepsat v `ApplicationManifest.xml` a poskytnout tak různé hodnoty pro různé instance služby. 

Datový **balíček** (není nastaven v předchozím příkladu) deklaruje složku s názvem atribut **Name** , která obsahuje libovolná statická data, která má proces za běhu spotřebovat.

**ConfigPackage** deklaruje složku, která je pojmenována atributem **Name** , který obsahuje soubor *Settings.xml* . Soubor nastavení obsahuje oddíly nastavení dvojice klíč-hodnota, které proces načítá za běhu zpět. Pokud se během upgradu změnila jenom  **verze** ConfigPackage, spuštěný proces se nerestartuje. Místo toho zpětné volání upozorní proces, že došlo ke změně nastavení konfigurace, aby bylo možné je znovu načíst dynamicky. Tady je příklad *Settings.xml* souboru:

```xml
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

Service Fabric **koncový bod** služby je příkladem Service Fabric prostředků. Prostředek Service Fabric lze deklarovat nebo změnit bez změny zkompilovaného kódu. Přístup k Service Fabric prostředkům, které jsou zadány v manifestu služby, lze ovládat prostřednictvím služby **zabezpečení** v manifestu aplikace. Pokud je prostředek koncového bodu definovaný v manifestu služby, Service Fabric přiřadí porty z rezervovaného rozsahu portů aplikace, když není explicitně zadaný port. Přečtěte si další informace o [zadávání nebo přepisu prostředků koncových bodů](service-fabric-service-manifest-resources.md).

 
> [!WARNING]
> Návrhem statických portů se nesmí překrývat s rozsahem portů aplikace zadaným v manifestem clusteru. Pokud zadáte statický port, přiřaďte ho mimo rozsah portů aplikace, jinak bude výsledkem konflikty portů. S vydáním verze 6.5 CU2 budeme při zjišťování takového konfliktu vystavovat **Upozornění na stav** , ale nasazení bude pokračovat v synchronizaci s dodaným chováním 6,5. Můžeme ale zabránit nasazení aplikace z dalších hlavních verzí.
>

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application-in-applicationmanifestxml"></a>Popis aplikace v ApplicationManifest.xml
Manifest aplikace deklarativně popisuje typ a verzi aplikace. Určuje metadata složení služby, jako jsou například stabilní názvy, schéma dělení, počet instancí/faktor replikace, zásady zabezpečení/izolace, omezení umístění, přepsání konfigurace a typy služeb prvků. Jsou popsány také domény vyrovnávání zatížení, do kterých je aplikace umístěna.

Proto manifest aplikace popisuje prvky na úrovni aplikace a odkazuje na jeden nebo více manifestů služby za účelem vytvoření typu aplikace. Tady je manifest aplikace pro [hlasovací ukázkovou aplikaci](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (a tady jsou některé [podrobnější příklady](service-fabric-manifest-examples.md)):

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
         <PlacementConstraints>(NodeType==NodeType0)</PlacementConstraints
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

Podobně jako manifesty služeb jsou atributy **verze** nestrukturované řetězce a nejsou analyzovány systémem. Atributy verze se používají také k navýšení jednotlivých komponent pro upgrady.

**Parametry** definují parametry používané v rámci manifestu aplikace. Hodnoty těchto parametrů lze zadat, když je vytvořena instance aplikace a může přepsat nastavení konfigurace aplikace nebo služby.  Výchozí hodnota parametru se použije, pokud se hodnota během vytváření instance aplikace nemění. Informace o údržbě různých parametrů aplikací a služeb pro jednotlivá prostředí najdete v tématu [Správa parametrů aplikace pro více prostředí](service-fabric-manage-multiple-environment-app-configuration.md).

**ServiceManifestImport** obsahuje odkazy na manifesty služby, které tvoří tento typ aplikace. Manifest aplikace může obsahovat více importů manifestu služby, každý z nich může být nezávisle. Importované manifesty služby určují, jaké typy služeb jsou platné v rámci tohoto typu aplikace. V rámci ServiceManifestImport přepíšete hodnoty konfigurace v Settings.xml a proměnných prostředí v souborech ServiceManifest.xml. **Zásady** (nejsou nastavené v předchozím příkladu) pro vytváření koncových bodů, zabezpečení a přístup a sdílení balíčků lze nastavit u importovaných manifestů služby.  Další informace najdete v tématu [Konfigurace zásad zabezpečení pro vaši aplikaci](service-fabric-application-runas-security.md).

**DefaultServices** deklaruje instance služby, které jsou automaticky vytvořeny pokaždé, když je vytvořena instance aplikace proti tomuto typu aplikace. Výchozí služby jsou v každém ohledu na pohodlí a chovají se jako běžné služby, a to po jejich vytvoření. Jsou upgradovány spolu s jinými službami v instanci aplikace a lze je také odebrat. Manifest aplikace může obsahovat několik výchozích služeb.

**Certifikáty** (nenastavené v předchozím příkladu) deklaruje certifikáty používané k [nastavení koncových bodů https](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) nebo k [šifrování tajných klíčů v manifestu aplikace](service-fabric-application-secret-management.md).

**Omezení umístění** jsou příkazy, které definují, kde by měly být spouštěny služby. Tyto příkazy jsou připojeny k jednotlivým službám, které vyberete pro jednu nebo více vlastností uzlu. Další informace najdete v tématu [omezení umístění a syntaxe vlastností uzlů](./service-fabric-cluster-resource-manager-cluster-description.md#placement-constraints-and-node-property-syntax) .

**Zásady** (nenastavené v předchozím příkladu) popisují shromažďování protokolů, [výchozí zásady spuštění jako](service-fabric-application-runas-security.md), [stav](service-fabric-health-introduction.md#health-policies)a [přístup](service-fabric-application-runas-security.md) k nastavení na úrovni aplikace, včetně toho, jestli služba (y) má přístup k modulu runtime Service Fabric.

> [!NOTE] 
> Ve výchozím nastavení mají aplikace Service Fabric přístup k modulu runtime Service Fabric, ve formě koncového bodu, který přijímá požadavky specifické pro aplikaci, a proměnných prostředí odkazujících na cesty k souborům na hostiteli, který obsahuje prostředky infrastruktury a soubory specifické pro aplikaci. Zvažte zakázání tohoto přístupu, pokud je aplikace hostitelem nedůvěryhodného kódu (tj. kód, jehož provenience je neznámá nebo který vlastník aplikace ví, že nebudete moci provádět zabezpečení). Další informace najdete [v tématu osvědčené postupy zabezpečení v Service Fabric](service-fabric-best-practices-security.md#platform-isolation). 
>

**Objekty** zabezpečení (nejsou nastavené v předchozím příkladu) popisují objekty zabezpečení (uživatele nebo skupiny), které jsou potřebné ke [spouštění služeb a zabezpečení prostředků služby](service-fabric-application-runas-security.md).  Na objekty zabezpečení se odkazuje v oddílech **zásad** .





<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>Další kroky
- [Zabalit aplikaci](service-fabric-package-apps.md) a připravit ji na nasazení.
- [Nasaďte a odeberte aplikace](service-fabric-deploy-remove-applications.md).
- [Nakonfigurujte parametry a proměnné prostředí pro různé instance aplikací](service-fabric-manage-multiple-environment-app-configuration.md).
- [Nakonfigurujte zásady zabezpečení pro vaši aplikaci](service-fabric-application-runas-security.md).
- [Nastavení koncových bodů https](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service)
- [Šifrování tajných klíčů v manifestu aplikace](service-fabric-application-secret-management.md)

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png
