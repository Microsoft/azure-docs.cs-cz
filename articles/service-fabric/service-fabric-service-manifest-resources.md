---
title: Určení koncových bodů služby Service Fabric
description: Popis prostředků koncového bodu v manifestu služby, včetně postupu nastavení koncových bodů HTTPS
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: cc4eedf5e5fee0bbfa0a763e9b9ec0dd25409afa
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79282156"
---
# <a name="specify-resources-in-a-service-manifest"></a>Určení prostředků v manifestu služby
## <a name="overview"></a>Přehled
Manifest služby umožňuje deklaraci a změnu prostředků používaných službou bez změny zkompilovaného kódu. Azure Service Fabric podporuje konfiguraci prostředků koncového bodu pro službu. Přístup k prostředkům, které jsou zadány v manifestu služby, lze ovládat prostřednictvím služby zabezpečení v manifestu aplikace. Deklarace prostředků umožňuje změnit tyto prostředky v době nasazení, což znamená, že služba nemusí zavést nový konfigurační mechanismus. Definice schématu pro soubor ServiceManifest. XML je nainstalovaná s Service Fabric SDK a nástroji do složky *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

## <a name="endpoints"></a>Koncové body
Pokud je prostředek koncového bodu definovaný v manifestu služby, Service Fabric přiřadí porty z rezervovaného rozsahu portů aplikace, když není explicitně zadaný port. Podívejte se například na koncový bod *ServiceEndpoint1* zadaný v fragmentu manifestu, který je k dispozici po tomto odstavci. Kromě toho můžou služby také požadovat konkrétní port v prostředku. K replikám služby spuštěným v různých uzlech clusteru je možné přiřadit různá čísla portů, zatímco repliky služby spuštěné ve stejném uzlu sdílejí port. Repliky služby pak můžou tyto porty použít pro replikaci a naslouchání požadavkům klientů.

> [!WARNING] 
> Návrhem statických portů se nesmí překrývat s rozsahem portů aplikace zadaným v manifestem clusteru. Pokud zadáte statický port, přiřaďte ho mimo rozsah portů aplikace, jinak bude výsledkem konflikty portů. S vydáním verze 6.5 CU2 budeme při zjišťování takového konfliktu vystavovat **Upozornění na stav** , ale nasazení bude pokračovat v synchronizaci s dodaným chováním 6,5. Můžeme ale zabránit nasazení aplikace z dalších hlavních verzí.
>
> S vydáním verze 7,0 budeme vystavovat **Upozornění na stav** , když zjistíme, že využití rozsahu portů aplikace bude vyšší než HostingConfig:: ApplicationPortExhaustThresholdPercentage (výchozí 80%).
>

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Pokud existuje více balíčků kódu v jednom balíčku služby, pak musí být balíček kódu také odkazován v oddílu **Endpoints** .  Například pokud jsou **ServiceEndpoint2a** a **ServiceEndpoint2b** koncovými body ze stejného balíčku služby odkazujícím na různé balíčky kódu, je balíček kódu odpovídající jednotlivým koncovým bodům vyjasněný následujícím způsobem:

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

Další informace o odkazování koncových bodů ze souboru nastavení balíčku konfigurace (Settings. XML) najdete v článku [Konfigurace stavového Reliable Services](service-fabric-reliable-services-configuration.md) .

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Příklad: určení koncového bodu HTTP pro vaši službu
Následující manifest služby definuje jeden prostředek koncového bodu TCP a dva prostředky koncového bodu HTTP v&gt; elementu &lt;ch prostředků.

Koncové body HTTP jsou automaticky ACL Service Fabric.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Příklad: určení koncového bodu HTTPS pro vaši službu
Protokol HTTPS zajišťuje ověřování serveru a používá se také k šifrování komunikace mezi klientem a serverem. Pokud chcete ve službě Service Fabric povolit protokol HTTPS, zadejte protokol v části *Resources-> Endpoints – > koncový bod* manifestu služby, jak je uvedeno výše pro koncový bod *ServiceEndpoint3*.

> [!NOTE]
> Protokol služby se během upgradu aplikace nedá změnit. Pokud dojde ke změně během upgradu, jedná se o zásadní změnu.
> 

> [!WARNING] 
> Při použití protokolu HTTPS nepoužívejte stejný port a certifikát pro různé instance služby (nezávisle na aplikaci) nasazené do stejného uzlu. Upgrade dvou různých služeb pomocí stejného portu v různých instancích aplikace způsobí selhání při upgradu. Další informace najdete v tématu [upgrade více aplikací s koncovými body https ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
>

Tady je příklad souboru ApplicationManifest, který je potřeba nastavit pro protokol HTTPS. Je nutné zadat kryptografický otisk pro certifikát. EndpointRef je odkaz na EndpointResource v ServiceManifest, pro který jste nastavili protokol HTTPS. Můžete přidat více než jeden EndpointCertificate.  

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint3"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_ ]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="TestCert1" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

V případě clusterů se systémem Linux je **Moje** úložiště standardně nastaveno na složku **/var/lib/sfcerts**.


## <a name="overriding-endpoints-in-servicemanifestxml"></a>Přepsání koncových bodů v souboru ServiceManifest. XML

V části souboru ApplicationManifest přidejte oddíl ResourceOverrides, který bude mít oddíl na stejné úrovni jako ConfigOverrides. V této části můžete zadat přepsání pro oddíl Endpoints v oddílu Resources, který je zadaný v manifestu služby. Přepsání koncových bodů je podporováno v modulu runtime 5.7.217/SDK 2.7.217 a vyšších.

Aby bylo možné přepsat koncový bod v ServiceManifest pomocí ApplicationParameters, změňte souboru ApplicationManifest následujícím způsobem:

V části ServiceManifestImport přidejte novou část "ResourceOverrides".

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="ServiceEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
        <Endpoint Name="ServiceEndpoint1" Port="[Port1]" Protocol="[Protocol1] "/>
      </Endpoints>
    </ResourceOverrides>
        <Policies>
           <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint"/>
        </Policies>
  </ServiceManifestImport>
```

Do pole níže přidejte následující parametry:

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

Při nasazování aplikace můžete tyto hodnoty předat jako ApplicationParameters.  Příklad:

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Poznámka: Pokud jsou hodnoty zadané pro ApplicationParameters prázdné, vrátíme výchozí hodnotu poskytnutou v ServiceManifest pro odpovídající koncové číslo koncového bodu.

Příklad:

Pokud jste zadali ServiceManifest

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

A hodnota Port1 a Protocol1 pro parametry aplikace je null nebo prázdná. Port se stále určuje pomocí ServiceFabric. A protokol bude TCP.

Předpokládejme, že zadáváte nesprávnou hodnotu. Podobně jako u portu jste zadali řetězcovou hodnotu "foo" namísto int.  Příkaz New-ServiceFabricApplication selže s chybou: parametr override s názvem ' ServiceEndpoint1 ' atributu ' PORT1 ' v oddílu ' ResourceOverrides ' je neplatný. Zadaná hodnota je ' foo ' a požadovaná je ' int '.
