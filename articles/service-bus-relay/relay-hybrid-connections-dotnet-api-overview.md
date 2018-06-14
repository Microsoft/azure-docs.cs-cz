---
title: Přehled Azure předávání přes standardní API technologie .NET | Microsoft Docs
description: Přehled služby Azure API standardní předávání přes rozhraní .NET
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: b1da9ac1-811b-4df7-a22c-ccd013405c40
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: aa1863a44f00ae17f63b02c7c247b2c9fd9925f6
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2018
ms.locfileid: "28016429"
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Přehled služby Azure předávání hybridní připojení .NET standardní API

Tento článek je uveden přehled Azure předávání hybridní připojení .NET standardní klíče [rozhraní API klienta](/dotnet/api/microsoft.azure.relay).
  
## <a name="relay-connection-string-builder-class"></a>Třída předávání Tvůrce připojovacích řetězců

[RelayConnectionStringBuilder] [ RelayConnectionStringBuilder] třída formáty připojovací řetězce, které jsou specifické pro předávání hybridní připojení. Můžete ho se ověřit formát připojovací řetězec, nebo vytvořit připojovací řetězec od začátku. Najdete příklad následující kód:

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

Připojovací řetězec můžete předat také přímo na `RelayConnectionStringBuilder` metoda. Tato operace umožňuje ověřte, zda je připojovací řetězec v platném formátu. Pokud některý z parametrů jsou neplatné, vygeneruje konstruktoru `ArgumentException`.

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

## <a name="hybrid-connection-stream"></a>Hybridní připojení datového proudu

[HybridConnectionStream] [ HCStream] třída je primární objekt použitý k odesílat a přijímat data z Azure předávání koncový bod, zda pracujete [HybridConnectionClient] [ HCClient], nebo [HybridConnectionListener][HCListener].

### <a name="getting-a-hybrid-connection-stream"></a>Získávání datový proud s hybridní připojení

#### <a name="listener"></a>Naslouchací proces

Použití [HybridConnectionListener] [ HCListener] objekt, můžete získat `HybridConnectionStream` objektu následujícím způsobem:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>Klient

Použití [HybridConnectionClient] [ HCClient] objekt, můžete získat `HybridConnectionStream` objektu následujícím způsobem:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>Přijetí dat

[HybridConnectionStream] [ HCStream] třída umožňuje obousměrnou komunikaci. Ve většině případů nepřetržitě obdržíte z datového proudu. Text při čtení z datového proudu, můžete také použít [StreamReader](https://msdn.microsoft.com/library/system.io.streamreader(v=vs.110).aspx) objekt, který umožňuje jednodušší analýza dat. Například můžete číst data jako text, nikoli jako `byte[]`.

Následující kód čte jednotlivé řádky textu z datového proudu, dokud se požaduje zrušení:

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

### <a name="sending-data"></a>Odeslání dat

Jakmile máte připojení bylo vytvořeno, můžete odeslat zprávu na předávání přes koncový bod. Protože objekt připojení dědí [datového proudu](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx), odesílat data podle `byte[]`. Následující příklad ukazuje, jak to udělat:

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

Ale pokud chcete odeslat text přímo, aniž by museli zakódujte řetězec pokaždé, když, můžete zabalit `hybridConnectionStream` objektu s [StreamWriter](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx) objektu.

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>Další postup

Další informace o předávání přes Azure, najdete pomocí těchto odkazů:

* [Odkaz na Microsoft.Azure.Relay](/dotnet/api/microsoft.azure.relay)
* [Co je Azure Relay?](relay-what-is-it.md)
* [K dispozici předávání přes rozhraní API](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener