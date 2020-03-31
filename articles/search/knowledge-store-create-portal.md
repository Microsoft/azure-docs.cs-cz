---
title: Vytvoření úložiště znalostní ho (preview) na webu Azure Portal
titleSuffix: Azure Cognitive Search
description: Průvodce importem dat slouží k vytvoření úložiště znalostí používaného pro uchování obohaceného obsahu. Připojte se k úložišti znalostí pro analýzu z jiných aplikací nebo odešlete obohacený obsah do navazujících procesů. Tato funkce je aktuálně ve verzi Public Preview.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/29/2020
ms.openlocfilehash: 21279b2b4735a25210e8373d76d0d63f9c711bfc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77472362"
---
# <a name="quickstart-create-an-azure-cognitive-search-knowledge-store-in-the-azure-portal"></a>Úvodní příručka: Vytvoření úložiště znalostí Azure Cognitive Search na webu Azure Portal

> [!IMPORTANT] 
> Úložiště znalostí je v současné době ve verzi Public Preview. Funkce náhledu je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Úložiště znalostí je funkce Azure Cognitive Search, která přetrvává výstup z kanálu kognitivnídovednosti pro následné analýzy nebo následné zpracování. 

Kanál přijímá nestrukturovaný text a obrázky jako nezpracovaný obsah, aplikuje umělou ai prostřednictvím služeb Cognitive Services (například OCR, analýzu obrázků a zpracování přirozeného jazyka), extrahuje informace a vyčlene nové struktury a informace. Jedním z fyzických artefaktů vytvořených kanálem je [úložiště znalostí](knowledge-store-concept-intro.md), ke kterému máte přístup prostřednictvím nástrojů pro analýzu a zkoumání obsahu.

V tomto rychlém startu budete kombinovat služby a data v cloudu Azure a vytvořit úložiště znalostí. Jakmile je vše na svém místě, spustíte na portálu Průvodce **importem dat,** abyste to všechno spojili. Konečným výsledkem je původní textový obsah plus obsah generovaný umělou ai, který můžete zobrazit na portálu ([Průzkumník úložiště](knowledge-store-view-storage-explorer.md)).

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="create-services-and-load-data"></a>Vytváření služeb a načítání dat

Tento rychlý start používá Azure Cognitive Search, Azure Blob storage a [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) pro UI. 

Vzhledem k tomu, že zatížení je tak malé, služby Cognitive Services jsou poklepány na pozadí, aby poskytovaly bezplatné zpracování až pro 20 transakcí denně. Vzhledem k tomu, že sada dat je tak malá, můžete přeskočit vytváření nebo připojení prostředku služeb Cognitive Services.

1. [Stáhnout HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Tato data jsou údaje o kontrole hotelu uložené v souboru CSV (pocházející z Kaggle.com) a obsahují 19 částí zpětné vazby od zákazníků o jednom hotelu. 

1. [Vytvořte si účet úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) nebo [najděte existující účet](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) v rámci aktuálního předplatného. Budete používat úložiště Azure pro nezpracovaný obsah, který se má importovat, a úložiště znalostí, které je konečným výsledkem.

   Zvolte typ účtu **StorageV2 (pro obecné účely V2).**

1. Otevřete stránky služby Blob a vytvořte kontejner s názvem *recenze hotelů*.

1. Klikněte na **Odeslat**.

    ![Nahrání dat](media/knowledge-store-create-portal/upload-command-bar.png "Nahrajte recenze hotelů")

1. Vyberte soubor **HotelReviews-Free.csv,** který jste stáhli v prvním kroku.

    ![Vytvoření kontejneru objektů blob Azure](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Vytvoření kontejneru objektů blob Azure")

1. S tímto zdrojem jste téměř hotovi, ale před opuštěním těchto stránek otevřete stránku **Přístupové klávesy** pomocí odkazu v levém navigačním podokně. Získejte připojovací řetězec pro načtení dat z úložiště objektů Blob. Připojovací řetězec vypadá podobně jako v následujícím příkladu:`DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. Stále na portálu, přepněte na Azure Cognitive Search. [Vytvořte novou službu](search-create-service-portal.md) nebo [vyhledejte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Pro tento rychlý start můžete využít bezplatnou službu.

Nyní jste připraveni přejít v průvodci importem dat.

## <a name="run-the-import-data-wizard"></a>Spuštění Průvodce importem dat

Na stránce Přehled vyhledávací služby klikněte na **importovat data** na panelu příkazů a vytvořte úložiště znalostí ve čtyřech krocích.

  ![Příkaz pro import dat](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>Krok 1: Vytvoření zdroje dat

1. V **části Připojit k datům**zvolte Azure **Blob storage**, vyberte účet a kontejner, který jste vytvořili. 
1. Do **Name**pole Název `hotel-reviews-ds`zadejte .
1. V **režimu analýzy**vyberte **oddělovač textu**a zaškrtněte políčko **První řádek obsahuje záhlaví.** Ujistěte se, **že znak oddělovač** je čárka (,).
1. V **připojovacím řetězci**vložte do připojovacího řetězce, který jste zkopírovali ze stránky **Přístupové klíče** ve službě Azure Storage.
1. V **kontejnerech**zadejte název kontejneru objektů blob, který obsahuje data.

    Vaše stránka by měla vypadat podobně jako na následujícím snímku obrazovky.

    ![Vytvoření objektu zdroje dat](media/knowledge-store-create-portal/hotel-reviews-ds.png "Vytvoření objektu zdroje dat")

1. Pokračujte na další stránku.

### <a name="step-2-add-cognitive-skills"></a>Krok 2: Přidání kognitivních dovedností

V tomto kroku průvodce vytvoříte sadu dovedností s kognitivními obohaceními dovedností. Zdrojová data se skládají z recenzí zákazníků v několika jazycích. Mezi dovednosti, které jsou relevantní pro tuto sadu dat, patří extrakce klíčových frází, detekce mínění a překlad textu. V pozdějším kroku budou tyto obohacení "promítnuty" do úložiště znalostí jako tabulky Azure.

1. Rozbalte **možnost Připojit služby Cognitive Services**. Ve výchozím nastavení je vybrána možnost **Volné (omezené obohacení).** Tento zdroj můžete použít, protože počet záznamů v HotelReviews-Free.csv je 19 a tento volný zdroj umožňuje až 20 transakcí denně.
1. Rozbalte **přidat obohacení**.
1. Do pole Název `hotel-reviews-ss`sady **dovedností**zadejte .
1. V **poli Zdrojová data**vyberte možnost **reviews_text**.
1. Pro **úroveň rozlišovací schopnost obohacení**vyberte **Stránky (5000 bloků znaků)**
1. Vyberte tyto kognitivní dovednosti:
    + **Extrakce klíčových frází**
    + **Přeložení textu**
    + **Detekce mínění**

      ![Vytvoření sady dovedností](media/knowledge-store-create-portal/hotel-reviews-ss.png "Vytvoření sady dovedností")

1. Rozbalte **možnost Uložit obohacení do úložiště znalostí**.
1. Vyberte tyto **projekce tabulky Azure**:
    + **Dokumenty**
    + **Stránky**
    + **Klíčové fráze**
1. Zadejte **připojovací řetězec účtu úložiště,** který jste uložili v předchozím kroku.

    ![Konfigurace úložiště znalostí](media/knowledge-store-create-portal/hotel-reviews-ks.png "Konfigurace úložiště znalostí")

1. Volitelně si stáhněte šablonu Power BI. Při přístupu k šabloně z průvodce je místní soubor .pbit přizpůsoben tak, aby odrážel tvar dat.

1. Pokračujte na další stránku.

### <a name="step-3-configure-the-index"></a>Krok 3: Konfigurace indexu

V tomto kroku průvodce nakonfigurujete index pro volitelné fulltextové vyhledávací dotazy. Průvodce zavzorkuje zdroj dat pro odvod polí a datových typů. Stačí vybrat atributy pro požadované chování. Například atribut **Retrievable** umožní vyhledávací službě vrátit hodnotu pole, zatímco **možnost Prohledávatelné** umožní fulltextové vyhledávání v poli.

1. Do pole Název `hotel-reviews-idx` **rejstříku**zadejte .
1. U atributů přijměte výchozí výběry: **Lze vyhledat** a **vyhledat** pro nová pole, která kanál vytváří.

    Index by měl vypadat podobně jako na následujícím obrázku. Vzhledem k tomu, že seznam je dlouhý, ne všechna pole jsou v obrázku viditelná.

    ![Konfigurace indexu](media/knowledge-store-create-portal/hotel-reviews-idx.png "Konfigurace indexu")

1. Pokračujte na další stránku.

### <a name="step-4-configure-the-indexer"></a>Krok 4: Konfigurace indexeru

V tomto kroku průvodce nakonfigurujete indexer, který spojí zdroj dat, skillset a index, který jste definovali v předchozích krocích průvodce.

1. Do **pole** `hotel-reviews-idxr`Název zadejte .
1. V **části Plán**ponechte výchozí **hodnotu Jednou**.
1. Chcete-li spustit indexer, klepněte na **tlačítko Odeslat.** Extrakce dat, indexování, aplikace kognitivních dovedností se v tomto kroku dějí.

## <a name="monitor-status"></a>Sledování stavu

Kognitivní indexování dovedností trvá déle než typické indexování na základě textu. Průvodce by měl otevřít seznam Indexer na stránce s přehledem, abyste mohli sledovat průběh. Vlastní navigaci najdete na stránce Přehled a klikněte na **Indexery**.

Na webu Azure Portal můžete taky monitorovat protokol aktivit oznámení pro odkaz stavu oznámení azure **kognitivní vyhledávání,** na který lze kliknout. Spuštění může trvat několik minut.

## <a name="next-steps"></a>Další kroky

Teď, když jste obohatili svá data pomocí služeb Cognitive Services a promítli výsledky do úložiště znalostí, můžete pomocí Průzkumníka úložiště nebo Power BI prozkoumat vaši obohacenou sadu dat.

Obsah si můžete prohlížet v Průzkumníkovi úložiště nebo ho s Power BI ještě o krok dál získat díky vizualizaci.

> [!div class="nextstepaction"]
> [Zobrazení pomocí Průzkumníka](knowledge-store-view-storage-explorer.md)
> úložiště[Připojení pomocí Power BI](knowledge-store-connect-power-bi.md)

> [!Tip]
> Pokud chcete toto cvičení zopakovat nebo vyzkoušet jiný návod k obohacení umělou ai, odstraňte indexer *hotel-reviews-idxr.* Odstranění množení obnoví počítadlo transakcí zdarma denní zpět na nulu pro zpracování cognitive services.
