---
title: Komunikace pro role v Cloud Services | Microsoft Docs
description: Instance rolí v Cloud Services můžou mít pro ně definované koncové body (http, https, TCP, UDP), které komunikují s vnějším nebo mezi ostatními instancemi role.
services: cloud-services
documentationcenter: ''
author: tgore03
manager: carmonm
ms.service: cloud-services
ms.topic: article
ms.date: 12/14/2016
ms.author: tagore
ms.openlocfilehash: 094e08becf4f3a60c98d89bfae7e7c3a69b677f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75386336"
---
# <a name="enable-communication-for-role-instances-in-azure"></a>Povolení komunikace pro instance rolí v Azure
Role cloudových služeb komunikují prostřednictvím interních a externích připojení. Externím připojením se říká **vstupní koncové body** , zatímco interní připojení se nazývají **interní koncové body**. Toto téma popisuje, jak upravit [definici služby](cloud-services-model-and-package.md#csdef) pro vytváření koncových bodů.

## <a name="input-endpoint"></a>Vstupní koncový bod
Vstupní koncový bod se používá, když chcete port vystavit vně. Zadejte typ protokolu a port koncového bodu, který pak platí pro externí i interní porty pro koncový bod. Pokud chcete, můžete pro koncový bod zadat jiný interní port s atributem [localPort](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) .

Vstupní koncový bod může používat tyto protokoly: **http, https, TCP, UDP**.

Chcete-li vytvořit vstupní koncový bod, přidejte podřízený element **InputEndpoint** do elementu **Endpoints** v rámci webové role nebo role pracovního procesu.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
</Endpoints> 
```

## <a name="instance-input-endpoint"></a>Vstupní koncový bod instance
Vstupní koncové body instance jsou podobné vstupním koncovým bodům, ale umožňují mapovat konkrétní veřejné porty pro jednotlivé instance role pomocí přesměrování portů v nástroji pro vyrovnávání zatížení. Můžete zadat jeden veřejný port nebo rozsah portů.

Vstupní koncový bod instance může jako protokol používat jenom **TCP** nebo **UDP** .

Chcete-li vytvořit vstupní koncový bod instance, přidejte podřízený element **InstanceInputEndpoint** do elementu **Endpoints** v rámci webové role nebo role pracovního procesu.

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
Vnitřní koncové body jsou k dispozici pro komunikaci mezi instancemi. Port je nepovinný a je-li tento parametr vynechán, je k koncovému bodu přiřazen dynamický port. Lze použít rozsah portů. Pro každou roli je povolený limit pěti interních koncových bodů.

Interní koncový bod může používat následující protokoly: **http, TCP, UDP, any**.

Chcete-li vytvořit interní vstupní koncový bod, přidejte podřízený element **InternalEndpoint** do elementu **Endpoints** v rámci webové role nebo role pracovního procesu.

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
Při práci s rolemi Worker i web je jeden malý rozdíl s koncovými body. Webová role musí mít minimálně jeden vstupní koncový bod pomocí protokolu **http** .

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
  <!-- more endpoints may be declared after the first InputEndPoint -->
</Endpoints>
```

## <a name="using-the-net-sdk-to-access-an-endpoint"></a>Použití sady .NET SDK pro přístup ke koncovému bodu
Spravovaná knihovna Azure poskytuje metody pro komunikaci instancí rolí za běhu. Z kódu spuštěného v instanci role můžete načíst informace o existenci ostatních instancí rolí a jejich koncových bodech a také informace o aktuální instanci role.

> [!NOTE]
> Můžete načíst jenom informace o instancích rolí, které běží v cloudové službě, a které definují aspoň jeden interní koncový bod. Nemůžete získat data o instancích rolí spuštěných v jiné službě.
> 
> 

K načtení instancí role můžete použít vlastnost [Instances](/previous-versions/azure/reference/ee741904(v=azure.100)) . Nejprve použijte [CurrentRoleInstance](/previous-versions/azure/reference/ee741907(v=azure.100)) a vraťte odkaz na aktuální instanci role a potom pomocí vlastnosti [role](/previous-versions/azure/reference/ee741918(v=azure.100)) vraťte odkaz na samotnou roli.

Pokud se k instanci role připojíte programově prostřednictvím .NET SDK, je poměrně snadné získat přístup k informacím o koncových bodech. Například po připojení ke konkrétnímu prostředí role můžete získat port konkrétního koncového bodu s tímto kódem:

```csharp
int port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["StandardWeb"].IPEndpoint.Port;
```

Vlastnost **Instances** vrátí kolekci objektů **RoleInstance** . Tato kolekce vždy obsahuje aktuální instanci. Pokud role nedefinuje interní koncový bod, kolekce zahrnuje aktuální instanci, ale ne jiné instance. Počet instancí rolí v kolekci bude vždycky 1 v případě, že pro danou roli není definovaný žádný interní koncový bod. Pokud role definuje interní koncový bod, jeho instance jsou zjistitelné za běhu a počet instancí v kolekci bude odpovídat počtu instancí zadaných pro roli v konfiguračním souboru služby.

> [!NOTE]
> Spravovaná knihovna Azure neposkytuje způsob, jak určit stav ostatních instancí rolí, ale pokud vaše služba potřebuje tuto funkci, můžete tuto posouzení implementovat sami. Pomocí [Azure Diagnostics](cloud-services-dotnet-diagnostics.md) můžete získat informace o spuštěných instancích rolí.
> 
> 

Chcete-li zjistit číslo portu pro interní koncový bod instance role, můžete použít vlastnost [InstanceEndpoints](/previous-versions/azure/reference/ee741917(v=azure.100)) k vrácení objektu Dictionary, který obsahuje názvy koncových bodů a jejich odpovídajících IP adres a portů. Vlastnost [IPEndPoint](/previous-versions/azure/reference/ee741919(v=azure.100)) vrací IP adresu a port pro zadaný koncový bod. Vlastnost **PublicIPEndpoint** vrací port pro koncový bod s vyrovnáváním zatížení. Část IP adresy vlastnosti **PublicIPEndpoint** se nepoužívá.

Tady je příklad, který prochází instance rolí.

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

Tady je příklad role pracovního procesu, která načte koncový bod vystavený prostřednictvím definice služby a začne naslouchat připojení.

> [!WARNING]
> Tento kód bude fungovat jenom u nasazené služby. Při spuštění v emulátoru služby výpočty Azure se prvky konfigurace služby, které vytvářejí přímé koncové body portů (**InstanceInputEndpoint** elementy), ignorují.
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

## <a name="network-traffic-rules-to-control-role-communication"></a>Pravidla síťového přenosu pro řízení komunikace mezi rolemi
Po definování vnitřních koncových bodů můžete přidat pravidla síťových přenosů (na základě koncových bodů, které jste vytvořili) a určit tak, jak mohou instance rolí vzájemně komunikovat. Následující diagram znázorňuje některé běžné scénáře pro řízení komunikace role:

![Scénáře pravidel přenosů sítě](./media/cloud-services-enable-communication-role-instances/scenarios.png "Scénáře pravidel přenosů sítě")

Následující příklad kódu ukazuje definice rolí pro role zobrazené v předchozím diagramu. Každá definice role zahrnuje aspoň jeden definovaný interní koncový bod:

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
> Omezení komunikace mezi rolemi se může vyskytnout s interními koncovými body pevných i automaticky přiřazených portů.
> 
> 

Ve výchozím nastavení platí, že po definování interního koncového bodu může komunikace směrovat z jakékoli role do interního koncového bodu role bez jakýchkoli omezení. Chcete-li omezit komunikaci, je nutné přidat element **NetworkTrafficRules** do elementu **ServiceDefinition** v souboru definice služby.

### <a name="scenario-1"></a>Scénář 1
Povolte síťový provoz jenom z **WebRole1** do **WorkerRole1**.

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
Povoluje jenom síťový provoz z **WebRole1** do **WorkerRole1** a **WorkerRole2**.

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
Povoluje jenom síťový provoz z **WebRole1** do **WorkerRole1**a **WorkerRole1** až **WorkerRole2**.

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
Povoluje jenom síťový provoz od **WebRole1** do **WorkerRole1**, **WebRole1** do **WorkerRole2**a **WorkerRole1** až **WorkerRole2**.

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

Odkaz na schéma XML pro výše použité prvky lze nalézt [zde](/previous-versions/azure/reference/gg557551(v=azure.100)).

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [modelu](cloud-services-model-and-package.md)cloudové služby.




