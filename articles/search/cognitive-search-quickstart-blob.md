---
title: 'Quickstart: Create a skillset in the Azure portal'
titleSuffix: Azure Cognitive Search
description: In this portal quickstart, learn how to use the Import data wizard to add cognitive skills to an indexing pipeline in Azure Cognitive Search. Skills include Optical Character Recognition (OCR) and natural language processing.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: 7c782a066d27ce3d58b5ef43f5790d9964d4de37
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406533"
---
# <a name="quickstart-create-an-azure-cognitive-search-cognitive-skillset-in-the-azure-portal"></a>Quickstart: Create an Azure Cognitive Search cognitive skillset in the Azure portal

A skillset is an AI feature that extracts information and structure from large undifferentiated text or image files, and makes it indexable and searchable for full text search queries in Azure Cognitive Search. 

In this quickstart, you'll combine services and data in the Azure cloud to create the skillset. Once everything is in place, you'll run the **Import data** wizard in the portal to pull it all together. The end result is a searchable index populated with data created by AI processing that you can query in the portal ([Search explorer](search-explorer.md)).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-services-and-load-data"></a>Create services and load data

This quickstart uses Azure Cognitive Search, Azure Blob storage, and [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) for the AI. 

Because the workload is so small, Cognitive Services is tapped behind the scenes to provide free processing for up to 20 transactions daily when invoked from Azure Cognitive Search. As long as you use the sample data we provide, you can skip creating or attaching a Cognitive Services resource.

1. [Stáhněte si ukázková data](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) sestávající z malé sady souborů různých typů. Unzip the files.

1. [Create an Azure storage account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) or [find an existing account](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) under your current subscription. 

   Choose the same region as Azure Cognitive Search. Choose the StorageV2 (general purpose V2) account type if you want to try out the knowledge store feature later, in another walkthrough. Otherwise, choose any type.

1. Open the Blob services pages and create a container. You can use the default public access level. 

1. In container, click **Upload** to upload the sample files you downloaded in the first step. Notice that you have a wide range of content types, including images and application files that are not full text searchable in their native formats.

   ![Zdrojové soubory ve službě Azure Blob Storage](./media/cognitive-search-quickstart-blob/sample-data.png)

1. [Create an Azure Cognitive Search service](search-create-service-portal.md) or [find an existing service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under the same subscription. You can use a free service for this quickstart.

<!-- 1. You are almost done with this resource, but before you leave these pages, use a link on the left navigation pane to open the **Access Keys** page. In many tutorials, especially those that use the REST API, you will need a connection string to retrieve data from Blob storage. A connection string looks similar to the following example: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net` -->

You are now ready to move on the Import data wizard.

## <a name="run-the-import-data-wizard"></a>Run the Import data wizard

In the search service Overview page, click **Import data** on the command bar to set up cognitive enrichment in four steps.

  ![Příkaz pro import dat](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>Krok 1: Vytvoření zdroje dat

1. In **Connect to your data**, choose **Azure Blob storage**, select the Storage account and container you created. Zadejte název pro zdroj dat a u ostatních položek nechejte výchozí hodnoty. 

   ![Konfigurace služby Azure Blob](./media/cognitive-search-quickstart-blob/blob-datasource.png)

1. Continue to the next page.

### <a name="step-2-add-cognitive-skills"></a>Krok 2: Přidání kognitivních dovedností

Next, add cognitive skills to invoke natural language processing. The sample data consists of 12 files, so the free allotment of 20 transaction on Cognitive Services is sufficient for this quickstart. Because we aren't using OCR, only the non-image files will be counted, cracked, and used in this process.

1. For this quickstart, we are using the **Free** Cognitive Services resource.

   ![Připojení Cognitive Services](media/cognitive-search-quickstart-blob/cog-search-attach.png)

1. Expand **Add skills** and select skills that perform natural language processing. Pro tento rychlý start zvolte rozpoznávání entit pro lidi, organizace a lokality.

   ![Připojení Cognitive Services](media/cognitive-search-quickstart-blob/skillset.png)

1. Accept the default source field: `content`. This might seem like a small target, but for Azure blobs the `content` field contains most of the blob document (for example, a Word doc or PowerPoint deck), which makes it a good candidate.

1. Continue to the next page.

> [!NOTE]
> Dovednosti zpracování přirozeného jazyka pracují s textovým obsahem v ukázkové sadě dat. Since we didn't select the OCR option, the JPEG and PNG files found in the sample data set won't be processed in this quickstart. 

### <a name="step-3-configure-the-index"></a>Krok 3: Konfigurace indexu

In Azure Cognitive Search, an index contains your searchable content and the **Import data** wizard can usually create the schema for you by sampling the data source. In this step, review the generated schema and potentially revise any settings. Below is the default schema created for the demo Blob data set.

Pro tento rychlý start průvodce odvedl dobrou práci při nastavování rozumných výchozích hodnot: 

+ Default name is *azureblob-index* based on the data source type. 

+ Default fields are based on the original source data field (`content`), plus the output fields (`people`, `organizations`, and `locations`) created by the cognitive skills. Default data types are inferred from metadata and data sampling.

+ Default document key is *metadata_storage_path* (selected because the field contains unique values).

+ Default attributes are **Retrievable** and **Searchable** for these fields. **Searchable** indicates a field can be searched. **Retrievable** means it can be returned in results. The wizard assumes you want these fields to be retrievable and searchable because you created them via a skillset.

  ![Pole indexu](media/cognitive-search-quickstart-blob/index-fields.png)

Notice the strikethrough and question mark on the **Retrievable** attribute by the `content` field. For text-heavy blob documents, the `content` field contains the bulk of the file, potentially running into thousands of lines. If you need to pass file contents to client code, make sure that **Retrievable** stays selected. Otherwise, consider clearing this attribute on `content` if the extracted elements (`people`, `organizations`, and `locations`) are sufficient for your purposes.

Marking a field as **Retrievable** does not mean that the field *must* be present in the search results. You can precisely control search results composition by using the **$select** query parameter to specify which fields to include. For text-heavy fields like `content`, the **$select** parameter is your solution for providing manageable search results to the human users of your application, while ensuring client code has access to all the information it needs via the **Retrievable** attribute.
  
Continue to the next page.

### <a name="step-4-configure-the-indexer"></a>Krok 4: Konfigurace indexeru

Indexer je prostředek vysoké úrovně, který řídí proces indexování. It specifies the data source name, a target index, and frequency of execution. The **Import data** wizard creates several objects, and of them is always an indexer that you can run repeatedly.

1. In the **Indexer** page, you can accept the default name and click the **Once** schedule option to run it immediately. 

   ![Definice indexeru](media/cognitive-search-quickstart-blob/indexer-def.png)

1. Click **Submit** to create and simultaneously run the indexer.

## <a name="monitor-status"></a>Monitor status

Cognitive skills indexing takes longer to complete than typical text-based indexing. To monitor progress, go to the Overview page and click **Indexers** in the middle of page.

The warning occurs because JPG and PNG image files are in the data source, and we omitted the OCR skill from this pipeline. You'll also find truncation notifications. Extraction is limited to 32,000 characters on the Free tier.

  ![Azure Cognitive Search notification](./media/cognitive-search-quickstart-blob/indexer-notification.png)

Indexování a rozšiřování může nějakou dobu trvat. To je důvod, proč se pro seznamování s touto službou doporučuje používat menší sady dat. 

In the Azure portal, you can also monitor the Notifications activity log for a clickable **Azure Cognitive Search notification** status link. Execution may take several minutes to complete.

## <a name="query-in-search-explorer"></a>Dotaz v Průzkumníku služby Hledání

Až se index vytvoří, můžete odesílat dotazy, aby se z indexu vracely dokumenty. Na portálu použijte **Průzkumníka služby Hledání**, pomocí kterého spusťte dotazy a zobrazte si výsledky. 

1. Na stránce řídicího panelu služby Search klikněte na panelu příkazů na **Průzkumník služby Hledání**.

1. V horní části vyberte **Změnit index**, abyste mohli vybrat index, který jste vytvořili.

1. Enter a search string to query the index, such as `search=Microsoft&searchFields=Organizations`.

Výsledky se vrátí ve formátu JSON, který může být dost podrobný a jen obtížně čitelný, a to hlavně v případě velkých dokumentů, které pocházejí z objektů blob Azure. Pokud není možné si výsledky snadno projít, můžete v dokumentech hledat pomocí CTRL+F. For this query, you could search within the JSON for specific terms. 

CTRL+F vám navíc může pomoct určit, kolik dokumentů v sadě výsledků je. Pro objekty blob Azure portál volí jako klíč metada_storage_path, protože každá hodnota je v dokumentu jedinečná. Pomocí CTRL+F vyhledejte metadata_storage_path a získáte počet dokumentů. 

  ![Příkaz Průzkumníka služby Hledání](./media/cognitive-search-quickstart-blob/search-explorer.png)

## <a name="takeaways"></a>Shrnutí

You've now created your first skillset and learned important concepts useful for prototyping an enriched search solution using your own data.

Mezi klíčové koncepty, které jste si snad osvojili, patří závislost na zdrojích dat Azure. A skillset is bound to an indexer, and indexers are Azure and source-specific. I když tento rychlý start používá službu Azure Blob Storage, je možné použít i jiné zdroje dat Azure. For more information, see [Indexers in Azure Cognitive Search](search-indexer-overview.md).

Další důležitý koncept je to, že dovednosti pracují se vstupními poli. Na portálu si musíte zvolit jedno zdrojové pole pro všechny dovednosti. V kódu se jako vstup dají použít jiná pole nebo výstup z nadřazené dovednosti.

Output is directed to a search index, and there is a mapping between name-value pairs created during indexing and individual fields in your index. Vnitřně portál nastaví [poznámky](cognitive-search-concept-annotations-syntax.md) a definuje [sadu dovedností](cognitive-search-defining-skillset.md), čímž určuje pořadí operací v obecném toku. Tyto kroky jsou na portálu skryté, ale až začnete psát kód, budou tyto koncepty důležité.

Finally, you learned that can verify content by querying the index. In the end, what Azure Cognitive Search provides is a searchable index, which you can query using either the [simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) or [fully extended query syntax](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search). Index, který obsahuje rozšířená pole, se v ničem neliší od ostatních indexů. If you want to incorporate standard or [custom analyzers](search-analyzers.md), [scoring profiles](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [synonyms](search-synonyms.md), [faceted filters](search-filters-facets.md), geo-search, or any other Azure Cognitive Search feature, you can certainly do so.

## <a name="clean-up-resources"></a>Vyčištění prostředků

When you're working in your own subscription, it's a good idea at the end of a project to identify whether you still need the resources you created. Resources left running can cost you money. You can delete resources individually or delete the resource group to delete the entire set of resources.

You can find and manage resources in the portal, using the **All resources** or **Resource groups** link in the left-navigation pane.

If you are using a free service, remember that you are limited to three indexes, indexers, and data sources. You can delete individual items in the portal to stay under the limit. 

> [!Tip]
> If you want to repeat this exercise or try a different AI enrichment walkthrough, delete the indexer in the portal. Deleting the indexer resets the free daily transaction counter back to zero for Cognitive Services processing.

## <a name="next-steps"></a>Další kroky

You can create skillsets using the portal, .NET SDK, or REST API. To further your knowledge, try the REST API using Postman and more sample data.

> [!div class="nextstepaction"]
> [Tutorial: Add structure to "unstructured content" with AI enrichment](cognitive-search-tutorial-blob.md)