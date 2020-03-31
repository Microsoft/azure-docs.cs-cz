---
title: Příklady manifestu kontejneru Azure Service Fabric
description: Zjistěte, jak nakonfigurovat nastavení manifestu aplikací a služeb pro aplikaci Service Fabric s více kontejnery.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/08/2018
ms.author: pepogors
ms.openlocfilehash: 5efdbd17db20e69ace33b379ddbb99b2c4a20e69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258392"
---
# <a name="multi-container-application-and-service-manifest-examples"></a>Příklady manifestu služeb a vícekontejnerové aplikace
Následují příklady manifestů aplikace a služby pro aplikaci Service Fabric s více kontejnery. Účelem těchto příkladů je ukázat, jaká nastavení jsou k dispozici a jak je používat. Tyto manifesty aplikací a služeb jsou založeny na [manifestech ukázky kontejneru systému Windows Server 2016.](https://github.com/Azure-Samples/service-fabric-containers/tree/master/Windows)

Jsou zobrazeny následující funkce:

|Manifest|Funkce|
|---|---|
|[Manifest aplikace](#application-manifest)| [přepsat proměnné prostředí](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [konfigurovat mapování mezi porty kontejneru](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery), [konfigurovat ověřování registru kontejnerů](service-fabric-get-started-containers.md#configure-container-repository-authentication), [zásady správného řízení prostředků](service-fabric-resource-governance.md), nastavit režim [izolace](service-fabric-get-started-containers.md#configure-isolation-mode), [zadat ifotky kontejnerů specifických pro sestavení operačního systému](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)| 
|[Manifest služby FrontEndService](#frontendservice-service-manifest)| [nastavit proměnné prostředí](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [nakonfigurovat koncový bod](service-fabric-get-started-containers.md#configure-communication), předat příkazy do kontejneru, [importovat certifikát do kontejneru](service-fabric-securing-containers.md)| 
|[Manifest služby BackEndService](#backendservice-service-manifest)|[nastavení proměnných prostředí](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [konfigurace koncového bodu](service-fabric-get-started-containers.md#configure-communication), konfigurace ovladače [svazku](service-fabric-containers-volume-logging-drivers.md)| 

Další informace o [konkrétních elementech](#application-manifest-elements)XML naleznete v tématu Application manifest elements , [FrontEndService service manifest elements](#frontendservice-service-manifest-elements)a [BackEndService service manifest.](#backendservice-service-manifest-elements)

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

### <a name="policies-element"></a>Prvek zásad
Popisuje zásady (vazba koncového bodu, sdílení balíčků, run-as a přístup k zabezpečení), které mají být použity v manifestu importované služby. Další informace naleznete v tématu [Zásady Element](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy Element
Definuje zásady zásady zásady správného řízení prostředků, které se používají na úrovni celého balíčku služeb. Další informace naleznete v tématu [ServicePackageResourceGovernancePolicy Element](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>ResourceGovernancePolicy Element
Určuje omezení prostředků pro balíček kódu. Další informace naleznete v tématu [ResourceGovernancePolicy Element](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="containerhostpolicies-element"></a>ContainerHostPolicies Element
Určuje zásady pro aktivaci hostitelů kontejnerů. Další informace naleznete v [tématu ContainerHostPolicies Element](service-fabric-service-model-schema-elements.md#ContainerHostPoliciesElementContainerHostPoliciesTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="repositorycredentials-element"></a>RepositoryCredentials Element
Pověření pro úložiště bitové kopie kontejneru pro vyžádat obrázky z. Další informace naleznete v [tématu RepositoryCredentials Element](service-fabric-service-model-schema-elements.md#RepositoryCredentialsElementRepositoryCredentialsTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="portbinding-element"></a>PortBinding Element
Určuje, který prostředek koncového bodu se má vázat na nechráněný port kontejneru. Další informace naleznete v tématu [PortBinding Element](service-fabric-service-model-schema-elements.md#PortBindingElementPortBindingTypeComplexTypeDefinedInServicePackageContainerPolicyTypecomplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="volume-element"></a>Element svazku
Určuje svazek, který má být vázán na kontejner. Další informace naleznete v tématu [Volume Element](service-fabric-service-model-schema-elements.md#VolumeElementContainerVolumeTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="driveroption-element"></a>DriverOption Element
Možnosti ovladače, které mají být předány řidiči. Další informace naleznete v tématu [DriverOption Element](service-fabric-service-model-schema-elements.md#DriverOptionElementDriverOptionTypeComplexTypeDefinedInContainerLoggingDriverTypecomplexTypeDefinedInContainerVolumeTypecomplexType)

### <a name="imageoverrides-element"></a>Prvek ImageOverrides
Kontejnery systému Windows Server nemusí být kompatibilní v různých verzích operačního systému.  Můžete zadat více bitových kopií operačního systému na kontejner a označit je verzemi sestavení operačního systému. Získejte verzi operačního systému sestavení spuštěním příkazu Winver na příkazovém řádku systému Windows. Pokud je základní operační systém sestavení verze 16299 (Windows Server verze 1709), Service Fabric vybere image kontejneru označené Os = "16299". Netagovaný image kontejneru se předpokládá, že pracovat ve všech verzích operačního systému a přepíše image zadané v manifestu služby. Další informace naleznete v tématu [ImageOverrides Element](service-fabric-service-model-schema-elements.md#ImageOverridesElementImageOverridesTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="image-element"></a>Prvek obrázku
Image kontejneru odpovídající číslo verze sestavení operačního systému, které má být spuštěno. Pokud není zadán atribut Operačního systému, předpokládá se, že image kontejneru bude fungovat ve všech verzích operačního systému a přepíše bitovou kopii zadanou v manifestu služby. Další informace naleznete v [tématu Image Element](service-fabric-service-model-schema-elements.md#ImageElementImageTypeComplexTypeDefinedInImageOverridesTypecomplexType)

### <a name="environmentoverrides-element"></a>Prvek EnvironmentOverrides
 Další informace naleznete v tématu [EnvironmentOverrides Element](service-fabric-service-model-schema-elements.md#EnvironmentOverridesElementEnvironmentOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="environmentvariable-element"></a>Proměnlivý prvek prostředí
Proměnná prostředí. Další informace naleznete v tématu [EnvironmentVariable Element](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="certificateref-element"></a>CertificateRef Element
Určuje informace o certifikátu X509, který má být vystaven prostředí kontejneru. Certifikát musí být nainstalován v úložišti LocalMachine všech uzlů clusteru.
Při spuštění aplikace runtime přečte certifikát a vygeneruje soubor PFX a heslo (v systému Windows) nebo soubor PEM (na Linuxu).
Soubor A heslo PFX jsou přístupné v kontejneru pomocí Certificates_ServicePackageName_CodePackageName_CertName_PFX a Certificates_ServicePackageName_CodePackageName_CertName_Password proměnné prostředí. Soubor PEM je přístupný v kontejneru pomocí Certificates_ServicePackageName_CodePackageName_CertName_PEM a Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey proměnné prostředí. Další informace naleznete v tématu [CertificateRef Element](service-fabric-service-model-schema-elements.md#CertificateRefElementContainerCertificateTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="defaultservices-element"></a>DefaultServices Element
Deklaruje instance služby, které jsou automaticky vytvořeny vždy, když je vytvořena instance aplikace proti tomuto typu aplikace. Další informace naleznete v tématu [DefaultServices Element](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Prvek služby
Deklaruje službu, která má být vytvořena automaticky při vytvoření instance aplikace. Další informace naleznete v tématu [Service Element](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statelessservice-element"></a>Prvek bezstavové služby
Definuje bezstavovou službu. Další informace naleznete v tématu [StatelessService Element](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)


## <a name="frontendservice-service-manifest-elements"></a>Elementy manifestu služby FrontEndService
### <a name="servicemanifest-element"></a>ServiceManifest Element
Deklarativně popisuje typ a verzi služby. Obsahuje seznam nezávisle rozšiřitelných balíčků kódu, konfigurace a dat, které společně tvoří balíček služeb pro podporu jednoho nebo více typů služeb. Prostředky, nastavení diagnostiky a metadata služby, jako je například typ služby, vlastnosti stavu a metriky vyrovnávání zatížení, jsou také určeny. Další informace naleznete v tématu [ServiceManifest Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes Element
Definuje, jaké typy služeb jsou podporovány CodePackage v tomto manifestu. Při vytvoření instance služby proti jednomu z těchto typů služeb jsou všechny balíčky kódu deklarované v tomto manifestu aktivovány spuštěním vstupních bodů. Typy služeb jsou deklarovány na úrovni manifestu a nikoli na úrovni balíčku kódu. Další informace naleznete v tématu [ServiceTypes Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>StatelessServiceType Element
Popisuje typ bezstavové služby. Další informace naleznete v tématu [StatelessServiceType Element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage Element
Popisuje balíček kódu, který podporuje definovaný typ služby. Při vytvoření instance služby proti jednomu z těchto typů služeb jsou všechny balíčky kódu deklarované v tomto manifestu aktivovány spuštěním vstupních bodů. Očekává se, že výsledné procesy budou registrovat podporované typy služeb za běhu. Pokud existuje více balíčků kódu, jsou všechny aktivovány vždy, když systém hledá některý z deklarovaných typů služeb. Další informace naleznete v tématu [CodePackage Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint Element
Spustitelný soubor určený entrypointem je obvykle dlouhotrvající hostitel služby. Přítomnost samostatného vstupního bodu nastavení zabraňuje nutnosti spouštět hostitele služby s vysokými oprávněními po delší dobu. Spustitelný soubor určený službou EntryPoint je spuštěn po úspěšném ukončení programu SetupEntryPoint. Výsledný proces je sledován a restartován (počínaje znovu setupEntryPoint), pokud někdy ukončí nebo dojde k chybě. Další informace naleznete v tématu [EntryPoint Element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>ContainerHost Element
 Další informace naleznete v [tématu ContainerHost Element](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>ImageName Element
Repo a image [https://hub.docker.com](https://hub.docker.com) na nebo Registru kontejnerů Azure. Další informace naleznete v tématu [ImageName Element](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>EnvironmentVariables Element
Předejte proměnné prostředí do kontejneru nebo exe.  Další informace naleznete v tématu [EnvironmentVariables Element](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>Proměnlivý prvek prostředí
Proměnná prostředí. Další informace naleznete v tématu [EnvironmentVariable Element](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>Prvek configPackage
Deklaruje složku pojmenovanou podle atributu Name, která obsahuje soubor Settings.xml. Tento soubor obsahuje části uživatelem definované nastavení páru klíč-hodnota, které může proces číst zpět za běhu. Pokud se během inovace změnila pouze verze ConfigPackage, nebude spuštěný proces restartován. Místo toho zpětné volání upozorní proces, že nastavení konfigurace se změnily tak, aby mohly být znovu načteny dynamicky. Další informace naleznete v tématu [ConfigPackage Element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>Element datového balíčku
Deklaruje složku pojmenovanou atributem Name, která obsahuje statické datové soubory. Service Fabric bude recyklovat všechny EXEs a DLLHOSTs zadané v hostitelských a podpůrných balíčků při upgradu některého z datových balíčků uvedených v manifestu služby. Další informace naleznete v tématu [DataPackage Element](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Prvek zdroje
Popisuje prostředky používané touto službou, které lze deklarovat bez úpravy kompilovaného kódu a změnit při nasazení služby. Přístup k těmto prostředkům je řízen prostřednictvím oddílů Objekty zásad a zásady manifestu aplikace. Další informace naleznete v tématu [Resources Element](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Element koncových bodů
Definuje koncové body pro službu. Další informace naleznete [v tématu Prvek koncových bodů](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Element koncového bodu
Další informace naleznete [v tématu Endpoint Element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)


## <a name="backendservice-service-manifest-elements"></a>Elementy manifestu služby BackEndService
### <a name="servicemanifest-element"></a>ServiceManifest Element
Deklarativně popisuje typ a verzi služby. Obsahuje seznam nezávisle rozšiřitelných balíčků kódu, konfigurace a dat, které společně tvoří balíček služeb pro podporu jednoho nebo více typů služeb. Prostředky, nastavení diagnostiky a metadata služby, jako je například typ služby, vlastnosti stavu a metriky vyrovnávání zatížení, jsou také určeny. Další informace naleznete v tématu [ServiceManifest Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes Element
Definuje, jaké typy služeb jsou podporovány CodePackage v tomto manifestu. Při vytvoření instance služby proti jednomu z těchto typů služeb jsou všechny balíčky kódu deklarované v tomto manifestu aktivovány spuštěním vstupních bodů. Typy služeb jsou deklarovány na úrovni manifestu a nikoli na úrovni balíčku kódu. Další informace naleznete v tématu [ServiceTypes Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>StatelessServiceType Element
Popisuje typ bezstavové služby. Další informace naleznete v tématu [StatelessServiceType Element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage Element
Popisuje balíček kódu, který podporuje definovaný typ služby. Při vytvoření instance služby proti jednomu z těchto typů služeb jsou všechny balíčky kódu deklarované v tomto manifestu aktivovány spuštěním vstupních bodů. Očekává se, že výsledné procesy budou registrovat podporované typy služeb za běhu. Pokud existuje více balíčků kódu, jsou všechny aktivovány vždy, když systém hledá některý z deklarovaných typů služeb. Další informace naleznete v tématu [CodePackage Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint Element
Spustitelný soubor určený entrypointem je obvykle dlouhotrvající hostitel služby. Přítomnost samostatného vstupního bodu nastavení zabraňuje nutnosti spouštět hostitele služby s vysokými oprávněními po delší dobu. Spustitelný soubor určený službou EntryPoint je spuštěn po úspěšném ukončení programu SetupEntryPoint. Výsledný proces je sledován a restartován (počínaje znovu setupEntryPoint), pokud někdy ukončí nebo dojde k chybě. Další informace naleznete v tématu [EntryPoint Element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>ContainerHost Element
Další informace naleznete v [tématu ContainerHost Element](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>ImageName Element
Repo a image [https://hub.docker.com](https://hub.docker.com) na nebo Registru kontejnerů Azure. Další informace naleznete v tématu [ImageName Element](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="commands-element"></a>Commands – element
Předají kontejneru seznam příkazů oddělených čárkou. Další informace naleznete v tématu [Commands Element](service-fabric-service-model-schema-elements.md#CommandsElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>EnvironmentVariables Element
Předejte proměnné prostředí do kontejneru nebo exe.  Další informace naleznete v tématu [EnvironmentVariables Element](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>Proměnlivý prvek prostředí
Proměnná prostředí. Další informace naleznete v tématu [EnvironmentVariable Element](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>Prvek configPackage
Deklaruje složku pojmenovanou podle atributu Name, která obsahuje soubor Settings.xml. Tento soubor obsahuje části uživatelem definované nastavení páru klíč-hodnota, které může proces číst zpět za běhu. Pokud se během inovace změnila pouze verze ConfigPackage, nebude spuštěný proces restartován. Místo toho zpětné volání upozorní proces, že nastavení konfigurace se změnily tak, aby mohly být znovu načteny dynamicky. Další informace naleznete v tématu [ConfigPackage Element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Prvek zdroje
Popisuje prostředky používané touto službou, které lze deklarovat bez úpravy kompilovaného kódu a změnit při nasazení služby. Přístup k těmto prostředkům je řízen prostřednictvím oddílů Objekty zásad a zásady manifestu aplikace. Další informace naleznete v tématu [Resources Element](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Element koncových bodů
Definuje koncové body pro službu. Další informace naleznete [v tématu Prvek koncových bodů](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Element koncového bodu
 Další informace naleznete [v tématu Endpoint Element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

