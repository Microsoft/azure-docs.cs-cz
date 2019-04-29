---
title: Odesílání a příjem událostí pomocí jazyka Go - Azure Event Hubs | Dokumentace Microsoftu
description: Tento článek poskytuje návod pro vytváření aplikací v jazyce Go, která zasílá události ze služby Azure Event Hubs.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: shvija
ms.openlocfilehash: 823ebc985c77785f8b48d12d5919dbbd1b2b1459
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821693"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-go"></a>Odesílání událostí do nebo přijímat události ze služby Event Hubs pomocí jazyka Go
Azure Event Hubs je platforma pro streamování velkých objemů dat a služba pro ingestování událostí, která je schopná přijmout a zpracovat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Podrobnější přehled služby Event Hubs najdete v tématech [Přehled služby Event Hubs](event-hubs-about.md) a [Funkce služby Event Hubs](event-hubs-features.md).

Tento kurz popisuje, jak psát aplikace, přejděte k odesílání událostí do nebo přijímat události z centra událostí. 

> [!NOTE]
> Tento rychlý start si můžete stáhnout jako ukázku z [GitHubu](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs), nahradit řetězce `EventHubConnectionString` a `EventHubName`, hodnotami pro vaše centrum událostí a spustit. Alternativně můžete vytvořit vlastní řešení podle kroků v tomto kurzu.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

- Přejděte nainstalovaný místně. Postupujte podle [tyto pokyny](https://golang.org/doc/install) v případě potřeby.
- Aktivní účet Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet][] před tím, než začnete.
- **Vytvořit obor názvů služby Event Hubs a centra událostí**. Použití [webu Azure portal](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, vaše aplikace potřebuje ke komunikaci s centrem událostí. Pokud chcete vytvořit obor názvů a centra událostí, postupujte podle pokynů v [v tomto článku](event-hubs-create.md).

## <a name="send-events"></a>Odesílání událostí
Tato část ukazuje, jak vytvořit aplikaci, přejděte k odesílání událostí do centra událostí. 

### <a name="install-go-package"></a>Nainstalujte balíček Go

Získat balíček Go pro Event Hubs s využitím `go get` nebo `dep`. Příklad:

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

### <a name="import-packages-in-your-code-file"></a>Importovat balíčky v souboru s kódem

K importu balíčků Go, použijte následující příklad kódu:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

### <a name="create-service-principal"></a>Vytvoření instančního objektu

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

### <a name="create-event-hubs-client"></a>Vytvoření klienta služby Event Hubs

Následující kód vytvoří klienta Event Hubs:

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

### <a name="write-code-to-send-messages"></a>Napsání kódu pro odesílání zpráv

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
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!"))
```

### <a name="extras"></a>Funkce

Získání ID oddílů v Centru událostí:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n", info.PartitionIDs)
```

Spusťte aplikaci k odesílání událostí do centra událostí. 

Blahopřejeme! Nyní jste odeslali zprávy do centra událostí.

## <a name="receive-events"></a>Příjem událostí

### <a name="create-a-storage-account-and-container"></a>Vytvoření účtu úložiště a kontejneru

Například zapůjčení na oddíly a kontrolní body stavu události datového proudu jsou sdílené mezi příjemci pomocí kontejneru služby Azure Storage. Můžete vytvořit účet úložiště a kontejner s Go SDK, ale můžete také vytvořit podle pokynů v [účty Azure storage](../storage/common/storage-create-storage-account.md).

Ukázky pro vytvoření úložiště artefaktů s Go SDK jsou k dispozici v [Go ukázkové úložiště](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) a v ukázce odpovídající do tohoto kurzu.

### <a name="go-packages"></a>Přejděte balíčky

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

### <a name="import-packages-in-your-code-file"></a>Importovat balíčky v souboru s kódem

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

### <a name="create-service-principal"></a>Vytvoření instančního objektu

Vytvořte nový instanční objekt služby podle pokynů v [vytvoření instančního objektu Azure pomocí příkazového řádku Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). Zadané přihlašovací údaje uložte ve vašem prostředí s následujícími názvy: Obě sady Azure SDK for Go a Event Hubs balíčku je předem nakonfigurované vyhledejte tyto názvy proměnných.

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

### <a name="get-metadata-struct"></a>Získat metadata – struktura

Získáte strukturu s metadata o prostředí Azure pomocí Azure Go SDK. Najít správné koncové body pomocí dalších činností této struktuře.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

### <a name="create-credential-helper"></a>Vytvoření přihlašovacích údajů pomocné rutiny 

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

### <a name="create-a-check-pointer-and-a-leaser"></a>Vytvořit kontrolu ukazatel a leaser 

Vytvoření **leaser**, je zodpovědný za zapůjčování oddílu na konkrétní příjemce a **zkontrolujte ukazatel**, odpovídají za zápis kontrolní body pro datový proud zpráv tak, aby ostatní příjemce může začít čtení z správný posun.

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

### <a name="construct-event-processor-host"></a>Vytvoření třídy Event Processor Host

Teď máte části potřebné k sestavení kompletních EventProcessorHost, následujícím způsobem. Stejné **StorageLeaserCheckpointer** slouží jako leaser a vrácení ukazatele, jak je popsáno výše:

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

### <a name="create-handler"></a>Vytvoření obslužné rutiny 

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

### <a name="write-code-to-receive-messages"></a>Napsání kódu pro přijímání zpráv

Všechna nastavení, můžete spouštět pomocí třídy Event Processor Host `Start(context)` zajistit jeho trvale pracovat, nebo s `StartNonBlocking(context)` ke spuštění, dokud zprávy jsou k dispozici.

V tomto kurzu, spustí se a spustí takto: najdete v ukázce Githubu pro příklad použití `StartNonBlocking`:

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="next-steps"></a>Další postup
V následujících článcích:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkce a terminologii používané v Azure Event Hubs](event-hubs-features.md)
- [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)


<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[bezplatný účet]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
