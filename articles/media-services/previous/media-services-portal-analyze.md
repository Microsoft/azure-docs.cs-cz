---
title: Analýza médií s využitím webu Azure portal | Dokumentace Microsoftu
description: Toto téma popisuje, jak zpracovat vašich multimédií pomocí Media Analytics procesory médií (sady Management Pack) pomocí webu Azure portal.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 02b972c0591bb05f72887e3e1c3d057d373ff48c
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56004865"
---
# <a name="analyze-your-media-using-the-azure-portal-legacy"></a>Analýza médií s využitím webu Azure portal (starší verze)
> [!NOTE]
> K dokončení tohoto kurzu potřebujete mít účet Azure. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

## <a name="overview"></a>Přehled
Azure Media Services Analytics je kolekce řečových a vizuálních komponent (v podnikovém měřítku, dodržování předpisů, zabezpečením a globálním dosahem), které organizacím a podnikům umožňují, aby ze svých videosouborů odvodily užitečné přehledy. Podrobnější přehled služby Azure Media Services Analytics najdete v článku [to](media-services-analytics-overview.md) tématu. 

Toto téma popisuje, jak zpracovat vašich multimédií pomocí Media Analytics procesory médií (sady Management Pack) pomocí webu Azure portal. Sady Management Pack Media Analytics vytvářejí soubory MP4 nebo soubory JSON. Pokud procesor médií vytvořil soubor MP4, progresivně stáhněte soubor. Pokud procesor médií vytvořil soubor JSON, stáhne soubor z úložiště objektů blob v Azure. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Vyberte asset, který chcete analyzovat
1. Na webu [Azure Portal](https://portal.azure.com/) zvolte účet Azure Media Services.
2. V okně **Nastavení** vyberte **Assety**.  
   
    ![Analýza videí](./media/media-services-portal-analyze/media-services-portal-analyze001.png)
3. Vyberte asset, který chcete analyzovat a stiskněte klávesu **analyzovat** tlačítko.
   
    ![Analýza videí](./media/media-services-portal-analyze/media-services-portal-analyze002.png)
4. V **zpracovat asset média pomocí Media Analytics** okna, vyberte procesor. 
   
    Zbývající části článku vysvětluje, proč a jak používat každý procesor. 
5. Stisknutím klávesy **vytvořit** na začátek a úlohy.

## <a name="azure-media-indexer"></a>Azure Media Indexer
**Azure Media Indexer** procesor médií umožňuje vytvořit mediální soubory a obsah prohledávatelné, jakož i generovat uzavřené stopy titulků. Tato část obsahuje některé informace o možnostech, které zadáte pro tento bod.

![Analýza videí](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Jazyk
Přirozeného jazyka k rozpoznat v multimediálním souboru. Například angličtina nebo španělština. 

### <a name="captions"></a>Titulky
Můžete zvolit popisek formátu, který se vygeneruje ze svého obsahu. Úloha indexování můžete vygenerovat soubory s titulky uvedené v následujících formátech:  

* **SAMI**
* **TTML**
* **WebVTT**

Zavření titulek (kopie) soubory do těchto formátů je možné provést zvukových souborů a videosouborů osobám s postižením sluchu.

### <a name="aib-file"></a>Soubor AIB
Tuto možnost vyberte, pokud chcete vygenerovat soubor Audio Index Blob pro použití s vlastním Ifilterem SQL serveru. Další informace najdete v tématu [to](https://azure.microsoft.com/blog/using-aib-files-with-azure-media-indexer-and-sql-server/) blogu.

### <a name="keywords"></a>Klíčová slova
Tuto možnost vyberte, pokud chcete vygenerovat soubor XML klíčových slov. Tento soubor obsahuje klíčová slova extrahovaná z mluveného obsahu s frekvencí a informace o posunu.

### <a name="job-name"></a>Název úlohy
Popisný název, který vám umožní identifikovat úlohu. [To](media-services-portal-check-job-progress.md) článku se dozvíte, jak můžete monitorovat průběh úlohy. 

### <a name="output-file"></a>Výstupní soubor
Popisný název, který vám umožní identifikovat výstupní obsah. 

## <a name="azure-media-hyperlapse"></a>Azure Media Hyperlapse
Azure Media Hyperlapse je na sadu Management Pack, která vytvoří smooth vypršelo čas videa z první, kdo nebo akce fotoaparátu obsahu.  Další informace najdete v [tomto](media-services-hyperlapse-content.md) tématu. Tato část obsahuje některé informace o možnostech, které zadáte pro tento bod.

![Analýza videí](./media/media-services-portal-analyze/media-services-portal-analyze004.png)

### <a name="speed"></a>Rychlost
Zadejte násobek zrychlení vstupního videa rychlost. Výstup je stabilizovaná a čas vypršelo interpretace vstupu videa.

### <a name="job-name"></a>Název úlohy
Popisný název, který vám umožní identifikovat úlohu. [To](media-services-portal-check-job-progress.md) článku se dozvíte, jak můžete monitorovat průběh úlohy. 

### <a name="output-file"></a>Výstupní soubor
Popisný název, který vám umožní identifikovat výstupní obsah. 

## <a name="azure-media-face-detector"></a>Azure Media Face Detector
**Azure Media Face Detectoru** procesor médií (PP) umožňuje počet, sledování pohybu a dokonce i vyhodnocovat zapojení cílové skupiny a reakce na ně prostřednictvím výrazu tváře. Tato služba obsahuje dvě funkce: 

* **Rozpoznávání tváře**
  
    Rozpoznávání tváře najde a sleduje lidské tváře ve videu. Více ploch lze zjistit a následně sledovat při přechodu, čas a umístění metadat vrátil v souboru JSON. Během sledování, pokusí se poskytnout konzistentní ID na stejný typ písma zatímco je osoba přesouvat na obrazovce, i když bráněno nebo stručně ponechejte rámce.
  
  > [!NOTE]
  > Tato služba neprovádí rozpoznávání obličeje. Osoba, která zůstanou rámce nebo bude bránit pro příliš dlouho dostanou nové ID když se vrátí.
  > 
  > 
* **Rozpoznávání emocí z výrazu**
  
    Rozpoznávání emocí z výrazu je volitelná součást pro rozpoznávání tváře detekce mediálním procesorem, který vrátí analýzy na více citové atributů z plochy zjistí, včetně štěstí, smutek, strach, hněv a další. 

![Analýza videí](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Režim detekce
Jeden z následujících režimů může využívat procesoru:

* Rozpoznávání tváře
* detekce emocí podle obličeje
* Agregovat detekci emocí

### <a name="job-name"></a>Název úlohy
Popisný název, který vám umožní identifikovat úlohu. [To](media-services-portal-check-job-progress.md) článku se dozvíte, jak můžete monitorovat průběh úlohy. 

### <a name="output-file"></a>Výstupní soubor
Popisný název, který vám umožní identifikovat výstupní obsah. 

## <a name="azure-media-motion-detector"></a>Azure Media Motion Detector
**Detektor pohybu médií Azure** procesor médií (PP) umožňuje efektivně identifikují oddíly zájmu v rámci jinak dlouhé a bezproblémové videa. Detekce pohybu lze použít na statické kamer k identifikaci části videa, kde dochází k pohybu. Generuje soubor JSON obsahující metadata s časová razítka a ohraničující konkrétní oblasti, kde k události došlo.

Zaměřené na zabezpečení videa informační kanály, tato technologie je schopen kategorizace provozu do relevantní události a falešně pozitivních zpráv, jako je například stíny a změn osvětlení. To umožňuje generovat výstrahy zabezpečení z fotoaparátu kanálů bez nevyžádané pošty s nekonečnou relevantní události, při zachování nedokáže extrahovat okamžiků zájmu z videí extrémně dlouhou institucí.

![Analýza videí](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Azure Media Video Thumbnails
Tento procesor vám umožňují vytvořit souhrny z dlouhých videí automaticky výběrem zajímavé fragmenty kódu ze zdrojového videa. To je užitečné, pokud byste chtěli poskytnout rychlý přehled toho, co očekávat při dlouhé videa. Podrobné informace a příklady najdete v tématu [miniatur videa v používání Azure Media k vytvoření Videosouhrnu](media-services-video-summarization.md)

![Analýza videí](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Název úlohy
Popisný název, který vám umožní identifikovat úlohu. [To](media-services-portal-check-job-progress.md) článku se dozvíte, jak můžete monitorovat průběh úlohy. 

### <a name="output-file"></a>Výstupní soubor
Popisný název, který vám umožní identifikovat výstupní obsah. 

## <a name="azure-media-content-moderator"></a>Azure Media Content Moderator
Tento procesor pomáhá detekovat potenciální obsahu pro dospělé nebo pikantního obsahu ve videích. Procesor automaticky rozpozná snímky a klíčové snímky ve videu. Stanoví skóre klíčové snímky pro možný obsah pro dospělé nebo pikantního a navrhne kontroly podle výchozí prahové hodnoty. Podrobné informace a příklady najdete v tématu [pomocí Azure Media Content Moderator, obscénnost videa](media-services-content-moderation.md)

![Moderování videa](./media/media-services-portal-analyze/media-services-portal-analyze-content-moderator.PNG)

### <a name="version"></a>Verze 
Použití "2.0".

### <a name="mode"></a>Mode
Ignorovat verze 2.0 `Mode` nastavení.

## <a name="next-steps"></a>Další postup
Postupy výuky zobrazení Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
