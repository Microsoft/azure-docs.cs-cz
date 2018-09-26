---
title: Azure Service Fabric container aplikace manifestu příklady | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat aplikace a služby manifestu nastavení pro vícekontejnerovou aplikaci Service Fabric.
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
ms.date: 06/08/2018
ms.author: ryanwi
ms.openlocfilehash: 6f538fa821e546d12c5a2bdb9585cc85871241fa
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47094148"
---
# <a name="multi-container-application-and-service-manifest-examples"></a>Příklady manifestu služeb a vícekontejnerové aplikace
Následují příklady manifesty aplikace a služby pro vícekontejnerovou aplikaci Service Fabric. Účelem těchto příkladech je zobrazit nastavení, které jsou k dispozici a jak je používat. Tyto aplikace a služby manifestů jsou založeny na [Windows serveru 2016 kontejneru ukázka](https://github.com/Azure-Samples/service-fabric-containers/tree/master/Windows) manifesty.

Jsou zobrazeny následující funkce:
|Manifest|Funkce|
|---|---|
|[Manifest aplikace](#application-manifest)| [přepsání proměnných prostředí](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [konfigurace mapování portu kontejneru na hostitele](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery), [Nakonfigurujte ověřování registru kontejneru](service-fabric-get-started-containers.md#configure-container-registry-authentication), [zásady správného řízení prostředků](service-fabric-resource-governance.md), [nastavit režim izolace](service-fabric-get-started-containers.md#configure-isolation-mode), [zadejte imagí kontejnerů konkrétní sestavení operačního systému](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)| 
|[Manifest služby FrontEndService](#frontendservice-service-manifest)| [nastavení proměnných prostředí](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [konfigurace koncového bodu](service-fabric-get-started-containers.md#configure-communication), předejte příkazy do kontejneru, [import certifikátu do kontejneru](service-fabric-securing-containers.md)| 
|[Manifest služby BackEndService](#backendservice-service-manifest)|[nastavení proměnných prostředí](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [konfigurace koncového bodu](service-fabric-get-started-containers.md#configure-communication), [nakonfigurovat ovladač svazku](service-fabric-containers-volume-logging-drivers.md)| 

Naleznete v tématu [elementy manifestu aplikace](#application-manifest-elements), [manifestu prvky služeb FrontEndService](#frontendservice-service-manifest-elements), a [manifestu prvky služeb BackEndService](#backendservice-service-manifest-elements) Další informace o konkrétní Elementy XML.

## <a name="application-manifest"></a>Manifest aplikace

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Container.ApplicationType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
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
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
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

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an 
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
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
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

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an 
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

## <a name="application-manifest-elements"></a>Elementy manifestu aplikace
### <a name="applicationmanifest-element"></a>ApplicationManifest – Element
Popisuje deklarativně, typ aplikace a její verzi. Manifesty služby jeden nebo více základních služeb odkazují k vytvoření aplikace typu. Nastavení konfigurace základních služeb lze přepsat pomocí parametrizovaných aplikaci nastavení. Ve výchozím nastavení služeb, šablony služeb, objekty zabezpečení, zásady, nastavení diagnostiky a certifikáty může také deklarovány na úrovni aplikace. Další informace najdete v tématu [ApplicationManifest – Element](service-fabric-service-model-schema-elements.md#ApplicationManifestElementApplicationManifestTypeComplexType)

### <a name="parameters-element"></a>Parametry – Element
Deklaruje parametry, které se používají v tomto manifestu aplikace. Hodnoty těchto parametrů lze zadat, když je vytvořena instance aplikace a můžete použít k přepsání aplikace nebo nastavení konfigurace služby. Další informace najdete v tématu [parametry – Element](service-fabric-service-model-schema-elements.md#ParametersElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="parameter-element"></a>Parameter – Element
Parametr aplikace pro použití v tomto manifestu. Hodnota tohoto parametru můžete změnit během vytvoření instance aplikace, nebo pokud není zadána žádná hodnota bude použita výchozí hodnota. Další informace najdete v tématu [Parameter – Element](service-fabric-service-model-schema-elements.md#ParameterElementanonymouscomplexTypeComplexTypeDefinedInParameterselement)

### <a name="servicemanifestimport-element"></a>ServiceManifestImport – Element
Importuje service manifest vytvořený vývojářem služeb. Manifest služby musí být importovány pro každou základní službu v aplikaci. Přepsání konfigurace a zásad mohou být deklarovány pro manifest služby. Další informace najdete v tématu [ServiceManifestImport – Element](service-fabric-service-model-schema-elements.md#ServiceManifestImportElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="servicemanifestref-element"></a>ServiceManifestRef – Element
Naimportuje manifest služby podle odkazu. Soubor manifestu služby (ServiceManifest.xml) musí být aktuálně k dispozici v balíčku buildu. Další informace najdete v tématu [ServiceManifestRef – Element](service-fabric-service-model-schema-elements.md#ServiceManifestRefElementServiceManifestRefTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="policies-element"></a>Zásady – Element
Popisuje zásady (koncový bod vazby balíčku sdílení, spustit jako a zabezpečení přístupu) má být použita na importovaný manifest služby. Další informace najdete v tématu [zásady – Element](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy – Element
Definuje zásady zásad správného řízení prostředků, která se má použít na úrovni balíčku celé služby. Další informace najdete v tématu [ServicePackageResourceGovernancePolicy – Element](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>ResourceGovernancePolicy – Element
Určuje omezení prostředků pro balíček kódu. Další informace najdete v tématu [ResourceGovernancePolicy – Element](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="containerhostpolicies-element"></a>ContainerHostPolicies – Element
Určuje zásady pro aktivaci hostitelích kontejnerů. Další informace najdete v tématu [ContainerHostPolicies – Element](service-fabric-service-model-schema-elements.md#ContainerHostPoliciesElementContainerHostPoliciesTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="repositorycredentials-element"></a>RepositoryCredentials – Element
Přihlašovací údaje k úložišti imagí kontejneru do o přijetí změn imagí z. Další informace najdete v tématu [RepositoryCredentials – Element](service-fabric-service-model-schema-elements.md#RepositoryCredentialsElementRepositoryCredentialsTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="portbinding-element"></a>PortBinding – Element
Určuje, který prostředek se koncový bod pro vytvoření vazby vystavené port kontejneru. Další informace najdete v tématu [PortBinding – Element](service-fabric-service-model-schema-elements.md#PortBindingElementPortBindingTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="volume-element"></a>Element svazku
Určuje svazku, který má být vázána ke kontejneru. Další informace najdete v tématu [svazek – Element](service-fabric-service-model-schema-elements.md#VolumeElementContainerVolumeTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="driveroption-element"></a>DriverOption – Element
Možnosti ovladače se mají předat ovladače. Další informace najdete v tématu [DriverOption – Element](service-fabric-service-model-schema-elements.md#DriverOptionElementDriverOptionTypeComplexTypeDefinedInContainerLoggingDriverTypecomplexTypeDefinedInContainerVolumeTypecomplexType)

### <a name="imageoverrides-element"></a>ImageOverrides – Element
Kontejnery Windows serveru nemusí být kompatibilní v různých verzích operačního systému.  Můžete zadat více bitových kopií operačního systému na kontejner a jejich označení pomocí verzí sestavení operačního systému. Získání verze sestavení operačního systému spuštěním "winver" na příkazovém řádku Windows. Pokud je základní operační systém sestavení verze 16299 (Windows Server verze 1709), Service Fabric vybere image kontejneru označené operačního systému = "16299". Obrázek neoznačených kontejneru se předpokládá, že fungují na všech verzí operačního systému a přepíše image zadanou v manifestu služby. Další informace najdete v tématu [ImageOverrides – Element](service-fabric-service-model-schema-elements.md#ImageOverridesElementImageOverridesTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="image-element"></a>Elementu obrázku
Image kontejneru, odpovídající číslo verze sestavení operačního systému, která se má spustit. Pokud není zadán atribut operační systém, image kontejneru se předpokládá, že fungují na všech verzí operačního systému a přepíše image zadanou v manifestu služby. Další informace najdete v tématu [elementu obrázku](service-fabric-service-model-schema-elements.md#ImageElementImageTypeComplexTypeDefinedInImageOverridesTypecomplexType)

### <a name="environmentoverrides-element"></a>EnvironmentOverrides – Element
 Další informace najdete v tématu [EnvironmentOverrides – Element](service-fabric-service-model-schema-elements.md#EnvironmentOverridesElementEnvironmentOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="environmentvariable-element"></a>Objekt EnvironmentVariable – Element
proměnné prostředí. Další informace najdete v tématu [EnvironmentVariable – Element](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexTypeDefinedInEnvironmentVariablesTypecomplexType)

### <a name="certificateref-element"></a>CertificateRef – Element
Určuje informace o x X509 certifikát, který má být vystavená pro kontejnerové prostředí. Certifikát musí být nainstalován v úložišti LocalMachine všech uzlů clusteru.
Při spuštění aplikace, modul runtime přečte certifikát a vygeneruje soubor PFX a hesla (ve Windows) nebo soubor PEM (v Linuxu).
Soubor PFX a heslo jsou k dispozici v kontejneru pomocí Certificates_ServicePackageName_CodePackageName_CertName_PFX a Certificates_ServicePackageName_CodePackageName_CertName_Password proměnné prostředí. Soubor PEM, který je dostupný v kontejneru pomocí Certificates_ServicePackageName_CodePackageName_CertName_PEM a Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey proměnné prostředí. Další informace najdete v tématu [CertificateRef – Element](service-fabric-service-model-schema-elements.md#CertificateRefElementContainerCertificateTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="defaultservices-element"></a>DefaultServices – Element
Deklaruje instancí služby, které se automaticky vytvoří pokaždé, když se aplikace je vytvořena instance pro tento typ aplikace. Další informace najdete v tématu [DefaultServices – Element](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Element služby
Deklaruje služba má být vytvořen automaticky při vytváření instance aplikace. Další informace najdete v tématu [Element služby](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statelessservice-element"></a>StatelessService – Element
Definuje bezstavovou službu. Další informace najdete v tématu [StatelessService – Element](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)


## <a name="frontendservice-service-manifest-elements"></a>Prvky manifestu FrontEndService služeb
### <a name="servicemanifest-element"></a>Elementu ServiceManifest
Popisuje deklarativně, typ služby a její verzi. Vypíše nezávisle na sobě je možné upgradovat balíčky kódu, konfigurace a data, které společně tvoří balíček služby pro podporu jeden nebo více typů služeb. Prostředky, nastavení diagnostiky a metadata služby, jako je například typ služby, Vlastnosti stavu a vyrovnávání zatížení metriky jsou také zadat. Další informace najdete v tématu [elementu ServiceManifest](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceType – Element
Definuje, jaké služby jsou podporovány CodePackage v manifestu. Při vytváření instance služby s některou z těchto typů služeb, aktivují se všechny balíčky kódu, které jsou deklarované v manifestu spuštěním jejich vstupní body. Typy služeb jsou deklarované v manifestu úroveň a úroveň typu balíček kódu. Další informace najdete v tématu [ServiceType – Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>StatelessServiceType – Element
Popisuje typ bezstavové služby. Další informace najdete v tématu [StatelessServiceType – Element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage – Element
Popisuje balíček kódu, který podporuje typ služeb. Při vytváření instance služby s některou z těchto typů služeb, aktivují se všechny balíčky kódu, které jsou deklarované v manifestu spuštěním jejich vstupní body. Výsledný procesy, které se očekává, že registrace typů podporovaných služeb v době běhu. Pokud existuje víc balíčků kódu, všechny aktivují se pokaždé, když se systém, hledá pro každý z typy deklarované služby. Další informace najdete v tématu [CodePackage – Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint – Element
Spustitelný soubor určený vstupní bod se obvykle dlouhotrvající hostitele služby. Přítomnost vstupní bod samostatného instalačního díky tomu není nutné ke spuštění hostitele služby s vysokou úrovní oprávnění pro dlouhou dobu. Spustitelný soubor určený vstupní bod se spustí po ukončení SetupEntryPoint úspěšně. Výsledný proces je monitorována a restartuje (začíná znovu SetupEntryPoint), pokud někdy ukončí nebo dojde k chybě. Další informace najdete v tématu [EntryPoint – Element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>ContainerHost – Element
 Další informace najdete v tématu [ContainerHost – Element](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>ImageName – Element
Úložiště a na image https://hub.docker.com nebo Azure Container Registry. Další informace najdete v tématu [ImageName – Element](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>EnvironmentVariables – Element
Předání proměnných prostředí kontejneru nebo souboru exe.  Další informace najdete v tématu [EnvironmentVariables – Element](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>Objekt EnvironmentVariable – Element
proměnné prostředí. Další informace najdete v tématu [EnvironmentVariable – Element](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexTypeDefinedInEnvironmentVariablesTypecomplexType)

### <a name="configpackage-element"></a>Složce ConfigPackage – Element
Deklaruje do složky s názvem podle atribut Name, obsahující souboru Settings.xml. Tento soubor obsahuje oddíly pár definovaný uživatelem, klíč hodnota nastavení, které proces může číst zpět v době běhu. Během upgradu pokud pouze složce ConfigPackage verze se změnila, pak spuštěný proces se nerestartuje. Místo toho zpětné volání upozornění procesu tak, že je možné znovu zavést dynamicky se nezměnila konfigurační nastavení. Další informace najdete v tématu [složce ConfigPackage – Element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>DataPackage – Element
Deklaruje složku s názvem atributem název, který obsahuje statické datové soubory. Service Fabric se recyklaci všech souborů exe a DLLHOSTs zadali v balíčcích hostitele a podporu při všech dat balíčky uvedené v manifestu služby budou upgradovány. Další informace najdete v tématu [DataPackage – Element](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Element prostředky
Popisuje prostředky využívané třídou této služby, které je možné deklarovat beze změny zkompilovaného kódu a změnit, pokud je služba nasazená. Přístup k těmto prostředkům je řízen pomocí objekty zabezpečení a zásady části manifestu aplikace. Další informace najdete v tématu [elementu Resources](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Koncové body – Element
Definuje koncové body pro službu. Další informace najdete v tématu [koncové body – Element](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Element koncového bodu
Další informace najdete v tématu [Element koncového bodu](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)


## <a name="backendservice-service-manifest-elements"></a>Prvky manifestu BackEndService služeb
### <a name="servicemanifest-element"></a>Elementu ServiceManifest
Popisuje deklarativně, typ služby a její verzi. Vypíše nezávisle na sobě je možné upgradovat balíčky kódu, konfigurace a data, které společně tvoří balíček služby pro podporu jeden nebo více typů služeb. Prostředky, nastavení diagnostiky a metadata služby, jako je například typ služby, Vlastnosti stavu a vyrovnávání zatížení metriky jsou také zadat. Další informace najdete v tématu [elementu ServiceManifest](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceType – Element
Definuje, jaké služby jsou podporovány CodePackage v manifestu. Při vytváření instance služby s některou z těchto typů služeb, aktivují se všechny balíčky kódu, které jsou deklarované v manifestu spuštěním jejich vstupní body. Typy služeb jsou deklarované v manifestu úroveň a úroveň typu balíček kódu. Další informace najdete v tématu [ServiceType – Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>StatelessServiceType – Element
Popisuje typ bezstavové služby. Další informace najdete v tématu [StatelessServiceType – Element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage – Element
Popisuje balíček kódu, který podporuje typ služeb. Při vytváření instance služby s některou z těchto typů služeb, aktivují se všechny balíčky kódu, které jsou deklarované v manifestu spuštěním jejich vstupní body. Výsledný procesy, které se očekává, že registrace typů podporovaných služeb v době běhu. Pokud existuje víc balíčků kódu, všechny aktivují se pokaždé, když se systém, hledá pro každý z typy deklarované služby. Další informace najdete v tématu [CodePackage – Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint – Element
Spustitelný soubor určený vstupní bod se obvykle dlouhotrvající hostitele služby. Přítomnost vstupní bod samostatného instalačního díky tomu není nutné ke spuštění hostitele služby s vysokou úrovní oprávnění pro dlouhou dobu. Spustitelný soubor určený vstupní bod se spustí po ukončení SetupEntryPoint úspěšně. Výsledný proces je monitorována a restartuje (začíná znovu SetupEntryPoint), pokud někdy ukončí nebo dojde k chybě. Další informace najdete v tématu [EntryPoint – Element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>ContainerHost – Element
Další informace najdete v tématu [ContainerHost – Element](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>ImageName – Element
Úložiště a na image https://hub.docker.com nebo Azure Container Registry. Další informace najdete v tématu [ImageName – Element](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="commands-element"></a>Commands – Element
Čárkami oddělený seznam příkazů předáte do kontejneru. Další informace najdete v tématu [Commands – Element](service-fabric-service-model-schema-elements.md#CommandsElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>EnvironmentVariables – Element
Předání proměnných prostředí kontejneru nebo souboru exe.  Další informace najdete v tématu [EnvironmentVariables – Element](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>Objekt EnvironmentVariable – Element
proměnné prostředí. Další informace najdete v tématu [EnvironmentVariable – Element](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexTypeDefinedInEnvironmentVariablesTypecomplexType)

### <a name="configpackage-element"></a>Složce ConfigPackage – Element
Deklaruje do složky s názvem podle atribut Name, obsahující souboru Settings.xml. Tento soubor obsahuje oddíly pár definovaný uživatelem, klíč hodnota nastavení, které proces může číst zpět v době běhu. Během upgradu pokud pouze složce ConfigPackage verze se změnila, pak spuštěný proces se nerestartuje. Místo toho zpětné volání upozornění procesu tak, že je možné znovu zavést dynamicky se nezměnila konfigurační nastavení. Další informace najdete v tématu [složce ConfigPackage – Element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Element prostředky
Popisuje prostředky využívané třídou této služby, které je možné deklarovat beze změny zkompilovaného kódu a změnit, pokud je služba nasazená. Přístup k těmto prostředkům je řízen pomocí objekty zabezpečení a zásady části manifestu aplikace. Další informace najdete v tématu [elementu Resources](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Koncové body – Element
Definuje koncové body pro službu. Další informace najdete v tématu [koncové body – Element](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Element koncového bodu
 Další informace najdete v tématu [Element koncového bodu](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

