---
title: Vytvoření úložiště znalostí v Azure Portal
titleSuffix: Azure Cognitive Search
description: Pomocí Průvodce importem dat vytvoříte úložiště znalostí používané pro zachování obohaceného obsahu. Připojte se ke službě Knowledge Store pro analýzu z jiných aplikací nebo odešlete obohacený obsah do navazujících procesů.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/17/2020
ms.openlocfilehash: 3225013f09abd326c619b67caf77918889a64859
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94741803"
---
# <a name="quickstart-create-an-azure-cognitive-search-knowledge-store-in-the-azure-portal"></a>Rychlý Start: vytvoření služby Azure Kognitivní hledání Knowledge Store v Azure Portal

Znalostní báze je funkce služby Azure Kognitivní hledání, která uchovává výstup z kanálu zpracování obsahu pro následné analýzy nebo zpracování pro příjem dat. 

Kanál přijímá nestrukturované textové a obrázkové obsahy, aplikuje AI s využitím Cognitive Services (například při zpracování OCR a přirozeného jazyka) a zapisuje nové struktury a informace, které dříve neexistovaly. Jedním z fyzických artefaktů vytvořených kanálem je [znalostní báze](knowledge-store-concept-intro.md), ke kterému můžete přistupovat prostřednictvím nástrojů pro účely analýzy a průzkumu obsahu.

V tomto rychlém startu spojíte služby a data v cloudu Azure a vytvoříte úložiště znalostí. Jakmile bude vše na svém místě, spustíte průvodce **importem dat** na portálu, aby se všechno načetlo dohromady. Konečným výsledkem je původní textový obsah a obsah generovaný AI, který můžete zobrazit na portálu ([Průzkumník služby Storage](knowledge-store-view-storage-explorer.md)).

## <a name="prerequisites"></a>Předpoklady

Než začnete, musíte mít následující:

+ Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/).

+ Služba Azure Kognitivní hledání. [Vytvořte službu](search-create-service-portal.md) nebo [vyhledejte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento rychlý Start můžete použít bezplatnou službu. 

+ Účet Azure Storage s [úložištěm objektů BLOB](../storage/blobs/index.yml).

> [!NOTE]
> V tomto rychlém startu se také pro AI používá [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) . Vzhledem k tomu, že úloha je tak malá, Cognitive Services po dobu bezplatného zpracování po dobu až 20 transakcí klepnuli na pozadí. To znamená, že můžete dokončit toto cvičení, aniž byste museli vytvořit další prostředek Cognitive Services.

## <a name="set-up-your-data"></a>Nastavení dat

V následujících krocích nastavte kontejner objektů BLOB v Azure Storage, aby se ukládaly heterogenní soubory obsahu.

1. [Stáhněte si HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Tato data jsou data recenze pro hotely uložená v souboru CSV (pocházející z Kaggle.com) a obsahují 19 kusů zpětné vazby od zákazníků k jednomu hotelu. 

1. [Vytvořte si účet Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal) nebo v rámci aktuálního předplatného [Najděte existující účet](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) . Použijete službu Azure Storage pro import nezpracovaného obsahu a úložiště Knowledge v konečném výsledku.

   + Vyberte typ účtu **StorageV2 (pro obecné účely v2)** .

1. Otevřete stránky služby BLOB Services a vytvořte kontejner s názvem *hotelové recenze*.

1. Klikněte na **Odeslat**.

    ![Nahrajte data](media/knowledge-store-create-portal/upload-command-bar.png "Nahrajte recenze hotelu.")

1. Vyberte soubor **HotelReviews-Free.csv** , který jste si stáhli v prvním kroku.

    ![Vytvoření kontejneru objektů blob Azure](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Vytvoření kontejneru objektů blob Azure")

1. Před ukončením stránek služby Blob Storage otevřete pomocí odkazu v levém navigačním podokně stránku **přístupových klíčů** . Získání připojovacího řetězce pro načtení dat z úložiště objektů BLOB Připojovací řetězec vypadá podobně jako v následujícím příkladu: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

Nyní jste připraveni na přesun průvodce **importem dat** .

## <a name="run-the-import-data-wizard"></a>Spuštění Průvodce importem dat

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí svého účtu Azure.

1. [Vyhledejte vyhledávací službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) a na stránce Přehled klikněte na příkaz **importovat data** na panelu příkazů a vytvořte znalostní bázi ve čtyřech krocích.

   ![Příkaz pro import dat](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>Krok 1: Vytvoření zdroje dat

1. V části **připojit k vašim datům** vyberte **úložiště objektů BLOB v Azure**, vyberte účet a kontejner, který jste vytvořili. 

1. Jako **název** zadejte `hotel-reviews-ds` .

1. V části **režim analýzy** vyberte **text s oddělovači** a potom zaškrtněte políčko **první řádek obsahuje záhlaví** . Ujistěte se, že **znak oddělovače** je čárka (,).

1. Do pole **připojovací řetězec** vložte připojovací řetězec, který jste zkopírovali ze stránky **přístupové klíče** v Azure Storage.

1. Do pole **kontejnery** zadejte název kontejneru objektů blob, který uchovává data.

    Stránka by měla vypadat podobně jako na následujícím snímku obrazovky.

    ![Vytvoření objektu zdroje dat](media/knowledge-store-create-portal/hotel-reviews-ds.png "Vytvoření objektu zdroje dat")

1. Pokračujte na další stránku.

### <a name="step-2-add-cognitive-skills"></a>Krok 2: Přidání kognitivních dovedností

V tomto kroku průvodce vytvoříte dovednosti s obohacením vnímání dovedností. Zdrojová data se skládají z revizí zákazníků v několika jazycích. Mezi dovednosti, které jsou pro tuto datovou sadu relevantní, patří extrakce klíčových frází, detekce mínění a překlad textu. V pozdějším kroku budou tato rozšíření "" provedená "do úložiště znalostní báze jako tabulky Azure.

1. Rozbalte položku **připojit Cognitive Services**. Ve výchozím nastavení je vybrané **bezplatné (omezená rozšíření)** . Tento prostředek můžete použít, protože počet záznamů v HotelReviews-Free.csv je 19 a tento bezplatný prostředek umožňuje až 20 transakcí za den.

1. Rozbalte položku **Přidat rozšíření**.

1. Do **dovednosti název** zadejte `hotel-reviews-ss` .

1. V **poli zdrojová data** zadejte **reviews_text**.

1. Pro **úroveň podrobností obohacení** vyberte **stránky (5000 znaků bloků dat)** .

1. Vyberte tyto dovednosti v rozpoznávání:
    + **Extrakce klíčových frází**
    + **Přeložení textu**
    + **Zjistit mínění**

      ![Vytvoření sady dovedností](media/knowledge-store-create-portal/hotel-reviews-ss.png "Vytvoření sady dovedností")

1. Rozbalte položku **Uložit obohacení do úložiště znalostí**.

1. Vyberte tyto **projekce tabulky Azure**:
    + **Dokumenty**
    + **Stránky**
    + **Klíčové fráze**

1. Zadejte **připojovací řetězec účtu úložiště** , který jste uložili v předchozím kroku.

    ![Konfigurace úložiště znalostí](media/knowledge-store-create-portal/hotel-reviews-ks.png "Konfigurace úložiště znalostí")

1. Volitelně můžete stáhnout šablonu Power BI. Při přístupu k šabloně z Průvodce je místní soubor. PBIT upraven tak, aby odrážel tvar vašich dat.

1. Pokračujte na další stránku.

### <a name="step-3-configure-the-index"></a>Krok 3: Konfigurace indexu

V tomto kroku průvodce nakonfigurujete index pro volitelné dotazy fulltextového vyhledávání. Průvodce zobrazí ukázkový zdroj dat pro odvození polí a datových typů. Stačí vybrat atributy pro požadované chování. Například **atribut, který lze** načíst, umožní službě Search vracet hodnotu pole, zatímco **vyhledávání** umožní fulltextové vyhledávání v poli.

1. Jako **název indexu** zadejte `hotel-reviews-idx` .

1. U atributů přijměte výchozí výběry: **Retrievable** načístelné a **prohledávatelné** pro nová pole, která vytváří kanál.

    Index by měl vypadat podobně jako na následujícím obrázku. Vzhledem k tomu, že seznam je dlouhý, ne všechna pole jsou v obrázku viditelná.

    ![Konfigurace indexu](media/knowledge-store-create-portal/hotel-reviews-idx.png "Konfigurace indexu")

1. Pokračujte na další stránku.

### <a name="step-4-configure-the-indexer"></a>Krok 4: Konfigurace indexeru

V tomto kroku průvodce nakonfigurujete indexer, který se bude načítat zdrojem dat, dovednosti a indexem, který jste definovali v předchozím kroku průvodce.

1. Jako **název** zadejte `hotel-reviews-idxr` .

1. Pro **plán** ponechte **výchozí nastavení**.

1. Kliknutím na **Odeslat** spusťte indexer. V tomto kroku dojde k extrakci dat, indexování a uplatnění odbornosti rozpoznávání.

## <a name="monitor-status"></a>Stav monitorování

Vyvnímání dovedností pro rozpoznávání trvá déle než typické indexování založené na textu. Průvodce by měl otevřít seznam indexerů na stránce Přehled, abyste mohli sledovat průběh. V případě samoobslužné navigace přejděte na stránku Přehled a klikněte na **indexery**.

V Azure Portal můžete také monitorovat protokol aktivit oznámení pro odkaz na stav **oznámení v Azure kognitivní hledání** . Provedení může trvat několik minut.

## <a name="next-steps"></a>Další kroky

Teď, když jste nastavili data pomocí Cognitive Services a výsledky jste prozkoumali do úložiště Knowledge Store, můžete k prozkoumání obohacené sady dat použít Průzkumník služby Storage nebo Power BI.

Můžete zobrazit obsah v Průzkumník služby Storage nebo se Power BI a získat přehledy prostřednictvím vizualizace.

> [!div class="nextstepaction"]
> [Zobrazit s Průzkumník služby Storage](knowledge-store-view-storage-explorer.md) 
>  [Připojení pomocí Power BI](knowledge-store-connect-power-bi.md)

> [!Tip]
> Pokud chcete tento cvičení opakovat nebo si vyzkoušet jiný návod k rozšíření AI, odstraňte indexer *idxr hotelového přezkoumání* . Odstranění indexeru resetuje čítač bezplatných denních transakcí zpět na nulu pro zpracování Cognitive Services.
