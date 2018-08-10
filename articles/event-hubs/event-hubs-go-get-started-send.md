---
title: Odesílání událostí do služby Azure Event Hubs pomocí jazyka Go | Dokumentace Microsoftu
description: Začínáme s odesíláním událostí do služby Event Hubs pomocí jazyka Go
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/23/2018
ms.author: shvija
ms.openlocfilehash: 40b3aa82c3e9e8ab9a30362c0a41998877655725
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005593"
---
# <a name="send-events-to-event-hubs-using-go"></a>Odesílání událostí do služby Event Hubs pomocí jazyka Go

Azure Event Hubs je vysoce škálovatelné událostí systém správy, který dokáže zpracovat miliony událostí za sekundu, které umožňuje aplikacím zpracovávat a analyzovat masivní objemy dat vytvářených připojenými zařízeními a dalšími systémy. Jakmile jsou shromážděna do centra událostí, může přijímat a zpracovávat události pomocí obslužných rutin v procesu nebo předáváním k jiným systémům pro analýzu.

Další informace o službě Event Hubs, najdete v článku [Přehled služby Event Hubs][Event Hubs overview].

Tento kurz popisuje, jak odesílat události do centra událostí z aplikace napsané v Go. Pokud chcete přijímat události, použijte **přejít eph** balíčku (Event Processor Host), jak je popsáno v [odpovídající článku Receive](event-hubs-go-get-started-receive-eph.md).

Kód v tomto kurzu je převzatý z [ukázek Githubu](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs), které můžete zkontrolovat zobrazíte kompletní funkční aplikaci, včetně příkazy pro import a deklarace proměnné.

Další příklady jsou k dispozici [události rozbočovače balíček úložiště](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples).

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

* Přejděte nainstalovaný místně. Postupujte podle [tyto pokyny](https://golang.org/doc/install) v případě potřeby.
* Aktivní účet Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet][] před tím, než začnete.
* Stávající služby Event Hubs oboru názvů a Centrum událostí. Tyto entity můžete vytvořit podle pokynů v [v tomto článku](event-hubs-create.md).

## <a name="install-go-package"></a>Nainstalujte balíček Go

Získat balíček Go pro Event Hubs s využitím `go get` nebo `dep`. Příklad:

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

## <a name="import-packages-in-your-code-file"></a>Importovat balíčky v souboru s kódem

K importu balíčků Go, použijte následující příklad kódu:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

## <a name="create-service-principal"></a>Vytvoření instančního objektu

Vytvořte nový instanční objekt služby podle pokynů v [vytvoření instančního objektu Azure pomocí příkazového řádku Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). Zadané přihlašovací údaje uložte ve vašem prostředí s následujícími názvy. Balíčky služby Event Hubs a Azure SDK for Go jsou předem nakonfigurovány pro vyhledání tyto názvy proměnných:

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Teď vytvořte poskytovatele ověřování pro službu Event Hubs klienta, který používá tyto přihlašovací údaje:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

## <a name="create-event-hubs-client"></a>Vytvoření klienta služby Event Hubs

Následující kód vytvoří klienta Event Hubs:

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

## <a name="send-messages"></a>Odesílání zpráv

V následujícím fragmentu kódu pomocí (1) můžete interaktivně odesílání zpráv z terminálu nebo (2) pro odesílání zpráv v rámci programu:

```go
// 1. send messages at the terminal
ctx = context.Background()
reader := bufio.NewReader(os.Stdin)
for {
    fmt.Printf("Input a message to send: ")
    text, _ := reader.ReadString('\n')
    hub.Send(ctx, eventhubs.NewEventFromString(text))
}

// 2. send messages within program
ctx = context.Background()
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!")
```

## <a name="extras"></a>Funkce

Získání ID oddílů v Centru událostí:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n, info.PartitionIDs)
```

## <a name="next-steps"></a>Další postup

Získáte další informace o službě Event Hubs na následujících stránkách:

* [Příjem událostí pomocí třídy EventProcessorHost](event-hubs-go-get-started-receive-eph.md)
* [Přehled služby Event Hubs][Event Hubs overview]
* [Vytvoření centra událostí](event-hubs-create.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[bezplatný účet]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
