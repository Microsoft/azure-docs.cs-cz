---
title: Analýza médiu pomocí portálu Azure | Microsoft Docs
description: Toto téma popisuje postupy zpracování médií s procesory médií Media Analytics (sad Management Pack) pomocí portálu Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: juliako
ms.openlocfilehash: fee9f8d2204869cbe5cac7f446e8011305e92bfa
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2018
ms.locfileid: "29129086"
---
# <a name="analyze-your-media-using-the-azure-portal"></a>Analýza médií s využitím webu Azure Portal
> [!NOTE]
> K dokončení tohoto kurzu potřebujete mít účet Azure. Podrobnosti najdete v tématu [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

## <a name="overview"></a>Přehled
Azure Media Services Analytics je kolekce řečových a vizuálních komponent (na škálování enterprise, dodržování předpisů, zabezpečení a globální reach), které usnadňují organizacím a podnikům umožňují k získání prakticky uplatnitelných informací ze svých videosouborů. Podrobnější přehled Azure Media Services Analytics najdete v článku [to](media-services-analytics-overview.md) tématu. 

Toto téma popisuje postupy zpracování médií s procesory médií Media Analytics (sad Management Pack) pomocí portálu Azure. MP Media Analytics vytvářejí soubory MP4 nebo soubory JSON. Pokud procesor médií vytvořil soubor MP4, progresivně stáhněte soubor. Pokud procesor médií vytvořil soubor JSON, můžete soubor stáhnout z Azure blob storage. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Vyberte asset, který chcete analyzovat
1. Na webu [Azure Portal](https://portal.azure.com/) zvolte účet Azure Media Services.
2. V okně **Nastavení** vyberte **Assety**.  
   .
    ![Analýza videa](./media/media-services-portal-analyze/media-services-portal-analyze001.png)
3. Vyberte asset, který chcete analyzovat a stiskněte klávesu **analyzovat** tlačítko.
   
    ![Analýza videa](./media/media-services-portal-analyze/media-services-portal-analyze002.png)
4. V **proces média asset Media Analytics** okně vyberte nastavení procesoru. 
   
    Zbývající část článek vysvětluje důvod, proč a jak používat každý procesor. 
5. Stiskněte klávesu **vytvořit** spuštění a úlohy.

## <a name="azure-media-indexer"></a>Azure Media Indexer
**Azure Media Indexer** procesor médií umožňuje vytvoření mediálních souborů a obsah s možností vyhledávání, jakož i generovat uzavřené titulků sleduje. Tato část obsahuje některé údaje o možnostech, které zadáte pro tento bod.

![Analýza videa](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Jazyk
Přirozeného jazyka rozpoznat v multimediálních souborů. Například angličtina nebo španělštině. 

### <a name="captions"></a>Titulky
Můžete formátu popisek, který bude vydána z vašeho obsahu. Úlohu indexování může generovat soubory titulků v následujících formátech:  

* **SAMI**
* **TTML**
* **WebVTT**

Popisek (kopie) soubory v těchto formátů lze Zpřístupněte soubory audia a videa pro osoby s postižením sluchu uzavřít.

### <a name="aib-file"></a>Soubor AIB
Tuto možnost vyberte, pokud chcete generovat soubor zvuk Index Blob pro použití s k objektu IFilter vlastní SQL Server. Další informace najdete v tématu [to](https://azure.microsoft.com/blog/using-aib-files-with-azure-media-indexer-and-sql-server/) blogu.

### <a name="keywords"></a>Klíčová slova
Tuto možnost vyberte, pokud chcete generovat soubor XML, klíčová slova. Tento soubor obsahuje klíčová slova extrahuje z obsahu řeči četnost a informace o posunu.

### <a name="job-name"></a>Název úlohy
Popisný název, který umožňuje identifikovat úlohu. [To](media-services-portal-check-job-progress.md) článek popisuje, jak můžete sledovat průběh úlohy. 

### <a name="output-file"></a>Výstupní soubor
Popisný název, který umožňuje identifikovat obsah výstup. 

## <a name="azure-media-hyperlapse"></a>Azure Media Hyperlapse
Azure Media Hyperlapse je na sadu Management Pack vytvoří smooth vypršelo čas videa z první, kdo nebo akce fotoaparát obsah.  Další informace najdete v [tomto](media-services-hyperlapse-content.md) tématu. Tato část obsahuje některé údaje o možnostech, které zadáte pro tento bod.

![Analýza videa](./media/media-services-portal-analyze/media-services-portal-analyze004.png)

### <a name="speed"></a>Rychlost
Určete rychlost ke které má být urychlení vstupní video. Výstup je stabilizované a čas vypršelo interpretace vstupu videa.

### <a name="job-name"></a>Název úlohy
Popisný název, který umožňuje identifikovat úlohu. [To](media-services-portal-check-job-progress.md) článek popisuje, jak můžete sledovat průběh úlohy. 

### <a name="output-file"></a>Výstupní soubor
Popisný název, který umožňuje identifikovat obsah výstup. 

## <a name="azure-media-face-detector"></a>Azure Media Face Detector
**Azure Media vzhled detektor** procesor médií (PP) umožňuje count, sledovat pohybů a i vyhodnocení zapojení cílové skupiny a reakce prostřednictvím obličeje výrazy. Tato služba obsahuje dvě funkce: 

* **Vzhled detekce**
  
    Vzhled zjišťování vyhledá a sleduje lidského tyto řezy v rámci video. Více řezy lze zjistit a následně je sledovat jako pohyb se s metadaty čas a umístění, vrátí se v souboru JSON. Při sledování, pokusí se dávat konzistentní ID stejné písmo při osoba, která je manipulaci se na obrazovce, přestože bráněno nebo stručně nechte rámečku.
  
  > [!NOTE]
  > Této služby nebude provádět rozpoznávání obličeje. Osoba, která zůstane rámečku nebo se stane nelze blokovat. pro příliš dlouho přidělí nové ID když se vrátí.
  > 
  > 
* **Emocí**
  
    Emocí je volitelná součást procesor média detekce řez, který vrátí analýzy na více duševní atributů z řezy detekuje, včetně štěstí, sadness, obavy, anger a další. 

![Analýza videa](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Detekce režimu
Jeden z následujících režimů mohou využívat procesor:

* Vzhled detekce
* každý řez emocí
* agregační emocí

### <a name="job-name"></a>Název úlohy
Popisný název, který umožňuje identifikovat úlohu. [To](media-services-portal-check-job-progress.md) článek popisuje, jak můžete sledovat průběh úlohy. 

### <a name="output-file"></a>Výstupní soubor
Popisný název, který umožňuje identifikovat obsah výstup. 

## <a name="azure-media-motion-detector"></a>Azure Media Motion Detector
**Detektor pohybu médií Azure** procesor médií (PP) umožňuje efektivně identifikaci části týkající se v rámci souboru jinak dlouhé a bezproblémové video. Detekce pohybu dají použít na statické kamer k identifikaci části videa, kde dochází k pohybu. Vygeneruje soubor JSON obsahující metadata s časová razítka a ohraničující oblasti, kde došlo k události.

Cílem směrem zabezpečení video informační kanály, tato technologie je možné zařadit do kategorií pohybu do příslušné události a falešně pozitivních například stínů a osvětlení změny. To umožňuje generovat výstrahy zabezpečení z fotoaparátu kanály bez nevyžádané pošty s nekonečná důležité události, při schopnost extrahovat z extrémně dlouhé sledováním videa situacích, které vás zajímají.

![Analýza videa](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Azure Media Video Thumbnails
Tato procesoru vám pomůžou vytvořit souhrnných informací o dlouho videa automaticky výběrem zajímavé fragmenty kódu z zdroj videa. To je užitečné, pokud byste chtěli poskytnout rychlý přehled toho, co očekávat při dlouhé video. Podrobné informace a příklady naleznete v tématu [miniatur videa v používání Azure Media k vytvoření Videosouhrnu](media-services-video-summarization.md)

![Analýza videa](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Název úlohy
Popisný název, který umožňuje identifikovat úlohu. [To](media-services-portal-check-job-progress.md) článek popisuje, jak můžete sledovat průběh úlohy. 

### <a name="output-file"></a>Výstupní soubor
Popisný název, který umožňuje identifikovat obsah výstup. 

## <a name="azure-media-content-moderator"></a>Azure Media obsahu moderátora
Tento procesor pomáhá rozpoznat potenciální obsah pro dospělé a zájem v videa. Procesor automaticky rozpozná snímky a klíčových snímků na videu. Skóre klíčových snímků pro možné obsah pro dospělé nebo zájem a navrhne recenze podle výchozí prahové hodnoty. Podrobné informace a příklady naleznete v tématu [pomocí Azure Media obsahu moderátora na střední videa](media-services-content-moderation.md)

![Střední videa](./media/media-services-portal-analyze/media-services-portal-analyze-content-moderator.PNG)

### <a name="version"></a>Verze 
Použijte "2.0".

### <a name="mode"></a>Mode
Ignorovat verze 2.0 `Mode` nastavení.

## <a name="next-steps"></a>Další postup
Zobrazení Media Services kurzů.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
