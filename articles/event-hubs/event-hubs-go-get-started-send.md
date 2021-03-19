---
title: 'Rychlý Start: odesílání a příjem událostí pomocí jazyka přejít – Azure Event Hubs'
description: 'Rychlý Start: Tento článek poskytuje návod pro vytvoření aplikace v cestách, která odesílá události z Azure Event Hubs.'
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 59cd0d757108e7579ce389d216b0ee4d569e12fd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87002450"
---
# <a name="quickstart-send-events-to-or-receive-events-from-event-hubs-using-go"></a>Rychlý Start: odeslání událostí do nebo příjem událostí z Event Hubs pomocí jazyka přejít
Azure Event Hubs je platforma pro streamování velkých objemů dat a služba pro ingestování událostí, která je schopná přijmout a zpracovat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Podrobnější přehled služby Event Hubs najdete v tématech [Přehled služby Event Hubs](event-hubs-about.md) a [Funkce služby Event Hubs](event-hubs-features.md).

V tomto kurzu se dozvíte, jak psát aplikace v cestách pro odesílání událostí nebo přijímání událostí z centra událostí. 

> [!NOTE]
> Tento rychlý start si můžete stáhnout jako ukázku z [GitHubu](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs), nahradit řetězce `EventHubConnectionString` a `EventHubName`, hodnotami pro vaše centrum událostí a spustit. Alternativně můžete vytvořit vlastní řešení podle kroků v tomto kurzu.

## <a name="prerequisites"></a>Předpoklady

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

- Místně se nainstaluje. V případě potřeby postupujte podle [těchto pokynů](https://golang.org/doc/install) .
- Aktivní účet Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet][].
- **Vytvoří obor názvů Event Hubs a centrum událostí**. Použijte [Azure Portal](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Pokud chcete vytvořit obor názvů a centrum událostí, postupujte podle pokynů v [tomto článku](event-hubs-create.md).

## <a name="send-events"></a>Odesílání událostí
V této části se dozvíte, jak vytvořit aplikaci v cestách pro odesílání událostí do centra událostí. 

### <a name="install-go-package"></a>Nainstalovat balíček přejít

Získejte balíček přejít pro Event Hubs s `go get` nebo `dep` . Například:

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

### <a name="import-packages-in-your-code-file"></a>Import balíčků do souboru kódu

K importu balíčků přejít použijte následující příklad kódu:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

### <a name="create-service-principal"></a>Vytvoření instančního objektu

Vytvořte nový instanční objekt podle pokynů v tématu [Vytvoření instančního objektu Azure pomocí Azure CLI 2,0](/cli/azure/create-an-azure-service-principal-azure-cli). Ve svém prostředí Uložte zadané přihlašovací údaje s následujícími názvy. Azure SDK pro Go i balíčky Event Hubs jsou předem nakonfigurované tak, aby vyhledaly tyto názvy proměnných:

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Nyní vytvořte poskytovatele autorizace pro klienta Event Hubs, který používá tyto přihlašovací údaje:

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

V následujícím fragmentu kódu použijte (1) k interaktivnímu posílání zpráv z terminálu nebo (2) k odesílání zpráv v rámci programu:

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

Získejte ID oddílů v centru událostí:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n", info.PartitionIDs)
```

Spusťte aplikaci, aby odesílala události do centra událostí. 

Gratulujeme! Nyní jste odeslali zprávy do centra událostí.

## <a name="receive-events"></a>Příjem událostí

### <a name="create-a-storage-account-and-container"></a>Vytvoření účtu úložiště a kontejneru

Stav, jako je například zapůjčení oddílů a kontrolních bodů v datovém proudu událostí, je sdílen mezi přijímači pomocí kontejneru Azure Storage. Pomocí sady SDK můžete vytvořit účet úložiště a kontejner, ale můžete ho také vytvořit podle pokynů v [tématu informace o účtech úložiště Azure](../storage/common/storage-account-create.md).

Ukázky pro vytváření artefaktů úložiště s využitím sady SDK jsou k dispozici v [úložišti ukázek přejít](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) a v ukázce odpovídající tomuto kurzu.

### <a name="go-packages"></a>Balíčky přejít

Chcete-li dostávat zprávy, Získejte balíčky přejít pro Event Hubs s `go get` nebo `dep` :

```bash
go get -u github.com/Azure/azure-event-hubs-go/...
go get -u github.com/Azure/azure-amqp-common-go/...
go get -u github.com/Azure/go-autorest/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
dep ensure -add github.com/Azure/go-autorest
```

### <a name="import-packages-in-your-code-file"></a>Import balíčků do souboru kódu

K importu balíčků přejít použijte následující příklad kódu:

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

Vytvořte nový instanční objekt podle pokynů v tématu [Vytvoření instančního objektu Azure pomocí Azure CLI 2,0](/cli/azure/create-an-azure-service-principal-azure-cli). Ve svém prostředí Uložte zadané přihlašovací údaje s následujícími názvy: Azure SDK pro Go i Event Hubs balíčky jsou předem nakonfigurované tak, aby vyhledaly tyto názvy proměnných.

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

V dalším kroku vytvořte poskytovatele autorizace pro klienta Event Hubs, který používá tyto přihlašovací údaje:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="get-metadata-struct"></a>Získat strukturu metadat

Získejte strukturu s metadaty týkajícími se vašeho prostředí Azure pomocí sady Azure cestách SDK. Pozdější operace používají tuto strukturu k nalezení správných koncových bodů.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

### <a name="create-credential-helper"></a>Vytvořit pomocníka pověření 

Vytvořte pomocníka přihlašovacích údajů, který používá předchozí přihlašovací údaje pro Azure Active Directory (AAD) k vytvoření přihlašovacích údajů sdíleného přístupového podpisu (SAS) pro úložiště. Poslední parametr říká tomuto konstruktoru, aby používal stejné proměnné prostředí, jako dříve použitý:

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

### <a name="create-a-check-pointer-and-a-leaser"></a>Vytvoření ukazatele pro kontrolu a pronájmu 

Vytvořte **zapůjčenou** osobu odpovědnou za zapůjčení oddílu na konkrétního příjemce a **Kontrolní ukazatel**, který zodpovídá za zápis kontrolních bodů pro datový proud zpráv, aby ostatní příjemci mohli začít číst ze správného posunu.

V současné době je k dispozici jeden **StorageLeaserCheckpointer** , který používá stejný kontejner úložiště ke správě zapůjčení a kontrolních bodů. Kromě účtu úložiště a názvů kontejnerů potřebuje **StorageLeaserCheckpointer** přihlašovací údaje vytvořené v předchozím kroku a strukturu prostředí Azure pro správný přístup ke kontejneru.

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

### <a name="construct-event-processor-host"></a>Sestavit hostitele procesoru událostí

Nyní máte figurky potřebné k vytvoření EventProcessorHost, a to následujícím způsobem. Stejný **StorageLeaserCheckpointer** se používá jako zapůjčení i ukazatel kontroly, jak je popsáno výše:

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

Nyní vytvořte obslužnou rutinu a zaregistrujte ji u hostitele procesoru událostí. Po spuštění hostitele to platí pro všechny ostatní zadané obslužné rutiny pro příchozí zprávy:

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

Díky nastavení všeho můžete spustit hostitele procesoru událostí s nástrojem, `Start(context)` aby bylo možné trvale běžet, nebo `StartNonBlocking(context)` Spustit pouze tak dlouho, dokud jsou k dispozici zprávy.

Tento kurz se spustí a spustí se takto: Příklady najdete v ukázce z webu GitHub `StartNonBlocking` :

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
