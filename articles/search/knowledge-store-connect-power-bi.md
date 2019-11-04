---
title: Připojení k znalostnímu obchodu pomocí Power BI
titleSuffix: Azure Cognitive Search
description: Připojte si Azure Kognitivní hledání Knowledge Store pomocí Power BI pro analýzu a průzkum.
author: lisaleib
manager: nitinme
ms.author: v-lilei
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 03f28cb40708b7ec77a0a342b5ec1b6faeaa8e3b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73485153"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Připojit znalostní bázi Knowledge Store s Power BI

> [!Note]
> Znalostní báze je ve verzi Preview a neměl by se používat v produkčním prostředí. Tuto funkci poskytuje portál a [REST API pro hledání verze 2019-05-06-Preview](search-api-preview.md) . V tuto chvíli není dostupná žádná podpora sady .NET SDK.
>

V tomto článku se dozvíte, jak se připojit k znalostnímu obchodu a prozkoumat ho pomocí Power Query v aplikaci Power BI Desktop. Můžete začít rychleji pracovat se šablonami nebo si vytvořit vlastní řídicí panel úplně od začátku.

## <a name="prerequisites"></a>Předpoklady

+ [Nainstalovat Power BI Desktop](https://powerbi.microsoft.com/downloads/)

+ Budete potřebovat znalostní bázi s projekcí do služby Azure Table Storage. Také budete potřebovat název účtu Azure Storage, který jste použili k vytvoření obchodu Knowledge Store, a jeho přístupový klíč z Azure Portal.

Pokud chcete pracovat s ukázkovým úložištěm Knowledge Store, postupujte podle pokynů pro [Vytvoření úložiště znalostí](knowledge-store-create-portal.md).

## <a name="create-a-custom-report"></a>Vytvoření vlastní sestavy

1. Spusťte Power BI Desktop a klikněte na **získat data**.

1. V okně **získat data** vyberte **Azure**a pak vyberte **Azure Table Storage**.

1. Klikněte na **Připojit**.

1. V poli **název účtu nebo adresa URL**zadejte název účtu Azure Storage (bude vytvořena úplná adresa URL).

1. Pokud se zobrazí výzva, zadejte klíč účtu úložiště.

1. Vyberte tabulky *hotelReviewsSsDocument*, *hotelReviewsSsKeyPhrases*a *hotelReviewsSsPages* . Tyto tabulky jsou Azure Table probíhají ukázková data v hotelu a zahrnují rozšíření AI, která byla vybrána při vytvoření znalostní databáze.

1. Klikněte na **načíst**.

1. Na horním pásu karet kliknutím na **Upravit dotazy** otevřete **Editor Power Query**.

   ![Otevřít Power Query](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "Otevřít Power Query")

1. Vyberte *hotelReviewsSsDocument*a pak odstraňte sloupce *PartitionKey*, *RowKey*a *timestamp* . 

   ![Upravit tabulky](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "Upravit tabulky")

1. Kliknutím na ikonu s protilehlými šipkami v pravé horní části tabulky rozbalte *obsah*. Když se zobrazí seznam sloupců, vyberte všechny sloupce a pak zrušte výběr sloupců, které začínají na metadata. Kliknutím na tlačítko **OK** zobrazíte vybrané sloupce.

   ![Upravit tabulky](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "Rozbalit obsah")

1. Kliknutím na ikonu ABC-123 v levém horním rohu sloupce změňte datový typ pro následující sloupce.

   + Pro položku *Content. Latitude* a *Content. Zeměpis*vyberte **desetinné číslo**.
   + Pro *Content. reviews_date* a *Content. reviews_dateAdded*vyberte **Datum a čas**.

   ![Změna datových typů](media/knowledge-store-connect-power-bi/powerbi-change-type.png "Změna datových typů")

1. Vyberte *hotelReviewsSsPages*a potom opakováním kroků 9 a 10 sloupce odstraňte a rozbalte *obsah*.
1. Změňte datový typ *Content. SentimentScore* na **desetinné číslo**.
1. Vyberte *hotelReviewsSsKeyPhrases* a opakováním kroků 9 a 10 sloupce odstraňte a rozbalte *obsah*. Pro tuto tabulku neexistují žádné úpravy datových typů.

1. Na panelu příkazů klikněte na **Zavřít a použít**.

1. Klikněte na dlaždici model v levém navigačním podokně a ověřte, že Power BI zobrazuje vztahy mezi všemi třemi tabulkami.

   ![Ověřit relace](media/knowledge-store-connect-power-bi/powerbi-relationships.png "Ověřit relace")

1. Dvakrát klikněte na každý vztah a ujistěte se, že je **směr křížového filtru** nastavený na **obojí**.  Tím umožníte, aby se vizuály aktualizovaly při použití filtru.

<!-- ## Try with larger data sets

We purposely kept the data set small to avoid charges for a demo walkthrough. For a more realistic experience, you can create and then attach a billable Cognitive Services resource to enable a larger number of transactions against the sentiment analyzer, keyphrase extraction, and language detector skills.

Create new containers in Azure Blob storage and upload each CSV file to its own container. Specify one of these containers in the data source creation step in Import data wizard.

| Description | Link |
|-------------|------|
| Free tier   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Small (500 Records) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Medium (6000 Records)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Large (Full dataset 35000 Records) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Be aware that very large data sets are expensive to process. This one costs roughly $1000 U.S dollars.|

In the enrichment step of the wizard, attach a billable [Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) resource, created at the *S0* tier, in the same region as Azure Cognitive Search to use larger data sets. 

  ![Create a Cognitive Services resource](media/knowledge-store-connect-power-bi/create-cognitive-service.png "Create a Cognitive Services resource") -->

## <a name="clean-up"></a>Vyčištění

Pokud pracujete ve vlastním předplatném, je vhodné na konci projektu zjistit, zda stále potřebujete prostředky, které jste vytvořili. Prostředky, které se na něm zbývá, můžou mít náklady na peníze. Prostředky můžete odstranit jednotlivě nebo odstranit skupinu prostředků, abyste odstranili celou sadu prostředků.

Prostředky můžete najít a spravovat na portálu pomocí odkazu **všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, pamatujte na to, že jste omezeni na tři indexy, indexery a zdroje dat. Jednotlivé položky na portálu můžete odstranit, aby zůstaly pod limitem.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak prozkoumat toto úložiště Knowledge Store pomocí Průzkumník služby Storage, najdete v následujícím článku.

> [!div class="nextstepaction"]
> [Zobrazit s Průzkumník služby Storage](knowledge-store-view-storage-explorer.md)

Informace o tom, jak vytvořit úložiště znalostí pomocí rozhraní REST API a post, najdete v následujícím článku.  

> [!div class="nextstepaction"]
> [Vytvoření úložiště znalostí v REST](knowledge-store-howto.md)
