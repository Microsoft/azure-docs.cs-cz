---
title: Určení koncové body služby Service Fabric | Microsoft Docs
description: Postupy popisují koncový bod prostředků v service manifest, včetně toho, jak nastavit koncové body HTTPS
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
ms.openlocfilehash: f486ce5c058286289873d87767f02bf92f91459e
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34701438"
---
# <a name="specify-resources-in-a-service-manifest"></a>Zadání prostředků v manifestu služby
## <a name="overview"></a>Přehled
Manifest služby umožňuje prostředky, které používají službu být deklarován či změnit beze změny zkompilovaný kód. Azure Service Fabric podporuje konfiguraci prostředků koncový bod pro službu. Přístup k prostředkům, které jsou určené v service manifest lze řídit prostřednictvím objektu SecurityGroup v manifestu aplikace. Prohlášení o prostředky umožňuje tyto prostředky se musí změnit v době nasazení, což znamená, že služba nepotřebuje zavést nové mechanismus konfigurace. Definice schématu pro soubor ServiceManifest.xml je nainstalován pomocí Service Fabric SDK a nástroje k *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

## <a name="endpoints"></a>Koncové body
Pokud prostředek koncového bodu je definován v service manifest, Service Fabric přiřazuje porty z rozsahu portů vyhrazené aplikace, pokud není port určen explicitně. Například, podívejte se na koncový bod *ServiceEndpoint1* zadané v manifestu fragmentu kódu zadaný po tomto odstavci. Služby mohou také požadovat specifického portu v prostředku. Repliky služby spuštěné v různých uzly lze přiřadit různá čísla portů, zatímco repliky služby spuštěné na stejném uzlu sdílet port. Repliky služby pak můžete použít tyto porty podle potřeby pro replikaci a naslouchá pro požadavky klientů.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Pokud existuje víc balíčků kódu v balíčku služby jednotného, pak balíček kódu se taky musí odkazovat ve **koncové body** části.  Například pokud **ServiceEndpoint2a** a **ServiceEndpoint2b** jsou koncové body ze stejného balíčku služby odkazující na jiný kód balíčky balíček kódu odpovídající každý koncový bod je vyjasněno, následujícím způsobem:

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

Odkazovat na [konfigurace stavová spolehlivé služby](service-fabric-reliable-services-configuration.md) číst informace o odkazování na koncové body z nastavení souboru config balíčku souboru (souborech settings.xml).

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Příklad: určení HTTP koncový bod služby
Následující service manifest definuje jeden prostředek koncový bod TCP a dva prostředky koncový bod HTTP v &lt;prostředky&gt; elementu.

Koncové body protokolu HTTP jsou automaticky že seznamu ACL by pomocí Service Fabric.

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

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an
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

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Příklad: určení koncový bod HTTPS pro služby
Protokol HTTPS zajišťuje ověřování na serveru a slouží pro komunikaci klienta se serverem šifrování. Pokud chcete povolit protokol HTTPS na služby Service Fabric, zadat protokol v *prostředků -> Koncové body -> koncový bod* oddílu manifest služby, jak je uvedeno výše pro koncový bod *ServiceEndpoint3*.

> [!NOTE]
> Protokol služby nelze změnit během upgradu aplikace. Pokud se změní při upgradu, je narušující změně.
> 

> [!WARNING] 
> Při použití protokolu HTTPS, nepoužívejte stejný port a certifikát pro instance různé služby (nezávisle na aplikaci), které jsou nasazené na stejném uzlu. Upgrade dvě různé služby pomocí stejný port na jinou aplikaci instancí povede selhání při upgradu. Další informace najdete v tématu [upgrade více aplikací s koncovými body HTTPS ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
>

Tady je příklad ApplicationManifest, který je nutné nastavit pro protokol HTTPS. Kryptografický otisk certifikátu musí být zadán. EndpointRef je odkaz na EndpointResource v ServiceManifest, u kterého jste nastavili protokol HTTPS. Můžete přidat více než jeden EndpointCertificate.  

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

Pro Linux clusterů **MY** uložení výchozích hodnot do složky **/var/lib/sfcerts**.


## <a name="overriding-endpoints-in-servicemanifestxml"></a>Přepsání koncových bodů v ServiceManifest.xml

ApplicationManifest přidejte ResourceOverrides oddíl, který bude na stejné úrovni ConfigOverrides sekci. V této části můžete zadat přepsání pro oddíl koncové body v části prostředky zadané v Service manifest. Přepsání koncových bodů je podporována v modulu runtime 5.7.217/SDK 2.7.217 a vyšší.

Chcete-li přepsat koncového bodu v ServiceManifest pomocí ApplicationParameters změn ApplicationManifest jako následující:

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

V parametrech přidat níže:

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

Při nasazení aplikace lze předat v tyto hodnoty jako ApplicationParameters.  Příklad:

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Poznámka: Pokud hodnoty ApplicationParameters je prázdný, jsme přejděte zpět na výchozí hodnotu dostupné ServiceManifest pro odpovídající EndPointName.

Příklad:

Pokud se v ServiceManifest jste zadali

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

A Port1 a Protocol1 hodnotu pro parametry aplikace je null nebo prázdná. Port je stále rozhodnuto pomocí ServiceFabric. A bude protokol tcp.

Předpokládejme, že zadáte chybnou hodnotu. Například pro Port jste zadali hodnotu řetězce "Foo" namísto typ int.  Nové ServiceFabricApplication příkaz se nezdaří s chybou: Parametr přepisu s názvem 'ServiceEndpoint1' atributu "Port1" v části 'ResourceOverrides' je neplatný. Zadaná hodnota je "Foo" a vyžaduje je 'int'.
