---
title: Příklady manifestu aplikace Reliable Services
description: Přečtěte si, jak nakonfigurovat nastavení manifestu aplikace a služby pro spolehlivé služby Service Fabric aplikaci.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: f40e54f5260f827f0b18c833d23d1f57b5ebc3a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84701094"
---
# <a name="reliable-services-application-and-service-manifest-examples"></a>Příklady manifestu služeb a aplikace Reliable Services
Níže jsou uvedeny příklady manifestů aplikace a služby pro Service Fabric aplikaci s ASP.NET Core webový front-end a stavový back-end. Účelem těchto příkladů je Ukázat, jaká nastavení jsou k dispozici a jak je používat. Tyto manifesty aplikací a služeb jsou založené na manifestech [rychlý Start pro Service Fabric .NET](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) .

Zobrazí se následující funkce:

|Manifest|Funkce|
|---|---|
|[Manifest aplikace](#application-manifest)| zásady [správného řízení prostředků](service-fabric-resource-governance.md), [Spusťte službu jako účet místního správce](service-fabric-application-runas-security.md), [použijte výchozí zásadu pro všechny balíčky kódu služby](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages), [vytvořte objekty zabezpečení uživatelů a skupin](service-fabric-application-runas-security.md), sdílejte Datový balíček mezi instancemi služby a [potlačením koncových bodů služby](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml) .| 
|Manifest služby FrontEndService| [Spuštění skriptu při spuštění služby](service-fabric-run-script-at-service-startup.md), [Definování KONCOVÉho bodu https](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest) | 
|Manifest služby BackEndService| [Deklarace konfiguračního balíčku](service-fabric-application-and-service-manifests.md), [deklarace datového balíčku](service-fabric-application-and-service-manifests.md), [Konfigurace koncového bodu](service-fabric-service-manifest-resources.md)| 

Další informace o konkrétních prvcích XML naleznete v tématech [prvky manifestu aplikace](#application-manifest-elements), [prvky manifestu služby VotingWeb](#votingweb-service-manifest-elements)a [prvky manifestu služby VotingData](#votingdata-service-manifest-elements) .

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
### <a name="applicationmanifest-element"></a>Element souboru ApplicationManifest
Deklarativně popisuje typ a verzi aplikace. Jeden nebo více manifestů služeb prvků se odkazuje na vytvoření typu aplikace. Nastavení konfigurace služeb prvků lze přepsat pomocí parametrizovaných nastavení aplikace. Výchozí služby, šablony služeb, objekty zabezpečení, zásady, nastavení diagnostiky a certifikáty mohou být deklarovány také na úrovni aplikace. Další informace naleznete v tématu [souboru ApplicationManifest element](service-fabric-service-model-schema-elements.md#ApplicationManifestElementApplicationManifestTypeComplexType)

### <a name="parameters-element"></a>Element Parameters
Deklaruje parametry, které se používají v manifestu této aplikace. Hodnotu těchto parametrů lze zadat při vytváření instance aplikace a lze ji použít k přepsání nastavení konfigurace aplikace nebo služby. Další informace naleznete v tématu [element Parameters](service-fabric-service-model-schema-elements.md#ParametersElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType) .

### <a name="parameter-element"></a>Parameter – element
Parametr aplikace, který se má použít v tomto manifestu. Hodnotu parametru lze změnit během vytváření instance aplikace, nebo, pokud není zadána žádná hodnota, je použita výchozí hodnota. Další informace naleznete v tématu [element Parameter](service-fabric-service-model-schema-elements.md#ParameterElementanonymouscomplexTypeComplexTypeDefinedInParameterselement) .

### <a name="servicemanifestimport-element"></a>Element ServiceManifestImport
Importuje manifest služby vytvořený vývojářem služby. Pro každou službu prvků v aplikaci je nutné importovat manifest služby. Pro manifest služby lze deklarovat přepsání a zásady konfigurace. Další informace naleznete v tématu [ServiceManifestImport element](service-fabric-service-model-schema-elements.md#ServiceManifestImportElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="servicemanifestref-element"></a>Element ServiceManifestRef
Importuje manifest služby podle odkazu. V současné době musí být soubor manifestu služby (ServiceManifest.xml) přítomen v balíčku buildu. Další informace naleznete v tématu [ServiceManifestRef element](service-fabric-service-model-schema-elements.md#ServiceManifestRefElementServiceManifestRefTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="resourceoverrides-element"></a>Element ResourceOverrides
Určuje přepsání prostředků pro koncové body deklarované v prostředcích manifestu služby. Další informace naleznete v tématu [ResourceOverrides element](service-fabric-service-model-schema-elements.md#ResourceOverridesElementResourceOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="endpoints-element"></a>Element Endpoints
Koncový bod (y), který se má přepsat Další informace naleznete v tématu [element Endpoints](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourceOverridesTypecomplexType)

### <a name="endpoint-element"></a>Element Endpoint
Koncový bod deklarovaný v manifestu služby, který se má přepsat. Další informace najdete v tématu [element Endpoint](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement) .

### <a name="policies-element"></a>Element policies
Popisuje zásady (vazby koncového bodu, sdílení balíčku, spuštění jako a zabezpečení přístupu), které se použijí na importovaný manifest služby. Další informace najdete v tématu věnovaném [elementu policies](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement) .

### <a name="servicepackageresourcegovernancepolicy-element"></a>Element ServicePackageResourceGovernancePolicy
Definuje zásadu správného řízení prostředků, která se použije na úrovni celého balíčku služby. Další informace naleznete v tématu [ServicePackageResourceGovernancePolicy element](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>Element ResourceGovernancePolicy
Určuje omezení prostředků pro codepackage. Další informace naleznete v tématu [ResourceGovernancePolicy element](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="packagesharingpolicy-element"></a>Element PackageSharingPolicy
Určuje, zda má být kód, konfigurace nebo balíček dat sdílen napříč instancemi služby stejného typu služby. Další informace naleznete v tématu [PackageSharingPolicy element](service-fabric-service-model-schema-elements.md#PackageSharingPolicyElementPackageSharingPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexType)

### <a name="securityaccesspolicy-element"></a>Element SecurityAccessPolicy
Uděluje přístupová oprávnění objektu zabezpečení u prostředku (například koncového bodu) definovaného v manifestu služby. Obvykle je velmi užitečné řídit a omezovat přístup ke službám různým prostředkům, aby se minimalizovala rizika zabezpečení. To je obzvláště důležité, když je aplikace sestavena z kolekce služeb z webu Marketplace, kterou vyvinuli různí vývojáři. Další informace naleznete v tématu [SecurityAccessPolicy element](service-fabric-service-model-schema-elements.md#SecurityAccessPolicyElementSecurityAccessPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInSecurityAccessPolicieselementDefinedInDigestedEndpointelement)

### <a name="runaspolicy-element"></a>Element RunAsPolicy
Určuje účet místního uživatele nebo místního systému, pod kterým se balíček kódu služby spustí. Účty domény jsou podporované v nasazeních Windows serveru, kde je Azure Active Directory k dispozici. Ve výchozím nastavení jsou aplikace spouštěny pod účtem, pod kterým běží proces Fabric.exe. Aplikace lze také spustit jako jiné účty, které musí být deklarovány v oddílu objekty zabezpečení. Použijete-li pro službu zásadu RunAs a manifest služby deklaruje prostředky koncového bodu s protokolem HTTP, je nutné zadat také SecurityAccessPolicy, aby bylo zajištěno, že porty přidělené těmto koncovým bodům budou mít správný přístup k řízení přístupu, které jsou uvedeny pro uživatelský účet RunAs, pod kterým je služba spuštěna. V případě koncového bodu HTTPS musíte také definovat EndpointBindingPolicy, který označuje název certifikátu, který se má vrátit klientovi. Další informace naleznete v tématu [RunAsPolicy element](service-fabric-service-model-schema-elements.md#RunAsPolicyElementRunAsPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="defaultservices-element"></a>Element DefaultServices
Deklaruje instance služby, které jsou automaticky vytvořeny pokaždé, když je vytvořena instance aplikace proti tomuto typu aplikace. Další informace naleznete v tématu [DefaultServices element](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Element služby
Deklaruje službu, která má být vytvořena automaticky při vytvoření instance aplikace. Další informace najdete v tématu [element služby](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType) .

### <a name="statefulservice-element"></a>Element StatefulService
Definuje stavovou službu. Další informace naleznete v tématu [StatefulService element](service-fabric-service-model-schema-elements.md#StatefulServiceElementStatefulServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="statelessservice-element"></a>Element StatelessService
Definuje bezstavovou službu. Další informace naleznete v tématu [StatelessService element](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="principals-element"></a>Prvky zabezpečení
Popisuje objekty zabezpečení (uživatele, skupiny) potřebné k tomu, aby tato aplikace spouštěla služby a zabezpečené prostředky. Na objekty zabezpečení se odkazuje v oddílech zásad. Další informace naleznete v tématu [element Principals](service-fabric-service-model-schema-elements.md#PrincipalsElementSecurityPrincipalsTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="groups-element"></a>Groups – element
Deklaruje sadu skupin jako objekty zabezpečení, na které lze odkazovat v zásadách. Skupiny jsou užitečné v případě, že existuje více uživatelů pro různé vstupní body služeb a potřebují určitá společná oprávnění, která jsou k dispozici na úrovni skupiny. Další informace naleznete v tématu [Groups – element](service-fabric-service-model-schema-elements.md#GroupsElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="group-element"></a>Group – element
Deklaruje skupinu jako objekt zabezpečení, na který se dá odkazovat v zásadách. Další informace najdete v tématu [Group element](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInGroupselement) .

### <a name="membership-element"></a>Element membership
 Další informace najdete v tématu [Membership – element](service-fabric-service-model-schema-elements.md#MembershipElementanonymouscomplexTypeComplexTypeDefinedInGroupelement) .

### <a name="systemgroup-element"></a>Systémový objekt
 Další informace naleznete v tématu [element System](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMembershipelement) .

### <a name="users-element"></a>Element Users
Deklaruje sadu uživatelů jako objekty zabezpečení, na které lze odkazovat v zásadách. Další informace naleznete v tématu [Uživatelé – element](service-fabric-service-model-schema-elements.md#UsersElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="user-element"></a>Element User
Deklaruje uživatele jako objekt zabezpečení, na který lze odkazovat v zásadách. Další informace naleznete v tématu [User element](service-fabric-service-model-schema-elements.md#UserElementanonymouscomplexTypeComplexTypeDefinedInUserselement)

### <a name="memberof-element"></a>MemberOf – element
Uživatele lze přidat do jakékoli existující skupiny členství, takže mohou dědit všechny vlastnosti a nastavení zabezpečení této skupiny členství. Skupina členství se dá použít k zabezpečení externích prostředků, ke kterým je potřeba mít k dispozici různé služby nebo stejnou službu (na jiném počítači). Další informace najdete v tématu [element memberOf](service-fabric-service-model-schema-elements.md#MemberOfElementanonymouscomplexTypeComplexTypeDefinedInUserelement) .

### <a name="systemgroup-element"></a>Systémový objekt
Skupina systému, do které se má přidat uživatel  Systémová skupina musí být definovaná v oddílu groups. Další informace naleznete v tématu [element System](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement) .

### <a name="group-element"></a>Group – element
Skupina, do které se má přidat uživatel  Skupina musí být definovaná v oddílu groups. Další informace najdete v tématu [Group element](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement) .

### <a name="policies-element"></a>Element policies
Popisuje zásady (shromažďování protokolů, výchozí spuštění jako, stav a přístup k zabezpečení), které se použijí na úrovni aplikace. Další informace najdete v tématu věnovaném [elementu policies](service-fabric-service-model-schema-elements.md#PoliciesElementApplicationPoliciesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType) .

### <a name="defaultrunaspolicy-element"></a>Element DefaultRunAsPolicy
Zadejte výchozí uživatelský účet pro všechny balíčky kódu služby, které nemají konkrétní RunAsPolicy definované v části ServiceManifestImport. Další informace naleznete v tématu [DefaultRunAsPolicy element](service-fabric-service-model-schema-elements.md#DefaultRunAsPolicyElementanonymouscomplexTypeComplexTypeDefinedInApplicationPoliciesTypecomplexType)




## <a name="votingweb-service-manifest-elements"></a>Prvky manifestu služby VotingWeb
### <a name="servicemanifest-element"></a>Element ServiceManifest
Deklarativně popisuje typ a verzi služby. Uvádí nezávisle aktualizovatelný kód, konfiguraci a datové balíčky, které dohromady tvoří balíček služby pro podporu jednoho nebo více typů služeb. Jsou také určeny prostředky, nastavení diagnostiky a metadata služby, jako je například typ služby, vlastnosti stavu a metriky vyrovnávání zatížení. Další informace naleznete v tématu [ServiceManifest element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes – element
Definuje typy služeb, které CodePackage v tomto manifestu podporují. Při vytváření instance služby proti jednomu z těchto typů služeb jsou všechny balíčky kódu deklarované v tomto manifestu aktivovány spuštěním jejich vstupních bodů. Typy služeb jsou deklarovány na úrovni manifestu a nikoli na úrovni balíčku kódu. Další informace naleznete v tématu [ServiceTypes element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>Element StatelessServiceType
Popisuje bezstavový typ služby. Další informace naleznete v tématu [StatelessServiceType element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>Element CodePackage
Popisuje balíček kódu, který podporuje definovaný typ služby. Při vytváření instance služby proti jednomu z těchto typů služeb jsou všechny balíčky kódu deklarované v tomto manifestu aktivovány spuštěním jejich vstupních bodů. U výsledných procesů se očekává, že se v době běhu zaregistrují podporované typy služeb. Pokud existuje více balíčků kódu, jsou všechny aktivovány vždy, když systém vyhledá některý z deklarovaných typů služeb. Další informace naleznete v tématu [CodePackage element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="setupentrypoint-element"></a>Element SetupEntryPoint
Privilegovaný vstupní bod, který se ve výchozím nastavení spouští se stejnými přihlašovacími údaji jako Service Fabric (obvykle účet NETWORKSERVICE) před jakýmkoli jiným vstupním bodem. Spustitelný soubor určený parametrem EntryPoint je obvykle dlouhodobě běžící hostitel služby. Přítomnost samostatného vstupního bodu instalace zabrání nutnosti spustit hostitele služby s vysokými oprávněními pro delší časová období. Další informace naleznete v tématu [SetupEntryPoint element](service-fabric-service-model-schema-elements.md#SetupEntryPointElementanonymouscomplexTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>Element ExeHost
 Další informace naleznete v tématu [ExeHost element](service-fabric-service-model-schema-elements.md#ExeHostElementExeHostEntryPointTypeComplexTypeDefinedInSetupEntryPointelement)

### <a name="program-element"></a>Program – element
Název spustitelného souboru.  Například "MySetup.bat" nebo "MyServiceHost.exe". Další informace najdete v tématu [programový prvek](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType) .

### <a name="arguments-element"></a>Arguments – element
 Další informace naleznete v tématu [element arguments](service-fabric-service-model-schema-elements.md#ArgumentsElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>Element WorkingFolder
Pracovní adresář pro proces v balíčku kódu na uzlu clusteru, kde je aplikace nasazena. Můžete zadat tři hodnoty: Work (výchozí), CodePackage nebo základ kódu. Základ kódu určuje, že pracovní adresář je nastaven na adresář, ve kterém je soubor EXE definován v balíčku kódu. CodePackage nastaví pracovní adresář tak, aby byl kořenem balíčku kódu bez ohledu na to, kde je soubor EXE definován v adresáři balíčku kódu. Práce nastaví pracovní adresář na jedinečnou složku vytvořenou na uzlu.  Tato složka je stejná pro celou instanci aplikace. Ve výchozím nastavení je pracovní adresář všech procesů v aplikaci nastaven na pracovní složku aplikace. To je místo, kde můžou procesy zapisovat data. Zápis dat do balíčku kódu nebo základního kódu se nedoporučuje, protože tyto složky by mohly být sdíleny mezi různými instancemi aplikace a mohou se odstranit. Další informace naleznete v tématu [WorkingFolder element](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="consoleredirection-element"></a>Element ConsoleRedirection

> [!WARNING]
> Nepoužívejte přesměrování konzoly v produkční aplikaci, použijte ji pouze pro místní vývoj a ladění. Přesměruje výstup konzoly z spouštěcího skriptu do výstupního souboru ve složce aplikace s názvem "protokol" na uzlu clusteru, kde je aplikace nasazená a spuštěná. Další informace naleznete v tématu [ConsoleRedirection element](service-fabric-service-model-schema-elements.md#ConsoleRedirectionElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="entrypoint-element"></a>EntryPoint – element
Spustitelný soubor určený parametrem EntryPoint je obvykle dlouhodobě běžící hostitel služby. Přítomnost samostatného vstupního bodu instalace zabrání nutnosti spustit hostitele služby s vysokými oprávněními pro delší časová období. Spustitelný soubor určený parametrem EntryPoint je spuštěn po úspěšném ukončení SetupEntryPoint. Výsledný proces se monitoruje a restartuje (znovu začíná SetupEntryPoint), pokud se někdy ukončí nebo dojde k chybě. Další informace naleznete v tématu [EntryPoint element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType) .

### <a name="exehost-element"></a>Element ExeHost
 Další informace naleznete v tématu [ExeHost element](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="configpackage-element"></a>Element ConfigPackage
Deklaruje složku, která je pojmenována atributem Name, v části PackageRoot, která obsahuje soubor Settings.xml. Tento soubor obsahuje oddíly nastavení páru klíč-hodnota definované uživatelem, které lze v průběhu běhu číst. Pokud se během upgradu změnila jenom verze ConfigPackage, spuštěný proces se nerestartuje. Místo toho zpětné volání upozorní proces, že došlo ke změně nastavení konfigurace, aby bylo možné je znovu načíst dynamicky. Další informace naleznete v tématu [ConfigPackage element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Resources – element
Popisuje prostředky používané touto službou, které mohou být deklarovány bez úprav zkompilovaného kódu a při nasazení služby změněny. Přístup k těmto prostředkům se řídí pomocí sekcí objekty zabezpečení a zásady v manifestu aplikace. Další informace naleznete v tématu [Resources – element](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Element Endpoints
Definuje koncové body pro službu. Další informace naleznete v tématu [element Endpoints](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Element Endpoint
Koncový bod deklarovaný v manifestu služby, který se má přepsat. Další informace najdete v tématu [element Endpoint](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement) .



## <a name="votingdata-service-manifest-elements"></a>Prvky manifestu služby VotingData
### <a name="servicemanifest-element"></a>Element ServiceManifest
Deklarativně popisuje typ a verzi služby. Uvádí nezávisle aktualizovatelný kód, konfiguraci a datové balíčky, které dohromady tvoří balíček služby pro podporu jednoho nebo více typů služeb. Jsou také určeny prostředky, nastavení diagnostiky a metadata služby, jako je například typ služby, vlastnosti stavu a metriky vyrovnávání zatížení. Další informace naleznete v tématu [ServiceManifest element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes – element
Definuje typy služeb, které CodePackage v tomto manifestu podporují. Při vytváření instance služby proti jednomu z těchto typů služeb jsou všechny balíčky kódu deklarované v tomto manifestu aktivovány spuštěním jejich vstupních bodů. Typy služeb jsou deklarovány na úrovni manifestu a nikoli na úrovni balíčku kódu. Další informace naleznete v tématu [ServiceTypes element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statefulservicetype-element"></a>Element StatefulServiceType
Popisuje typ stavové služby. Další informace naleznete v tématu [StatefulServiceType element](service-fabric-service-model-schema-elements.md#StatefulServiceTypeElementStatefulServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>Element CodePackage
Popisuje balíček kódu, který podporuje definovaný typ služby. Při vytváření instance služby proti jednomu z těchto typů služeb jsou všechny balíčky kódu deklarované v tomto manifestu aktivovány spuštěním jejich vstupních bodů. U výsledných procesů se očekává, že se v době běhu zaregistrují podporované typy služeb. Pokud existuje více balíčků kódu, jsou všechny aktivovány vždy, když systém vyhledá některý z deklarovaných typů služeb. Další informace naleznete v tématu [CodePackage element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint – element
Spustitelný soubor určený parametrem EntryPoint je obvykle dlouhodobě běžící hostitel služby. Přítomnost samostatného vstupního bodu instalace zabrání nutnosti spustit hostitele služby s vysokými oprávněními pro delší časová období. Spustitelný soubor určený parametrem EntryPoint je spuštěn po úspěšném ukončení SetupEntryPoint. Výsledný proces se monitoruje a restartuje (znovu začíná SetupEntryPoint), pokud se někdy ukončí nebo dojde k chybě. Další informace naleznete v tématu [EntryPoint element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType) .

### <a name="exehost-element"></a>Element ExeHost
 Další informace naleznete v tématu [ExeHost element](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="program-element"></a>Program – element
Název spustitelného souboru.  Například "MySetup.bat" nebo "MyServiceHost.exe". Další informace najdete v tématu [programový prvek](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType) .

### <a name="workingfolder-element"></a>Element WorkingFolder
Pracovní adresář pro proces v balíčku kódu na uzlu clusteru, kde je aplikace nasazena. Můžete zadat tři hodnoty: Work (výchozí), CodePackage nebo základ kódu. Základ kódu určuje, že pracovní adresář je nastaven na adresář, ve kterém je soubor EXE definován v balíčku kódu. CodePackage nastaví pracovní adresář tak, aby byl kořenem balíčku kódu bez ohledu na to, kde je soubor EXE definován v adresáři balíčku kódu. Práce nastaví pracovní adresář na jedinečnou složku vytvořenou na uzlu.  Tato složka je stejná pro celou instanci aplikace. Ve výchozím nastavení je pracovní adresář všech procesů v aplikaci nastaven na pracovní složku aplikace. To je místo, kde můžou procesy zapisovat data. Zápis dat do balíčku kódu nebo základního kódu se nedoporučuje, protože tyto složky by mohly být sdíleny mezi různými instancemi aplikace a mohou se odstranit. Další informace naleznete v tématu [WorkingFolder element](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="configpackage-element"></a>Element ConfigPackage
Deklaruje složku, která je pojmenována atributem Name, v části PackageRoot, která obsahuje soubor Settings.xml. Tento soubor obsahuje oddíly nastavení páru klíč-hodnota definované uživatelem, které lze v průběhu běhu číst. Pokud se během upgradu změnila jenom verze ConfigPackage, spuštěný proces se nerestartuje. Místo toho zpětné volání upozorní proces, že došlo ke změně nastavení konfigurace, aby bylo možné je znovu načíst dynamicky. Další informace naleznete v tématu [ConfigPackage element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>Element DataPackage
Deklaruje složku, která je pojmenována atributem Name v rámci PackageRoot, který obsahuje soubory statických dat, které mají být zpracovány procesem v době běhu. Service Fabric bude recyklovat všechny exe a DLLHOSTs zadané v hostitelích a balíčky podpory, pokud jsou všechny datové balíčky uvedené v manifestu služby upgradovány. Další informace naleznete v tématu [element DataPackage](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement) .

### <a name="resources-element"></a>Resources – element
Popisuje prostředky používané touto službou, které mohou být deklarovány bez úprav zkompilovaného kódu a při nasazení služby změněny. Přístup k těmto prostředkům se řídí pomocí sekcí objekty zabezpečení a zásady v manifestu aplikace. Další informace naleznete v tématu [Resources – element](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Element Endpoints
Definuje koncové body pro službu. Další informace naleznete v tématu [element Endpoints](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Element Endpoint
Koncový bod deklarovaný v manifestu služby, který se má přepsat. Další informace najdete v tématu [element Endpoint](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement) .

