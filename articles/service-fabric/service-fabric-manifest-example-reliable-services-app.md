---
title: Azure Service Fabric reliable services – příklady manifestu aplikace | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat aplikace a služby manifestu nastavení pro aplikace Service Fabric reliable services.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: xml
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/11/2018
ms.author: ryanwi
ms.openlocfilehash: 9cb41bfde38d9b47f5db994c0ca39c64b453ef1d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55171452"
---
# <a name="reliable-services-application-and-service-manifest-examples"></a>Příklady manifestu služeb a aplikace Reliable Services
Následují příklady manifestů aplikace a služby pro aplikaci Service Fabric s front-endového webového rozhraní ASP.NET Core a stavovým back endem. Účelem těchto příkladech je zobrazit nastavení, které jsou k dispozici a jak je používat. Tyto aplikace a služby manifestů jsou založeny na [rychlý úvod k Service Fabric .NET](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) manifesty.

Jsou zobrazeny následující funkce:
|Manifest|Funkce|
|---|---|
|[Manifest aplikace](#application-manifest)| [zásady správného řízení prostředků](service-fabric-resource-governance.md), [spuštění služby jako účet místního správce](service-fabric-application-runas-security.md), [výchozí zásady platí pro všechny balíčky kódu služby](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages), [vytvoření uživatelů a skupin objektů zabezpečení](service-fabric-application-runas-security.md), sdílené složky balíčku dat mezi instancemi služeb [přepsat koncové body služby](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)| 
|[Manifest služby FrontEndService](#frontendservice-service-manifest)| [Spuštění skriptu při spuštění služby](service-fabric-run-script-at-service-startup.md), [definovat koncový bod HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest) | 
|[Manifest služby BackEndService](#backendservice-service-manifest)| [Deklarovat konfigurační balíček](service-fabric-application-and-service-manifests.md), [deklarovat balíček dat](service-fabric-application-and-service-manifests.md), [konfigurace koncového bodu](service-fabric-service-manifest-resources.md)| 

Naleznete v tématu [elementy manifestu aplikace](#application-manifest-elements), [elementy manifestu služby VotingWeb](#votingweb-service-manifest-elements), a [manifestu prvky služeb VotingData](#votingdata-service-manifest-elements) Další informace o konkrétní XML elementy.

## <a name="application-manifest"></a>Manifest aplikace

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
      <PackageSharingPolicy PackageRef="VotingDataPkg.Data"/>

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
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
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

## <a name="votingdata-service-manifest"></a>VotingData service manifest

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingDataPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
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

## <a name="application-manifest-elements"></a>Elementy manifestu aplikace
### <a name="applicationmanifest-element"></a>ApplicationManifest – Element
Popisuje deklarativně, typ aplikace a její verzi. Manifesty služby jeden nebo více základních služeb odkazují k vytvoření aplikace typu. Nastavení konfigurace základních služeb lze přepsat pomocí parametrizovaných aplikaci nastavení. Ve výchozím nastavení služeb, šablony služeb, objekty zabezpečení, zásady, nastavení diagnostiky a certifikáty může také deklarovány na úrovni aplikace. Další informace najdete v tématu [ApplicationManifest – Element](service-fabric-service-model-schema-elements.md#ApplicationManifestElementApplicationManifestTypeComplexType)

### <a name="parameters-element"></a>Parametry – Element
Deklaruje parametry, které se používají v tomto manifestu aplikace. Hodnoty těchto parametrů lze zadat, když je vytvořena instance aplikace a můžete použít k přepsání aplikace nebo nastavení konfigurace služby. Další informace najdete v tématu [parametry – Element](service-fabric-service-model-schema-elements.md#ParametersElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="parameter-element"></a>Parameter – Element
Parametr aplikace pro použití v tomto manifestu. Hodnota tohoto parametru můžete změnit během vytvoření instance aplikace, nebo pokud není zadána žádná hodnota bude použita výchozí hodnota. Další informace najdete v tématu [Parameter – Element](service-fabric-service-model-schema-elements.md#ParameterElementanonymouscomplexTypeComplexTypeDefinedInParameterselement)

### <a name="servicemanifestimport-element"></a>ServiceManifestImport – Element
Importuje service manifest vytvořený vývojářem služeb. Manifest služby musí být importovány pro každou základní službu v aplikaci. Přepsání konfigurace a zásad mohou být deklarovány pro manifest služby. Další informace najdete v tématu [ServiceManifestImport – Element](service-fabric-service-model-schema-elements.md#ServiceManifestImportElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="servicemanifestref-element"></a>ServiceManifestRef Element
Naimportuje manifest služby podle odkazu. Soubor manifestu služby (ServiceManifest.xml) musí být aktuálně k dispozici v balíčku buildu. Další informace najdete v tématu [ServiceManifestRef – Element](service-fabric-service-model-schema-elements.md#ServiceManifestRefElementServiceManifestRefTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="resourceoverrides-element"></a>ResourceOverrides – Element
Určuje prostředek přepsání pro koncové body, které jsou deklarované v manifestu prostředků služeb. Další informace najdete v tématu [ResourceOverrides – Element](service-fabric-service-model-schema-elements.md#ResourceOverridesElementResourceOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="endpoints-element"></a>Koncové body – Element
Koncových bodů pro přepsání. Další informace najdete v tématu [koncové body – Element](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourceOverridesTypecomplexType)

### <a name="endpoint-element"></a>Element koncového bodu
Koncový bod, deklarované v manifestu služby, chcete-li přepsat. Další informace najdete v tématu [Element koncového bodu](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

### <a name="policies-element"></a>Zásady – Element
Popisuje zásady (koncový bod vazby balíčku sdílení, spustit jako a zabezpečení přístupu) má být použita na importovaný manifest služby. Další informace najdete v tématu [zásady – Element](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy Element
Definuje zásady zásad správného řízení prostředků, která se má použít na úrovni balíčku celé služby. Další informace najdete v tématu [ServicePackageResourceGovernancePolicy – Element](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>ResourceGovernancePolicy – Element
Určuje omezení prostředků pro codepackage. Další informace najdete v tématu [ResourceGovernancePolicy – Element](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="packagesharingpolicy-element"></a>PackageSharingPolicy Element
Označuje, pokud balíčku code, config nebo data by měla být sdílena napříč instancemi služby stejného typu služby. Další informace najdete v tématu [PackageSharingPolicy – Element](service-fabric-service-model-schema-elements.md#PackageSharingPolicyElementPackageSharingPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexType)

### <a name="securityaccesspolicy-element"></a>SecurityAccessPolicy – Element
Uděluje přístup k oprávnění objektu zabezpečení pro prostředek (například koncový bod) definované v manifestu služby. Obvykle je velmi užitečné pro řízení a omezení služeb přístup k různým prostředkům za účelem minimalizace rizika zabezpečení. To je obzvláště důležité, když je aplikace sestavená z kolekce služby na webu Marketplace, které jsou vyvíjeny s různými vývojáři. Další informace najdete v tématu [SecurityAccessPolicy – Element](service-fabric-service-model-schema-elements.md#SecurityAccessPolicyElementSecurityAccessPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInSecurityAccessPolicieselementDefinedInDigestedEndpointelement)

### <a name="runaspolicy-element"></a>RunAsPolicy Element
Určuje místního uživatele nebo místní systémový účet, který balíček kódu služby se spustí jako. Účty domény jsou podporovány v nasazení systému Windows Server, kde je k dispozici služby Azure Active Directory. Ve výchozím nastavení aplikace běží pod účtem, který spouští proces Fabric.exe pod. Aplikace můžete také spustit jako další účty, které musí být deklarován v části objekty zabezpečení. Pokud použijete zásady RunAs na službu a manifest služby deklaruje koncový bod prostředků pomocí protokolu HTTP, musíte zadat také SecurityAccessPolicy zajistit, že porty přidělené tyto koncové body jsou správně řízení přístupu u RunAs uživatelský účet, který je služba spuštěna pod. Pro koncový bod HTTPS musíte také definovat EndpointBindingPolicy k zadání názvu certifikát, který má být vrácena klientovi. Další informace najdete v tématu [RunAsPolicy – Element](service-fabric-service-model-schema-elements.md#RunAsPolicyElementRunAsPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="defaultservices-element"></a>DefaultServices – Element
Deklaruje instancí služby, které se automaticky vytvoří pokaždé, když se aplikace je vytvořena instance pro tento typ aplikace. Další informace najdete v tématu [DefaultServices – Element](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Element služby
Deklaruje služba má být vytvořen automaticky při vytváření instance aplikace. Další informace najdete v tématu [Element služby](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statefulservice-element"></a>StatefulService – Element
Definuje stavovou službu. Další informace najdete v tématu [StatefulService – Element](service-fabric-service-model-schema-elements.md#StatefulServiceElementStatefulServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="statelessservice-element"></a>StatelessService – Element
Definuje bezstavovou službu. Další informace najdete v tématu [StatelessService – Element](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="principals-element"></a>Objekty zabezpečení – Element
Popisuje objekty zabezpečení (uživatelé, skupiny) požadované pro tuto aplikaci spustit služby a zabezpečeným prostředkům. Objekty zabezpečení jsou odkazovány v části zásady. Další informace najdete v tématu [objekty zabezpečení – Element](service-fabric-service-model-schema-elements.md#PrincipalsElementSecurityPrincipalsTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="groups-element"></a>Groups – Element
Deklaruje sadu skupin zabezpečení objektů, které lze odkazovat v zásadách. Skupiny jsou užitečné, pokud existuje více uživatelů pro jiné služby vstupní body a potřebují mít určitá oprávnění zabezpečení, které jsou k dispozici na úrovni skupiny. Další informace najdete v tématu [Groups – Element](service-fabric-service-model-schema-elements.md#GroupsElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="group-element"></a>Skupinového elementu
Deklaruje skupinu jako objekt zabezpečení, která může být odkazováno v zásadách. Další informace najdete v tématu [skupinového elementu](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInGroupselement)

### <a name="membership-element"></a>Prvek Membership
 Další informace najdete v tématu [prvek Membership](service-fabric-service-model-schema-elements.md#MembershipElementanonymouscomplexTypeComplexTypeDefinedInGroupelement)

### <a name="systemgroup-element"></a>SystemGroup – Element
 Další informace najdete v tématu [SystemGroup – Element](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMembershipelement)

### <a name="users-element"></a>Uživatelé – Element
Deklaruje sadu uživatelů jako zabezpečení objektů, které lze odkazovat v zásadách. Další informace najdete v tématu [uživatelů – Element](service-fabric-service-model-schema-elements.md#UsersElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="user-element"></a>Element uživatele
Deklaruje uživatele jako objekt zabezpečení, která může být odkazováno v zásadách. Další informace najdete v tématu [Element uživatele](service-fabric-service-model-schema-elements.md#UserElementanonymouscomplexTypeComplexTypeDefinedInUserselement)

### <a name="memberof-element"></a>MemberOf – Element
K žádné existující členství ve skupině, můžete přidat uživatele, tak může dědit vlastnosti a nastavení zabezpečení této skupiny členství. Členství ve skupině je možné zabezpečit externí prostředky, které potřebují ke kterým přistupují jiné služby nebo stejnou službu (na jiném počítači). Další informace najdete v tématu [MemberOf – Element](service-fabric-service-model-schema-elements.md#MemberOfElementanonymouscomplexTypeComplexTypeDefinedInUserelement)

### <a name="systemgroup-element"></a>SystemGroup – Element
Skupina systému přidat uživatele.  Skupina systému musí být definován v části skupiny. Další informace najdete v tématu [SystemGroup – Element](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="group-element"></a>Skupinového elementu
Skupinu, kterou chcete přidat uživatele.  Skupiny musí být definován v části skupiny. Další informace najdete v tématu [skupinového elementu](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="policies-element"></a>Zásady – Element
Popisuje zásady (shromažďování protokolů, výchozí spustit jako, stavu a zabezpečení přístupu) použité na úrovni aplikace. Další informace najdete v tématu [zásady – Element](service-fabric-service-model-schema-elements.md#PoliciesElementApplicationPoliciesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="defaultrunaspolicy-element"></a>DefaultRunAsPolicy – Element
Zadejte výchozí uživatelský účet pro všechny balíčky kódu služby, které nemají zvláštní zásady RunAsPolicy definovaná v části ServiceManifestImport. Další informace najdete v tématu [DefaultRunAsPolicy – Element](service-fabric-service-model-schema-elements.md#DefaultRunAsPolicyElementanonymouscomplexTypeComplexTypeDefinedInApplicationPoliciesTypecomplexType)




## <a name="votingweb-service-manifest-elements"></a>Elementy manifestu služby VotingWeb
### <a name="servicemanifest-element"></a>Elementu ServiceManifest
Popisuje deklarativně, typ služby a její verzi. Vypíše nezávisle na sobě je možné upgradovat balíčky kódu, konfigurace a data, které společně tvoří balíček služby pro podporu jeden nebo více typů služeb. Prostředky, nastavení diagnostiky a metadata služby, jako je například typ služby, Vlastnosti stavu a vyrovnávání zatížení metriky jsou také zadat. Další informace najdete v tématu [elementu ServiceManifest](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceType – Element
Definuje, jaké služby jsou podporovány CodePackage v manifestu. Při vytváření instance služby s některou z těchto typů služeb, aktivují se všechny balíčky kódu, které jsou deklarované v manifestu spuštěním jejich vstupní body. Typy služeb jsou deklarované v manifestu úroveň a úroveň typu balíček kódu. Další informace najdete v tématu [ServiceType – Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>StatelessServiceType – Element
Popisuje typ bezstavové služby. Další informace najdete v tématu [StatelessServiceType – Element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage – Element
Popisuje balíček kódu, který podporuje typ služeb. Při vytváření instance služby s některou z těchto typů služeb, aktivují se všechny balíčky kódu, které jsou deklarované v manifestu spuštěním jejich vstupní body. Výsledný procesy, které se očekává, že registrace typů podporovaných služeb v době běhu. Pokud existuje víc balíčků kódu, všechny aktivují se pokaždé, když se systém, hledá pro každý z typy deklarované služby. Další informace najdete v tématu [CodePackage – Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="setupentrypoint-element"></a>Prvek SetupEntryPoint
Privilegované vstupní bod, který ve výchozím nastavení běží se stejnými přihlašovacími údaji jako Service Fabric (obvykle pod účtem NETWORKSERVICE) před všechny ostatní vstupní bod. Spustitelný soubor určený vstupní bod se obvykle dlouhotrvající hostitele služby. Přítomnost vstupní bod samostatného instalačního díky tomu není nutné ke spuštění hostitele služby s vysokou úrovní oprávnění pro dlouhou dobu. Další informace najdete v tématu [SetupEntryPoint – Element](service-fabric-service-model-schema-elements.md#SetupEntryPointElementanonymouscomplexTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost – Element
 Další informace najdete v tématu [ExeHost – Element](service-fabric-service-model-schema-elements.md#ExeHostElementExeHostEntryPointTypeComplexTypeDefinedInSetupEntryPointelement)

### <a name="program-element"></a>Prvek programu
Název spustitelného souboru.  Například "MySetup.bat" nebo "MyServiceHost.exe". Další informace najdete v tématu [elementu programu](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="arguments-element"></a>Argumenty – Element
 Další informace najdete v tématu [argumenty – Element](service-fabric-service-model-schema-elements.md#ArgumentsElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>WorkingFolder – Element
Pracovní adresář pro daný proces v balíčku kódu na uzlu clusteru, ve kterém je aplikace nasazená. Můžete zadat tři hodnoty: Pracovní (výchozí), CodePackage nebo základu kódu. Základu kódu určuje, zda pracovní adresář nastaven na adresář, ve kterém je definována souboru EXE v balíčku kódu. CodePackage nastaví pracovní adresář, který se použije kořen balíček kódu bez ohledu na to, kde je definován souboru EXE v adresáři balíčku kódu. Pracovní nastaví pracovní adresář unikátní složku vytvořit na uzlu.  Tato složka je stejný pro instanci celé aplikace. Ve výchozím nastavení je nastavit pracovní adresář všech procesů v aplikaci pracovní složky aplikace. Je to, kde procesy, které může zapisovat data. Zápis dat v balíčku kódu nebo základu kódu se nedoporučuje, protože tyto složky může být sdílena mezi různé instance aplikace a může se odstraní. Další informace najdete v tématu [WorkingFolder – Element](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="consoleredirection-element"></a>ConsoleRedirection – Element

> [!WARNING]
> Není v produkční aplikace používat přesměrování konzoly, použít klíč jen pro místní vývoj a ladění. Přesměruje výstup konzoly z spouštěcí skript do výstupního souboru ve složce aplikace nazvané "protokol" na uzlu clusteru, kde nasazení a spuštění aplikace. Další informace najdete v tématu [ConsoleRedirection – Element](service-fabric-service-model-schema-elements.md#ConsoleRedirectionElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="entrypoint-element"></a>EntryPoint – Element
Spustitelný soubor určený vstupní bod se obvykle dlouhotrvající hostitele služby. Přítomnost vstupní bod samostatného instalačního díky tomu není nutné ke spuštění hostitele služby s vysokou úrovní oprávnění pro dlouhou dobu. Spustitelný soubor určený vstupní bod se spustí po ukončení SetupEntryPoint úspěšně. Výsledný proces je monitorována a restartuje (začíná znovu SetupEntryPoint), pokud někdy ukončí nebo dojde k chybě. Další informace najdete v tématu [EntryPoint – Element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost – Element
 Další informace najdete v tématu [ExeHost – Element](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="configpackage-element"></a>Složce ConfigPackage – Element
Deklaruje složku, v názvu atributu packageroot, který obsahuje souboru Settings.xml. Tento soubor obsahuje oddíly pár definovaný uživatelem, klíč hodnota nastavení, které proces může číst zpět v době běhu. Během upgradu pokud pouze složce ConfigPackage verze se změnila, pak spuštěný proces se nerestartuje. Místo toho zpětné volání upozornění procesu tak, že je možné znovu zavést dynamicky se nezměnila konfigurační nastavení. Další informace najdete v tématu [složce ConfigPackage – Element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Element prostředky
Popisuje prostředky využívané třídou této služby, které je možné deklarovat beze změny zkompilovaného kódu a změnit, pokud je služba nasazená. Přístup k těmto prostředkům je řízen pomocí objekty zabezpečení a zásady části manifestu aplikace. Další informace najdete v tématu [elementu Resources](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Koncové body – Element
Definuje koncové body pro službu. Další informace najdete v tématu [koncové body – Element](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Element koncového bodu
Koncový bod, deklarované v manifestu služby, chcete-li přepsat. Další informace najdete v tématu [Element koncového bodu](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)



## <a name="votingdata-service-manifest-elements"></a>Prvky manifestu VotingData služeb
### <a name="servicemanifest-element"></a>Elementu ServiceManifest
Popisuje deklarativně, typ služby a její verzi. Vypíše nezávisle na sobě je možné upgradovat balíčky kódu, konfigurace a data, které společně tvoří balíček služby pro podporu jeden nebo více typů služeb. Prostředky, nastavení diagnostiky a metadata služby, jako je například typ služby, Vlastnosti stavu a vyrovnávání zatížení metriky jsou také zadat. Další informace najdete v tématu [elementu ServiceManifest](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceType – Element
Definuje, jaké služby jsou podporovány CodePackage v manifestu. Při vytváření instance služby s některou z těchto typů služeb, aktivují se všechny balíčky kódu, které jsou deklarované v manifestu spuštěním jejich vstupní body. Typy služeb jsou deklarované v manifestu úroveň a úroveň typu balíček kódu. Další informace najdete v tématu [ServiceType – Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statefulservicetype-element"></a>StatefulServiceType – Element
Popisuje typ stavové služby. Další informace najdete v tématu [StatefulServiceType – Element](service-fabric-service-model-schema-elements.md#StatefulServiceTypeElementStatefulServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage – Element
Popisuje balíček kódu, který podporuje typ služeb. Při vytváření instance služby s některou z těchto typů služeb, aktivují se všechny balíčky kódu, které jsou deklarované v manifestu spuštěním jejich vstupní body. Výsledný procesy, které se očekává, že registrace typů podporovaných služeb v době běhu. Pokud existuje víc balíčků kódu, všechny aktivují se pokaždé, když se systém, hledá pro každý z typy deklarované služby. Další informace najdete v tématu [CodePackage – Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint – Element
Spustitelný soubor určený vstupní bod se obvykle dlouhotrvající hostitele služby. Přítomnost vstupní bod samostatného instalačního díky tomu není nutné ke spuštění hostitele služby s vysokou úrovní oprávnění pro dlouhou dobu. Spustitelný soubor určený vstupní bod se spustí po ukončení SetupEntryPoint úspěšně. Výsledný proces je monitorována a restartuje (začíná znovu SetupEntryPoint), pokud někdy ukončí nebo dojde k chybě. Další informace najdete v tématu [EntryPoint – Element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost – Element
 Další informace najdete v tématu [ExeHost – Element](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="program-element"></a>Prvek programu
Název spustitelného souboru.  Například "MySetup.bat" nebo "MyServiceHost.exe". Další informace najdete v tématu [elementu programu](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>WorkingFolder – Element
Pracovní adresář pro daný proces v balíčku kódu na uzlu clusteru, ve kterém je aplikace nasazená. Můžete zadat tři hodnoty: Pracovní (výchozí), CodePackage nebo základu kódu. Základu kódu určuje, zda pracovní adresář nastaven na adresář, ve kterém je definována souboru EXE v balíčku kódu. CodePackage nastaví pracovní adresář, který se použije kořen balíček kódu bez ohledu na to, kde je definován souboru EXE v adresáři balíčku kódu. Pracovní nastaví pracovní adresář unikátní složku vytvořit na uzlu.  Tato složka je stejný pro instanci celé aplikace. Ve výchozím nastavení je nastavit pracovní adresář všech procesů v aplikaci pracovní složky aplikace. Je to, kde procesy, které může zapisovat data. Zápis dat v balíčku kódu nebo základu kódu se nedoporučuje, protože tyto složky může být sdílena mezi různé instance aplikace a může se odstraní. Další informace najdete v tématu [WorkingFolder – Element](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="configpackage-element"></a>Složce ConfigPackage – Element
Deklaruje složku, v názvu atributu packageroot, který obsahuje souboru Settings.xml. Tento soubor obsahuje oddíly pár definovaný uživatelem, klíč hodnota nastavení, které proces může číst zpět v době běhu. Během upgradu pokud pouze složce ConfigPackage verze se změnila, pak spuštěný proces se nerestartuje. Místo toho zpětné volání upozornění procesu tak, že je možné znovu zavést dynamicky se nezměnila konfigurační nastavení. Další informace najdete v tématu [složce ConfigPackage – Element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>DataPackage – Element
Deklaruje složku, v názvu atributu packageroot, který obsahuje statické datové soubory pro proces v době běhu. Service Fabric se recyklaci všech souborů exe a DLLHOSTs zadali v balíčcích hostitele a podporu při všech dat balíčky uvedené v manifestu služby budou upgradovány. Další informace najdete v tématu [DataPackage – Element](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Element prostředky
Popisuje prostředky využívané třídou této služby, které je možné deklarovat beze změny zkompilovaného kódu a změnit, pokud je služba nasazená. Přístup k těmto prostředkům je řízen pomocí objekty zabezpečení a zásady části manifestu aplikace. Další informace najdete v tématu [elementu Resources](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Koncové body – Element
Definuje koncové body pro službu. Další informace najdete v tématu [koncové body – Element](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Element koncového bodu
Koncový bod, deklarované v manifestu služby, chcete-li přepsat. Další informace najdete v tématu [Element koncového bodu](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

