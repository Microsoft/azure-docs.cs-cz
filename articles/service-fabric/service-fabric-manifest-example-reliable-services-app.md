---
title: Příklady manifestu spolehlivých služeb
description: Zjistěte, jak nakonfigurovat nastavení manifestu aplikací a služeb pro spolehlivou aplikaci Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: f40e54f5260f827f0b18c833d23d1f57b5ebc3a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282351"
---
# <a name="reliable-services-application-and-service-manifest-examples"></a>Příklady manifestu služeb a aplikace Reliable Services
Následují příklady manifestů aplikace a služby pro aplikaci Service Fabric s ASP.NET základního webového front-endu a stavového back-endu. Účelem těchto příkladů je ukázat, jaká nastavení jsou k dispozici a jak je používat. Tyto manifesty aplikací a služeb jsou založeny na manifestech [Service Fabric .NET QuickStart.](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/)

Jsou zobrazeny následující funkce:

|Manifest|Funkce|
|---|---|
|[Manifest aplikace](#application-manifest)| [zásady správného řízení prostředků](service-fabric-resource-governance.md), [spuštění služby jako místní účet správce](service-fabric-application-runas-security.md), použití výchozí ch [odmítavých zásad pro všechny balíčky kódu služby](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages), [vytvoření objektů zabezpečení uživatelů a skupin](service-fabric-application-runas-security.md), sdílení datového balíčku mezi instancemi služby, [přepsání koncových bodů služby](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)| 
|Manifest služby FrontEndService| [Spuštění skriptu při spuštění služby](service-fabric-run-script-at-service-startup.md), [definování koncového bodu HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest) | 
|Manifest služby BackEndService| [Deklarovat balíček konfigurace](service-fabric-application-and-service-manifests.md), [deklarovat datový balíček](service-fabric-application-and-service-manifests.md), [konfigurovat koncový bod](service-fabric-service-manifest-resources.md)| 

Další informace o [konkrétních elementech](#application-manifest-elements)XML naleznete v tématu Application manifest elements , [VotingWeb service manifest elements](#votingweb-service-manifest-elements)a [VotingData service manifest.](#votingdata-service-manifest-elements)

## <a name="application-manifest"></a>Manifest aplikace

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="Memory" DefaultValue="4084" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="Port" DefaultValue="8081" />
    <Parameter Name="Protocol" DefaultValue="tcp" />
    <Parameter Name="Type" DefaultValue="internal" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <!-- Override endpoints declared in the service manifest. -->
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="DataEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
      </Endpoints>
    </ResourceOverrides>

    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[Memory]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[Memory]" BlockIOWeight="[BlockIOWeight]" 
                                MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" 
                                MemorySwapInMB="[MemorySwapInMB]"/>

      <!-- Share the data package across multiple instances of the VotingData service-->
      <PackageSharingPolicy PackageRef="Data"/>

      <!-- Give read rights on the "DataEndpoint" endpoint to the Customer2 account.-->
      <SecurityAccessPolicy GrantRights="Read" PrincipalRef="Customer2" ResourceRef="DataEndpoint" ResourceType="Endpoint"/>         
    </Policies>
  </ServiceManifestImport>
  
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Run the setup entry point (defined in the imported service manifest) as the SetupAdminUser account 
      (declared in the following Principals section). -->
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      
    </Policies>

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
  <!-- Define users and groups required to run the services and access resources.  Principals are used in the Policies section(s). -->
  <Principals>
    <!-- Declare a set of groups as security principals, which can be referenced in policies. Groups are useful if there are multiple users 
    for different service entry points and they need to have certain common privileges that are available at the group level. -->
    <Groups>
      <!-- Create a group that has administrator privileges. -->
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <!-- Declare a user and add the user to the Administrators system group. The SetupAdminUser account is used to run the 
      setup entry point of the VotingWebPkg code package (described in the preceding Policies section).-->
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
      <!-- Create a user. Local user accounts are created on the machines where the application is deployed. By default, these accounts 
      do not have the same names as those specified here. Instead, they are dynamically generated and have random passwords. -->
      <User Name="Customer1" >
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as a local user with the specified account name and password. Local user accounts are created on the machines 
      where the application is deployed. -->
      <User Name="Customer2" AccountType="LocalUser" AccountName="Customer1" Password="MyPassword">
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as NetworkService. -->
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <!-- Policies applied at the application level. -->
  <Policies>
    <!-- Specify a default user account for all code packages that don’t have a specific RunAsPolicy defined in 
    the ServiceManifestImport section(s). -->
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
    
  </Policies>
</ApplicationManifest>

```

## <a name="votingweb-service-manifest"></a>Manifest služby VotingWeb

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
    <!-- A privileged entry point that by default runs with the same credentials as Service Fabric (typically the NetworkService account) before 
    any other entry point. Use the setup entry point to set system environment variables, give the account running the service (NETWORK SERVICE, by default) 
    access to a certificate's private key, or perform other setup tasks. In the application manifest, you can change the security permissions to run the startup script 
    under a local system account or an administrator account.  -->
    <SetupEntryPoint>
      <ExeHost>
        <!-- The setup script to run. -->
        <Program>Setup.bat</Program>
        <!-- Pass arguments to the script when it runs.-->
        <Arguments>MyValue</Arguments>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
        <!-- Warning! Do not use console redirection in a production application, only use it for local development and debugging. Redirects console output from the startup
        script to an output file in the application folder called "log" on the cluster node where the application is deployed and run. Also set the number of output files
        to retain and the maximum file size (in KB). -->
        <ConsoleRedirection FileRetentionCount="10" FileMaxSizeInKb="20480"/>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- Configure a HTTPS endpoint on port 443. This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>

```

## <a name="votingdata-service-manifest"></a>Manifest služby VotingData

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingDataPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="VotingDataType"  HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingData.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Declares a folder, named by the Name attribute, under PackageRoot that contains a Settings.xml file. This file contains sections of user-defined, 
  key-value pair settings that the process can read back at run time. During an upgrade, if only the ConfigPackage version has changed, 
  then the running process is not restarted. Instead, a callback notifies the process that configuration settings have changed so they can be reloaded dynamically. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Declares a folder, named by the Name attribute, under PackageRoot which contains static data files to be consumed by the process at run time. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- Define an internal (used for intra-application communication) TCP endpoint. Since no port is specified, one is created and assigned dynamically
           to the service.-->
      <Endpoint Name="DataEndpoint" Protocol="tcp" Type="Internal" />
    </Endpoints>
  </Resources>

</ServiceManifest>

```

## <a name="application-manifest-elements"></a>Prvky manifestu aplikace
### <a name="applicationmanifest-element"></a>ApplicationManifest Element
Deklarativně popisuje typ aplikace a verzi. Jeden nebo více manifestů služby základních služeb se odkazuje na sestavení typu aplikace. Nastavení konfigurace základních služeb lze přepsat pomocí parametrizovaného nastavení aplikace. Výchozí služby, šablony služeb, objekty zabezpečení, zásady, nastavení diagnostiky a certifikáty mohou být deklarovány také na úrovni aplikace. Další informace naleznete v tématu [ApplicationManifest Element](service-fabric-service-model-schema-elements.md#ApplicationManifestElementApplicationManifestTypeComplexType)

### <a name="parameters-element"></a>Parametry Element
Deklaruje parametry, které se používají v tomto manifestu aplikace. Hodnotu těchto parametrů lze zadat při vytvoření instance aplikace a lze ji použít k přepsání nastavení konfigurace aplikace nebo služby. Další informace naleznete v tématu [Element Parameters](service-fabric-service-model-schema-elements.md#ParametersElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="parameter-element"></a>Parameter – element
Parametr aplikace, který má být použit v tomto manifestu. Hodnotu parametru lze změnit během instance aplikace nebo, pokud není zadána žádná hodnota, je použita výchozí hodnota. Další informace naleznete [v tématu Element parametru](service-fabric-service-model-schema-elements.md#ParameterElementanonymouscomplexTypeComplexTypeDefinedInParameterselement)

### <a name="servicemanifestimport-element"></a>ServiceManifestImport Element
Importuje manifest služby vytvořený vývojářem služby. Manifest služby musí být importován pro každou základní službu v aplikaci. Přepsání konfigurace a zásady lze deklarovat pro manifest služby. Další informace naleznete v tématu [ServiceManifestImport Element](service-fabric-service-model-schema-elements.md#ServiceManifestImportElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="servicemanifestref-element"></a>ServiceManifestRef Element
Importuje manifest služby odkazem. V současné době musí být soubor manifestu služby (ServiceManifest.xml) přítomen v balíčku sestavení. Další informace naleznete v tématu [ServiceManifestRef Element](service-fabric-service-model-schema-elements.md#ServiceManifestRefElementServiceManifestRefTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="resourceoverrides-element"></a>ResourceOverrides Element
Určuje přepsání prostředků pro koncové body deklarované ve zdrojích manifestu služby. Další informace naleznete v tématu [ResourceOverrides Element](service-fabric-service-model-schema-elements.md#ResourceOverridesElementResourceOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="endpoints-element"></a>Element koncových bodů
Koncové ho přepsání. Další informace naleznete [v tématu Prvek koncových bodů](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourceOverridesTypecomplexType)

### <a name="endpoint-element"></a>Element koncového bodu
Koncový bod deklarovaný v manifestu služby přepsat. Další informace naleznete [v tématu Endpoint Element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

### <a name="policies-element"></a>Prvek zásad
Popisuje zásady (vazba koncového bodu, sdílení balíčků, run-as a přístup k zabezpečení), které mají být použity v manifestu importované služby. Další informace naleznete v tématu [Zásady Element](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy Element
Definuje zásady zásady zásady správného řízení prostředků, které se používají na úrovni celého balíčku služeb. Další informace naleznete v tématu [ServicePackageResourceGovernancePolicy Element](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>ResourceGovernancePolicy Element
Určuje omezení prostředků pro balíček kódu. Další informace naleznete v tématu [ResourceGovernancePolicy Element](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="packagesharingpolicy-element"></a>PackageSharingPolicy Element
Označuje, zda by měl být kód, konfigurace nebo datový balíček sdílen mezi instancemi služby stejného typu služby. Další informace naleznete v tématu [PackageSharingPolicy Element](service-fabric-service-model-schema-elements.md#PackageSharingPolicyElementPackageSharingPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexType)

### <a name="securityaccesspolicy-element"></a>SecurityAccessPolicy Element
Uděluje přístupová oprávnění k objektu zabezpečení prostředku (například koncového bodu) definovaného v manifestu služby. Obvykle je velmi užitečné řídit a omezit přístup služeb k různým prostředkům, aby se minimalizovala bezpečnostní rizika. To je obzvláště důležité, když je aplikace vytvořena ze kolekce služeb z trhu, které jsou vyvíjeny různými vývojáři. Další informace naleznete v tématu [SecurityAccessPolicy Element](service-fabric-service-model-schema-elements.md#SecurityAccessPolicyElementSecurityAccessPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInSecurityAccessPolicieselementDefinedInDigestedEndpointelement)

### <a name="runaspolicy-element"></a>Prvek RunAsPolicy
Určuje místní uživatelský nebo místní systémový účet, za kterého bude balíček kódu služby spuštěn. Účty domén jsou podporované v nasazeních windows serveru, kde je služba Azure Active Directory dostupná. Ve výchozím nastavení jsou aplikace spuštěny pod účtem, pod kterým je spuštěn proces Fabric.exe. Aplikace lze také spustit jako jiné účty, které musí být deklarovány v části Objekty zabezpečení. Pokud použijete zásadu RunAs pro službu a manifest služby deklaruje prostředky koncového bodu pomocí protokolu HTTP, musíte také zadat SecurityAccessPolicy, abyste zajistili, že porty přidělené těmto koncovým bodům jsou správně řízení přístupu uvedené pro runas uživatelský účet, pod kterým je služba spuštěna. Pro koncový bod HTTPS je také třeba definovat EndpointBindingPolicy k označení názvu certifikátu vrátit klientovi. Další informace naleznete v tématu [RunAsPolicy Element](service-fabric-service-model-schema-elements.md#RunAsPolicyElementRunAsPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="defaultservices-element"></a>DefaultServices Element
Deklaruje instance služby, které jsou automaticky vytvořeny vždy, když je vytvořena instance aplikace proti tomuto typu aplikace. Další informace naleznete v tématu [DefaultServices Element](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Prvek služby
Deklaruje službu, která má být vytvořena automaticky při vytvoření instance aplikace. Další informace naleznete v tématu [Service Element](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statefulservice-element"></a>Element stavové služby
Definuje stavovou službu. Další informace naleznete v tématu [Element StatefulService](service-fabric-service-model-schema-elements.md#StatefulServiceElementStatefulServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="statelessservice-element"></a>Prvek bezstavové služby
Definuje bezstavovou službu. Další informace naleznete v tématu [StatelessService Element](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="principals-element"></a>Element principů
Popisuje objekty zabezpečení (uživatelé, skupiny) potřebné pro tuto aplikaci ke spuštění služeb a zabezpečení prostředků. Objekty jsou odkazovány v oddílech zásad. Další informace naleznete [v tématu Principals Element](service-fabric-service-model-schema-elements.md#PrincipalsElementSecurityPrincipalsTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="groups-element"></a>Groups – element
Deklaruje sadu skupin jako objekty zabezpečení, na které lze odkazovat v zásadách. Skupiny jsou užitečné, pokud existuje více uživatelů pro různé vstupní body služby a potřebují mít určitá společná oprávnění, která jsou k dispozici na úrovni skupiny. Další informace naleznete v tématu [Element skupiny](service-fabric-service-model-schema-elements.md#GroupsElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="group-element"></a>Group – element
Deklaruje skupinu jako zaregistrovaný objekt zabezpečení, na který lze odkazovat v zásadách. Další informace naleznete v tématu [Prvek skupiny](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInGroupselement)

### <a name="membership-element"></a>Prvek členství
 Další informace naleznete v tématu [Prvek členství](service-fabric-service-model-schema-elements.md#MembershipElementanonymouscomplexTypeComplexTypeDefinedInGroupelement)

### <a name="systemgroup-element"></a>SystemGroup Element
 Další informace naleznete v tématu [SystemGroup Element](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMembershipelement)

### <a name="users-element"></a>Prvek uživatelů
Deklaruje sadu uživatelů jako objekty zabezpečení, na které lze odkazovat v zásadách. Další informace naleznete v tématu [Users Element](service-fabric-service-model-schema-elements.md#UsersElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="user-element"></a>Uživatelský prvek
Deklaruje uživatele jako zaregistrovaný objekt zabezpečení, na který lze odkazovat v zásadách. Další informace naleznete [v tématu Uživatelský prvek](service-fabric-service-model-schema-elements.md#UserElementanonymouscomplexTypeComplexTypeDefinedInUserselement)

### <a name="memberof-element"></a>MemberOf Element
Uživatelé mohou být přidáni do libovolné existující skupiny členství, takže mohou dědit všechny vlastnosti a nastavení zabezpečení této skupiny členství. Členskou skupinu lze použít k zabezpečení externích prostředků, ke kterým je třeba přistupovat různými službami nebo stejnou službou (v jiném počítači). Další informace naleznete [v tématu MemberOf Element](service-fabric-service-model-schema-elements.md#MemberOfElementanonymouscomplexTypeComplexTypeDefinedInUserelement)

### <a name="systemgroup-element"></a>SystemGroup Element
Systémová skupina, do které chcete přidat uživatele.  Systémová skupina musí být definována v části Skupiny. Další informace naleznete v tématu [SystemGroup Element](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="group-element"></a>Group – element
Skupina, do které chcete přidat uživatele.  Skupina musí být definována v části Skupiny. Další informace naleznete v tématu [Prvek skupiny](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="policies-element"></a>Prvek zásad
Popisuje zásady (kolekce protokolů, výchozí run-as, stav a přístup k zabezpečení), které mají být použity na úrovni aplikace. Další informace naleznete v tématu [Zásady Element](service-fabric-service-model-schema-elements.md#PoliciesElementApplicationPoliciesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="defaultrunaspolicy-element"></a>Výchozí prvek Zásady RunAs
Zadejte výchozí uživatelský účet pro všechny balíčky kódu služby, které nemají konkrétní RunAsPolicy definované v serviceManifestImport části. Další informace naleznete v tématu [DefaultRunAsPolicy Element](service-fabric-service-model-schema-elements.md#DefaultRunAsPolicyElementanonymouscomplexTypeComplexTypeDefinedInApplicationPoliciesTypecomplexType)




## <a name="votingweb-service-manifest-elements"></a>Prvky manifestu služby VotingWeb
### <a name="servicemanifest-element"></a>ServiceManifest Element
Deklarativně popisuje typ a verzi služby. Obsahuje seznam nezávisle rozšiřitelných balíčků kódu, konfigurace a dat, které společně tvoří balíček služeb pro podporu jednoho nebo více typů služeb. Prostředky, nastavení diagnostiky a metadata služby, jako je například typ služby, vlastnosti stavu a metriky vyrovnávání zatížení, jsou také určeny. Další informace naleznete v tématu [ServiceManifest Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes Element
Definuje, jaké typy služeb jsou podporovány CodePackage v tomto manifestu. Při vytvoření instance služby proti jednomu z těchto typů služeb jsou všechny balíčky kódu deklarované v tomto manifestu aktivovány spuštěním vstupních bodů. Typy služeb jsou deklarovány na úrovni manifestu a nikoli na úrovni balíčku kódu. Další informace naleznete v tématu [ServiceTypes Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>StatelessServiceType Element
Popisuje typ bezstavové služby. Další informace naleznete v tématu [StatelessServiceType Element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage Element
Popisuje balíček kódu, který podporuje definovaný typ služby. Při vytvoření instance služby proti jednomu z těchto typů služeb jsou všechny balíčky kódu deklarované v tomto manifestu aktivovány spuštěním vstupních bodů. Očekává se, že výsledné procesy budou registrovat podporované typy služeb za běhu. Pokud existuje více balíčků kódu, jsou všechny aktivovány vždy, když systém hledá některý z deklarovaných typů služeb. Další informace naleznete v tématu [CodePackage Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="setupentrypoint-element"></a>SetupEntryPoint Element
Privilegovaný vstupní bod, který ve výchozím nastavení běží se stejnými pověřeními jako Service Fabric (obvykle účet NETWORKSERVICE) před jakýmkoli jiným vstupním bodem. Spustitelný soubor určený entrypointem je obvykle dlouhotrvající hostitel služby. Přítomnost samostatného vstupního bodu nastavení zabraňuje nutnosti spouštět hostitele služby s vysokými oprávněními po delší dobu. Další informace naleznete v tématu [SetupEntryPoint Element](service-fabric-service-model-schema-elements.md#SetupEntryPointElementanonymouscomplexTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>Prvek ExeHost
 Další informace naleznete [v tématu ExeHost Element](service-fabric-service-model-schema-elements.md#ExeHostElementExeHostEntryPointTypeComplexTypeDefinedInSetupEntryPointelement)

### <a name="program-element"></a>Prvek programu
Název spustitelného souboru.  Například "MySetup.bat" nebo "MyServiceHost.exe". Další informace naleznete v [tématu Prvek programu](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="arguments-element"></a>Prvek argumentů
 Další informace naleznete v tématu [Arguments Element](service-fabric-service-model-schema-elements.md#ArgumentsElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>Element pracovní složky
Pracovní adresář pro proces v balíčku kódu v uzlu clusteru, kde je aplikace nasazena. Můžete zadat tři hodnoty: Work (výchozí), CodePackage nebo CodeBase. CodeBase určuje, že pracovní adresář je nastaven na adresář, ve kterém je definován EXE v balíčku kódu. CodePackage nastaví pracovní adresář jako kořen balíčku kódu bez ohledu na to, kde exe je definován v adresáři balíčku kódu. Práce nastaví pracovní adresář na jedinečnou složku vytvořenou v uzlu.  Tato složka je stejná pro celou instanci aplikace. Ve výchozím nastavení je pracovní adresář všech procesů v aplikaci nastaven na pracovní složku aplikace. Toto je místo, kde procesy mohou zapisovat data. Zápis dat v balíčku kódu nebo základu kódu se nedoporučuje, protože tyto složky mohou být sdíleny mezi různými instancemi aplikace a mohou být odstraněny. Další informace naleznete v tématu [WorkingFolder Element](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="consoleredirection-element"></a>Prvek přesměrování konzoly

> [!WARNING]
> Nepoužívejte přesměrování konzoly v produkční aplikaci, používejte jej pouze pro místní vývoj a ladění. Přesměruje výstup konzoly ze spouštěcího skriptu do výstupního souboru ve složce aplikace s názvem "log" v uzlu clusteru, kde je aplikace nasazena a spuštěna. Další informace naleznete v tématu [ConsoleRedirection Element](service-fabric-service-model-schema-elements.md#ConsoleRedirectionElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="entrypoint-element"></a>EntryPoint Element
Spustitelný soubor určený entrypointem je obvykle dlouhotrvající hostitel služby. Přítomnost samostatného vstupního bodu nastavení zabraňuje nutnosti spouštět hostitele služby s vysokými oprávněními po delší dobu. Spustitelný soubor určený službou EntryPoint je spuštěn po úspěšném ukončení programu SetupEntryPoint. Výsledný proces je sledován a restartován (počínaje znovu setupEntryPoint), pokud někdy ukončí nebo dojde k chybě. Další informace naleznete v tématu [EntryPoint Element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>Prvek ExeHost
 Další informace naleznete [v tématu ExeHost Element](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="configpackage-element"></a>Prvek configPackage
Deklaruje složku pojmenovanou podle atributu Name v části PackageRoot, která obsahuje soubor Settings.xml. Tento soubor obsahuje části uživatelem definované nastavení páru klíč-hodnota, které může proces číst zpět za běhu. Pokud se během inovace změnila pouze verze ConfigPackage, nebude spuštěný proces restartován. Místo toho zpětné volání upozorní proces, že nastavení konfigurace se změnily tak, aby mohly být znovu načteny dynamicky. Další informace naleznete v tématu [ConfigPackage Element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Prvek zdroje
Popisuje prostředky používané touto službou, které lze deklarovat bez úpravy kompilovaného kódu a změnit při nasazení služby. Přístup k těmto prostředkům je řízen prostřednictvím oddílů Objekty zásad a zásady manifestu aplikace. Další informace naleznete v tématu [Resources Element](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Element koncových bodů
Definuje koncové body pro službu. Další informace naleznete [v tématu Prvek koncových bodů](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Element koncového bodu
Koncový bod deklarovaný v manifestu služby přepsat. Další informace naleznete [v tématu Endpoint Element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)



## <a name="votingdata-service-manifest-elements"></a>Prvky manifestu služby VotingData
### <a name="servicemanifest-element"></a>ServiceManifest Element
Deklarativně popisuje typ a verzi služby. Obsahuje seznam nezávisle rozšiřitelných balíčků kódu, konfigurace a dat, které společně tvoří balíček služeb pro podporu jednoho nebo více typů služeb. Prostředky, nastavení diagnostiky a metadata služby, jako je například typ služby, vlastnosti stavu a metriky vyrovnávání zatížení, jsou také určeny. Další informace naleznete v tématu [ServiceManifest Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes Element
Definuje, jaké typy služeb jsou podporovány CodePackage v tomto manifestu. Při vytvoření instance služby proti jednomu z těchto typů služeb jsou všechny balíčky kódu deklarované v tomto manifestu aktivovány spuštěním vstupních bodů. Typy služeb jsou deklarovány na úrovni manifestu a nikoli na úrovni balíčku kódu. Další informace naleznete v tématu [ServiceTypes Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statefulservicetype-element"></a>StatefulServiceType Element
Popisuje typ stavové služby. Další informace naleznete v tématu [Element StatefulServiceType](service-fabric-service-model-schema-elements.md#StatefulServiceTypeElementStatefulServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage Element
Popisuje balíček kódu, který podporuje definovaný typ služby. Při vytvoření instance služby proti jednomu z těchto typů služeb jsou všechny balíčky kódu deklarované v tomto manifestu aktivovány spuštěním vstupních bodů. Očekává se, že výsledné procesy budou registrovat podporované typy služeb za běhu. Pokud existuje více balíčků kódu, jsou všechny aktivovány vždy, když systém hledá některý z deklarovaných typů služeb. Další informace naleznete v tématu [CodePackage Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint Element
Spustitelný soubor určený entrypointem je obvykle dlouhotrvající hostitel služby. Přítomnost samostatného vstupního bodu nastavení zabraňuje nutnosti spouštět hostitele služby s vysokými oprávněními po delší dobu. Spustitelný soubor určený službou EntryPoint je spuštěn po úspěšném ukončení programu SetupEntryPoint. Výsledný proces je sledován a restartován (počínaje znovu setupEntryPoint), pokud někdy ukončí nebo dojde k chybě. Další informace naleznete v tématu [EntryPoint Element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>Prvek ExeHost
 Další informace naleznete [v tématu ExeHost Element](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="program-element"></a>Prvek programu
Název spustitelného souboru.  Například "MySetup.bat" nebo "MyServiceHost.exe". Další informace naleznete v [tématu Prvek programu](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>Element pracovní složky
Pracovní adresář pro proces v balíčku kódu v uzlu clusteru, kde je aplikace nasazena. Můžete zadat tři hodnoty: Work (výchozí), CodePackage nebo CodeBase. CodeBase určuje, že pracovní adresář je nastaven na adresář, ve kterém je definován EXE v balíčku kódu. CodePackage nastaví pracovní adresář jako kořen balíčku kódu bez ohledu na to, kde exe je definován v adresáři balíčku kódu. Práce nastaví pracovní adresář na jedinečnou složku vytvořenou v uzlu.  Tato složka je stejná pro celou instanci aplikace. Ve výchozím nastavení je pracovní adresář všech procesů v aplikaci nastaven na pracovní složku aplikace. Toto je místo, kde procesy mohou zapisovat data. Zápis dat v balíčku kódu nebo základu kódu se nedoporučuje, protože tyto složky mohou být sdíleny mezi různými instancemi aplikace a mohou být odstraněny. Další informace naleznete v tématu [WorkingFolder Element](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="configpackage-element"></a>Prvek configPackage
Deklaruje složku pojmenovanou podle atributu Name v části PackageRoot, která obsahuje soubor Settings.xml. Tento soubor obsahuje části uživatelem definované nastavení páru klíč-hodnota, které může proces číst zpět za běhu. Pokud se během inovace změnila pouze verze ConfigPackage, nebude spuštěný proces restartován. Místo toho zpětné volání upozorní proces, že nastavení konfigurace se změnily tak, aby mohly být znovu načteny dynamicky. Další informace naleznete v tématu [ConfigPackage Element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>Element datového balíčku
Deklaruje složku, pojmenovanou atributem Name, v části PackageRoot, která obsahuje statické datové soubory, které mají být spotřebovány procesem za běhu. Service Fabric bude recyklovat všechny EXEs a DLLHOSTs zadané v hostitelských a podpůrných balíčků při upgradu některého z datových balíčků uvedených v manifestu služby. Další informace naleznete v tématu [DataPackage Element](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Prvek zdroje
Popisuje prostředky používané touto službou, které lze deklarovat bez úpravy kompilovaného kódu a změnit při nasazení služby. Přístup k těmto prostředkům je řízen prostřednictvím oddílů Objekty zásad a zásady manifestu aplikace. Další informace naleznete v tématu [Resources Element](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Element koncových bodů
Definuje koncové body pro službu. Další informace naleznete [v tématu Prvek koncových bodů](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Element koncového bodu
Koncový bod deklarovaný v manifestu služby přepsat. Další informace naleznete [v tématu Endpoint Element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

