---
title: 'Úvodní příručka: Odesílání a přijímání událostí pomocí Go – Azure Event Hubs'
description: 'Úvodní příručka: Tento článek poskytuje návod pro vytvoření aplikace Go, která odesílá události z Centra událostí Azure.'
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: e5f52d0ddbf9a66d974732d6d98ca8a5b09cc2d0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "73720581"
---
# <a name="quickstart-send-events-to-or-receive-events-from-event-hubs-using-go"></a>Úvodní příručka: Odesílejte události do centra událostí nebo z nich přijímáte pomocí služby Go
Azure Event Hubs je platforma pro streamování velkých objemů dat a služba pro ingestování událostí, která je schopná přijmout a zpracovat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Podrobnější přehled služby Event Hubs najdete v tématech [Přehled služby Event Hubs](event-hubs-about.md) a [Funkce služby Event Hubs](event-hubs-features.md).

Tento kurz popisuje, jak psát aplikace Go pro odesílání událostí nebo přijímat události z centra událostí. 

> [!NOTE]
> Tento rychlý start si můžete stáhnout jako ukázku z [GitHubu](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs), nahradit řetězce `EventHubConnectionString` a `EventHubName`, hodnotami pro vaše centrum událostí a spustit. Alternativně můžete vytvořit vlastní řešení podle kroků v tomto kurzu.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

- Přejít nainstalován místně. V případě potřeby [postupujte podle těchto pokynů.](https://golang.org/doc/install)
- Aktivní účet Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,][] než začnete.
- **Vytvořte obor názvů Event Hubs a centrum událostí**. Na [portálu Azure](https://portal.azure.com) můžete vytvořit obor názvů typu Event Hubs a získat přihlašovací údaje pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Chcete-li vytvořit obor názvů a centrum událostí, postupujte podle postupu v [tomto článku](event-hubs-create.md).

## <a name="send-events"></a>Odesílání událostí
V této části se zobrazí, jak vytvořit aplikaci Go pro odesílání událostí do centra událostí. 

### <a name="install-go-package"></a>Nainstalovat balíček Go

Získejte balíček Go pro `go get` `dep`centra událostí s nebo . Například:

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

### <a name="import-packages-in-your-code-file"></a>Import balíčků v souboru kódu

Chcete-li importovat balíčky Go, použijte následující příklad kódu:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

### <a name="create-service-principal"></a>Vytvoření instančního objektu

Vytvořte nový instanční objekt podle pokynů v [části Vytvoření instančního objektu Azure pomocí Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). Uložte zadaný pověření ve vašem prostředí s následujícími názvy. Balíčky Azure SDK for Go a Event Hubs jsou předem nakonfigurované tak, aby hledaly tyto názvy proměnných:

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Nyní vytvořte autorizačního zprostředkovatele pro klienta Event Hubs, který používá tato pověření:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="create-event-hubs-client"></a>Vytvořit klienta Event Hubs

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

V následujícím úryvku použijte (1) k interaktivnímu odesílání zpráv z terminálu nebo (2) k odesílání zpráv v rámci programu:

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

### <a name="extras"></a>Extra

Získejte ID oddílů v centru událostí:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n", info.PartitionIDs)
```

Spusťte aplikaci a odešlete události do centra událostí. 

Blahopřejeme! Nyní jste odeslali zprávy do centra událostí.

## <a name="receive-events"></a>Příjem událostí

### <a name="create-a-storage-account-and-container"></a>Vytvoření účtu úložiště a kontejneru

Stav, jako je například zapůjčení na oddíly a kontrolní body v datovém proudu událostí jsou sdíleny mezi příjemci pomocí kontejneru úložiště Azure. Můžete vytvořit účet úložiště a kontejner s Go SDK, ale můžete také vytvořit podle pokynů v [o účtech úložiště Azure](../storage/common/storage-create-storage-account.md).

Ukázky pro vytváření artefaktů úložiště pomocí sady Go SDK jsou k dispozici v [úložišti ukázky přejíací a](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) v ukázce odpovídající tomuto kurzu.

### <a name="go-packages"></a>Přejít balíčky

Chcete-li přijímat zprávy, získejte balíčky `go get` `dep`Go pro centra událostí s nebo :

```bash
go get -u github.com/Azure/azure-event-hubs-go/...
go get -u github.com/Azure/azure-amqp-common-go/...
go get -u github.com/Azure/go-autorest/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
dep ensure -add github.com/Azure/go-autorest
```

### <a name="import-packages-in-your-code-file"></a>Import balíčků v souboru kódu

Chcete-li importovat balíčky Go, použijte následující příklad kódu:

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

Vytvořte nový instanční objekt podle pokynů v [části Vytvoření instančního objektu Azure pomocí Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). Uložte zadaný pověření ve vašem prostředí s následujícími názvy: Oba Azure SDK pro Go a Event Hubs balíček jsou předem nakonfigurovány tak, aby hledat tyto názvy proměnných.

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Dále vytvořte autorizačního zprostředkovatele pro klienta Event Hubs, který používá tato pověření:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="get-metadata-struct"></a>Získat strukturu metadat

Získejte strukturu s metadaty o vašem prostředí Azure pomocí sady Azure Go SDK. Pozdější operace pomocí této struktury najít správné koncové body.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

### <a name="create-credential-helper"></a>Vytvoření pomocníka pro pověření 

Vytvořte pomocníka pro pověření, který používá předchozí přihlašovací údaje služby Azure Active Directory (AAD) k vytvoření pověření sdíleného přístupového podpisu (SAS) pro úložiště. Poslední parametr říká tomuto konstruktoru, aby používal stejné proměnné prostředí, jak bylo použito dříve:

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

### <a name="create-a-check-pointer-and-a-leaser"></a>Vytvoření kontrolního ukazatele a nájemce 

Vytvořte **nájemce**, odpovědný za zapůjčení oddílu konkrétnímu příjemci a **ukazatel kontroly**, který je zodpovědný za zápis kontrolních bodů pro datový proud zpráv, aby ostatní příjemci mohli začít číst ze správného posunu.

V současné době jeden **StorageLeaserCheckpointer** je k dispozici, který používá stejný kontejner úložiště pro správu zapůjčení a kontrolní body. Kromě účtu úložiště a názvy kontejnerů **StorageLeaserCheckpointer** potřebuje pověření vytvořené v předchozím kroku a struktury prostředí Azure pro správný přístup ke kontejneru.

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

### <a name="construct-event-processor-host"></a>Vytvořit hostitele procesoru událostí

Nyní máte kusy potřebné k vytvoření EventProcessorHost, takto. Stejný **StorageLeaserCheckpointer** se používá jako správce zapůjčení a kontrolní ukazatel, jak je popsáno výše:

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

### <a name="create-handler"></a>Vytvořit obslužnou rutinu 

Nyní vytvořte obslužnou rutinu a zaregistrujte ji u hostitele procesoru událostí. Při spuštění hostitele použije tuto a všechny další zadané obslužné rutiny na příchozí zprávy:

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

Se všemi nastavenými můžete spustit hostitele `Start(context)` procesoru událostí, aby `StartNonBlocking(context)` byl trvale spuštěn, nebo se spustit pouze tak dlouho, dokud jsou k dispozici zprávy.

Tento kurz se spustí a spustí následujícím způsobem; viz ukázka GitHubu `StartNonBlocking`pro příklad pomocí:

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="next-steps"></a>Další kroky
Přečtěte si následující články:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkce a terminologie ve službě Azure Event Hubs](event-hubs-features.md)
- [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)


<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[bezplatný účet]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
