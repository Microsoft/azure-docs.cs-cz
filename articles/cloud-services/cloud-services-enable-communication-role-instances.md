---
title: Komunikace pro role v cloudové služby | Microsoft Docs
description: Instance role v cloudové služby může mít koncové body (http, https, tcp, udp) definované pro ně komunikující s vnější nebo mezi dalších instancí rolí.
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
ms.assetid: 7008a083-acbe-4fb8-ae60-b837ef971ca1
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: adegeo
ms.openlocfilehash: 96ca9bb2d7a9f30a7d6492be43bfb44edc02fd93
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2018
ms.locfileid: "27701886"
---
# <a name="enable-communication-for-role-instances-in-azure"></a>Povolit komunikaci pro instance rolí v azure
Role cloudové služby komunikují prostřednictvím interní a externí připojení. Externí připojení se nazývají **vstupní koncové body** při se označují jako vnitřní připojení **vnitřních koncových bodů**. Toto téma popisuje postup úpravy [služby definice](cloud-services-model-and-package.md#csdef) k vytvoření koncových bodů.

## <a name="input-endpoint"></a>Vstupní koncový bod
Vstupní koncový bod se používá, když chcete vystavit port ven. Určete typ protokol a port koncového bodu, který pak platí pro externí i interní porty pro koncový bod. Pokud chcete, můžete zadat jiný interní port koncového bodu se [Místní_port](https://msdn.microsoft.com/library/azure/gg557552.aspx#InputEndpoint) atribut.

Vstupní koncový bod můžete použít následující protokoly: **http, https, tcp, udp**.

Chcete-li vytvořit vstupní koncový bod, přidejte **InputEndpoint** podřízený element pro **koncové body** element role web nebo worker.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
</Endpoints> 
```

## <a name="instance-input-endpoint"></a>Vstupní koncový bod instance
Instance vstupní koncové body jsou podobná vstupních koncových bodů ale můžete namapovat konkrétní veřejné porty pro každou jednotlivou roli instanci pomocí přesměrování portu v nástroji pro vyrovnávání zatížení. Můžete zadat jediný port veřejné, nebo rozsah portů.

Vstupní koncový bod instanci lze použít pouze **tcp** nebo **udp** jako protokol.

Chcete-li vytvořit instanci vstupní koncový bod, přidejte **InstanceInputEndpoint** podřízený element pro **koncové body** element role web nebo worker.

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
Vnitřních koncových bodů jsou k dispozici pro komunikaci instance instance. Port je volitelný a pokud tento parametr vynechán, je dynamický port přiřazené ke koncovému bodu. Rozsah portů lze použít. Existuje limit pět vnitřních koncových bodů podle příslušné role.

Vnitřní koncový bod můžete použít následující protokoly: **http, tcp, udp, všechny**.

Chcete-li vytvořit interní vstupní koncový bod, přidejte **InternalEndpoint** podřízený element pro **koncové body** element role web nebo worker.

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
Při práci s worker a webové role je jeden dílčí rozdíl s koncovými body. Webové role musí mít alespoň jeden vstupní koncový bod pomocí **HTTP** protokolu.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
  <!-- more endpoints may be declared after the first InputEndPoint -->
</Endpoints>
```

## <a name="using-the-net-sdk-to-access-an-endpoint"></a>Pomocí sady .NET SDK pro přístup k koncový bod
Spravované knihovny Azure poskytuje metody pro instance rolí pro komunikaci za běhu. Z kódu spuštěné v instanci role můžete načíst informace o existenci další instance rolí a jejich koncových bodů, a také informace o aktuální instance role.

> [!NOTE]
> Pouze můžete načíst informace o instancích role běží v cloudové služby, které definují aspoň jeden vnitřní koncový bod. Nelze získat data o instancích role spuštěné v jinou službu.
> 
> 

Můžete použít [instance](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.role.instances.aspx) vlastnost načtení instancí role. Prvním použití [CurrentRoleInstance](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.currentroleinstance.aspx) vrátíte odkaz na aktuální instanci role, a potom pomocí [Role](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.role.aspx) vlastnost vrací odkaz k roli sám sebe.

Při připojení k instanci role programově pomocí .NET SDK je poměrně snadné pro přístup k informacím koncový bod. Například po již připojení k prostředí konkrétní roli, můžete získat port konkrétní koncový bod s tímto kódem:

```csharp
int port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["StandardWeb"].IPEndpoint.Port;
```

**Instance** vlastnost vrátí kolekci **RoleInstance** objekty. Tato kolekce vždy obsahuje aktuální instance. Pokud roli nedefinuje vnitřní koncový bod, kolekce zahrnuje aktuální instance, ale žádné jiné instance. Počet instancí role v kolekci bude vždy 1 v případě, kde je definován žádný vnitřní koncový bod pro roli. Pokud role definuje vnitřní koncový bod, její instance jsou zjistitelný za běhu, a počet instancí v kolekci odpovídá počtu instancí zadaný pro roli v konfiguračním souboru služby.

> [!NOTE]
> Spravované knihovny Azure neposkytuje způsob určování stavu další instance rolí, ale pokud vaše služba musí tuto funkci budete moct implementovat sami takové vyhodnocování stavu. Můžete použít [Azure Diagnostics](cloud-services-dotnet-diagnostics.md) získat informace o spuštění instancí role.
> 
> 

Chcete-li zjistit číslo portu pro vnitřní koncový bod na instanci role, můžete použít [InstanceEndpoints](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.instanceendpoints.aspx) vlastnost vrátit objekt slovník, který obsahuje názvy koncových bodů a jejich odpovídající IP adresy a porty. [IPEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstanceendpoint.ipendpoint.aspx) vlastnost vrací IP adresu a port pro zadaný koncový bod. **PublicIPEndpoint** vlastnost vrací port pro koncový bod Vyrovnávání zatížení. Část adresy IP **PublicIPEndpoint** vlastnost nepoužívá.

Tady je příklad, který iteruje instancí rolí.

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

Tady je příklad role pracovního procesu, který získá koncový bod vystavený prostřednictvím definice služby a začne naslouchat pro připojení.

> [!WARNING]
> Tento kód bude fungovat pouze pro nasazené služby. Při spouštění v emulátoru výpočetní Azure, služby konfigurační prvky, které vytvoření přímé port koncových bodů (**InstanceInputEndpoint** elementy) jsou ignorovány.
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
Po definování vnitřních koncových bodů, můžete přidat pravidla pro provoz sítě (založené na koncových bodů, které jste vytvořili) řídit, jak můžete instance rolí vzájemně komunikovat. Následující diagram znázorňuje některé běžné scénáře pro řízení komunikace role:

![Síťový provoz pravidla scénáře](./media/cloud-services-enable-communication-role-instances/scenarios.png "síťový provoz pravidla scénáře")

Následující příklad kódu ukazuje definice rolí u rolí vidět na předchozím obrázku. Každý definice role obsahuje alespoň jeden interní koncového bodu definovaného:

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
> Omezení komunikace mezi rolemi mohou nastat u vnitřních koncových bodů i fixed a automaticky přiřadí porty.
> 
> 

Ve výchozím nastavení po definování vnitřní koncový bod komunikace můžete procházet z žádné role, do vnitřní koncový bod role bez jakýchkoli omezení. Pokud chcete omezit komunikaci, je nutné přidat **NetworkTrafficRules** elementu, který chcete **ServiceDefinition** element v souboru definice služby.

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
Umožňuje použití jenom provoz sítě z **WebRole1** k **WorkerRole1** a **WorkerRole2**.

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
Umožňuje použití jenom provoz sítě z **WebRole1** k **WorkerRole1**, a **WorkerRole1** k **WorkerRole2**.

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
Umožňuje použití jenom provoz sítě z **WebRole1** k **WorkerRole1**, **WebRole1** k **WorkerRole2**, a **WorkerRole1** k **WorkerRole2**.

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

Odkaz na schéma XML pro prvky používané výše naleznete [zde](https://msdn.microsoft.com/library/azure/gg557551.aspx).

## <a name="next-steps"></a>Další postup
Další informace o cloudové službě [modelu](cloud-services-model-and-package.md).

