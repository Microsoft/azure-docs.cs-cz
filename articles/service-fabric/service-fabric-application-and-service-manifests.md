---
title: Popis aplikací a služeb Azure Service Fabric
description: Popisuje, jak manifesty se používají k popisu service fabric aplikací a služeb.
ms.topic: conceptual
ms.date: 8/12/2019
ms.openlocfilehash: 6014ef6a9b6ec810aafd5e5be96223b8ed92d576
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75349960"
---
# <a name="service-fabric-application-and-service-manifests"></a>Manifesty aplikací a služeb Service Fabric
Tento článek popisuje, jak jsou definovány a verzí aplikací a služeb Service Fabric pomocí souborů ApplicationManifest.xml a ServiceManifest.xml.  Podrobnější příklady naleznete v příkladech [manifestu aplikace a služby](service-fabric-manifest-examples.md).  Schéma XML pro tyto soubory manifestu je popsáno v [dokumentaci schématu ServiceFabricServiceModel.xsd](service-fabric-service-model-schema.md).

> [!WARNING]
> Schéma souboru XML manifestu vynucuje správné řazení podřízených prvků.  Jako částečné řešení otevřete v sadě Visual Studio "C:\Program Files\Microsoft SDKS\Service Fabric\Schemas\ServiceFabricServiceModel.xsd" při vytváření nebo úpravě některého z manifestů service fabric. To vám umožní zkontrolovat pořadí podřízených prvků a poskytuje intelli-sense.

## <a name="describe-a-service-in-servicemanifestxml"></a>Popište službu v souboru ServiceManifest.xml
Manifest služby deklarativně definuje typ a verzi služby. Určuje metadata služby, jako je typ služby, vlastnosti stavu, metriky vyrovnávání zatížení, binární soubory služby a konfigurační soubory.  Jinak řečeno popisuje kód, konfiguraci a datové balíčky, které tvoří balíček služby pro podporu jednoho nebo více typů služeb. Manifest služby může obsahovat více balíčků kódu, konfigurace a dat, které mohou být verzí nezávisle. Zde je manifest služby pro ASP.NET core web front-end služby [hlasování ukázkové aplikace](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (a zde jsou některé [podrobnější příklady](service-fabric-manifest-examples.md)):

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

**Atributy verze** jsou nestrukturované řetězce a nejsou analyzovány systémem. Atributy verze se používají k verzi každé součásti pro inovace.

**ServiceTypes** deklaruje, jaké typy služeb jsou **podporovány CodePackages** v tomto manifestu. Při vytvoření instance služby proti jednomu z těchto typů služeb jsou všechny balíčky kódu deklarované v tomto manifestu aktivovány spuštěním vstupních bodů. Očekává se, že výsledné procesy budou registrovat podporované typy služeb za běhu. Typy služeb jsou deklarovány na úrovni manifestu a nikoli na úrovni balíčku kódu. Takže když existuje více balíčků kódu, jsou všechny aktivovány vždy, když systém hledá některý z deklarovaných typů služeb.

Spustitelný soubor určený **entrypointem** je obvykle dlouhotrvající hostitel služby. **SetupEntryPoint** je privilegovaný vstupní bod, který běží se stejnými pověřeními jako Service Fabric (obvykle účet *LocalSystem)* před jakýmkoli jiným vstupním bodem.  Přítomnost samostatného vstupního bodu nastavení zabraňuje nutnosti spouštět hostitele služby s vysokými oprávněními po delší dobu. Spustitelný soubor určený službou **EntryPoint** je spuštěn po úspěšném ukončení **programu SetupEntryPoint.** Pokud se proces někdy ukončí nebo dojde k chybě, výsledný proces je sledován a restartován (počínaje znovu **setupentrypoint**).  

Typické scénáře pro použití **SetupEntryPoint** jsou při spuštění spustitelného souboru před spuštěním služby nebo provést operaci se zvýšenými oprávněními. Například:

* Nastavení a inicializace proměnných prostředí, které potřebuje spustitelný soubor služby. To není omezeno pouze na spustitelné soubory napsané prostřednictvím programovacích modelů Service Fabric. Například npm.exe potřebuje některé proměnné prostředí nakonfigurované pro nasazení aplikace node.js.
* Nastavení řízení přístupu instalací certifikátů zabezpečení.

Další informace o konfiguraci instalačního bodu SetupEntryPoint naleznete [v tématu Konfigurace zásad pro vstupní bod nastavení služby](service-fabric-application-runas-security.md)

**EnvironmentVariables** (není nastavena v předchozím příkladu) obsahuje seznam proměnných prostředí, které jsou nastaveny pro tento balíček kódu. Proměnné prostředí mohou být přepsány `ApplicationManifest.xml` v poskytnout různé hodnoty pro různé instance služby. 

**DataPackage** (není nastavena v předchozím příkladu) deklaruje složku, pojmenovanou atributem **Name,** která obsahuje libovolná statická data, která mají být spotřebována procesem za běhu.

**ConfigPackage** deklaruje složku pojmenovanou atributem **Name,** která obsahuje soubor *Settings.xml.* Soubor nastavení obsahuje části uživatelem definované nastavení páru klíč-hodnota, které proces přečte zpět za běhu. Pokud se během inovace změnila pouze **verze** **ConfigPackage,** nebude spuštěný proces restartován. Místo toho zpětné volání upozorní proces, že nastavení konfigurace se změnily tak, aby mohly být znovu načteny dynamicky. Zde je příklad souboru *Settings.xml:*

```xml
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

A Service Fabric Service **Endpoint** is an example of a Service Fabric Resource. Prostředek service fabric prostředek lze deklarovat nebo změnit beze změny zkompilovaného kódu. Přístup k prostředkům Service Fabric, které jsou zadány v manifestu služby lze řídit prostřednictvím **SecurityGroup** v manifestu aplikace. Pokud je prostředek koncového bodu definován v manifestu služby, Service Fabric přiřadí porty z rozsahu vyhrazeného portu aplikace, když port není zadán explicitně. Přečtěte si další informace o [určení nebo přepsání prostředků koncového bodu](service-fabric-service-manifest-resources.md).

 
> [!WARNING]
> Statické porty by se podle návrhu neměly překrývat s rozsahem portů aplikace zadaným v manifestu clusteru. Pokud zadáte statický port, přiřaďte jej mimo rozsah portů aplikace, jinak to bude mít za následek konflikty portů. S verzí 6.5CU2 vydáme **zdravotní varování,** když zjistíme takový konflikt, ale necháme nasazení pokračovat v synchronizaci s dodaným chováním 6.5. Můžeme však zabránit nasazení aplikace z dalších hlavních verzí.
>

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application-in-applicationmanifestxml"></a>Popište aplikaci v applicationManifest.xml
Manifest aplikace deklarativně popisuje typ a verzi aplikace. Určuje metadata složení služby, jako jsou stabilní názvy, schéma dělení, faktor počítání/replikace, zásady zabezpečení/izolace, omezení umístění, přepsání konfigurace a typy základních služeb. Jsou také popsány domény vyrovnávání zatížení, do kterých je aplikace umístěna.

Manifest aplikace tedy popisuje prvky na úrovni aplikace a odkazuje na jeden nebo více manifestů služby k sestavení typu aplikace. Zde je manifest žádosti o [hlasování vzorku aplikace](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (a zde jsou některé podrobnější [příklady](service-fabric-manifest-examples.md)):

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

Stejně jako manifesty **služby, Version** atributy jsou nestrukturované řetězce a nejsou analyzovány systémem. Atributy verze se také používají k verzi každé součásti pro inovace.

**Parametry** definují parametry použité v celém manifestu aplikace. Hodnoty těchto parametrů mohou být zadány, když je aplikace vytvořena instance a může přepsat nastavení konfigurace aplikace nebo služby.  Výchozí hodnota parametru se používá, pokud se hodnota nezmění během instance aplikace. Informace o tom, jak udržovat různé parametry aplikací a služeb pro jednotlivá prostředí, naleznete [v tématu Správa parametrů aplikace pro více prostředí](service-fabric-manage-multiple-environment-app-configuration.md).

**ServiceManifestImport** obsahuje odkazy na manifesty služby, které tvoří tento typ aplikace. Manifest aplikace může obsahovat více importů manifestu služby, každý z nich může být verzí nezávisle. Importované manifesty služby určují, jaké typy služeb jsou platné v rámci tohoto typu aplikace. V rámci služby ServiceManifestImport přepíšete hodnoty konfigurace v souborech Settings.xml a proměnné prostředí v souborech ServiceManifest.xml. **Zásady** (není nastavena v předchozím příkladu) pro vazby koncového bodu, zabezpečení a přístup a sdílení balíčků lze nastavit na importované služby manifesty.  Další informace naleznete v [tématu Konfigurace zásad zabezpečení pro vaši aplikaci](service-fabric-application-runas-security.md).

**DefaultServices** deklaruje instance služby, které jsou automaticky vytvořeny vždy, když je vytvořena instance aplikace proti tomuto typu aplikace. Výchozí služby jsou jen pohodlí a chovat se jako normální služby v každém ohledu poté, co byly vytvořeny. Jsou upgradovány spolu s dalšími službami v instanci aplikace a mohou být také odebrány. Manifest aplikace může obsahovat více výchozích služeb.

**Certifikáty** (nejsou nastaveny v předchozím příkladu) deklaruje certifikáty používané k [nastavení koncových bodů HTTPS](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) nebo šifrování [tajných kódů v manifestu aplikace](service-fabric-application-secret-management.md).

**Omezení umístění** jsou příkazy, které definují, kde by měly být spuštěny služby. Tyto příkazy jsou připojeny k jednotlivým službám, které vyberete pro jeden nebo více vlastností uzlu. Další informace naleznete v tématu [Hledání omezení a syntaxe vlastnosti uzlu.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#placement-constraints-and-node-property-syntax)

**Zásady** (není nastavena v předchozím příkladu) popisuje kolekci protokolů, [výchozí run-as](service-fabric-application-runas-security.md), [stav](service-fabric-health-introduction.md#health-policies)a zásady [přístupu k zabezpečení](service-fabric-application-runas-security.md) nastavit na úrovni aplikace, včetně toho, zda služby mají přístup k service fabric runtime.

> [!NOTE] 
> Ve výchozím nastavení mají aplikace Service Fabric přístup k runtime Service Fabric ve formě koncového bodu, který přijímá požadavky specifické pro aplikaci, a proměnných prostředí odkazujících na cesty souborů na hostiteli obsahující fabric a soubory specifické pro aplikaci . Zvažte zakázání tohoto přístupu, pokud aplikace hostuje nedůvěryhodný kód (tj. kód, jehož původ je neznámý nebo který vlastník aplikace ví, že není bezpečné spustit). Další informace naleznete [v doporučených postupech zabezpečení v service fabric .](service-fabric-best-practices-security.md#platform-isolation) 
>

**Objekty zabezpečení** (nejsou nastaveny v předchozím příkladu) popisují objekty zabezpečení (uživatelé nebo skupiny) potřebné ke [spuštění služeb a zabezpečení prostředků služby](service-fabric-application-runas-security.md).  Objekty jsou odkazovány v části **Zásady.**





<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>Další kroky
- [Zabalte aplikaci](service-fabric-package-apps.md) a připravte ji k nasazení.
- [Nasazujte a odeberte aplikace](service-fabric-deploy-remove-applications.md).
- [Konfigurace parametrů a proměnných prostředí pro různé instance aplikace](service-fabric-manage-multiple-environment-app-configuration.md).
- [Konfigurace zásad zabezpečení pro vaši aplikaci](service-fabric-application-runas-security.md).
- [Instalační koncové body HTTPS](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service).
- [Šifrování tajných kódů v manifestu aplikace](service-fabric-application-secret-management.md)

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png



