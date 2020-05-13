---
title: Využití indexu objektů BLOB ke správě a hledání dat v úložišti objektů BLOB v Azure
description: Podívejte se na příklady použití značek indexu objektů BLOB ke kategorizaci, správě a dotazování pro zjišťování objektů BLOB.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 9ba151aa1ddc7f4b14d5f4ec7f1990e2fd760602
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121231"
---
# <a name="utilize-blob-index-tags-preview-to-manage-and-find-data-on-azure-blob-storage"></a>Použití značek indexu objektů BLOB (Preview) ke správě a hledání dat v úložišti objektů BLOB v Azure

Rejstříky objektů BLOB kategorizují data v účtu úložiště s využitím atributů značek klíč-hodnota. Tyto značky jsou automaticky indexovány a zpřístupněny jako Queryable multidimenzionální index, který umožňuje snadno najít data. V tomto článku se dozvíte, jak pomocí značek indexu objektů BLOB nastavit, získat a najít data.

Další informace o indexu objektů BLOB najdete v tématu [Správa a hledání dat v Azure Blob Storage s využitím indexu objektů BLOB (Preview)](storage-manage-find-blobs.md).

> [!NOTE]
> Index objektu BLOB je ve verzi Public Preview a je dostupný v oblastech **Francie – střed** a Francie – **jih** . Další informace o této funkci spolu se známými problémy a omezeních najdete v tématu [Správa a hledání dat v Azure Blob Storage s využitím indexu objektů BLOB (Preview)](storage-manage-find-blobs.md).

## <a name="prerequisites"></a>Požadavky
# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
- Předplatné je zaregistrované a schválené pro přístup k náhledu indexu objektů BLOB.
- Přístup k [Azure Portal](https://portal.azure.com/)

# <a name="net"></a>[.NET](#tab/net)
Protože index BLOB je ve verzi Public Preview, balíček úložiště .NET je vydaný v kanálu NuGet Preview. Tato knihovna se může změnit mezi nyní a když se změní na oficiální. 

1. V aplikaci Visual Studio přidejte adresu URL `https://azuresdkartifacts.blob.core.windows.net/azure-sdk-for-net/index.json` do zdrojů balíčků NuGet. 

   Informace o postupu najdete v tématu [zdroje balíčků](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources).

2. V okně Správce balíčků NuGet vyhledejte balíček **Azure. Storage. BLOBs** a do svého projektu nainstalujte Version **12.5.0-dev. 20200422.2** . Můžete také spustit příkaz```Install-Package Azure.Storage.Blobs -Version12.5.0-dev.20200422.2```

   Informace o postupu najdete v tématu [vyhledání a instalace balíčku](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package).

3. Do horní části souboru kódu přidejte následující příkazy using.
```csharp
using Azure;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
```
---

## <a name="upload-a-new-blob-with-index-tags"></a>Nahrát nový objekt BLOB se značkami indexu
# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V [Azure Portal](https://portal.azure.com/)vyberte svůj účet úložiště. 

2. Přejděte na možnost **kontejnery** v části **BLOB Service**, vyberte svůj kontejner.

3. Kliknutím na tlačítko **nahrát** otevřete okno Odeslat a vyhledejte soubor, který se má nahrát jako objekt blob bloku, a přejděte do místního systému souborů.

4. Rozbalíte **Rozšířené** rozevírací seznam a přejdete do oddílu **značky indexu objektu BLOB** .

5. Zadejte značky indexu objektu BLOB klíč/hodnota, které chcete použít pro vaše data.

6. Kliknutím na tlačítko **nahrát** nahrajte objekt BLOB.

![Nahrávání dat pomocí značek indexu objektů BLOB](media/storage-blob-index-concepts/blob-index-upload-data-with-tags.png)

# <a name="net"></a>[.NET](#tab/net)
Následující příklad ukazuje, jak vytvořit doplňovací objekt BLOB se značkami nastavenými při vytváření.
```csharp
static async Task BlobIndexTagsOnCreate()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create an append blob
          AppendBlobClient appendBlobWithTags = container.GetAppendBlobClient("myAppendBlob0.logs");
         
          // Blob Index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Sealed", "false" },
              { "Content", "logs" },
              { "Date", "2020-04-20" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags.CreateAsync(appendOptions);
      }
      finally
      {
      }
   }
```

---

## <a name="get-set-and-update-blob-index-tags"></a>Získání, nastavení a aktualizace značek indexu objektů BLOB
# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V [Azure Portal](https://portal.azure.com/)vyberte svůj účet úložiště. 

2. Přejděte na možnost **kontejnery** v části **BLOB Service**, vyberte svůj kontejner.

3. Vyberte požadovaný objekt BLOB ze seznamu objektů BLOB v rámci vybraného kontejneru.

4. Na kartě Přehled objektů BLOB se zobrazí vlastnosti vašeho objektu BLOB včetně všech **značek indexu objektu BLOB** .

5. U objektu blob můžete získat, nastavit, upravit nebo odstranit všechny značky indexu klíč/hodnota.

6. Kliknutím na tlačítko **Uložit** potvrďte všechny aktualizace svého objektu BLOB.

![Získání, nastavení, aktualizace a odstranění značek indexu objektů BLOB u objektů](media/storage-blob-index-concepts/blob-index-get-set-tags.png)

# <a name="net"></a>[.NET](#tab/net)
```csharp
static async Task BlobIndexTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create a new append blob
          AppendBlobClient appendBlob = container.GetAppendBlobClient("myAppendBlob1.logs");
          await appendBlob.CreateAsync();

          // Set or Update Blob Index tags on existing blob
          Dictionary<string, string> tags = new Dictionary<string, string>
          {
              { "Project", "Contoso" },
              { "Status", "Unprocessed" },
              { "Sealed", "true" }
          };
          await appendBlob.SetTagsAsync(tags);

          // Get Blob Index tags
          Response<IDictionary<string, string>> tagsResponse = await appendBlob.GetTagsAsync();
          Console.WriteLine(appendBlob.Name);
          foreach (KeyValuePair<string, string> tag in tagsResponse.Value)
          {
              Console.WriteLine($"{tag.Key}={tag.Value}");
          }

          // List Blobs with all options returned including Blob Index tags
          await foreach (BlobItem blobItem in container.GetBlobsAsync(BlobTraits.All))
          {
              Console.WriteLine(Environment.NewLine + blobItem.Name);
              foreach (KeyValuePair<string, string> tag in blobItem.Tags)
              {
                  Console.WriteLine($"{tag.Key}={tag.Value}");
              }
          }

          // Delete existing Blob Index tags by replacing all tags
          Dictionary<string, string> noTags = new Dictionary<string, string>();
          await appendBlob.SetTagsAsync(noTags);

      }
      finally
      {
      }
   }
```

---

## <a name="filter-and-find-data-with-blob-index-tags"></a>Filtrování a hledání dat pomocí značek indexu objektů BLOB

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

V rámci Azure Portal filtr značek indexu objektů BLOB automaticky použije `@container` parametr k určení oboru vybraného kontejneru. Pokud chcete vyfiltrovat a vyhledávat data označená v celém účtu úložiště, použijte prosím naše REST API, sady SDK nebo nástroje.

1. V [Azure Portal](https://portal.azure.com/)vyberte svůj účet úložiště. 

2. Přejděte na možnost **kontejnery** v části **BLOB Service**, vyberte svůj kontejner.

3. Vyberte tlačítko **Filtr značek indexu objektů BLOB** pro filtrování v rámci vybraného kontejneru.

4. Zadejte klíč značky indexu objektu BLOB a hodnotu značky.

5. Vyberte tlačítko **Filtr značek indexu objektů BLOB** a přidejte další filtry značek (až 10).

![Filtrování a hledání tagovaných objektů pomocí značek indexu objektů BLOB](media/storage-blob-index-concepts/blob-index-tag-filter-within-container.png)

# <a name="net"></a>[.NET](#tab/net)
```csharp
static async Task FindBlobsByTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container1 = serviceClient.GetBlobContainerClient("mycontainer");
      BlobContainerClient container2 = serviceClient.GetBlobContainerClient("mycontainer2");

      // Blob Index queries and selection
      String singleEqualityQuery = @"""Archive"" = 'false'";
      String andQuery = @"""Archive"" = 'false' AND ""Priority"" = '01'";
      String rangeQuery = @"""Date"" >= '2020-04-20' AND ""Date"" <= '2020-04-30'";
      String containerScopedQuery = @"@container = 'mycontainer' AND ""Archive"" = 'false'";

      String queryToUse = containerScopedQuery;

      try
      {
          // Create a container
          await container1.CreateIfNotExistsAsync();
          await container2.CreateIfNotExistsAsync();

          // Create append blobs
          AppendBlobClient appendBlobWithTags0 = container1.GetAppendBlobClient("myAppendBlob00.logs");
          AppendBlobClient appendBlobWithTags1 = container1.GetAppendBlobClient("myAppendBlob01.logs");
          AppendBlobClient appendBlobWithTags2 = container1.GetAppendBlobClient("myAppendBlob02.logs");
          AppendBlobClient appendBlobWithTags3 = container2.GetAppendBlobClient("myAppendBlob03.logs");
          AppendBlobClient appendBlobWithTags4 = container2.GetAppendBlobClient("myAppendBlob04.logs");
          AppendBlobClient appendBlobWithTags5 = container2.GetAppendBlobClient("myAppendBlob05.logs");
           
          // Blob Index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Archive", "false" },
              { "Priority", "01" },
              { "Date", "2020-04-20" }
          };
          
          CreateAppendBlobOptions appendOptions2 = new CreateAppendBlobOptions();
          appendOptions2.Tags = new Dictionary<string, string>
          {
              { "Archive", "true" },
              { "Priority", "02" },
              { "Date", "2020-04-24" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags0.CreateAsync(appendOptions);
          await appendBlobWithTags1.CreateAsync(appendOptions);
          await appendBlobWithTags2.CreateAsync(appendOptions2);
          await appendBlobWithTags3.CreateAsync(appendOptions);
          await appendBlobWithTags4.CreateAsync(appendOptions2);
          await appendBlobWithTags5.CreateAsync(appendOptions2);

          // Find Blobs given a tags query
          Console.WriteLine("Find Blob by Tags query: " + queryToUse + Environment.NewLine);

          List<FilterBlobItem> blobs = new List<FilterBlobItem>();
          await foreach (FilterBlobItem filterBlobItem in serviceClient.FindBlobsByTagsAsync(queryToUse))
          {
              blobs.Add(filterBlobItem);
          }

          foreach (var filteredBlob in blobs)
          {
              Console.WriteLine($"BlobIndex result: ContainerName= {filteredBlob.ContainerName}, " +
                  $"BlobName= {filteredBlob.Name}");
          }

      }
      finally
      {
      }
   }
```

---

## <a name="lifecycle-management-with-blob-index-tag-filters"></a>Správa životního cyklu s filtry značek indexu objektů BLOB

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V [Azure Portal](https://portal.azure.com/)vyberte svůj účet úložiště. 

2. Přejděte do možnosti **Správa životního cyklu** v části **BLOB Service** .

3. Vyberte *Přidat pravidlo* a potom vyplňte pole formuláře sady akcí.

4. Vyberte možnost sada filtrů pro přidání volitelného filtru pro porovnávání předpon a index objektů BLOB ![ Přidat filtry značek indexu objektů BLOB pro správu životního cyklu.](media/storage-blob-index-concepts/blob-index-match-lifecycle-filter-set.png)

5. Vyberte **zkontrolovat + přidat** a zkontrolujte ![ pravidlo správy životního cyklu nastavení pravidla s příkladem filtru značek indexu objektů BLOB.](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)

6. Vyberte **Přidat** a použijte nové pravidlo pro zásady správy životního cyklu.

# <a name="net"></a>[.NET](#tab/net)
Zásady [správy životního cyklu](storage-lifecycle-management-concepts.md) se aplikují pro každý účet úložiště na úrovni roviny ovládacího prvku. V případě rozhraní .NET nainstalujte [knihovnu úložiště Microsoft Azure Management verze 16.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/) nebo vyšší, abyste mohli využít výhod filtru shody indexu objektů BLOB v rámci pravidla správy životního cyklu.

---

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o indexu objektů BLOB. Přečtěte si téma [Správa a hledání dat v Azure Blob Storage s využitím indexu objektů BLOB (Preview)](storage-manage-find-blobs.md ) .

Přečtěte si další informace o správě životního cyklu. Viz [Správa životního cyklu služby Azure Blob Storage](storage-lifecycle-management-concepts.md) .
