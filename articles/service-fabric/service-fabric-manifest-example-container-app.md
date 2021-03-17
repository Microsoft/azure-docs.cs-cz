---
title: Příklady manifestu aplikace Azure Service Fabric Container
description: Přečtěte si, jak nakonfigurovat nastavení manifestu aplikace a služby pro aplikaci Service Fabric více kontejnerů.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/08/2018
ms.author: pepogors
ms.openlocfilehash: 5efdbd17db20e69ace33b379ddbb99b2c4a20e69
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84701162"
---
# <a name="multi-container-application-and-service-manifest-examples"></a>Příklady manifestu služeb a vícekontejnerové aplikace
Níže jsou uvedeny příklady manifestů aplikace a služby pro aplikaci Service Fabric více kontejnerů. Účelem těchto příkladů je Ukázat, jaká nastavení jsou k dispozici a jak je používat. Tyto manifesty aplikací a služeb jsou založené na [ukázkových manifestech kontejnerů pro Windows Server 2016](https://github.com/Azure-Samples/service-fabric-containers/tree/master/Windows) .

Zobrazí se následující funkce:

|Manifest|Funkce|
|---|---|
|[Manifest aplikace](#application-manifest)| [přepsat proměnné prostředí](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [nakonfigurovat mapování portů kontejneru na hostitele](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery), [Konfigurovat ověřování registrů kontejnerů](service-fabric-get-started-containers.md#configure-container-repository-authentication), zásady [správného řízení prostředků](service-fabric-resource-governance.md), [nastavit režim izolace](service-fabric-get-started-containers.md#configure-isolation-mode), [zadat image kontejneru specifické pro sestavení operačního systému](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)| 
|[Manifest služby FrontEndService](#frontendservice-service-manifest)| [nastavení proměnných prostředí](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [Konfigurace koncového bodu](service-fabric-get-started-containers.md#configure-communication), předávání příkazů do kontejneru, [Import certifikátu do kontejneru](service-fabric-securing-containers.md)| 
|[Manifest služby BackEndService](#backendservice-service-manifest)|[nastavení proměnných prostředí](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [Konfigurace koncového bodu](service-fabric-get-started-containers.md#configure-communication), [Konfigurace ovladače svazku](service-fabric-containers-volume-logging-drivers.md)| 

Další informace o konkrétních prvcích XML naleznete v tématech [prvky manifestu aplikace](#application-manifest-elements), [prvky manifestu služby FrontEndService](#frontendservice-service-manifest-elements)a [prvky manifestu služby BackEndService](#backendservice-service-manifest-elements) .

## <a name="application-manifest"></a>Manifest aplikace

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Container.ApplicationType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="BackEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="FrontEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="MaximumIOps" DefaultValue="20"/>
    <Parameter Name="MemoryFront" DefaultValue="4084" />
    <Parameter Name="MemoryBack" DefaultValue="2048" />
    <Parameter Name="CertThumbprint" DefaultValue=""/>
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="BackEndServicePkg" ServiceManifestVersion="1.0.0" />    
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryFront]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[MemoryFront]" BlockIOWeight="[BlockIOWeight]" MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" MemorySwapInMB="[MemorySwapInMB]"/>
      
      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
        
        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>
        
        <!-- This binds the port the container is listening on (8905 in this sample) to an endpoint resource named "BackEndServiceTypeEndpoint", which is defined in the service manifest.  -->
        <PortBinding ContainerPort="8905" EndpointRef="BackEndServiceTypeEndpoint"/>
        
        <!-- Configure the Azure Files volume plugin.  Bind the source folder on the host VM or a remote share to the destination folder within the running container. -->
        <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
          <!-- Driver options to be passed to driver. The Azure Files volume plugin supports the following driver options:
            shareName (the Azure Files file share that provides the volume for the container), storageAccountName (the Azure storage account
            that contains the Azure Files file share), storageAccountKey (Access key for the Azure storage account that contains the Azure Files file share).
            These three driver options are required. -->
          <DriverOption Name="shareName" Value="" />
          <DriverOption Name="storageAccountName" Value="MY-STORAGE-ACCOUNT-NAME" />
          <DriverOption Name="storageAccountKey" Value="MY-STORAGE-ACCOUNT-KEY" />
        </Volume>
        
        <!-- Windows Server containers may not be compatible across different versions of the OS.  You can specify multiple OS images per container and tag 
        them with the build versions of the OS. Get the build version of the OS by running "winver" at a Windows command prompt. -->
        <ImageOverrides>
          <!-- If the underlying OS is build version 16299 (Windows Server version 1709), Service Fabric picks the container image tagged with Os="16299". -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_1709" Os="16299" />
          
          <!-- An untagged container image is assumed to work across all versions of the OS and overrides the image specified in the service manifest. -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_default" />          
        </ImageOverrides>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <!-- Policies to be applied to the imported service manifest. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
    
    <!-- This enables you to provide different values for environment variables when creating a FrontEndService
         Theses environment variables are declared in the FrontEndServiceType service manifest-->
    <EnvironmentOverrides CodePackageRef="Code">
      <EnvironmentVariable Name="BackendServiceName" Value="Container.Application/BackEndService"/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentOverrides>
    
    <!-- This policy maps the  port of the container (80) to the endpoint declared in the service, 
         FrontEndServiceTypeEndpoint which is exposed as port 80 on the host-->    
    <Policies>

      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryBack]"/>

      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">

        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>

        <!-- Binds an endpoint resource (declared in the service manifest) to the exposed container port. -->
        <PortBinding ContainerPort="80" EndpointRef="FrontEndServiceTypeEndpoint"/>

        <!-- Import a certificate into the container.  The certificate must be installed in the LocalMachine store of all the cluster nodes.
          When the application starts, the runtime reads the certificate and generates a PFX file and password (on Windows) or a PEM file (on Linux).
          The PFX file and password are accessible in the container using the Certificates_ServicePackageName_CodePackageName_CertName_PFX and 
          Certificates_ServicePackageName_CodePackageName_CertName_Password environment variables. The PEM file is accessible in the container using the 
          Certificates_ServicePackageName_CodePackageName_CertName_PEM and Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey environment variables.-->
        <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[CertThumbprint]" />

        <!-- If the certificate is already in PFX or PEM form, you can create a data package inside your application and reference that certificate here. -->
        <CertificateRef Name="MyCert2" DataPackageRef="Data" DataPackageVersion="1.0.0" RelativePath="MyCert2.PFX" Password="ENCRYPTED-PASSWORD" IsPasswordEncrypted="true"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
        
    <Service Name="FrontEndService" >
      <StatelessService ServiceTypeName="FrontEndServiceType" InstanceCount="[FrontEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
        <Service Name="BackEndService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="BackEndServiceType" InstanceCount="[BackEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="frontendservice-service-manifest"></a>Manifest služby FrontEndService

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="FrontEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="FrontEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricfrontendservice:v1</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container or exe.  These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="IsContainer" Value=""/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Data package is the contents of the Data directory under PackageRoot that contains an 
       independently-updateable and versioned static data that's consumed by the process at runtime. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme and port 80 -->
      <Endpoint Name="FrontEndServiceTypeEndpoint" UriScheme="http" Port="80"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="backendservice-service-manifest"></a>Manifest služby BackEndService

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="BackEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="BackEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricbackendservice:v1</ImageName>
        
        <!-- Pass comma delimited commands to your container. -->
        <Commands> dotnet, myproc.dll, 5 </Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container. These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the host port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme. In this case since no port is specified, one is created and assigned dynamically
           to the service. This dynamically assigned host port is mapped to the container port (8905 in this sample),
            which was specified in the application manifest.-->
      <Endpoint Name="BackEndServiceTypeEndpoint" UriScheme="http" />
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

### <a name="policies-element"></a>Element policies
Popisuje zásady (vazby koncového bodu, sdílení balíčku, spuštění jako a zabezpečení přístupu), které se použijí na importovaný manifest služby. Další informace najdete v tématu věnovaném [elementu policies](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement) .

### <a name="servicepackageresourcegovernancepolicy-element"></a>Element ServicePackageResourceGovernancePolicy
Definuje zásadu správného řízení prostředků, která se použije na úrovni celého balíčku služby. Další informace naleznete v tématu [ServicePackageResourceGovernancePolicy element](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>Element ResourceGovernancePolicy
Určuje omezení prostředků pro balíček kódu. Další informace naleznete v tématu [ResourceGovernancePolicy element](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="containerhostpolicies-element"></a>Element ContainerHostPolicies
Určuje zásady pro aktivaci hostitelů kontejnerů. Další informace naleznete v tématu [ContainerHostPolicies element](service-fabric-service-model-schema-elements.md#ContainerHostPoliciesElementContainerHostPoliciesTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="repositorycredentials-element"></a>Element RepositoryCredentials
Přihlašovací údaje pro úložiště imagí kontejneru pro vyžádání imagí z. Další informace naleznete v tématu [RepositoryCredentials element](service-fabric-service-model-schema-elements.md#RepositoryCredentialsElementRepositoryCredentialsTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="portbinding-element"></a>Element PortBinding
Určuje prostředek koncového bodu, který se má navazovat na port vystaveného kontejneru. Další informace naleznete v tématu [PortBinding element](service-fabric-service-model-schema-elements.md#PortBindingElementPortBindingTypeComplexTypeDefinedInServicePackageContainerPolicyTypecomplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="volume-element"></a>Element Volume
Určuje svazek, který má být svázán s kontejnerem. Další informace naleznete v tématu [Volume element](service-fabric-service-model-schema-elements.md#VolumeElementContainerVolumeTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="driveroption-element"></a>Element DriverOption
Možnosti ovladače, které mají být předány ovladači. Další informace naleznete v tématu [DriverOption element](service-fabric-service-model-schema-elements.md#DriverOptionElementDriverOptionTypeComplexTypeDefinedInContainerLoggingDriverTypecomplexTypeDefinedInContainerVolumeTypecomplexType)

### <a name="imageoverrides-element"></a>Element ImageOverrides
Kontejnery Windows serveru nemusí být kompatibilní napříč různými verzemi operačního systému.  Můžete zadat více imagí operačního systému na kontejner a označit je pomocí verzí sestavení operačního systému. Získat verzi buildu operačního systému spuštěním příkazu "winver" na příkazovém řádku systému Windows. Pokud je základní operační systém sestavení verze 16299 (Windows Server verze 1709), Service Fabric vybere image kontejneru označená operačním systémem = "16299". Předpokládá se, že neoznačená image kontejneru funguje ve všech verzích operačního systému a Přepisuje image zadanou v manifestu služby. Další informace naleznete v tématu [ImageOverrides element](service-fabric-service-model-schema-elements.md#ImageOverridesElementImageOverridesTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="image-element"></a>Element image
Image kontejneru odpovídající číslu verze buildu operačního systému, který se má spustit Pokud atribut OS není zadán, předpokládá se, že image kontejneru bude fungovat ve všech verzích operačního systému a přepíše bitovou kopii zadanou v manifestu služby. Další informace naleznete v tématu [element image](service-fabric-service-model-schema-elements.md#ImageElementImageTypeComplexTypeDefinedInImageOverridesTypecomplexType) .

### <a name="environmentoverrides-element"></a>Element EnvironmentOverrides
 Další informace naleznete v tématu [EnvironmentOverrides element](service-fabric-service-model-schema-elements.md#EnvironmentOverridesElementEnvironmentOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="environmentvariable-element"></a>Element objekt EnvironmentVariable
Proměnná prostředí. Další informace naleznete v tématu [objekt EnvironmentVariable element](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="certificateref-element"></a>Element CertificateRef
Určuje informace o certifikátu x509, který má být vystaven prostředí kontejneru. Certifikát musí být nainstalovaný v LocalMachine úložišti všech uzlů clusteru.
Po spuštění aplikace modul runtime přečte certifikát a vygeneruje soubor PFX a heslo (ve Windows) nebo soubor PEM (na platformě Linux).
Soubor PFX a heslo jsou přístupné v kontejneru pomocí proměnných prostředí Certificates_ServicePackageName_CodePackageName_CertName_PFX a Certificates_ServicePackageName_CodePackageName_CertName_Password. Soubor PEM je v kontejneru přístupný pomocí proměnných prostředí Certificates_ServicePackageName_CodePackageName_CertName_PEM a Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey. Další informace naleznete v tématu [CertificateRef element](service-fabric-service-model-schema-elements.md#CertificateRefElementContainerCertificateTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="defaultservices-element"></a>Element DefaultServices
Deklaruje instance služby, které jsou automaticky vytvořeny pokaždé, když je vytvořena instance aplikace proti tomuto typu aplikace. Další informace naleznete v tématu [DefaultServices element](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Element služby
Deklaruje službu, která má být vytvořena automaticky při vytvoření instance aplikace. Další informace najdete v tématu [element služby](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType) .

### <a name="statelessservice-element"></a>Element StatelessService
Definuje bezstavovou službu. Další informace naleznete v tématu [StatelessService element](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)


## <a name="frontendservice-service-manifest-elements"></a>Prvky manifestu služby FrontEndService
### <a name="servicemanifest-element"></a>Element ServiceManifest
Deklarativně popisuje typ a verzi služby. Uvádí nezávisle aktualizovatelný kód, konfiguraci a datové balíčky, které dohromady tvoří balíček služby pro podporu jednoho nebo více typů služeb. Jsou také určeny prostředky, nastavení diagnostiky a metadata služby, jako je například typ služby, vlastnosti stavu a metriky vyrovnávání zatížení. Další informace naleznete v tématu [ServiceManifest element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes – element
Definuje typy služeb, které CodePackage v tomto manifestu podporují. Při vytváření instance služby proti jednomu z těchto typů služeb jsou všechny balíčky kódu deklarované v tomto manifestu aktivovány spuštěním jejich vstupních bodů. Typy služeb jsou deklarovány na úrovni manifestu a nikoli na úrovni balíčku kódu. Další informace naleznete v tématu [ServiceTypes element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>Element StatelessServiceType
Popisuje bezstavový typ služby. Další informace naleznete v tématu [StatelessServiceType element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>Element CodePackage
Popisuje balíček kódu, který podporuje definovaný typ služby. Při vytváření instance služby proti jednomu z těchto typů služeb jsou všechny balíčky kódu deklarované v tomto manifestu aktivovány spuštěním jejich vstupních bodů. U výsledných procesů se očekává, že se v době běhu zaregistrují podporované typy služeb. Pokud existuje více balíčků kódu, jsou všechny aktivovány vždy, když systém vyhledá některý z deklarovaných typů služeb. Další informace naleznete v tématu [CodePackage element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint – element
Spustitelný soubor určený parametrem EntryPoint je obvykle dlouhodobě běžící hostitel služby. Přítomnost samostatného vstupního bodu instalace zabrání nutnosti spustit hostitele služby s vysokými oprávněními pro delší časová období. Spustitelný soubor určený parametrem EntryPoint je spuštěn po úspěšném ukončení SetupEntryPoint. Výsledný proces se monitoruje a restartuje (znovu začíná SetupEntryPoint), pokud se někdy ukončí nebo dojde k chybě. Další informace naleznete v tématu [EntryPoint element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType) .

### <a name="containerhost-element"></a>Element ContainerHost
 Další informace naleznete v tématu [ContainerHost element](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>Element ImageName
Úložiště a obrázek na [https://hub.docker.com](https://hub.docker.com) nebo Azure Container Registry. Další informace naleznete v tématu [ImageName element](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>Element EnvironmentVariables
Předání proměnných prostředí kontejneru nebo exe.  Další informace naleznete v tématu [EnvironmentVariables element](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>Element objekt EnvironmentVariable
Proměnná prostředí. Další informace naleznete v tématu [objekt EnvironmentVariable element](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>Element ConfigPackage
Deklaruje složku, která je pojmenována atributem Name, který obsahuje soubor Settings.xml. Tento soubor obsahuje oddíly nastavení páru klíč-hodnota definované uživatelem, které lze v průběhu běhu číst. Pokud se během upgradu změnila jenom verze ConfigPackage, spuštěný proces se nerestartuje. Místo toho zpětné volání upozorní proces, že došlo ke změně nastavení konfigurace, aby bylo možné je znovu načíst dynamicky. Další informace naleznete v tématu [ConfigPackage element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>Element DataPackage
Deklaruje složku, která je pojmenována atributem Name, který obsahuje soubory statických dat. Service Fabric bude recyklovat všechny exe a DLLHOSTs zadané v hostitelích a balíčky podpory, pokud jsou všechny datové balíčky uvedené v manifestu služby upgradovány. Další informace naleznete v tématu [element DataPackage](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement) .

### <a name="resources-element"></a>Resources – element
Popisuje prostředky používané touto službou, které mohou být deklarovány bez úprav zkompilovaného kódu a při nasazení služby změněny. Přístup k těmto prostředkům se řídí pomocí sekcí objekty zabezpečení a zásady v manifestu aplikace. Další informace naleznete v tématu [Resources – element](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Element Endpoints
Definuje koncové body pro službu. Další informace naleznete v tématu [element Endpoints](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Element Endpoint
Další informace najdete v tématu [element Endpoint](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement) .


## <a name="backendservice-service-manifest-elements"></a>Prvky manifestu služby BackEndService
### <a name="servicemanifest-element"></a>Element ServiceManifest
Deklarativně popisuje typ a verzi služby. Uvádí nezávisle aktualizovatelný kód, konfiguraci a datové balíčky, které dohromady tvoří balíček služby pro podporu jednoho nebo více typů služeb. Jsou také určeny prostředky, nastavení diagnostiky a metadata služby, jako je například typ služby, vlastnosti stavu a metriky vyrovnávání zatížení. Další informace naleznete v tématu [ServiceManifest element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes – element
Definuje typy služeb, které CodePackage v tomto manifestu podporují. Při vytváření instance služby proti jednomu z těchto typů služeb jsou všechny balíčky kódu deklarované v tomto manifestu aktivovány spuštěním jejich vstupních bodů. Typy služeb jsou deklarovány na úrovni manifestu a nikoli na úrovni balíčku kódu. Další informace naleznete v tématu [ServiceTypes element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>Element StatelessServiceType
Popisuje bezstavový typ služby. Další informace naleznete v tématu [StatelessServiceType element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>Element CodePackage
Popisuje balíček kódu, který podporuje definovaný typ služby. Při vytváření instance služby proti jednomu z těchto typů služeb jsou všechny balíčky kódu deklarované v tomto manifestu aktivovány spuštěním jejich vstupních bodů. U výsledných procesů se očekává, že se v době běhu zaregistrují podporované typy služeb. Pokud existuje více balíčků kódu, jsou všechny aktivovány vždy, když systém vyhledá některý z deklarovaných typů služeb. Další informace naleznete v tématu [CodePackage element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint – element
Spustitelný soubor určený parametrem EntryPoint je obvykle dlouhodobě běžící hostitel služby. Přítomnost samostatného vstupního bodu instalace zabrání nutnosti spustit hostitele služby s vysokými oprávněními pro delší časová období. Spustitelný soubor určený parametrem EntryPoint je spuštěn po úspěšném ukončení SetupEntryPoint. Výsledný proces se monitoruje a restartuje (znovu začíná SetupEntryPoint), pokud se někdy ukončí nebo dojde k chybě. Další informace naleznete v tématu [EntryPoint element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType) .

### <a name="containerhost-element"></a>Element ContainerHost
Další informace naleznete v tématu [ContainerHost element](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>Element ImageName
Úložiště a obrázek na [https://hub.docker.com](https://hub.docker.com) nebo Azure Container Registry. Další informace naleznete v tématu [ImageName element](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="commands-element"></a>Commands – element
Předat kontejneru čárkami oddělený seznam příkazů. Další informace naleznete v tématu [element Commands](service-fabric-service-model-schema-elements.md#CommandsElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>Element EnvironmentVariables
Předání proměnných prostředí kontejneru nebo exe.  Další informace naleznete v tématu [EnvironmentVariables element](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>Element objekt EnvironmentVariable
Proměnná prostředí. Další informace naleznete v tématu [objekt EnvironmentVariable element](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>Element ConfigPackage
Deklaruje složku, která je pojmenována atributem Name, který obsahuje soubor Settings.xml. Tento soubor obsahuje oddíly nastavení páru klíč-hodnota definované uživatelem, které lze v průběhu běhu číst. Pokud se během upgradu změnila jenom verze ConfigPackage, spuštěný proces se nerestartuje. Místo toho zpětné volání upozorní proces, že došlo ke změně nastavení konfigurace, aby bylo možné je znovu načíst dynamicky. Další informace naleznete v tématu [ConfigPackage element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Resources – element
Popisuje prostředky používané touto službou, které mohou být deklarovány bez úprav zkompilovaného kódu a při nasazení služby změněny. Přístup k těmto prostředkům se řídí pomocí sekcí objekty zabezpečení a zásady v manifestu aplikace. Další informace naleznete v tématu [Resources – element](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Element Endpoints
Definuje koncové body pro službu. Další informace naleznete v tématu [element Endpoints](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Element Endpoint
 Další informace najdete v tématu [element Endpoint](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement) .

