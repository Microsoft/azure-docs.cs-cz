---
title: Připojení k znalostnímu obchodu pomocí Power BI
titleSuffix: Azure Cognitive Search
description: Připojte si Azure Kognitivní hledání Knowledge Store pomocí Power BI pro analýzu a průzkum.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 91e75b60f5324288c9f1adac59e31b9c1a1b0e9e
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89289167"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Připojit znalostní bázi Knowledge Store s Power BI

V tomto článku se dozvíte, jak se připojit k znalostnímu obchodu a prozkoumat ho pomocí Power Query v aplikaci Power BI Desktop. Můžete začít rychleji pracovat se šablonami nebo si vytvořit vlastní řídicí panel úplně od začátku. Toto krátké video demonstruje, jak můžete rozšířit možnosti práce s daty pomocí Azure Kognitivní hledání v kombinaci s Power BI.


> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3&start=593&end=663]



+ Postupujte podle kroků v [části vytvoření znalostní báze ve Azure Portal](knowledge-store-create-portal.md) nebo [vytvořte službu Azure kognitivní hledání Knowledge Store pomocí REST](knowledge-store-create-rest.md) a vytvořte ukázkové úložiště Knowledge Store použité v tomto návodu. Také budete potřebovat název účtu Azure Storage, který jste použili k vytvoření obchodu Knowledge Store, a jeho přístupová klávesa z Azure Portal.

+ [Nainstalovaný Power BI Desktop](https://powerbi.microsoft.com/downloads/)

## <a name="sample-power-bi-template---azure-portal-only"></a>Ukázka Power BI šablony – pouze Azure Portal

Při vytváření [úložiště znalostí pomocí Azure Portal](knowledge-store-create-portal.md)máte možnost stahovat [šablonu Power BI](https://github.com/Azure-Samples/cognitive-search-templates) na druhé stránce průvodce **importem dat** . Tato šablona poskytuje několik vizualizací, jako jsou WordCloud a Network navigátor, pro textový obsah. 

Kliknutím na **získat Power BI šablonu** na stránce **Přidat dovednosti pro rozpoznávání** načtěte a stáhněte šablonu z jejího veřejného umístění GitHubu. Průvodce upraví šablonu tak, aby odpovídala tvaru vašich dat, jak je zaznamenáno v projekcech služby Knowledge Store zadaných v průvodci. Z tohoto důvodu se šablona, kterou stáhnete, bude lišit při každém spuštění Průvodce za předpokladu různých vstupů dat a výběr dovedností.

![Ukázka šablony Azure Kognitivní hledání Power BI](media/knowledge-store-connect-power-bi/powerbi-sample-template-portal-only.png "Ukázka šablony Power BI")

> [!NOTE]
> I když je šablona stažena v době, kdy je průvodce v polovině letu, budete muset počkat, až bude úložiště znalostí ve službě Azure Table Storage skutečně vytvořeno, a teprve potom ho můžete použít.

## <a name="connect-with-power-bi"></a>Propojení s Power BI

1. Spusťte Power BI Desktop a klikněte na **získat data**.

1. V okně **získat data** vyberte **Azure**a pak vyberte **Azure Table Storage**.

1. Klikněte na **Připojit**.

1. V poli **název účtu nebo adresa URL**zadejte název účtu Azure Storage (bude vytvořena úplná adresa URL).

1. Pokud se zobrazí výzva, zadejte klíč účtu úložiště.

1. Vyberte tabulky obsahující data o recenzích hotelu vytvořená předchozími návody. 

   + V návodu k portálu jsou názvy tabulek *hotelReviewsSsDocument*, *hotelReviewsSsEntities*, *hotelReviewsSsKeyPhrases*a *hotelReviewsSsPages*. 
   
   + Pro návod REST jsou názvy tabulek *hotelReviewsDocument*, *hotelReviewsPages*, *hotelReviewsKeyPhrases*a *hotelReviewsSentiment*.

1. Klikněte na **načíst**.

1. Na horním pásu karet kliknutím na **Upravit dotazy** otevřete **Editor Power Query**.

   ![Otevřít Power Query](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "Otevřít Power Query")

1. Vyberte *hotelReviewsSsDocument*a pak odstraňte sloupce *PartitionKey*, *RowKey*a *timestamp* . 
   ![Upravit tabulky](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "Upravit tabulky")

1. Kliknutím na ikonu s protilehlými šipkami v pravé horní části tabulky rozbalte *obsah*. Když se zobrazí seznam sloupců, vyberte všechny sloupce a pak zrušte výběr sloupců, které začínají na metadata. Kliknutím na tlačítko **OK** zobrazíte vybrané sloupce.

   ![Rozbalit obsah](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "Rozbalit obsah")

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

1. Kliknutím na dlaždici sestavy v levém navigačním podokně Prozkoumejte data prostřednictvím vizualizací. Pro textová pole, tabulky a karty jsou užitečné vizualizace. Můžete vybrat pole z každé ze tří tabulek, která se mají vyplnit v tabulce nebo kartě. 

<!-- ## Try with larger data sets

We purposely kept the data set small to avoid charges for a demo walkthrough. For a more realistic experience, you can create and then attach a billable Cognitive Services resource to enable a larger number of transactions against the sentiment analyzer, keyphrase extraction, and language detector skills.

Create new containers in Azure Blob storage and upload each CSV file to its own container. Specify one of these containers in the data source creation step in Import data wizard.

| Description | Link |
|-------------|------|
| Free tier   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Small (500 Records) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Medium (6000 Records)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Large (Full dataset 35000 Records) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Be aware that very large data sets are expensive to process. This one costs roughly $1000 U.S dollars.|

In the enrichment step of the wizard, attach a billable [Cognitive Services](../cognitive-services/cognitive-services-apis-create-account.md) resource, created at the *S0* tier, in the same region as Azure Cognitive Search to use larger data sets. 

  ![Create a Cognitive Services resource](media/knowledge-store-connect-power-bi/create-cognitive-service.png "Create a Cognitive Services resource") -->

## <a name="clean-up"></a>Vyčištění

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás stojí peníze. Prostředky můžete odstraňovat jednotlivě nebo můžete odstranit skupinu prostředků, a odstranit tak celou sadu prostředků najednou.

Prostředky můžete najít a spravovat na portálu pomocí odkazu **všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, pamatujte na to, že jste omezeni na tři indexy, indexery a zdroje dat. Jednotlivé položky na portálu můžete odstranit, aby zůstaly pod limitem.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak prozkoumat toto úložiště Knowledge Store pomocí Průzkumník služby Storage, najdete v následujícím článku.

> [!div class="nextstepaction"]
> [Zobrazení s využitím Průzkumníka služby Storage](knowledge-store-view-storage-explorer.md)