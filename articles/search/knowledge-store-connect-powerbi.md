---
title: Připojení k znalostnímu obchodu pomocí Power BI-Azure Search
description: Vytvořte úložiště znalostí pomocí Průvodce importem dat v Azure Portal a pak se připojte pomocí Power BI pro analýzu a průzkum.
author: heidisteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 07/30/2019
ms.author: heidist
ms.openlocfilehash: 04b04e8080590aa6e9fe1c17369e83fa5bb6b894
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668221"
---
# <a name="create-an-azure-search-knowledge-store-and-connect-using-power-bi"></a>Vytvoření Azure Search znalostní báze Knowledge Store a připojení pomocí Power BI

> [!Note]
> Znalostní báze je ve verzi Preview a neměl by se používat v produkčním prostředí. Tuto funkci poskytuje [Azure Search REST API verze 2019-05-06-Preview](search-api-preview.md) . V tuto chvíli není dostupná žádná podpora sady .NET SDK.
>

Znalostní báze je funkce Azure Search, která uchovává výstup kanálu rozšíření AI pro následné analýzy nebo jiné zpracování dat. Kanál obohacený AI akceptuje soubory obrázků nebo nestrukturované textové soubory z podporovaného zdroje dat, odesílá je Azure Search indexování, aplikuje rozšíření AI z Cognitive Services (například analýzu obrázků a zpracování přirozeného jazyka), a pak výsledky uloží. do úložiště znalostí ve službě Azure Storage. Z znalostní báze Store můžete k prozkoumání výsledků připojit nástroje, jako je Power BI nebo Průzkumník služby Storage.

V tomto článku vytvořte na portálu znalostní bázi Knowledge Store a pak se připojte a prozkoumejte pomocí Power Query v Power BI Desktop. 

Tento návod používá datovou sadu, která se skládá z recenzí a hodnocení zákazníků, bodování mínění z Cognitive Services a pak Power Query dotazování dokumentů. Data pocházejí z dat o prohlídek z hotelu na Kaggle.com. 

## <a name="prerequisites"></a>Požadavky

+ [Stáhnout soubor CSV recenzí hotelů (HotelReviews_Free. csv)](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) Tato datová sada obsahuje záznamy o názorech zákazníků na hotely.

+ [Power BI Desktop](https://powerbi.microsoft.com/downloads/)

+ [Azure Search](search-create-service-portal.md). Pro tento návod můžete použít bezplatnou službu. 

+ [Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) Ujistěte se, že účet je "pro obecné účely", a to buď *StorageV2 (obecné účely v2)* , což je výchozí hodnota, nebo *úložiště (pro obecné účely V1)* . Vyberte stejnou oblast jako Azure Search.
 
## <a name="prepare-data"></a>Příprava dat 

Načtěte soubor. CSV do úložiště objektů BLOB v Azure tak, aby k němu měl pøístup Azure Search indexer.

1. Přihlaste se [k Azure Portal](https://portal.azure.com), přejděte k účtu úložiště Azure, klikněte na **objekty blob**a pak klikněte na **+ kontejner**.

1. [Vytvořte kontejner objektů BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) , který bude obsahovat vzorová data: 

   1. Pojmenujte `hotel-reviews`kontejner. 
   
   1. Nastavte úroveň veřejného přístupu na jakoukoli z jeho platných hodnot. Použili jsme výchozí.

1. Po vytvoření kontejneru ho otevřete a na panelu příkazů vyberte **nahrát** .

1. Přejděte do složky, která obsahuje **HotelReviews-Free. csv**, vyberte soubor a klikněte na **nahrát**.

   ![Nahrajte soubor. csv.](media/knowledge-store-howto-powerbi/hotel-reviews-blob-container.png "Nahrajte soubor. csv.")

1. Když jste ve službě Azure Storage, Získejte připojovací řetězec a název kontejneru.  Oba tyto řetězce budete potřebovat při vytváření objektu zdroje dat:

   1. Na stránce Přehled klikněte na **přístupové klíče** a zkopírujte *připojovací řetězec*. Začíná `DefaultEndpointsProtocol=https;` a končí `EndpointSuffix=core.windows.net`. Název vašeho účtu a klíč jsou mezi. 

   1. Název kontejneru by měl být `hotel-reviews` nebo libovolný název, který jste přiřadili. 

## <a name="create-and-run-ai-enrichments"></a>Vytvoření a spuštění obohacení AI

K vytvoření úložiště Knowledge Store použijte Průvodce importem dat. Naimportujete datovou sadu, zvolíte rozšíření, nakonfigurujete úložiště znalostí a index a pak spustíte.

1. [Vyhledejte vyhledávací službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na Azure Portal.

1. Na panelu příkazů klikněte na **importovat data** .

1. Vytvořte objekt zdroje dat na první stránce průvodce.

   - Vyberte **Azure Blob Storage**.

   - Zadejte název zdroje dat, například *hotelového přezkoumání – DS*.

   - Vzhledem k tomu, že jsou data. csv, vyberte pro režim analýzy **text** s oddělovači, vyberte **první řádek obsahuje záhlaví**a ujistěte se, že znak oddělovače je čárka.

   - Připojovací řetězec k vašemu účtu Azure Storage můžete získat na portálu v části **přístupové klíče**.

   - Název kontejneru se dá získat taky z portálu v seznamu objektů blob Azure Storage.

      ![Vytvoření objektu zdroje dat](media/knowledge-store-howto-powerbi/hotel-reviews-ds.png "Vytvoření objektu zdroje dat")

1. Přidejte obohacení a nakonfigurujte znalostní bázi na druhé stránce průvodce.

   - V části **připojit Cognitive Services**můžete použít bezplatný prostředek, který umožňuje až 20 transakcí za den. Počet záznamů v souboru HotelReviews-Free. CSV je menší než 20.

   - V **části Přidat obohacení**pojmenujte dovednosti *hotelu – recenze – SS*, zvolte pole *reviews_text* , zvolte úroveň členitosti *stránek (bloky 5000 znaků)* a pak vyberte tyto tři dovednosti při rozpoznávání: *Extrakce klíčových frází*, *zjištění jazyka*, *detekce mínění*.

      ![Vytvoření dovednosti](media/knowledge-store-howto-powerbi/hotel-reviews-ss.png "Vytvoření dovednosti")

   - V části **Uložit obohacení do úložiště znalostí**zadejte připojovací řetězec k vašemu účtu Azure Storage pro obecné účely. Znalostní báze se vytvoří ve službě Azure Table Storage, když vyberete možnosti *projekty Azure Table* v této části.

      ![Konfigurace úložiště znalostí](media/knowledge-store-howto-powerbi/hotel-reviews-ks.png "Konfigurace úložiště znalostí")
   
   Klikněte **na další: Pokud chcete pokračovat** k dalšímu kroku, upravte cílový index.

1. Nakonfigurujte index pro volitelné dotazy fulltextového vyhledávání v Azure Search. I když je fokus tohoto návodu Power BI připojen ke službě Azure Table Storage, průvodce **importem dat** může také vytvořit index, který se používá pro fulltextové vyhledávání v Azure Search. 

   Průvodce vyvzorkuje zdroj dat pro odvození polí a datových typů, takže stačí vybrat potřebné atributy pro dosažení požadovaného chování. Například možnost získatelné *znamená,* že obsah pole lze načíst ze služby, zatímco prohledávatelné umožňuje fulltextové vyhledávání u vybraných polí.

   - Zadejte název indexu, například *Hotel-recenze-IDX*.
   - Nastavte všechna **pole jako**získatelné.
   - Nastavte *City*, *Name*, *reviews_text*, *Language*, klíčová *fráze* jako **prohledávatelné**.
   - Nastavte *mínění*, *Language*, klíčová *fráze* jako **Filtered** a **Face**. 
   
    Index by měl vypadat podobně jako na následujícím obrázku.

     ![Konfigurace indexu](media/knowledge-store-howto-powerbi/hotel-reviews-idx.png "Konfigurace indexu")

   Klikněte **na další: Vytvořte indexer** , abyste mohli pokračovat k dalšímu kroku.

1. Nakonfigurujte indexer tak, že mu udělíte název a spuštění tempo. Pro tento návod použijte jako název indexeru *idxr hotelového přezkoumání* a **použijte výchozí plán** pro okamžité spuštění indexeru.

   Provádění indexeru vloží všechny předchozí konfigurace v pohybu. V tomto kroku se nachází extrakce, zpracování a ingestování všech situací.

1. Sledujte indexování ve frontě oznámení na portálu. Dokončení provádění trvá několik minut.

## <a name="connect-with-power-bi"></a>Propojení s Power BI

1. Spusťte Power BI Desktop a vyberte **získat data**.

1. V nástroji získat data vyberte **Azure** a pak **Azure Table Storage**. Klikněte na **Připojit**.

1. Do pole název účtu nebo adresa URL vložte název účtu Azure Storage (pro vás bude vyřešena úplná adresa URL).

1. Zadejte klíč účtu.

1. Vyberte dokument, klíčová slova a stránky. Jedná se o tabulky vytvořené Průvodcem importem dat, když v konfiguraci úložiště Knowledge Store vyberete stejné položky s názvem. Klikněte na **načíst**.

1. Otevřete Power Query kliknutím na příkaz **Upravit dotazy** .

   ![Otevřít Power Query](media/knowledge-store-howto-powerbi/powerbi-edit-queries.png "Otevřít Power Query")

1. Pro dokumenty:

   - Odstraňte sloupce PartitionKey, RowKey a timestamp vytvořené službou Azure Table Storage. Znalostní báze poskytuje vztahy používané v této analýze.

   - Rozbalte sloupec obsah.

     ![Upravit tabulky](media/knowledge-store-howto-powerbi/powerbi-edit-table.png "Upravit tabulky")

1. Vyberte všechna pole a pak zrušte výběr položek začínajících na metadata.

1. Opravte datový typ pro následující sloupce pomocí ikony ABC-123 na každém sloupci:

   - Sloupce data by měly být **DateTime**
   - *Zeměpisná šířka* musí být **desetinné číslo** .
   - *Zeměpisná délka* by měla být **desetinné číslo** .

1. Opakujte předchozí kroky pro klíčová fráze, odeberte PartitionKey a další sloupce, rozbalíte sloupce obsahu a nastavte *SentimentScore* na **desetinné číslo**.

1. Opakujte akci pro stránky, odeberte PartitionKey a další sloupce a rozbalí se sloupce obsahu. Pro tuto tabulku neexistují žádné úpravy datových typů.

1. Klikněte na **Zavřít a použijte** úplně vlevo na panelu příkazů Power Query.

1. Ověřte, že Power BI rozpoznává vztahy, které úložiště znalostí vytvořilo v rámci vašich dat. Klikněte na dlaždici relace v levém navigačním podokně. Všechny tři tabulky by měly souviset.

   ![Ověřit relace](media/knowledge-store-howto-powerbi/powerbi-relationships.png "Ověřit relace")

## <a name="try-with-larger-data-sets"></a>Vyzkoušejte s většími datovými sadami

Nastavili jsme pro vás datovou sadu malou, aby se předešlo poplatkům za ukázkový návod. Pro realističtější prostředí můžete vytvořit a pak připojit Fakturovatelné Cognitive Services prostředku, abyste mohli povolit větší počet transakcí proti mínění Analyzer, extrakci keyphrase a dovednostím v jazykovém detektoru.

Vytvořte nové kontejnery ve službě Azure Blob Storage a nahrajte všechny soubory CSV do vlastního kontejneru. V Průvodci importem dat zadejte jeden z těchto kontejnerů v kroku vytvoření zdroje dat.

| Popis | Odkaz |
|-------------|------|
| Úroveň Free   | [HotelReviews_Free. csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Malé (500 záznamů) | [HotelReviews_Small. csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Střední (6000 záznamů)| [HotelReviews_Medium. csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Velký (úplný záznam datové sady 35000) | [HotelReviews_Large. csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Mějte na paměti, že zpracování velkých datových sad je náročné na zpracování. Tato jedna cena zhruba $1000. S dolarů.|

V kroku průvodce pro rozšíření připojte Fakturovatelné [Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) prostředek vytvořený na úrovni *S0* ve stejné oblasti jako Azure Search pro použití větších datových sad. 

  ![Vytvoření prostředku Cognitive Services](media/knowledge-store-howto-powerbi/create-cognitive-service.png "Vytvoření prostředku Cognitive Services")

## <a name="next-steps"></a>Další postup

Pokud chcete toto cvičení opakovat nebo provést jiný návod k rozšíření AI, odstraňte indexer hotelového *přezkoumání-IDX* , který jste právě vytvořili. Odstranění indexeru obnoví čítač bezplatných denních transakcí zpátky na nulu. 

Další podrobné návody demonstrující znalostní bázi Knowledge Store najdete v dalším článku, který ukazuje, jak vytvořit znalostní bázi pomocí rozhraní REST API a následného.

> [!div class="nextstepaction"]
> [Začínáme s úložištěm Knowledge Store](knowledge-store-howto.md)