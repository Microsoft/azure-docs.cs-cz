---
title: Vytvoření úložiště znalostí v Azure Portal-Azure Search
description: Pomocí Průvodce importem dat v Azure Portal Vytvořte Azure Search znalostnímu obchodu pro zachování rozšíření z kanálu vyhledávání rozpoznávání.
author: lisaleib
services: search
ms.service: search
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: v-lilei
ms.openlocfilehash: fb979a7ff4144694aecad0985c5bce9be2de05bd
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265199"
---
# <a name="create-an-azure-search-knowledge-store-in-the-azure-portal"></a>Vytvoření Azure Search Knowledge Store v Azure Portal

> [!Note]
> Znalostní báze je ve verzi Preview a neměl by se používat v produkčním prostředí. Tuto funkci poskytuje [Azure Search REST API verze 2019-05-06-Preview](search-api-preview.md) . V tuto chvíli není dostupná žádná podpora sady .NET SDK.
>

Znalostní báze je funkce Azure Search, která uchovává výstup kanálu rozšíření AI pro pozdější analýzu nebo jiné zpracování dat. Kanál obohacený AI akceptuje soubory obrázků nebo nestrukturované textové soubory, indexuje je pomocí Azure Search, aplikuje rozšíření AI z Cognitive Services (například analýzu obrázků a zpracování přirozeného jazyka) a pak výsledky uloží do úložiště znalostí v Azure. pamì. Pomocí nástrojů jako Power BI nebo Průzkumník služby Storage pak můžete prozkoumat znalostní bázi Knowledge Store.

V tomto článku použijete Průvodce importem dat na Azure Portal k ingestování, indexování a používání rozšíření AI na sadu pohlídek za Hotel. Recenze hotelu se importují do služby Azure blog Storage a výsledky se ukládají jako znalostní báze ve službě Azure Table Storage.

Po vytvoření obchodu Knowledge Store se dozvíte, jak získat přístup k tomuto znalostnímu obchodu pomocí Průzkumník služby Storage nebo Power BI.

## <a name="prerequisites"></a>Požadavky

+ [Vytvořte službu Azure Search](search-create-service-portal.md) nebo [Najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento kurz můžete použít bezplatnou službu.

+ [Vytvořte účet úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) pro ukládání ukázkových dat a úložiště znalostí. Váš účet úložiště musí používat stejné umístění (například US-WEas vaši službu Azure Search a *druh účtu* musí být *StorageV2 (pro obecné účely v2)* (výchozí) nebo *úložiště (pro obecné účely V1)* .

## <a name="load-the-data"></a>Načtení dat

Načtěte soubor. CSV pro kontrolu hotelu do úložiště objektů BLOB v Azure, aby k němu měl k dispozici Azure Search indexer a mohl by být vydáván prostřednictvím kanálu pro rozšíření AI.

### <a name="create-an-azure-blob-container-with-the-data"></a>Vytvoření kontejneru objektů BLOB v Azure s daty

1. [Stáhněte si data kontroly hotelu uložená v souboru CSV (HotelReviews_Free. csv)](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Tato data pocházejí z Kaggle.com a obsahují názory zákazníků na hotely.
1. [Přihlaste se k Azure Portal](https://portal.azure.com)a přejděte k účtu služby Azure Storage.
1. [Vytvoření kontejneru objektů BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) Provedete to tak, že v levém navigačním panelu svého účtu úložiště kliknete na **objekty blob**a pak na panelu příkazů kliknete na **+ kontejner** .
1. Jako **název**nového kontejneru zadejte `hotel-reviews`.
1. Vyberte libovolnou **úroveň veřejného přístupu**. Použili jsme výchozí.
1. Kliknutím na **OK** vytvořte kontejner objektů blob Azure.
1. Otevřete nový `hotels-review` kontejner, klikněte na **Odeslat**a vyberte soubor **HotelReviews-Free. csv** , který jste stáhli v prvním kroku.

    ![Nahrajte data](media/knowledge-store-create-portal/upload-command-bar.png "Nahrajte recenze hotelu") .

1. Kliknutím na **nahrát** IMPORTUJTE soubor CSV do Azure Blob Storage. Zobrazí se nový kontejner.

    ![Vytvoření kontejneru objektů blob Azure](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Vytvoření kontejneru objektů blob Azure")

### <a name="get-the-azure-storage-account-connection-string"></a>Získání připojovacího řetězce účtu Azure Storage

1. Na portálu přejděte na svůj účet Azure Storage.
1. V levém navigačním panelu pro službu klikněte na **přístupové klíče**.
1. V části **klíč 1**zkopírujte *připojovací řetězec*a uložte ho. Řetězec začíná `DefaultEndpointsProtocol=https`na. Název a klíč vašeho účtu úložiště jsou vložené do řetězce. Tento řetězec nechejte užitečný. Budete ho potřebovat v budoucích krocích.

## <a name="create-and-run-ai-enrichments"></a>Vytvoření a spuštění obohacení AI

K vytvoření úložiště Knowledge Store použijte Průvodce importem dat. Vytvoříte zdroj dat, kliknete na rozšíření, nakonfigurujete znalostní bázi a index a potom spustíte.

### <a name="start-the-import-data-wizard"></a>Spuštění Průvodce importem dat

1. V Azure Portal [Najděte vyhledávací službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Na panelu příkazů kliknutím na **importovat data** spusťte Průvodce importem.

### <a name="connect-to-your-data-import-data-wizard"></a>Připojení k datům (Průvodce importem dat)

V tomto kroku průvodce vytvoříte zdroj dat z objektu blob Azure s daty hotelů.

1. V seznamu **zdroj dat** vyberte **Azure Blob Storage**.
1. Jako **název**zadejte `hotel-reviews-ds`.
1. V části **režim analýzy**vyberte **text s oddělovači**a potom zaškrtněte políčko **první řádek obsahuje záhlaví** . Ujistěte se, že **znak oddělovače** je čárka (,).
1. Zadejte **připojovací řetězec** služby úložiště, který jste uložili v předchozím kroku.
1. Jako **název kontejneru**zadejte `hotel-reviews`.
1. Klikněte na **Další: Přidat vyhledávání rozpoznávání (volitelné)** .

      ![Vytvoření objektu zdroje dat](media/knowledge-store-create-portal/hotel-reviews-ds.png "Vytvoření objektu zdroje dat")

## <a name="add-cognitive-search-import-data-wizard"></a>Přidat hledání rozpoznávání (Průvodce importem dat)

V tomto kroku průvodce vytvoříte dovednosti s obohacením vnímání dovedností. Dovednosti, které používáme v této ukázce, extrahují klíčové fráze a určí jazyk a mínění. Tato rozšíření budou "provedená" do úložiště znalostí jako tabulky Azure.

1. Rozbalte položku **připojit Cognitive Services**. Ve výchozím nastavení je vybrané **bezplatné (omezená rozšíření)** . Tento prostředek můžete použít, protože počet záznamů v HotelReviews-Free. CSV je 19 a tento bezplatný prostředek umožňuje až 20 transakcí za den.
1. Rozbalte položku **Přidat rozšíření**.
1. Do **dovednosti název**zadejte `hotel-reviews-ss`.
1. V **poli zdrojová data**zadejte **reviews_text*.
1. Pro **úroveň podrobností obohacení**vyberte **stránky (5000 znaků bloků dat)** .
1. Vyberte tyto dovednosti v rozpoznávání:
    + **Extrakce klíčových frází**
    + **Zjistit jazyk**
    + **Zjistit mínění**

      ![Vytvoření dovednosti](media/knowledge-store-create-portal/hotel-reviews-ss.png "Vytvoření dovednosti")

1. Rozbalte položku **Uložit obohacení do úložiště znalostí**.
1. Zadejte **připojovací řetězec účtu úložiště** , který jste uložili v předchozím kroku.
1. Vyberte tyto **projekce tabulky Azure**:
    + **Document**
    + **Stránky**
    + **Klíčové fráze**

    ![Konfigurace úložiště znalostí](media/knowledge-store-create-portal/hotel-reviews-ks.png "Konfigurace úložiště znalostí")

1. Klikněte na **Další: Přizpůsobení cílového indexu**.

### <a name="import-data-import-data-wizard"></a>Importovat data (Průvodce importem dat)

V tomto kroku průvodce nakonfigurujete index pro volitelné dotazy fulltextového vyhledávání. Průvodce zobrazí ukázkový zdroj dat pro odvození polí a datových typů. Stačí vybrat atributy pro požadované chování. Například **atribut, který lze** načíst, umožní službě Search vracet hodnotu pole, zatímco **vyhledávání** umožní fulltextové vyhledávání v poli.

1. Jako **název indexu**zadejte `hotel-reviews-idx`.
1. V případě atributů proveďte tyto výběry:
    + Vyberte **možnost** načístelné pro všechna pole.
    + Vyberte možnost **filtrovatelné** a **plošky** pro tato pole: *Mínění*, *jazyk*, *slovní fráze*
    + Vyberte **možnost prohledávatelné** pro tato pole: *město*, *název*, *reviews_text*, *jazyk*, *slovní fráze*

    Index by měl vypadat podobně jako na následujícím obrázku. Vzhledem k tomu, že seznam je dlouhý, ne všechna pole jsou v obrázku viditelná.

    ![Konfigurace indexu](media/knowledge-store-create-portal/hotel-reviews-idx.png "Konfigurace indexu")

1. Klikněte na **Další: Vytvořte indexer**.

### <a name="create-an-indexer"></a>Vytvořit indexer

V tomto kroku průvodce nakonfigurujete indexer, který se bude načítat zdrojem dat, dovednosti a indexem, který jste definovali v předchozím kroku průvodce.

1. Jako **název**zadejte `hotel-reviews-idxr`.
1. Pro **plán**ponechte **výchozí nastavení**.
1. Kliknutím na **Odeslat** spusťte indexer. V tomto kroku dojde k extrakci dat, indexování a uplatnění odbornosti rozpoznávání.

### <a name="monitor-the-notifications-queue-for-status"></a>Monitorování fronty oznámení pro stav

1. V Azure Portal Sledujte odkaz na stav **oznámení Azure Search** kliknutím na odkaz. Provedení může trvat několik minut.

## <a name="next-steps"></a>Další kroky

Teď, když jste nastavili data pomocí služeb rozpoznávání a výsledky jste prozkoumali do úložiště Knowledge Store, můžete k prozkoumání obohacené sady dat použít Průzkumník služby Storage nebo Power BI.

Další informace o tom, jak prozkoumat toto úložiště znalostí pomocí Průzkumník služby Storage, najdete v následujícím návodu.

> [!div class="nextstepaction"]
> [Zobrazit s Průzkumník služby Storage](knowledge-store-view-storage-explorer.md)

Další informace o tom, jak připojit toto úložiště znalostní báze k Power BI, najdete v následujícím návodu.

> [!div class="nextstepaction"]
> [Propojení s Power BI](knowledge-store-connect-power-bi.md)

Pokud chcete tento cvičení opakovat nebo si vyzkoušet jiný návod k rozšíření AI, odstraňte indexer *idxr hotelového přezkoumání* . Odstranění indexeru obnoví čítač bezplatných denních transakcí zpátky na nulu.
