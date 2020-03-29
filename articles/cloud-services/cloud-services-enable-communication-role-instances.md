---
title: Komunikace o rolích v cloudových službách | Dokumenty společnosti Microsoft
description: Instance rolí v cloudových službách mohou mít koncové body (http, https, tcp, udp) definované pro ně, které komunikují s vnějším i vnějšími instancemi role.
services: cloud-services
documentationcenter: ''
author: tgore03
manager: carmonm
ms.service: cloud-services
ms.topic: article
ms.date: 12/14/2016
ms.author: tagore
ms.openlocfilehash: 094e08becf4f3a60c98d89bfae7e7c3a69b677f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386336"
---
# <a name="enable-communication-for-role-instances-in-azure"></a>Povolení komunikace pro instance rolí v azure
Role cloudových služeb komunikují prostřednictvím interních a externích připojení. Externí připojení se nazývají **vstupní koncové body,** zatímco interní připojení se nazývají **interní koncové body**. Toto téma popisuje, jak upravit [definici služby](cloud-services-model-and-package.md#csdef) k vytvoření koncových bodů.

## <a name="input-endpoint"></a>Vstupní koncový bod
Vstupní koncový bod se používá, pokud chcete vystavit port na vnější straně. Zadáte typ protokolu a port koncového bodu, který pak platí pro externí i vnitřní porty pro koncový bod. Pokud chcete, můžete zadat jiný vnitřní port pro koncový bod s atributem [localPort.](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint)

Vstupní koncový bod může používat následující protokoly: **http, https, tcp, udp**.

Chcete-li vytvořit vstupní koncový bod, přidejte podřízený prvek **InputEndpoint** do prvku **Endpoints** webové nebo pracovní role.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
</Endpoints> 
```

## <a name="instance-input-endpoint"></a>Koncový bod vstupní instance
Koncové body vstupu instance jsou podobné vstupním koncovým bodům, ale umožňují mapovat konkrétní veřejné porty pro každou jednotlivou instanci role pomocí předávání portů v systému vyrovnávání zatížení. Můžete určit jeden veřejný port nebo rozsah portů.

Koncový bod vstupní instance může jako protokol používat pouze **protokol tcp** nebo **udp.**

Chcete-li vytvořit koncový bod vstupu instance, přidejte podřízený prvek **InstanceInputEndpoint** do elementu **Endpoints** webové nebo pracovní role.

```xml
<Endpoints>
  <InstanceInputEndpoint name="Endpoint2" protocol="tcp" localPort="10100">
    <AllocatePublicPortFrom>
      <FixedPortRange max="10109" min="10105" />
    </AllocatePublicPortFrom>
  </InstanceInputEndpoint>
</Endpoints>
```

## <a name="internal-endpoint"></a>Interní koncový bod
Interní koncové body jsou k dispozici pro komunikaci mezi instancemi. Port je volitelný a pokud je vynechán, dynamický port je přiřazen ke koncovému bodu. Lze použít rozsah portů. Existuje limit pěti interních koncových bodů na roli.

Interní koncový bod může používat následující protokoly: **http, tcp, udp, any**.

Chcete-li vytvořit vnitřní vstupní koncový bod, přidejte podřízený prvek **InternalEndpoint** do prvku **Endpoints** webové nebo pracovní role.

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


## <a name="worker-roles-vs-web-roles"></a>Role pracovního procesu vs. webové role
Existuje jeden menší rozdíl s koncovými body při práci s pracovními a webovými rolemi. Webová role musí mít minimálně jeden vstupní koncový bod pomocí protokolu **HTTP.**

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
  <!-- more endpoints may be declared after the first InputEndPoint -->
</Endpoints>
```

## <a name="using-the-net-sdk-to-access-an-endpoint"></a>Použití sady .NET SDK pro přístup ke koncovému bodu
Správa Azure poskytuje metody pro instance rolí pro komunikaci za běhu. Z kódu spuštěného v instanci role můžete načíst informace o existenci dalších instancí rolí a jejich koncových bodů, stejně jako informace o aktuální instanci role.

> [!NOTE]
> Můžete načíst pouze informace o instancích rolí, které jsou spuštěny ve vaší cloudové službě a které definují alespoň jeden interní koncový bod. Nelze získat data o instancích rolí spuštěných v jiné službě.
> 
> 

Vlastnost [Instances](/previous-versions/azure/reference/ee741904(v=azure.100)) můžete použít k načtení instancí role. Nejprve použijte [CurrentRoleInstance](/previous-versions/azure/reference/ee741907(v=azure.100)) vrátit odkaz na aktuální instanci role a potom použijte [Role](/previous-versions/azure/reference/ee741918(v=azure.100)) vlastnost vrátit odkaz na samotnou roli.

Když se k instanci role připojíte programově prostřednictvím sady .NET SDK, je relativně snadný přístup k informacím o koncovém bodu. Například poté, co jste již připojeni k určitému prostředí role, můžete získat port konkrétního koncového bodu s tímto kódem:

```csharp
int port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["StandardWeb"].IPEndpoint.Port;
```

Vlastnost **Instancevrátí** kolekci objektů **InstanceInstance.** Tato kolekce vždy obsahuje aktuální instanci. Pokud role nedefinuje vnitřní koncový bod, kolekce obsahuje aktuální instanci, ale žádné jiné instance. Počet instancí role v kolekci bude vždy 1 v případě, kdy je pro roli definován žádný vnitřní koncový bod. Pokud role definuje vnitřní koncový bod, jeho instance jsou zjistitelné za běhu a počet instancí v kolekci bude odpovídat počtu instancí určených pro roli v konfiguračním souboru služby.

> [!NOTE]
> Správa Azure neposkytuje prostředky k určení stavu jiných instancí role, ale můžete implementovat tato posouzení stavu sami, pokud vaše služba potřebuje tuto funkci. [Diagnostika Azure](cloud-services-dotnet-diagnostics.md) můžete použít k získání informací o spuštění instancí rolí.
> 
> 

Chcete-li určit číslo portu pro interní koncový bod v instanci role, můžete použít vlastnost [InstanceEndpoints](/previous-versions/azure/reference/ee741917(v=azure.100)) k vrácení objektu Dictionary, který obsahuje názvy koncových bodů a jejich odpovídající adresy IP a porty. Vlastnost [IPEndpoint](/previous-versions/azure/reference/ee741919(v=azure.100)) vrátí adresu IP a port pro zadaný koncový bod. Vlastnost **PublicIPEndpoint** vrátí port pro koncový bod s vyrovnáváním zatížení. Část ip adresy vlastnosti **PublicIPEndpoint** se nepoužívá.

Zde je příklad, který itezuje instance rolí.

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

Zde je příklad role pracovního procesu, který získá koncový bod vystavené prostřednictvím definice služby a začne naslouchat připojení.

> [!WARNING]
> Tento kód bude fungovat pouze pro nasazenou službu. Při spuštění v Azure Compute Emulator, prvky konfigurace služby, které vytvářejí cílové body portu **(InstanceInputEndpoint** prvky) jsou ignorovány.
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
      // see the MSDN topic at https://go.microsoft.com/fwlink/?LinkId=166357.
      return base.OnStart();
    }
  }
}
```

## <a name="network-traffic-rules-to-control-role-communication"></a>Pravidla síťového provozu pro řízení komunikace rolí
Po definování interních koncových bodů můžete přidat pravidla síťového provozu (na základě koncových bodů, které jste vytvořili) a řídit tak, jak mohou instance rolí vzájemně komunikovat. Následující diagram znázorňuje některé běžné scénáře pro řízení komunikace rolí:

![Scénáře pravidel síťového provozu](./media/cloud-services-enable-communication-role-instances/scenarios.png "Scénáře pravidel síťového provozu")

Následující příklad kódu ukazuje definice rolí zobrazených v předchozím diagramu. Každá definice role obsahuje alespoň jeden vnitřní koncový bod definovaný:

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
> Omezení komunikace mezi rolemi může nastat s interními koncovými body pevných i automaticky přiřazených portů.
> 
> 

Ve výchozím nastavení po definování vnitřní koncový bod komunikace může tok z libovolné role do vnitřníkoncový bod role bez omezení. Chcete-li omezit komunikaci, je nutné přidat prvek **NetworkTrafficRules** do prvku **ServiceDefinition** v souboru definice služby.

### <a name="scenario-1"></a>Scénář 1
Povolit pouze síťový provoz z **WebRole1** **workerrole1**.

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
Umožňuje pouze síťový provoz z **WebRole1** **workerrole1** a **WorkerRole2**.

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
Umožňuje pouze síťový provoz z **WebRole1** **workerrole1**a **WorkerRole1** **workerrole2**.

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
Umožňuje pouze síťový provoz z **WebRole1** **workerrole1**, **WebRole1** **workerrole2**a **WorkerRole1** **workerrole2**.

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

Odkaz na schéma XML pro výše použité prvky naleznete [zde](/previous-versions/azure/reference/gg557551(v=azure.100)).

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [modelu](cloud-services-model-and-package.md)cloudové služby .




