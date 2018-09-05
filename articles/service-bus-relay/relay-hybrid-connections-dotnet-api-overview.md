---
title: Přehled služby Azure Relay standardní rozhraní API .NET | Dokumentace Microsoftu
description: Přehled služby Azure Relay .NET API úrovně Standard
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
ms.openlocfilehash: a6a1706c8d1e849fd1bb4309c46063dd3f9439c1
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43700136"
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Přehled služby Azure Relay Hybrid připojení .NET Standard API

Tento článek obsahuje souhrn některých klíč Azure Relay Hybrid připojení .NET Standard [klientských rozhraní API](/dotnet/api/microsoft.azure.relay).
  
## <a name="relay-connection-string-builder-class"></a>Třídy přenosu Tvůrce připojovacích řetězců

[RelayConnectionStringBuilder] [ RelayConnectionStringBuilder] třídy formáty připojovací řetězce, které jsou specifické pro Relay Hybrid Connections. Slouží k ověření formátu připojovacího řetězce nebo chcete vytvořit připojovací řetězec od začátku. Viz následující kód například:

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

Můžete také předat připojovací řetězec přímo `RelayConnectionStringBuilder` metody. Tato operace umožňuje ověřte, zda je připojovací řetězec v platném formátu. Pokud některý z parametrů jsou neplatné, vygeneruje konstruktor `ArgumentException`.

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

## <a name="hybrid-connection-stream"></a>Datový proud hybridní připojení

[HybridConnectionStream] [ HCStream] třída je primární objekt použitý k odesílání a příjem dat z koncového bodu Azure Relay, ať už pracujete [HybridConnectionClient] [ HCClient], nebo [HybridConnectionListener][HCListener].

### <a name="getting-a-hybrid-connection-stream"></a>Získání datového proudu hybridní připojení

#### <a name="listener"></a>Naslouchací proces

Použití [HybridConnectionListener] [ HCListener] objektu, můžete získat `HybridConnectionStream` objektu následujícím způsobem:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>Klient

Použití [HybridConnectionClient] [ HCClient] objektu, můžete získat `HybridConnectionStream` objektu následujícím způsobem:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>Příjem dat

[HybridConnectionStream] [ HCStream] třída umožňuje obousměrnou komunikaci. Ve většině případů se průběžně zobrazí z datového proudu. Při čtení textu z datového proudu, můžete také použít [StreamReader](https://msdn.microsoft.com/library/system.io.streamreader(v=vs.110).aspx) objektu, který umožňuje jednodušší analýzu data. Například může číst data jako text, nikoli jako `byte[]`.

Následující kód načte jednotlivé řádky textu z datového proudu, dokud je požadováno zrušení:

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

Jakmile se připojení naváže, můžete odeslat zprávu do koncového bodu propojení. Protože objekt připojení dědí [Stream](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx), poslat data jako `byte[]`. Následující příklad ukazuje, jak to udělat:

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

Ale pokud chcete odeslat text přímo, aniž by bylo nutné kódovat řetězec pokaždé, když, můžete zalomit `hybridConnectionStream` objektu [StreamWriter](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx) objektu.

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>Další postup

Další informace o Azure Relay, najdete pomocí těchto odkazů:

* [Odkaz na Microsoft.Azure.Relay](/dotnet/api/microsoft.azure.relay)
* [Co je Azure Relay?](relay-what-is-it.md)
* [K dispozici předávání přes rozhraní API](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener