---
title: Azure Cosmos DB přílohy
description: Tento článek představuje přehled příloh Azure Cosmos DB.
author: aliuy
ms.author: andrl
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/07/2020
ms.reviewer: sngun
ms.openlocfilehash: 56e8212d60494d469e225c25edbbd331c601ea6b
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91804156"
---
# <a name="azure-cosmos-db-attachments"></a>Azure Cosmos DB přílohy

Azure Cosmos DB přílohy jsou speciální položky, které obsahují odkazy na přidružená metadata s externím objektem BLOB nebo mediálním souborem.

Azure Cosmos DB podporuje dva typy příloh:

* **Nespravované přílohy** jsou obálkou kolem odkazu identifikátoru URI na objekt blob, který je uložený v externí službě (například Azure Storage, OneDrive atd.). Tento přístup je podobný jako uložení vlastnosti identifikátoru URI ve standardní Azure Cosmos DB položce.
* **Spravované přílohy** jsou objekty blob spravované a ukládané interně pomocí Azure Cosmos DB a zveřejněné prostřednictvím systémem generovaného mediaLink.


> [!NOTE]
> Příloha je starší verze funkce. Podpora je zavedená do oboru, pokud už tuto funkci používáte.
> 
> Místo používání příloh doporučujeme, abyste používali Azure Blob Storage jako účelově vytvořenou službu BLOB Storage pro ukládání dat objektů BLOB. Metadata týkající se objektů blob můžete dál ukládat společně s odkazy referenčního identifikátoru URI v Azure Cosmos DB jako vlastnosti položky. Uložení těchto dat v Azure Cosmos DB poskytuje možnost dotazování na metadata a odkazy na objekty blob uložené ve službě Azure Blob Storage.
> 
> Společnost Microsoft se zavazuje, že před úplným zastaralými přílohami dokončí minimálně 36 měsíců – což bude oznámeno další datum.

## <a name="known-limitations"></a>Známá omezení

Spravované přílohy Azure Cosmos DB se liší od podpory standardních položek – pro které nabízí neomezenou škálovatelnost, globální distribuci a integraci s jinými službami Azure.

- Přílohy nejsou podporovány ve všech verzích sad SDK Azure Cosmos DB.
- Spravované přílohy jsou omezené na 2 GB úložiště na jeden databázový účet.
- Spravované přílohy nejsou kompatibilní s globální distribucí Azure Cosmos DB a nereplikují se do různých oblastí.

## <a name="migrating-attachments-to-azure-blob-storage"></a>Migrace příloh do Azure Blob Storage

Doporučujeme migrovat Azure Cosmos DB přílohy do Azure Blob Storage pomocí následujících kroků:

1. Zkopírujte data příloh ze zdrojového Azure Cosmos DB kontejneru do cílového kontejneru Azure Blob Storage.
2. Ověřte nahraná data objektu BLOB v cílovém kontejneru Azure Blob Storage.
3. V případě potřeby přidejte odkazy identifikátoru URI do objektů BLOB obsažených v Azure Blob Storage jako vlastnosti řetězce v rámci datové sady Azure Cosmos DB.
4. Refaktorujte kód aplikace pro čtení a zápis objektů BLOB z nového kontejneru Azure Blob Storage.

Následující ukázka kódu dotnet ukazuje, jak kopírovat přílohy z Azure Cosmos DB do úložiště objektů BLOB v Azure jako součást migrace pomocí Azure Cosmos DB .NET SDK v2 a Azure Blob Storage .NET SDK V12. Nezapomeňte nahradit `<placeholder values>` zdrojový Azure Cosmos DB účet a cílit na kontejner úložiště objektů BLOB v Azure.

```csharp

using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;

namespace attachments
{
    class Program
    {
        private static string cosmosAccount = "<Your_Azure_Cosmos_account_URI>";
        private static string cosmosKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>";
        private static string cosmosDatabaseName = "<Your_Azure_Cosmos_database>";
        private static string cosmosCollectionName = "<Your_Azure_Cosmos_collection>";
        private static string storageConnectionString = "<Your_Azure_Storage_connection_string>";
        private static string storageContainerName = "<Your_Azure_Storage_container_name>";
        private static DocumentClient cosmosClient = new DocumentClient(new Uri(cosmosAccount), cosmosKey);
        private static BlobServiceClient storageClient = new BlobServiceClient(storageConnectionString);
        private static BlobContainerClient storageContainerClient = storageClient.GetBlobContainerClient(storageContainerName);

        static void Main(string[] args)
        {
            CopyAttachmentsToBlobsAsync().Wait();
        }

        private async static Task CopyAttachmentsToBlobsAsync()
        {
            Console.WriteLine("Copying Azure Cosmos DB Attachments to Azure Blob Storage ...");

            int totalCount = 0;
            string docContinuation = null;

            // Iterate through each item (document in v2) in the Azure Cosmos DB container (collection in v2) to look for attachments.
            do
            {
                FeedResponse<dynamic> response = await cosmosClient.ReadDocumentFeedAsync(
                    UriFactory.CreateDocumentCollectionUri(cosmosDatabaseName, cosmosCollectionName),
                    new FeedOptions
                    {
                        MaxItemCount = -1,
                        RequestContinuation = docContinuation
                    });
                docContinuation = response.ResponseContinuation;

                foreach (Document document in response)
                {
                    string attachmentContinuation = null;
                    PartitionKey docPartitionKey = new PartitionKey(document.Id);

                    // Iterate through each attachment within the item (if any).
                    do
                    {
                        FeedResponse<Attachment> attachments = await cosmosClient.ReadAttachmentFeedAsync(
                            document.SelfLink,
                            new FeedOptions
                            {
                                PartitionKey = docPartitionKey,
                                RequestContinuation = attachmentContinuation
                            }
                        );
                        attachmentContinuation = attachments.ResponseContinuation;

                        foreach (var attachment in attachments)
                        {
                            // Download the attachment in to local memory.
                            MediaResponse content = await cosmosClient.ReadMediaAsync(attachment.MediaLink);

                            byte[] buffer = new byte[content.ContentLength];
                            await content.Media.ReadAsync(buffer, 0, buffer.Length);

                            // Upload the locally buffered attachment to blob storage
                            string blobId = String.Concat(document.Id, "-", attachment.Id);

                            Azure.Response<BlobContentInfo> uploadedBob = await storageContainerClient.GetBlobClient(blobId).UploadAsync(
                                new MemoryStream(buffer, writable: false),
                                true
                            );

                            Console.WriteLine("Copied attachment ... Item Id: {0} , Attachment Id: {1}, Blob Id: {2}", document.Id, attachment.Id, blobId);
                            totalCount++;

                            // Clean up attachment from Azure Cosmos DB.
                            // Warning: please verify you've succesfully migrated attachments to blog storage prior to cleaning up Azure Cosmos DB.
                            // await cosmosClient.DeleteAttachmentAsync(
                            //     attachment.SelfLink,
                            //     new RequestOptions { PartitionKey = docPartitionKey }
                            // );

                            // Console.WriteLine("Cleaned up attachment ... Document Id: {0} , Attachment Id: {1}", document.Id, attachment.Id);
                        }

                    } while (!string.IsNullOrEmpty(attachmentContinuation));
                }
            }
            while (!string.IsNullOrEmpty(docContinuation));

            Console.WriteLine("Finished copying {0} attachments to blob storage", totalCount);
        }
    }
}

```

## <a name="next-steps"></a>Další kroky

- Začínáme se službou [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet)
- Získání odkazů na používání příloh prostřednictvím [sady Azure Cosmos DB .NET SDK v2](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.attachment?view=azure-dotnet&preserve-view=true)
- Získat odkazy na používání příloh prostřednictvím [Azure Cosmos DB Java SDK v2](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.attachment?view=azure-java-stable&preserve-view=true)
- Získat odkazy na používání příloh prostřednictvím [Azure Cosmos DB REST API](https://docs.microsoft.com/rest/api/cosmos-db/attachments)
