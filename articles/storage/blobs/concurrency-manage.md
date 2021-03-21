---
title: Správa souběžnosti v úložišti objektů BLOB
titleSuffix: Azure Storage
description: Naučte se spravovat více zapisovačů do objektu BLOB implementací optimistické nebo pesimistické souběžnosti ve vaší aplikaci. Optimistická souběžnost kontroluje hodnotu ETag pro objekt BLOB a porovnává ho se zadaným ETag. Pesimistická souběžnost používá exkluzivní zapůjčení k uzamknutí objektu BLOB na jiné moduly pro zápis.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: ea0bed0884a3a03e2cd15b274b2afb0f054b0cbd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96523441"
---
# <a name="managing-concurrency-in-blob-storage"></a>Správa souběžnosti v úložišti objektů BLOB

Moderní aplikace často mají více uživatelů, kteří současně prohlížejí a aktualizují data. Vývojáři aplikací musí pečlivě zvážit, jak zajistit předvídatelné prostředí koncovým uživatelům, zejména u scénářů, ve kterých může aktualizovat stejná data více uživatelů. Existují tři hlavní strategie souběžnosti dat, které vývojáři obvykle uvažují:  

- **Optimistická souběžnost**: aplikace, která provádí aktualizaci, bude v rámci své aktualizace určit, jestli se data od posledního načtení těchto dat změnila. Například pokud dva uživatelé, kteří si prohlížejí stránku wikiwebu, provedou aktualizaci této stránky, pak musí platforma wiki zajistit, aby druhá aktualizace přepsala první aktualizaci. Musí také zajistit, aby oba uživatelé pochopili, zda byla aktualizace úspěšná. Tato strategie se nejčastěji používá ve webových aplikacích.

- **Pesimistická souběžnost**: aplikace, která se pokouší provést aktualizaci, bude pořizovat zámek u objektu, který ostatním uživatelům brání v aktualizaci dat, dokud se zámek neuvolní. Například v případě scénáře replikace primárních a sekundárních dat, ve kterém pouze primární provádí aktualizace, primární obvykle uchovává výhradní zámek dat po delší dobu, aby se zajistilo, že nikdo jiný ho nemůže aktualizovat.

- **Poslední zapisovač WINS**: přístup, který umožňuje operacím aktualizace pokračovat, aniž by bylo nutné určit, zda jiná aplikace aktualizovala data od jejich čtení. Tento přístup se obvykle používá, když jsou data rozdělená takovým způsobem, že více uživatelů nebude mít přístup ke stejným datům současně. Může být také užitečné, pokud jsou zpracovávány krátkodobé datové streamy.

Azure Storage podporuje všechny tři strategie, i když je jejich schopnost poskytovat úplnou podporu pro optimistickou a pesimistickou souběžnost. Azure Storage bylo navrženo pro zajištění silného modelu konzistence, který zaručuje, že po provedení služby operace INSERT nebo Update vrátí následné operace čtení nejnovější aktualizaci.

Kromě výběru vhodné strategie souběžnosti by měli vývojáři také vědět, jak platforma úložiště izoluje změny, zejména změny u stejného objektu napříč transakcemi. Azure Storage používá izolaci snímků k povolení operací čtení souběžně s operacemi zápisu v rámci jednoho oddílu. Izolace snímků zaručuje, že všechny operace čtení vrátí konzistentní snímek dat, i když se objevují aktualizace.

Pro správu přístupu k objektům blob a kontejnerům můžete použít buď optimistické, nebo pesimistické modely souběžnosti. Pokud explicitně neurčíte strategii, pak ve výchozím nastavení bude služba WINS posledního zapisovače.  

## <a name="optimistic-concurrency"></a>Optimistická souběžnost

Azure Storage přiřadí identifikátor každému uloženému objektu. Tento identifikátor se aktualizuje pokaždé, když se u objektu provede operace zápisu. Identifikátor se vrátí klientovi jako součást odpovědi HTTP GET v hlavičce ETag, která je definovaná protokolem HTTP.

Klient, který provádí aktualizaci, může odeslat původní značku ETag spolu s podmíněnou hlavičkou, aby se zajistilo, že k aktualizaci dojde pouze v případě, že byla splněna určitá podmínka. Pokud je například zadána hlavička **If-Match** , Azure Storage ověří, že hodnota ETag zadaná v žádosti o aktualizaci je stejná jako značka ETag pro objekt, který se aktualizuje. Další informace o podmíněných hlavičkách naleznete v tématu [určení podmíněných hlaviček pro operace BLOB Service](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).

Osnova tohoto procesu je následující:  

1. Načte objekt BLOB z Azure Storage. Odpověď obsahuje hodnotu hlavičky ETag protokolu HTTP, která identifikuje aktuální verzi objektu.
1. Při aktualizaci objektu BLOB zahrňte hodnotu ETag, kterou jste dostali v kroku 1 v podmíněné hlavičce **If-Match** žádosti o zápis. Azure Storage porovná hodnotu ETag v požadavku s aktuální hodnotou ETag objektu BLOB.
1. Pokud je aktuální hodnota ETag objektu BLOB odlišná od zadaného v podmíněné hlavičce **If-Match** zadané v požadavku, Azure Storage vrátí stavový kód HTTP 412 (Předběžná podmínka se nezdařila). Tato chyba indikuje klientovi, že objekt BLOB aktualizoval jiný proces, protože ho nejdřív načetl klient.
1. Pokud má aktuální hodnota ETag objektu BLOB stejnou verzi jako ETag v podmíněné hlavičce **If-Match** v žádosti, Azure Storage provede požadovanou operaci a aktualizuje aktuální hodnotu ETag objektu BLOB.  

Následující příklady kódu ukazují, jak vytvořit podmínku **If-Match** u žádosti o zápis, která kontroluje hodnotu ETag pro objekt BLOB. Azure Storage vyhodnotí, zda je aktuální ETag objektu BLOB stejný jako značka ETag poskytnutá na žádosti a provede operaci zápisu pouze v případě, že se dvě hodnoty ETag shodují. Pokud se v provizorním objektu BLOB aktualizoval jiný proces, Azure Storage vrátí stavovou zprávu HTTP 412 (Předběžná podmínka selhala).  

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstrateOptimisticConcurrencyBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
public void DemonstrateOptimisticConcurrencyBlob(string containerName, string blobName)
{
    Console.WriteLine("Demonstrate optimistic concurrency");

    // Parse connection string and create container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExists();

    // Create test blob. The default strategy is last writer wins, so
    // write operation will overwrite existing blob if present.
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);
    blockBlob.UploadText("Hello World!");

    // Retrieve the ETag from the newly created blob.
    string originalETag = blockBlob.Properties.ETag;
    Console.WriteLine("Blob added. Original ETag = {0}", originalETag);

    /// This code simulates an update by another client.
    string helloText = "Blob updated by another client.";
    // No ETag was provided, so original blob is overwritten and ETag updated.
    blockBlob.UploadText(helloText);
    Console.WriteLine("Blob updated. Updated ETag = {0}", blockBlob.Properties.ETag);

    // Now try to update the blob using the original ETag value.
    try
    {
        Console.WriteLine(@"Attempt to update blob using original ETag
                            to generate if-match access condition");
        blockBlob.UploadText(helloText, accessCondition: AccessCondition.GenerateIfMatchCondition(originalETag));
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine(@"Precondition failure as expected.
                                Blob's ETag does not match.");
        }
        else
        {
            throw;
        }
    }
    Console.WriteLine();
}
```

---

Azure Storage také podporuje další podmíněná záhlaví, včetně **If-Modified-od**, **Pokud-unmodified – od** a **If-None-Match**. Další informace najdete v tématu [určení podmíněných hlaviček pro operace služby BLOB Service](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).  

## <a name="pessimistic-concurrency-for-blobs"></a>Pesimistická souběžnost pro objekty blob

K uzamknutí objektu BLOB pro výhradní použití můžete získat zapůjčení. Po získání zapůjčení zadáte dobu trvání zapůjčení. Konečná zapůjčená adresa může být platná od 15 do 60 sekund. Zapůjčení může také být nekonečné, což platí pro výhradní zámek. Omezené zapůjčení můžete prodloužit tak, že ho budete moct prodloužit, a až budete s ním hotovi, můžete zapůjčení uvolnit. Azure Storage automaticky uvolňuje konečná zapůjčení, pokud vyprší jejich platnost.  

Zapůjčení umožňují podporovat různé strategie synchronizace, včetně exkluzivních operací čtení a sdílení, exkluzivních operací čtení/výhradního čtení a sdílených operací čtení/výhradních operací čtení. Pokud existuje zapůjčení, Azure Storage vynutila výhradní přístup k operacím zápisu pro držitele zapůjčení. Nicméně zajištění výhradního oprávnění pro operace čtení vyžaduje, aby vývojář zajistil, že všechny klientské aplikace používají ID zapůjčení a že v jednom okamžiku má k dispozici pouze jeden klient s platným ID zapůjčení. Operace čtení, které neobsahují ID zapůjčení, mají za následek sdílené čtení.  

Následující příklady kódu ukazují, jak získat exkluzivní zapůjčení u objektu blob, aktualizovat obsah objektu BLOB poskytnutím ID zapůjčení a pak uvolnit zapůjčení. Pokud je zapůjčení aktivní a ID zapůjčení není pro požadavek na zápis k dispozici, operace zápisu se nezdařila s kódem chyby 412 (Předběžná podmínka se nezdařila).  

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstratePessimisticConcurrencyBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
public void DemonstratePessimisticConcurrencyBlob(string containerName, string blobName)
{
    Console.WriteLine("Demonstrate pessimistic concurrency");

    // Parse connection string and create container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExists();

    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);
    blockBlob.UploadText("Hello World!");
    Console.WriteLine("Blob added.");

    // Acquire lease for 15 seconds.
    string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
    Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

    // Update blob using lease. This operation should succeed.
    const string helloText = "Blob updated";
    var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
    blockBlob.UploadText(helloText, accessCondition: accessCondition);
    Console.WriteLine("Blob updated using an exclusive lease");

    // Simulate another client attempting to update to blob without providing lease.
    try
    {
        // Operation will fail as no valid lease was provided.
        Console.WriteLine("Now try to update blob without valid lease.");
        blockBlob.UploadText("Update operation will fail without lease.");
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine(@"Precondition failure error as expected.
                                Blob lease not provided.");
        }
        else
        {
            throw;
        }
    }

    // Release lease proactively.
    blockBlob.ReleaseLease(accessCondition);
    Console.WriteLine();
}
```

---

## <a name="pessimistic-concurrency-for-containers"></a>Pesimistická souběžnost pro kontejnery

Zapůjčení u kontejnerů umožňuje stejné strategie synchronizace, které jsou podporované pro objekty blob, včetně exkluzivního čtení/sdíleného čtení, exkluzivního zápisu/výhradního čtení a sdíleného zápisu/výhradní čtení. U kontejnerů se ale výhradní zámek vynutil jenom při operacích odstranění. Aby mohl klient odstranit kontejner s aktivním zapůjčením, musí do žádosti o odstranění zahrnout aktivní ID zapůjčení. Všechny ostatní operace s kontejnerem budou úspěšné v případě pronajatého kontejneru bez ID zapůjčení.  

## <a name="next-steps"></a>Další kroky

* [Určení podmíněných hlaviček pro operace Blob service](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)
* [Kontejner zapůjčení](/rest/api/storageservices/lease-container)
* [Operace Lease Blob](/rest/api/storageservices/lease-blob)
