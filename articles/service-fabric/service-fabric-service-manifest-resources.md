---
title: Určení koncových bodů služby Fabric
description: Jak popsat prostředky koncového bodu v manifestu služby, včetně nastavení koncových bodů HTTPS
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 88e71d15829e68bde635f5b4d40224b8fa914f40
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417598"
---
# <a name="specify-resources-in-a-service-manifest"></a>Určení prostředků v manifestu služby
## <a name="overview"></a>Přehled
Manifest služby umožňuje prostředky, které jsou používány službou deklarovat nebo změnit, beze změny zkompilovaného kódu. Service Fabric podporuje konfiguraci prostředků koncového bodu pro službu. Přístup k prostředkům, které jsou určeny v manifestu služby lze řídit prostřednictvím SecurityGroup v manifestu aplikace. Deklarace prostředků umožňuje tyto prostředky změnit v době nasazení, což znamená, že služba není nutné zavést nový mechanismus konfigurace. Definice schématu pro soubor ServiceManifest.xml je nainstalována sadou Service Fabric SDK a nástroji na *C:\Program Files\Microsoft SDKs\Service Fabric\Schemas\ServiceFabricServiceModel.xsd*.

## <a name="endpoints"></a>Koncové body
Pokud je prostředek koncového bodu definován v manifestu služby, Service Fabric přiřadí porty z rozsahu vyhrazeného portu aplikace, když port není zadán explicitně. Například podívejte se na koncový bod *ServiceEndpoint1* zadaný ve fragmentu manifestu za daným po tomto odstavci. Kromě toho služby mohou také požadovat konkrétní port v prostředku. Replikám služeb spuštěným v různých uzlech clusteru lze přiřadit různá čísla portů, zatímco repliky služby spuštěné na stejném uzlu sdílejí port. Repliky služby pak můžete použít tyto porty podle potřeby pro replikaci a naslouchání pro požadavky klientů.

Po aktivaci služby, která určuje koncový bod https, service fabric nastaví položku řízení přístupu pro port, sváže zadaný certifikát serveru s portem a také udělí identitu, kterou je služba spuštěna jako oprávnění k soukromému klíči certifikátu. Tok aktivace je vyvolána při spuštění Service Fabric nebo při změně deklarace certifikátu aplikace prostřednictvím upgradu. Certifikát koncového bodu bude také monitorován pro změny nebo obnovení a oprávnění budou podle potřeby pravidelně znovu použita.

Po ukončení služby Service Fabric vyčistí položku řízení přístupu koncového bodu a odebere vazbu certifikátu. Všechna oprávnění použitá pro soukromý klíč certifikátu však nebudou vyčištěna.

> [!WARNING] 
> Statické porty by se podle návrhu neměly překrývat s rozsahem portů aplikace zadaným v manifestu clusteru. Pokud zadáte statický port, přiřaďte jej mimo rozsah portů aplikace, jinak to bude mít za následek konflikty portů. S verzí 6.5CU2 vydáme **zdravotní varování,** když zjistíme takový konflikt, ale necháme nasazení pokračovat v synchronizaci s dodaným chováním 6.5. Můžeme však zabránit nasazení aplikace z dalších hlavních verzí.
>
> S verzí 7.0 vydáme **upozornění na stav,** když zjistíme, že využití rozsahu portů aplikace přesahuje HostingConfig::ApplicationPortExhaustThresholdPercentage (výchozí 80%).
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

Pokud existuje více balíčků kódu v jednom balíčku služby, pak balíček kódu také musí odkazovat v části **Koncové body.**  Například pokud **ServiceEndpoint2a** a **ServiceEndpoint2b** jsou koncové body ze stejného balíčku služby odkazující na různé balíčky kódu, balíček kódu odpovídající každému koncovému bodu je objasněn následujícím způsobem:

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

Další informace o odkazování na koncové body ze souboru nastavení balíčku konfigurace (settings.xml) naleznete v [části Konfigurace spolehlivých služeb.](service-fabric-reliable-services-configuration.md)

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Příklad: určení koncového bodu HTTP pro vaši službu
Následující manifest služby definuje jeden prostředek koncového bodu TCP &lt;&gt; a dva prostředky koncového bodu HTTP v elementu Resources.

Koncové body protokolu HTTP jsou automaticky acl'd podle service fabric.

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
      <Endpoint Name="ServiceEndpoint4" Protocol="https" Port="14023"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Příklad: zadání koncového bodu HTTPS pro vaši službu
Protokol HTTPS poskytuje ověřování serveru a používá se také pro šifrování komunikace mezi klientem a serverem. Chcete-li povolit protokol HTTPS ve službě Service Fabric, zadejte protokol v části *Resources -> Endpoints -> Endpoint* manifestu služby, jak je uvedeno dříve pro koncový bod *ServiceEndpoint3*.

> [!NOTE]
> Protokol služby nelze změnit během upgradu aplikace. Pokud se změní během upgradu, je to narušující změna.
> 

> [!WARNING] 
> Při použití protokolu HTTPS nepoužívejte stejný port a certifikát pro různé instance služby (nezávisle na aplikaci) nasazené do stejného uzlu. Inovace dvou různých služeb pomocí stejného portu v různých instancích aplikace bude mít za následek selhání upgradu. Další informace naleznete v [tématu Inovace více aplikací pomocí koncových bodů HTTPS ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
>

Zde je příklad ApplicationManifest demonstrující konfiguraci požadovanou pro koncový bod HTTPS. Certifikát serveru/koncového bodu může být deklarován kryptografickým otiskem nebo běžným názvem subjektu a musí být poskytnuta hodnota. EndpointRef je odkaz na EndpointResource v ServiceManifest a jehož protokol musí být nastavena na protokol https. Můžete přidat více než jeden EndpointCertificate.  

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
      <EndpointBindingPolicy CertificateRef="SslCertByTP" EndpointRef="ServiceEndpoint3"/>
      <EndpointBindingPolicy CertificateRef="SslCertByCN" EndpointRef="ServiceEndpoint4"/>
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
    <EndpointCertificate Name="SslCertByTP" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
    <EndpointCertificate Name="SslCertByCN" X509FindType="FindBySubjectName" X509FindValue="ServiceFabric-EndpointCertificateBinding-Test" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

U linuxových clusterů je výchozí hodnota úložiště **MY** ve složce **/var/lib/sfcerts**.


## <a name="overriding-endpoints-in-servicemanifestxml"></a>Přepsání koncových bodů v souboru ServiceManifest.xml

V ApplicationManifest přidat ResourceOverrides části, která bude na stejné úrovni configOverrides části. V této části můžete určit přepsání části Koncové body v části prostředky určené v manifestu Služby. Přepsání koncových bodů je podporováno v modulu runtime 5.7.217/SDK 2.7.217 a vyšší.

Chcete-li přepsat EndPoint v ServiceManifest pomocí ApplicationParameters změnit ApplicationManifest jako následující:

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
           <EndpointBindingPolicy CertificateRef="SslCertByTP" EndpointRef="ServiceEndpoint"/>
        </Policies>
  </ServiceManifestImport>
```

V parametry přidat níže:

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

Při nasazování aplikace můžete předat tyto hodnoty jako ApplicationParameters.  Příklad:

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Poznámka: Pokud hodnoty poskytují ApplicationParameters je prázdný, vrátíme se k výchozí hodnotě uvedené v ServiceManifest pro odpovídající EndPointName.

Příklad:

Pokud jste v servicemanifestu zadali

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

A Port1 a Protocol1 hodnota pro parametry aplikace je null nebo prázdné. O portu stále rozhoduje servicefabric. A protokol bude tcp.

Předpokládejme, že zadáte nesprávnou hodnotu. Stejně jako u portu jste zadali řetězcovou hodnotu "Foo" namísto int.  Příkaz New-ServiceFabricApplication se nezdaří s chybou : Parametr přepsání s atributem ServiceEndpoint1 "Port1" v části ResourceOverrides je neplatný. Zadaná hodnota je "Foo" a povinná je "int".
