---
title: Určení koncových bodů služeb Service Fabric | Dokumentace Microsoftu
description: Tom, jak popisují koncový bod prostředků v manifestu služby, jak nastavit koncové body HTTPS
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: da36cbdb-6531-4dae-88e8-a311ab71520d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 3a796733f5987f4cc550a606e06166395d1595cc
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55156648"
---
# <a name="specify-resources-in-a-service-manifest"></a>Zadání prostředků v manifestu služby
## <a name="overview"></a>Přehled
Manifest služby umožňuje prostředky, které používají službu být deklarován nebo upravené beze změny zkompilovaný kód. Azure Service Fabric podporuje konfiguraci koncového bodu prostředků pro službu. Přes skupiny SecurityGroup v manifestu aplikace se dá řídit přístup k prostředkům, které jsou určené v manifestu služby. Deklarace prostředků umožňuje změnit v době nasazení, což znamená, že službu nemusí zavést nový mechanismus konfigurace těchto prostředků. Definice schématu pro soubor ServiceManifest.xml se instaluje se sadou Service Fabric SDK a nástrojů k *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

## <a name="endpoints"></a>Koncové body
Když prostředek koncového bodu je definovaný v manifestu služby, Service Fabric přiřazuje porty z rozsahu portů aplikace vyhrazené, pokud není explicitně zadán port. Například, podívejte se na koncový bod *ServiceEndpoint1* zadaný v manifestu fragmentu kódu, které jsou uvedené za tímto odstavcem. Služby mohou také požadovat konkrétní port v prostředku. Služba replik spuštěných v různých uzlech je možné přiřadit různá čísla portů, zatímco repliky služby spuštěné na stejném uzlu sdílet port. Repliky služby potom můžete pomocí těchto portů podle potřeby pro replikaci a naslouchá požadavkům klientů.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Pokud existuje víc balíčků kódu v jedné službě balíčku, pak balíček kódu se také musí odkazovat **koncové body** oddílu.  Například pokud **ServiceEndpoint2a** a **ServiceEndpoint2b** jsou koncové body služby balíčkem stejné odkazující na různý kód balíčky balíček kódu odpovídající každý koncový bod je obsahuje upřesnění následujícím způsobem:

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

Odkazovat na [konfigurace stavovém modelu Reliable Services](service-fabric-reliable-services-configuration.md) přečíst další informace o odkazování na koncové body z nastavení souboru config balíček soubor (settings.xml).

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Příklad: zadání koncového bodu HTTP pro vaši službu
Následující manifest služby definuje jeden prostředek koncového bodu TCP a dva prostředky koncový bod protokolu HTTP v &lt;prostředky&gt; elementu.

Koncové body HTTP jsou automaticky že ACL by Service Fabric.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
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

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Příklad: určení koncový bod HTTPS pro vaši službu
Protokol HTTPS zajišťuje ověřování na server a slouží také k šifrování komunikace klient server. Pokud chcete povolit HTTPS pro vaši službu Service Fabric, zadejte v protokolu *prostředků -> Koncové body -> koncový bod* manifestu služby, jak je uvedeno výše pro koncový bod *ServiceEndpoint3*.

> [!NOTE]
> Protokol služby nelze změnit během upgradu aplikace. Pokud se změní při upgradu, je zásadní změnu.
> 

> [!WARNING] 
> Při použití protokolu HTTPS, nepoužívejte stejný port a certifikát pro instance různé služby (nezávisle na aplikaci) nasadí do stejného uzlu. Upgrade dvou různých služeb pomocí stejný port v různé instance aplikace povede k selhání upgradu. Další informace najdete v tématu [upgrade více aplikací pomocí koncových bodů HTTPS ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
>

Tady je příklad ApplicationManifest, který je potřeba nastavit pro protokol HTTPS. Musí být zadaný kryptografický otisk certifikátu. EndpointRef je odkaz na EndpointResource v souboru ServiceManifest, u kterého jste nastavili protokol HTTPS. Můžete přidat více než jeden EndpointCertificate.  

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
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

Pro clustery s Linuxem **MY** uložení výchozích hodnot do složky **/var/lib/sfcerts**.


## <a name="overriding-endpoints-in-servicemanifestxml"></a>Koncové body v souboru ServiceManifest.xml přepsání

V manifestu aplikace přidáte ResourceOverrides oddílu, který může mít na stejné úrovni ConfigOverrides části. V této části můžete zadat přepsání pro části koncové body v části prostředky určená v manifestu služby. Přepsání koncové body se podporuje v modulu runtime 5.7.217/SDK 2.7.217 a vyšší.

Za účelem přepsání koncového bodu v souboru ServiceManifest pomocí ApplicationParameters změnit ApplicationManifest následujícím způsobem:

V části ServiceManifestImport přidejte nový oddíl "ResourceOverrides".

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

V parametrech přidejte níže:

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

Při nasazení aplikace můžete předat tyto hodnoty jako ApplicationParameters.  Příklad:

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Poznámka: Pokud hodnoty ApplicationParameters je prázdný, vrátíme výchozí hodnotu pro odpovídající Název_koncového_bodu k dispozici v souboru ServiceManifest.

Příklad:

Pokud se v souboru ServiceManifest jste zadali

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

A jejich Port1 a Protocol1 hodnoty pro parametry aplikace je null nebo prázdný. Port, který je stále rozhodnuto pomocí ServiceFabric. A bude protokol tcp.

Předpokládejme, že zadáte chybnou hodnotu. Například pro Port jste zadali hodnotu řetězce "Foo" místo celé číslo  Nové ServiceFabricApplication příkaz se nezdaří s chybou: Parametr přepsání s atributem "ServiceEndpoint1" název "Port1" v části 'ResourceOverrides' je neplatný. Zadaná hodnota je "Foo" a "int" vyžaduje se.
