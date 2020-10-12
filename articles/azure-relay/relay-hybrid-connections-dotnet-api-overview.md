---
title: Přehled rozhraní API pro Azure Relay .NET Standard | Microsoft Docs
description: Tento článek shrnuje část klíče a přehled Azure Relay rozhraní API Hybrid Connections .NET Standard.
ms.topic: article
ms.custom: devx-track-csharp
ms.date: 06/23/2020
ms.openlocfilehash: 44d5800c08b49118e99a678e31d02e5b7a1f550c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935666"
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Přehled rozhraní API pro Azure Relay Hybrid Connections .NET Standard

Tento článek shrnuje některé z klíčových Azure Relay Hybrid Connections .NET Standard [klientských rozhraní API](/dotnet/api/microsoft.azure.relay).
  
## <a name="relay-connection-string-builder-class"></a>Třída tvůrce připojovacího řetězce přenosu

Třída [RelayConnectionStringBuilder][RelayConnectionStringBuilder] formátuje připojovací řetězce, které jsou specifické pro Relay Hybrid Connections. Můžete ji použít k ověření formátu připojovacího řetězce nebo k vytvoření připojovacího řetězce od začátku. Podívejte se na následující kód pro příklad:

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

Připojovací řetězec lze také předat přímo do `RelayConnectionStringBuilder` metody. Tato operace umožňuje ověřit, zda je připojovací řetězec v platném formátu. Pokud některý z parametrů není platný, konstruktor vygeneruje `ArgumentException` .

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

## <a name="hybrid-connection-stream"></a>Stream hybridního připojení

Třída [HybridConnectionStream][HCStream] je primární objekt, který slouží k posílání a přijímání dat z Azure Relayho koncového bodu, ať už pracujete s [HybridConnectionClient][HCClient]nebo [HybridConnectionListener][HCListener].

### <a name="getting-a-hybrid-connection-stream"></a>Získání streamu hybridního připojení

#### <a name="listener"></a>Naslouchací proces

Pomocí objektu [HybridConnectionListener][HCListener] můžete získat `HybridConnectionStream` objekt následujícím způsobem:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>Klient

Pomocí objektu [HybridConnectionClient][HCClient] můžete získat `HybridConnectionStream` objekt následujícím způsobem:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>Příjem dat

Třída [HybridConnectionStream][HCStream] umožňuje obousměrnou komunikaci. Ve většině případů nepřetržitě přijímáte z datového proudu. Pokud čtete text z datového proudu, můžete také použít objekt [StreamReader](/dotnet/api/system.io.streamreader?view=netcore-3.1) , který umožňuje snazší analýzu dat. Například můžete číst data jako text, nikoli jako `byte[]` .

Následující kód přečte jednotlivé řádky textu ze streamu, dokud se nepožaduje zrušení:

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

Po navázání připojení můžete odeslat zprávu na koncový bod Relay. Vzhledem k tomu, že objekt připojení dědí [datový proud](/dotnet/api/system.io.stream?view=netcore-3.1), odešlete data jako `byte[]` . Následující příklad ukazuje, jak to provést:

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

Pokud však chcete odeslat text přímo, aniž byste museli zakódovat řetězec pokaždé, můžete zabalit `hybridConnectionStream` objekt pomocí objektu [StreamWriter](/dotnet/api/system.io.streamwriter?view=netcore-3.1) .

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>Další kroky

Další informace o Azure Relay najdete na těchto odkazech:

* [Referenční informace k Microsoft. Azure. Relay](/dotnet/api/microsoft.azure.relay)
* [Co je Azure Relay?](relay-what-is-it.md)
* [Dostupná předávací rozhraní API](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener
