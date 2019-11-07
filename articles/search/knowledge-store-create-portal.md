---
title: Vytvoření úložiště znalostí v Azure Portal
titleSuffix: Azure Cognitive Search
description: Pomocí Průvodce importem dat vytvoříte úložiště znalostí používané pro zachování obohaceného obsahu. Připojte se ke službě Knowledge Store pro analýzu z jiných aplikací nebo odešlete obohacený obsah do navazujících procesů.
author: lisaleib
manager: nitinme
ms.author: v-lilei
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: 689b5aa0cd8a5fb310925b2c52baeec6860af0d5
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607078"
---
# <a name="quickstart-create-an-azure-cognitive-search-knowledge-store-in-the-azure-portal"></a>Rychlý Start: vytvoření služby Azure Kognitivní hledání Knowledge Store v Azure Portal

> [!Note]
> Znalostní báze je ve verzi Preview a neměl by se používat v produkčním prostředí. Tuto funkci poskytuje Azure Portal i [REST API vyhledávání verze 2019-05-06-Preview](search-api-preview.md) . V tuto chvíli není dostupná žádná podpora sady .NET SDK.
>

Znalostní báze je funkce služby Azure Kognitivní hledání, která uchovává výstup kanálu vnímání dovedností pro následné analýzy nebo zpracování pro příjem dat. 

Kanál přijímá obrázky a nestrukturovaný text jako nezpracovaný obsah, aplikuje AI prostřednictvím Cognitive Services (například zpracování obrazu a přirozeného jazyka) a jako výstup vytvoří obohacený obsah (nové struktury a informace). Jedním z fyzických artefaktů vytvořených kanálem je [znalostní báze](knowledge-store-concept-intro.md), ke kterému můžete přistupovat prostřednictvím nástrojů pro účely analýzy a průzkumu obsahu.

V tomto rychlém startu spojíte služby a data v cloudu Azure a vytvoříte úložiště znalostí. Jakmile bude vše na svém místě, spustíte průvodce **importem dat** na portálu, aby se všechno načetlo dohromady. Konečným výsledkem je původní obsah generovaný AI, který můžete zobrazit na portálu ([Průzkumník služby Storage](knowledge-store-view-storage-explorer.md)).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-services-and-load-data"></a>Vytváření služeb a načítání dat

Tento rychlý Start používá pro AI Azure Kognitivní hledání, Azure Blob Storage a [azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) . 

Vzhledem k tomu, že je zatížení tak malé, Cognitive Services na pozadí po dobu, po které se vyvolají z Azure Kognitivní hledání, po dobu až 20 transakcí denně zajišťovat bezplatné zpracování. Pokud používáte ukázková data, která poskytujeme, můžete přeskočit vytvoření nebo připojení prostředku Cognitive Services.

1. [Stáhněte si HotelReviews_Free. csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Tato data jsou data recenze pro hotely uložená v souboru CSV (pocházející z Kaggle.com) a obsahují 19 kusů zpětné vazby od zákazníků k jednomu hotelu. 

1. [Vytvořte si účet Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) nebo v rámci aktuálního předplatného [Najděte existující účet](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) . Použijete službu Azure Storage pro import nezpracovaného obsahu a úložiště Knowledge v konečném výsledku.

   Pro tento účet existují dva požadavky:

   + Vyberte stejnou oblast jako Azure Kognitivní hledání. 
   
   + Vyberte typ účtu StorageV2 (pro obecné účely v2). 

1. Otevřete stránky služby BLOB Services a vytvořte kontejner.  

1. Klikněte na **Odeslat**.

    ![Nahrajte data](media/knowledge-store-create-portal/upload-command-bar.png "Nahrajte recenze hotelu.")

1. Vyberte soubor **HotelReviews-Free. csv** , který jste stáhli v prvním kroku.

    ![Vytvoření kontejneru objektů blob Azure](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Vytvoření kontejneru objektů blob Azure")

<!-- 1. You are almost done with this resource, but before you leave these pages, use a link on the left navigation pane to open the **Access Keys** page. Get a connection string to retrieve data from Blob storage. A connection string looks similar to the following example: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net` -->

1. [Vytvořte službu Azure kognitivní hledání](search-create-service-portal.md) nebo [Najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci stejného předplatného. Pro tento rychlý Start můžete použít bezplatnou službu.

Nyní jste připraveni na přesun Průvodce importem dat.

## <a name="run-the-import-data-wizard"></a>Spuštění Průvodce importem dat

Na stránce Přehled služby Search klikněte na panelu příkazů na **importovat data** a vytvořte znalostní báze ve čtyřech krocích.

  ![Příkaz pro import dat](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>Krok 1: Vytvoření zdroje dat

1. V části **připojit k vašim datům**vyberte **úložiště objektů BLOB v Azure**, vyberte účet a kontejner, který jste vytvořili. 
1. Jako **název**zadejte `hotel-reviews-ds`.
1. V části **režim analýzy**vyberte **text s oddělovači**a potom zaškrtněte políčko **první řádek obsahuje záhlaví** . Ujistěte se, že **znak oddělovače** je čárka (,).
1. Zadejte **připojovací řetězec** služby úložiště, který jste uložili v předchozím kroku.
1. Jako **název kontejneru**zadejte `hotel-reviews`.
1. Klikněte na **Další: přidat rozšíření AI (volitelné)** .

      ![Vytvoření objektu zdroje dat](media/knowledge-store-create-portal/hotel-reviews-ds.png "Vytvoření objektu zdroje dat")

1. Pokračujte na další stránku.

### <a name="step-2-add-cognitive-skills"></a>Krok 2: Přidání kognitivních dovedností

V tomto kroku průvodce vytvoříte dovednosti s obohacením vnímání dovedností. Dovednosti, které používáme v této ukázce, extrahují klíčové fráze a určí jazyk a mínění. V pozdějším kroku budou tato rozšíření "" provedená "do úložiště znalostní báze jako tabulky Azure.

1. Rozbalte položku **připojit Cognitive Services**. Ve výchozím nastavení je vybrané **bezplatné (omezená rozšíření)** . Tento prostředek můžete použít, protože počet záznamů v HotelReviews-Free. CSV je 19 a tento bezplatný prostředek umožňuje až 20 transakcí za den.
1. Rozbalte možnost **Přidat dovednosti rozpoznávání**.
1. Do **dovednosti název**zadejte `hotel-reviews-ss`.
1. V **poli zdrojová data**zadejte **reviews_text**.
1. Pro **úroveň podrobností obohacení**vyberte **stránky (5000 znaků bloků dat)** .
1. Vyberte tyto dovednosti v rozpoznávání:
    + **Extrakce klíčových frází**
    + **Zjistit jazyk**
    + **Zjistit mínění**

      ![Vytvoření dovednosti](media/knowledge-store-create-portal/hotel-reviews-ss.png "Vytvoření sady dovedností")

1. Rozbalte položku **Uložit obohacení do úložiště znalostí**.
1. Zadejte **připojovací řetězec účtu úložiště** , který jste uložili v předchozím kroku.
1. Vyberte tyto **projekce tabulky Azure**:
    + **Document**
    + **Stránky**
    + **Klíčové fráze**

    ![Konfigurace úložiště znalostí](media/knowledge-store-create-portal/hotel-reviews-ks.png "Konfigurace úložiště znalostí")

1. Pokračujte na další stránku.

### <a name="step-3-configure-the-index"></a>Krok 3: Konfigurace indexu

V tomto kroku průvodce nakonfigurujete index pro volitelné dotazy fulltextového vyhledávání. Průvodce zobrazí ukázkový zdroj dat pro odvození polí a datových typů. Stačí vybrat atributy pro požadované chování. Například **atribut, který lze** načíst, umožní službě Search vracet hodnotu pole, zatímco **vyhledávání** umožní fulltextové vyhledávání v poli.

1. Jako **název indexu**zadejte `hotel-reviews-idx`.
1. V případě atributů proveďte tyto výběry:
    + Vyberte **možnost** načístelné pro všechna pole.
    + Vyberte možnost **filtrovatelné** a **plošky** pro tato pole: *mínění*, *Language*, klíčová *fráze*
    + Vyberte **možnost prohledávatelné** pro tato pole: *město*, *název*, *reviews_text*, *jazyk*, *slovní fráze*

    Index by měl vypadat podobně jako na následujícím obrázku. Vzhledem k tomu, že seznam je dlouhý, ne všechna pole jsou v obrázku viditelná.

    ![Konfigurace indexu](media/knowledge-store-create-portal/hotel-reviews-idx.png "Konfigurace indexu")

1. Pokračujte na další stránku.

### <a name="step-4-configure-the-indexer"></a>Krok 4: Konfigurace indexeru

V tomto kroku průvodce nakonfigurujete indexer, který se bude načítat zdrojem dat, dovednosti a indexem, který jste definovali v předchozím kroku průvodce.

1. Jako **název**zadejte `hotel-reviews-idxr`.
1. Pro **plán**ponechte **výchozí nastavení**.
1. Kliknutím na **Odeslat** spusťte indexer. V tomto kroku dojde k extrakci dat, indexování a uplatnění odbornosti rozpoznávání.

## <a name="monitor-status"></a>Stav monitorování

Vyvnímání dovedností pro rozpoznávání trvá déle než typické indexování založené na textu. Průvodce by měl otevřít seznam indexerů na stránce Přehled, abyste mohli sledovat průběh. V případě samoobslužné navigace přejděte na stránku Přehled a klikněte na **indexery**.

V Azure Portal můžete také monitorovat protokol aktivit oznámení pro odkaz na stav **oznámení v Azure kognitivní hledání** . Provedení může trvat několik minut.

## <a name="next-steps"></a>Další kroky

Teď, když jste nastavili data pomocí Cognitive Services a výsledky jste prozkoumali do úložiště Knowledge Store, můžete k prozkoumání obohacené sady dat použít Průzkumník služby Storage nebo Power BI.

Můžete zobrazit obsah v Průzkumník služby Storage nebo se Power BI a získat přehledy prostřednictvím vizualizace.

> [!div class="nextstepaction"]
> [Zobrazení pomocí Průzkumník služby Storage](knowledge-store-view-storage-explorer.md)
> [připojení pomocí Power BI](knowledge-store-connect-power-bi.md)

> [!Tip]
> Pokud chcete tento cvičení opakovat nebo si vyzkoušet jiný návod k rozšíření AI, odstraňte indexer *idxr hotelového přezkoumání* . Odstranění indexeru resetuje čítač bezplatných denních transakcí zpět na nulu pro zpracování Cognitive Services.
