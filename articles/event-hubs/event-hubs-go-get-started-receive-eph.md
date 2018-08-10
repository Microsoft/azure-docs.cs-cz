---
title: Příjem událostí ze služby Azure Event Hubs pomocí jazyka Go | Dokumentace Microsoftu
description: Začínáme s příjmem události ze služby Event Hubs pomocí jazyka Go
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/23/2018
ms.author: shvija
ms.openlocfilehash: eaea6adbaef7baf9bb1e617ba0a709cf14edf781
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005574"
---
# <a name="receive-events-from-event-hubs-using-go"></a>Příjem událostí ze služby Event Hubs pomocí jazyka Go

Azure Event Hubs je vysoce škálovatelné událostí systém správy, který dokáže zpracovat miliony událostí za sekundu, které umožňuje aplikacím zpracovávat a analyzovat masivní objemy dat vytvářených připojenými zařízeními a dalšími systémy. Jakmile jsou shromážděna do centra událostí, může přijímat a zpracovávat události pomocí obslužných rutin v procesu nebo předáváním k jiným systémům pro analýzu.

Další informace o službě Event Hubs, najdete v článku [Přehled služby Event Hubs][Event Hubs overview].

Tento kurz popisuje, jak chcete přijímat události z centra událostí v aplikací v jazyce Go. Postup pro odeslání události, viz [odpovídající článku odeslat](event-hubs-go-get-started-send.md).

Kód v tomto kurzu je převzatý z [ukázek Githubu](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs), které můžete zkontrolovat zobrazíte kompletní pracovní aplikace včetně importovat příkazy a deklarace proměnné.

Další příklady jsou k dispozici [události rozbočovače balíček úložiště](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu budete potřebovat následující:

* Přejděte nainstalovaný místně. Postupujte podle [tyto pokyny](https://golang.org/doc/install) v případě potřeby.
* Aktivní účet Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet][] před tím, než začnete.
* Pro příjem zpráv, musí být zpráv centra událostí cíl. Zjistěte, jak odesílat zprávy [odeslat kurzu](event-hubs-go-get-started-send.md).
* Existující centrum událostí (viz následující část).
* Existujícího účtu úložiště a kontejner (viz část po další části).

### <a name="create-an-event-hub"></a>Vytvoření centra událostí

Tento kurz pracuje s existující služby Event Hubs oboru názvů a Centrum událostí. Tyto entity můžete vytvořit podle pokynů v [v tomto článku](event-hubs-create.md).

### <a name="create-a-storage-account-and-container"></a>Vytvoření účtu úložiště a kontejneru

Například zapůjčení na oddíly a kontrolní body stavu události datového proudu jsou sdílené mezi příjemci pomocí kontejneru služby Azure Storage. Můžete vytvořit účet úložiště a kontejner s Go SDK, ale můžete také vytvořit podle pokynů v [účty Azure storage](../storage/common/storage-create-storage-account.md).

Ukázky pro vytvoření úložiště artefaktů s Go SDK jsou k dispozici v [Go ukázkové úložiště](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) a v ukázce odpovídající do tohoto kurzu.

## <a name="receive-messages"></a>Příjem zpráv

Pokud chcete přijímat zprávy, získat Go balíčky pro Event Hubs s využitím `go get` nebo `dep`:

```bash
go get -u github.com/Azure/azure-event-hubs-go/...
go get -u github.com/Azure/azure-amqp-common-go/...
go get -u github.com/Azure/go-autorest/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
dep ensure -add github.com/Azure/go-autorest
```

## <a name="import-packages-in-your-code-file"></a>Importovat balíčky v souboru s kódem

K importu balíčků Go, použijte následující příklad kódu:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
    eph "github.com/Azure/azure-event-hubs-go/eph"
    storageLeaser "github.com/Azure/azure-event-hubs-go/storage"
    azure "github.com/Azure/go-autorest/autorest/azure"
)
```

## <a name="create-service-principal"></a>Vytvoření instančního objektu

Vytvořte nový instanční objekt služby podle pokynů v [vytvoření instančního objektu Azure pomocí příkazového řádku Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). Zadané přihlašovací údaje uložte ve vašem prostředí s následujícími názvy. Azure SDK for Go a balíček Event Hubs jsou předem nakonfigurovány pro vyhledání tyto názvy proměnných.

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Dále vytvořte poskytovatele ověřování pro službu Event Hubs klienta, který používá tyto přihlašovací údaje:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

## <a name="get-metadata-struct"></a>Získat metadata – struktura

Získáte strukturu s metadata o prostředí Azure pomocí Azure Go SDK. Najít správné koncové body pomocí dalších činností této struktuře.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

## <a name="create-credential-helper"></a>Vytvoření přihlašovacích údajů pomocné rutiny 

Vytvoření přihlašovacích údajů pomocné rutiny, která používá předchozí přihlašovací údaje Azure Active Directory (AAD) k vytvoření pověření sdíleného přístupového podpisu (SAS) pro úložiště. Určuje poslední parametr tohoto konstruktoru pro použít stejné proměnné prostředí jako používané dříve:

```go
cred, err := storageLeaser.NewAADSASCredential(
    subscriptionID,
    resourceGroupName,
    storageAccountName,
    storageContainerName,
    storageLeaser.AADSASCredentialWithEnvironmentVars())
if err != nil {
    log.Fatalf("could not prepare a storage credential: %s\n", err)
}
```

## <a name="create-checkpointer-and-leaser"></a>Vytvoření Checkpointer a Leaser 

Vytvoření **Leaser**, je zodpovědný za zapůjčování oddílu na konkrétní příjemce a **Checkpointer**, odpovídají za zápis kontrolní body pro datový proud zpráv tak, aby ostatní příjemce může začít. čtení z správný posun.

V současné době jediného **StorageLeaserCheckpointer** je k dispozici, který používá stejný kontejner úložiště pro správu zapůjčení a kontrolní body. Kromě úložiště názvy účtu a kontejneru **StorageLeaserCheckpointer** potřebuje přihlašovací údaje, které vytvořili v předchozím kroku a struktura prostředí Azure správný přístup k kontejneru.

```go
leaserCheckpointer, err := storageLeaser.NewStorageLeaserCheckpointer(
    cred,
    storageAccountName,
    storageContainerName,
    azureEnv)
if err != nil {
    log.Fatalf("could not prepare a storage leaserCheckpointer: %s\n", err)
}
```

## <a name="construct-event-processor-host"></a>Vytvoření třídy Event Processor Host

Teď máte části potřebné k sestavení kompletních EventProcessorHost, následujícím způsobem. Stejné **StorageLeaserCheckpointer** slouží jako Leaser i Checkpointer, jak je popsáno výše:

```go
ctx := context.Background()
p, err := eph.New(
    ctx,
    nsName,
    hubName,
    tokenProvider,
    leaserCheckpointer,
    leaserCheckpointer)
if err != nil {
    log.Fatalf("failed to create EPH: %s\n", err)
}
defer p.Close(context.Background())
```

## <a name="create-handler"></a>Vytvoření obslužné rutiny 

Teď vytvořte obslužnou rutinu a zaregistrovat ji pomocí třídy Event Processor Host. Když je hostitel se spustil, platí to a jakékoli zadané obslužné rutiny pro příchozí zprávy:

```go
handler := func(ctx context.Context, event *eventhubs.Event) error {
    fmt.Printf("received: %s\n", string(event.Data))
    return nil
}

// register the handler with the EPH
_, err := p.RegisterHandler(ctx, handler)
if err != nil {
    log.Fatalf("failed to register handler: %s\n", err)
}
```

## <a name="receive-messages"></a>Příjem zpráv

Všechna nastavení, můžete spouštět pomocí třídy Event Processor Host `Start(context)` zajistit jeho trvale pracovat, nebo s `StartNonBlocking(context)` ke spuštění, dokud zprávy jsou k dispozici.

V tomto kurzu, spustí se a spustí takto: najdete v ukázce Githubu pro příklad použití `StartNonBlocking`:

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="notes"></a>Poznámky

Tento kurz používá jednu instanci třídy **EventProcessorHost**. Pokud chcete zvýšit propustnost a spolehlivost, měli byste spustit víc instancí **EventProcessorHost** v různých systémech. Leaser systému zajistí, že pouze jeden příjemce je přidružené a přijímá zprávy z zadaný oddíl na určitou dobu.

## <a name="next-steps"></a>Další postup

Navštivte tyto stránky, další informace o službě Event Hubs:

* [Odesílání událostí s využitím Go](event-hubs-go-get-started-send.md)
* [Přehled služby Event Hubs](event-hubs-about.md)
* [Vytvoření centra událostí](event-hubs-create.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[bezplatný účet]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
