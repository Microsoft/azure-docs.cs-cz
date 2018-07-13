---
title: Komunikace pro role v cloudových službách | Dokumentace Microsoftu
description: Instance rolí v cloudových službách můžete mít koncové body (http, https, tcp, udp) pro ně definována, které komunikují s vnější nebo mezi dalších instancí rolí.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 7008a083-acbe-4fb8-ae60-b837ef971ca1
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: jeconnoc
ms.openlocfilehash: 9d6cc542b6af0b508529c1392e16df93e1ba084d
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005940"
---
# <a name="enable-communication-for-role-instances-in-azure"></a>Povolit komunikaci pro instance rolí v azure
Role cloudové služby komunikují prostřednictvím interní a externí připojení. Externí připojení, se nazývají **vstupní koncové body** při interní připojení se nazývají **koncovým bodům s interním**. Toto téma popisuje postup úpravy [služby definice](cloud-services-model-and-package.md#csdef) k vytvoření koncových bodů.

## <a name="input-endpoint"></a>Vstupní koncový bod
Vstupní koncový bod se používá, když chcete vystavit port na vnější. Zadejte typ protokolu a portu koncového bodu, který se použije pro externí a interní porty koncového bodu. Pokud chcete, můžete zadat jiný interní port koncového bodu se [localPort](https://msdn.microsoft.com/library/azure/gg557552.aspx#InputEndpoint) atribut.

Vstupní koncový bod pomocí těchto protokolů: **http, https, tcp, udp**.

Chcete-li vytvořit vstupní koncový bod, přidejte **InputEndpoint** podřízený element pro **koncové body** prvku webové nebo pracovní role.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
</Endpoints> 
```

## <a name="instance-input-endpoint"></a>Vstupní koncový bod instance
Vstupní koncové body instance je podobný jako vstupní koncové body ale vám umožní mapovat určité porty veřejně přístupných u každé instance jednotlivých rolí pomocí přesměrování portu v nástroji pro vyrovnávání zatížení. Můžete zadat jeden port veřejná nebo rozsah portů.

Vstupní koncový bod instance lze použít pouze **tcp** nebo **udp** jako protokol.

Chcete-li vytvořit instanci vstupní koncový bod, přidejte **InstanceInputEndpoint** podřízený element pro **koncové body** prvku webové nebo pracovní role.

```xml
<Endpoints>
  <InstanceInputEndpoint name="Endpoint2" protocol="tcp" localPort="10100">
    <AllocatePublicPortFrom>
      <FixedPortRange max="10109" min="10105" />
    </AllocatePublicPortFrom>
  </InstanceInputEndpoint>
</Endpoints>
```

## <a name="internal-endpoint"></a>Vnitřní koncový bod
Vnitřní koncové body jsou k dispozici pro instanci instance komunikace. Port je volitelný a pokud tento parametr vynechán, je dynamický port přiřazen ke koncovému bodu. Rozsah portů je možné. Existuje limit pět koncovým bodům s interním na roli.

Vnitřní koncový bod pomocí těchto protokolů: **http, tcp, udp, všechny**.

Chcete-li vytvořit interní vstupní koncový bod, přidejte **InternalEndpoint** podřízený element pro **koncové body** prvku webové nebo pracovní role.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" port="8999" />
</Endpoints> 
```

Můžete také použít rozsah portů.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any">
    <FixedPortRange max="8999" min="8995" />
  </InternalEndpoint>
</Endpoints>
```


## <a name="worker-roles-vs-web-roles"></a>Vs role pracovního procesu. Webové role
Při práci s worker a webových rolí je jeden menší rozdíl s koncovými body. Webová role musí mít aspoň jeden vstupní koncový bod pomocí **HTTP** protokolu.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
  <!-- more endpoints may be declared after the first InputEndPoint -->
</Endpoints>
```

## <a name="using-the-net-sdk-to-access-an-endpoint"></a>Pomocí sady .NET SDK pro přístup k koncový bod
Řízená knihovna Azure poskytuje metody pro instance rolí, aby za běhu komunikovat. Z kódu spuštěného v instanci role můžete načíst informace o existenci ostatních instancí role a jejich koncové body, stejně jako informace o aktuální instance role.

> [!NOTE]
> Můžete se dá načíst jenom informace o instancích rolí, která běží v cloudové službě a, které definují aspoň jeden vnitřní koncový bod. Nelze získat údaje o instance role spuštěné v jiné služby.
> 
> 

Můžete použít [instance](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.role.instances.aspx) vlastnost pro načtení instance rolí. Nejprve pomocí [CurrentRoleInstance](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.currentroleinstance.aspx) vrátí odkaz na aktuální instanci role, a potom pomocí [Role](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.role.aspx) vlastnost vrátí odkaz na tuto roli, samotného.

Při připojení k instanci role prostřednictvím kódu programu pomocí sady .NET SDK je relativně jednoduché je získat přístup k informacím koncový bod. Například po už jste připojili k určité role prostředí, můžete získat port určitý koncový bod s tímto kódem:

```csharp
int port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["StandardWeb"].IPEndpoint.Port;
```

**Instance** vlastnost vrátí kolekci **instance role** objekty. Tato kolekce vždy obsahuje aktuální instance. Pokud roli nedefinuje interního koncového bodu, kolekce obsahuje aktuální instance, ale žádné jiné instance. Počet instancí role v kolekci, bude vždy 1 v případě, kde je definován žádný vnitřní koncový bod pro danou roli. Pokud roli definuje interního koncového bodu, její instance organizací se dají najít v době běhu a počtu instancí v kolekci, budou odpovídat na počet instancí zadaných pro roli v konfiguračním souboru služby.

> [!NOTE]
> Řízená knihovna Azure neposkytuje způsob určení stavu ostatních instancí role, ale můžete implementovat takové posouzení stavu sami Pokud vaše služba potřebuje tuto funkci. Můžete použít [Azure Diagnostics](cloud-services-dotnet-diagnostics.md) získat informace o spuštěných instancí rolí.
> 
> 

Pokud chcete zjistit číslo portu pro vnitřní koncový bod v instanci role, můžete použít [InstanceEndpoints](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.instanceendpoints.aspx) vlastnost vrátí objekt slovníku obsahující názvy koncových bodů a jejich odpovídající IP adresy a porty. [IPEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstanceendpoint.ipendpoint.aspx) vlastnost vrací IP adresu a port pro zadaný koncový bod. **PublicIPEndpoint** vlastnost vrací port pro koncový bod s vyrovnáváním zatížení. Část IP adresy **PublicIPEndpoint** vlastnost se nepoužívá.

Tady je příklad, který iteruje v instancích rolí.

```csharp
foreach (RoleInstance roleInst in RoleEnvironment.CurrentRoleInstance.Role.Instances)
{
    Trace.WriteLine("Instance ID: " + roleInst.Id);
    foreach (RoleInstanceEndpoint roleInstEndpoint in roleInst.InstanceEndpoints.Values)
    {
        Trace.WriteLine("Instance endpoint IP address and port: " + roleInstEndpoint.IPEndpoint);
    }
}
```

Tady je příklad role pracovního procesu, který získá koncový bod vystavený prostřednictvím definici služby a začne naslouchat pro připojení.

> [!WARNING]
> Tento kód bude fungovat jenom pro službu nasazenou. Při spouštění v emulátoru Azure Compute, konfigurační prvky, které vytvářejí přímo portovaný koncové body služby (**InstanceInputEndpoint** elementy) jsou ignorovány.
> 
> 

```csharp
using System;
using System.Diagnostics;
using System.Linq;
using System.Net;
using System.Net.Sockets;
using System.Threading;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.Diagnostics;
using Microsoft.WindowsAzure.ServiceRuntime;
using Microsoft.WindowsAzure.StorageClient;

namespace WorkerRole1
{
  public class WorkerRole : RoleEntryPoint
  {
    public override void Run()
    {
      try
      {
        // Initialize method-wide variables
        var epName = "Endpoint1";
        var roleInstance = RoleEnvironment.CurrentRoleInstance;

        // Identify direct communication port
        var myPublicEp = roleInstance.InstanceEndpoints[epName].PublicIPEndpoint;
        Trace.TraceInformation("IP:{0}, Port:{1}", myPublicEp.Address, myPublicEp.Port);

        // Identify public endpoint
        var myInternalEp = roleInstance.InstanceEndpoints[epName].IPEndpoint;

        // Create socket listener
        var listener = new Socket(
          myInternalEp.AddressFamily, SocketType.Stream, ProtocolType.Tcp);

        // Bind socket listener to internal endpoint and listen
        listener.Bind(myInternalEp);
        listener.Listen(10);
        Trace.TraceInformation("Listening on IP:{0},Port: {1}",
          myInternalEp.Address, myInternalEp.Port);

        while (true)
        {
          // Block the thread and wait for a client request
          Socket handler = listener.Accept();
          Trace.TraceInformation("Client request received.");

          // Define body of socket handler
          var handlerThread = new Thread(
            new ParameterizedThreadStart(h =>
            {
              var socket = h as Socket;
              Trace.TraceInformation("Local:{0} Remote{1}",
                socket.LocalEndPoint, socket.RemoteEndPoint);

              // Shut down and close socket
              socket.Shutdown(SocketShutdown.Both);
              socket.Close();
            }
          ));

          // Start socket handler on new thread
          handlerThread.Start(handler);
        }
      }
      catch (Exception e)
      {
        Trace.TraceError("Caught exception in run. Details: {0}", e);
      }
    }

    public override bool OnStart()
    {
      // Set the maximum number of concurrent connections 
      ServicePointManager.DefaultConnectionLimit = 12;

      // For information on handling configuration changes
      // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.
      return base.OnStart();
    }
  }
}
```

## <a name="network-traffic-rules-to-control-role-communication"></a>Pravidla pro provoz sítě pro řízení komunikace role
Po definování koncovým bodům s interním můžete přidat pravidla pro provoz sítě (založené na koncových bodech, které jste vytvořili) na ovládací prvek, jak může instance role komunikovat mezi sebou. Následující diagram znázorňuje některé běžné scénáře pro řízení komunikace role:

![Síťové scénáře pravidla provozu](./media/cloud-services-enable-communication-role-instances/scenarios.png "síťové scénáře pravidel provozu")

Následující příklad kódu ukazuje definice rolí u rolí je vidět na předchozím obrázku. Každá definice role obsahuje alespoň jeden vnitřní koncový bod definovaný:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalTCP1" protocol="tcp" />
    </Endpoints>
  </WebRole>
  <WorkerRole name="WorkerRole1">
    <Endpoints>
      <InternalEndpoint name="InternalTCP2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
  <WorkerRole name="WorkerRole2">
    <Endpoints>
      <InternalEndpoint name="InternalTCP3" protocol="tcp" />
      <InternalEndpoint name="InternalTCP4" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

> [!NOTE]
> Omezení komunikace mezi rolemi mohou nastat u koncovým bodům s interním i pevně a automaticky přiřadí porty.
> 
> 

Ve výchozím nastavení po definování vnitřní koncový bod komunikace může směrovat z jakékoli role do vnitřní koncový bod role bez jakýchkoli omezení. Omezuje komunikaci, je nutné přidat **NetworkTrafficRules** elementu **ServiceDefinition** element v definičním souboru služby.

### <a name="scenario-1"></a>Scénář 1
Povolit pouze provoz sítě z **WebRole1** k **WorkerRole1**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-2"></a>Scénář 2
Umožňuje pouze síťového provozu z **WebRole1** k **WorkerRole1** a **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-3"></a>Scénář 3
Umožňuje pouze síťového provozu z **WebRole1** k **WorkerRole1**, a **WorkerRole1** k **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-4"></a>Scénář 4
Umožňuje pouze síťového provozu z **WebRole1** k **WorkerRole1**, **WebRole1** k **WorkerRole2**, a **WorkerRole1**  k **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP4" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

Najdete odkaz na schéma XML pro elementy využité nad [tady](https://msdn.microsoft.com/library/azure/gg557551.aspx).

## <a name="next-steps"></a>Další postup
Další informace o cloudové službě [modelu](cloud-services-model-and-package.md).

