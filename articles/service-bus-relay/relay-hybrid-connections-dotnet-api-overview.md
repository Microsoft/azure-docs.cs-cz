---
title: Přehled standardních rozhraní API Azure Relay .NET | Dokumenty společnosti Microsoft
description: Tento článek shrnuje některé klíč přehled hybridní připojení Azure Relay .NET Standard API.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: b1da9ac1-811b-4df7-a22c-ccd013405c40
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: spelluru
ms.openlocfilehash: 18eaf2d2daae817107be6cdb0da9359bb5f9b4e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514531"
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Přehled rozhraní API pro hybridní připojení přenosových zařízení Azure Relay .NET

Tento článek shrnuje některé klíčové rozhraní [API klienta](/dotnet/api/microsoft.azure.relay)Azure Relay Hybrid Connections .NET Standard .
  
## <a name="relay-connection-string-builder-class"></a>Třída tvůrce připojovacího řetězce přenosu

Třída [RelayConnectionStringBuilder][RelayConnectionStringBuilder] formátuje připojovací řetězce, které jsou specifické pro hybridní připojení přenosu. Můžete ji použít k ověření formátu připojovacího řetězce nebo k vytvoření připojovacího řetězce od začátku. Podívejte se na následující kód pro příklad:

```csharp
var endpoint = "[Relay namespace]";
var entityPath = "[Name of the Hybrid Connection]";
var sharedAccessKeyName = "[SAS key name]";
var sharedAccessKey = "[SAS key value]";

var connectionStringBuilder = new RelayConnectionStringBuilder()
{
    Endpoint = endpoint,
    EntityPath = entityPath,
    SharedAccessKeyName = sasKeyName,
    SharedAccessKey = sasKeyValue
};
```

Můžete také předat připojovací `RelayConnectionStringBuilder` řetězec přímo metodě. Tato operace umožňuje ověřit, zda je připojovací řetězec v platném formátu. Pokud některý z parametrů jsou neplatné, konstruktor generuje `ArgumentException`.

```csharp
var myConnectionString = "[RelayConnectionString]";
// Declare the connectionStringBuilder so that it can be used outside of the loop if needed
RelayConnectionStringBuilder connectionStringBuilder;
try
{
    // Create the connectionStringBuilder using the supplied connection string
    connectionStringBuilder = new RelayConnectionStringBuilder(myConnectionString);
}
catch (ArgumentException ae)
{
    // Perform some error handling
}
```

## <a name="hybrid-connection-stream"></a>Hybridní přenos dat připojení

Třída [HybridConnectionStream][HCStream] je primární objekt používaný k odesílání a přijímání dat z koncového bodu Azure Relay, ať už pracujete s [hybridConnectionClient][HCClient]nebo [HybridConnectionListener][HCListener].

### <a name="getting-a-hybrid-connection-stream"></a>Získání datového proudu hybridního připojení

#### <a name="listener"></a>Naslouchací proces

Pomocí objektu [HybridConnectionListener][HCListener] můžete `HybridConnectionStream` získat objekt následujícím způsobem:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>Klient

Pomocí [objektu HybridConnectionClient][HCClient] můžete `HybridConnectionStream` získat objekt následujícím způsobem:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>Příjem dat

Třída [HybridConnectionStream][HCStream] umožňuje obousměrnou komunikaci. Ve většině případů průběžně přijímat z datového proudu. Pokud čtete text z datového proudu, můžete také použít [objekt StreamReader,](https://msdn.microsoft.com/library/system.io.streamreader(v=vs.110).aspx) který umožňuje snadnější analýzu dat. Můžete například číst data jako text, `byte[]`nikoli jako .

Následující kód čte jednotlivé řádky textu z datového proudu, dokud není požadováno zrušení:

```csharp
// Create a CancellationToken, so that we can cancel the while loop
var cancellationToken = new CancellationToken();
// Create a StreamReader from the 'hybridConnectionStream`
var streamReader = new StreamReader(hybridConnectionStream);

while (!cancellationToken.IsCancellationRequested)
{
    // Read a line of input until a newline is encountered
    var line = await streamReader.ReadLineAsync();
    if (string.IsNullOrEmpty(line))
    {
        // If there's no input data, we will signal that 
        // we will no longer send data on this connection
        // and then break out of the processing loop.
        await hybridConnectionStream.ShutdownAsync(cancellationToken);
        break;
    }
}
```

### <a name="sending-data"></a>Odesílání dat

Jakmile máte navázání připojení, můžete odeslat zprávu do koncového bodu přenosu. Vzhledem k tomu, že objekt připojení `byte[]`dědí [Stream](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx), odešlete data jako . Následující příklad ukazuje, jak to provést:

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

Pokud však chcete odeslat text přímo, aniž byste museli pokaždé kódovat `hybridConnectionStream` řetězec, můžete objekt zalomit objektem [StreamWriter.](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx)

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>Další kroky

Další informace o Azure Relay najdete na těchto odkazech:

* [Odkaz Microsoft.Azure.Relay](/dotnet/api/microsoft.azure.relay)
* [Co je Azure Relay?](relay-what-is-it.md)
* [Dostupná rozhraní API pro přenos](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener