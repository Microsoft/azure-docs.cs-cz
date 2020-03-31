---
title: Připojení k úložišti znalostí (preview) pomocí Power BI
titleSuffix: Azure Cognitive Search
description: Připojte úložiště znalostí (preview) Azure Cognitive Search s Power BI pro analýzu a průzkum.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: 4fd71a7f322cb2672eb485f17e4de2619a7c2d2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270032"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Propojení úložiště znalostí s Power BI

> [!IMPORTANT] 
> Úložiště znalostí je v současné době ve verzi Public Preview. Funkce náhledu je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Rozhraní REST API verze 2019-05-06-Preview](search-api-preview.md) poskytuje funkce náhledu. V současné době je omezená podpora portálu a žádná podpora sady .NET SDK.

V tomto článku se dozvíte, jak se připojit k úložišti znalostí pomocí Power Query v aplikaci Power BI Desktop a prozkoumat ho. Můžete začít rychleji se šablonami nebo vytvořit vlastní řídicí panel od začátku.

+ Postupujte podle kroků v [části Vytvoření úložiště znalostí na webu Azure Portal](knowledge-store-create-portal.md) nebo Vytvoření úložiště [znalostní hospo- účelem azure kognitivního vyhledávání pomocí rest](knowledge-store-create-rest.md) k vytvoření ukázkového úložiště znalostí použitého v tomto návodu. Budete také potřebovat název účtu Azure Storage, který jste použili k vytvoření úložiště znalostí, spolu s jeho přístupový klíč z portálu Azure.

+ [Nainstalovaný Power BI Desktop](https://powerbi.microsoft.com/downloads/)

## <a name="sample-power-bi-template---azure-portal-only"></a>Ukázková šablona Power BI – jenom portál Azure

Při vytváření [úložiště znalostí pomocí portálu Azure](knowledge-store-create-portal.md)máte možnost stáhnout [šablonu Power BI](https://github.com/Azure-Samples/cognitive-search-templates) na druhé stránce Průvodce **importem dat.** Tato šablona poskytuje několik vizualizací, jako je WordCloud a Network Navigator, pro textový obsah. 

Klikněte na **Získat šablonu Power BI** na stránce Přidat kognitivní **dovednosti** a načtěte a stáhněte si šablonu z jejího veřejného umístění GitHubu. Průvodce upraví šablonu tak, aby odpovídala tvaru dat, jak je zachycen v projekcích úložiště znalostí určených v průvodci. Z tohoto důvodu se stažená šablona bude při každém spuštění průvodce lišit za předpokladu, že se budou řídit různými datovými vstupy a výběry dovedností.

![Ukázka azure kognitivní vyhledávání Power BI šablony](media/knowledge-store-connect-power-bi/powerbi-sample-template-portal-only.png "Ukázková šablona Power BI")

> [!NOTE]
> I když se šablona stáhne, když je průvodce v polovině letu, budete muset počkat, až se úložiště znalostí skutečně vytvoří v úložišti Azure Table, než ho budete moci použít.

## <a name="connect-with-power-bi"></a>Propojení s Power BI

1. Spusťte Power BI Desktop a klikněte na **Získat data**.

1. V okně **Získat data** vyberte **Azure**a pak vyberte Azure **Table Storage**.

1. Klikněte na **Připojit**.

1. Název **účtu nebo adresa URL**zadejte do účtu Azure Storage (úplná adresa URL se vytvoří za vás).

1. Pokud se zobrazí výzva, zadejte klíč účtu úložiště.

1. Vyberte tabulky obsahující hotel kontroluje data vytvořená v předchozích návodech. 

   + Pro portál návod, názvy tabulek jsou *hotelReviewsSsDocument*, *hotelReviewsSsEntities*, *hotelReviewsSsKeyPhrases*, a *hotelReviewsSsPages*. 
   
   + Pro rest návod, názvy tabulek jsou *hotelReviewsDocument*, *hotelReviewsPages*, *hotelReviewsKeyPhrases*a *hotelReviewsSentiment*.

1. Klepněte na tlačítko **Načíst**.

1. Na horním pásu karet klikněte na **Upravit dotazy** a otevřete Power **Query Editor**.

   ![Otevřít Power Query](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "Otevřít Power Query")

1. Vyberte *hotelReviewsSsDocument*a potom odeberte sloupce *PartitionKey*, *RowKey*a *Timestamp.* 
   ![Úpravy tabulek](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "Úpravy tabulek")

1. Kliknutím na ikonu s protilehlými šipkami v pravé horní části tabulky rozbalte *obsah*. Po zobrazení seznamu sloupců vyberte všechny sloupce a odznačte sloupce začínající na metadata. Klepnutím na **tlačítko OK** zobrazíte vybrané sloupce.

   ![Úpravy tabulek](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "Rozbalení obsahu")

1. Změňte datový typ pro následující sloupce klepnutím na ikonu ABC-123 v levém horním rohu sloupce.

   + Pro *soubor content.latitude* a *Content.longitude*vyberte **možnost Desetinné číslo**.
   + V *části Content.reviews_date* a *Content.reviews_dateAdded*vyberte **možnost Datum a čas**.

   ![Změna datových typů](media/knowledge-store-connect-power-bi/powerbi-change-type.png "Změna datových typů")

1. Vyberte *hotelReviewsSsPages*a opakujte kroky 9 a 10, abyste odstranili sloupce a rozbalili *obsah*.
1. Změňte datový typ pro *Content.SentimentScore* na **desítkové číslo**.
1. Vyberte *hotelReviewsSsKeyPhrases* a opakujte kroky 9 a 10 odstranit sloupce a rozbalte *obsah*. Pro tuto tabulku nejsou žádné změny datového typu.

1. Na panelu příkazů klepněte na **tlačítko Zavřít a použít**.

1. Klikněte na dlaždici Model v levém navigačním podokně a ověřte, zda Power BI zobrazuje relace mezi všemi třemi tabulkami.

   ![Ověřit vztahy](media/knowledge-store-connect-power-bi/powerbi-relationships.png "Ověřit vztahy")

1. Poklepejte na jednotlivé relace a ujistěte se, že **směr křížového filtru** je nastaven na **obě**.  To umožňuje, aby se vizuály aktualizovaly při použití filtru.

1. Kliknutím na dlaždici Sestava v levém navigačním podokně prozkoumejte data prostřednictvím vizualizací. Pro textová pole jsou tabulky a karty užitečné vizualizace. Můžete si vybrat pole z každé ze tří tabulek, které chcete vyplnit v tabulce nebo na kartě. 

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

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás můžou stát peníze. Prostředky můžete odstraňovat jednotlivě nebo můžete odstranit skupinu prostředků, a odstranit tak celou sadu prostředků najednou.

Můžete najít a spravovat prostředky na portálu pomocí odkazu **Všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, nezapomeňte, že jste omezeni na tři indexy, indexery a zdroje dat. Můžete odstranit jednotlivé položky na portálu, abyste zůstali pod limitem.

## <a name="next-steps"></a>Další kroky

Informace o tom, jak prozkoumat toto úložiště znalostí pomocí Průzkumníka úložiště, najdete v následujícím článku.

> [!div class="nextstepaction"]
> [Zobrazení s využitím Průzkumníka služby Storage](knowledge-store-view-storage-explorer.md)