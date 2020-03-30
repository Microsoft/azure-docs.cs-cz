---
title: Analýza médií pomocí portálu Azure | Dokumenty společnosti Microsoft
description: Toto téma popisuje, jak zpracovat média pomocí mediálních procesorů Media Analytics pomocí portálu Azure.
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
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 0e49e90209c7337081458b7c214d27b37d3b4da1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74462615"
---
# <a name="analyze-your-media-using-the-azure-portal"></a>Analýza médií pomocí portálu Azure 

> [!IMPORTANT]
> Zkontrolujte [plány vyřazení](media-services-analytics-overview.md#retirement-plans) některých mediálních procesorů.

## <a name="overview"></a>Přehled
Azure Media Services Analytics je kolekce součástí řeči a vize (v podnikovém měřítku, dodržování předpisů, zabezpečení a globální dosah), které usnadňují organizacím a podnikům odvodit užitečné poznatky z jejich video souborů. Podrobnější přehled analýzy Azure Media Services Analytics najdete [v tomto](media-services-analytics-overview.md) tématu. 

Toto téma popisuje, jak zpracovat média pomocí mediálních procesorů Media Analytics pomocí portálu Azure. MPs Media Analytics produkují soubory MP4 nebo JSON. Pokud mediální procesor vytvořil soubor MP4, postupně jej stahujete. Pokud mediální procesor vytvořil soubor JSON, stáhnete soubor z úložiště objektů blob Azure. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Vyberte datový zdroj, který chcete analyzovat.
1. Na webu [Azure Portal](https://portal.azure.com/) zvolte účet Azure Media Services.
2. V okně **Nastavení** vyberte **Assety**.  
   
    ![Analýza videí](./media/media-services-portal-analyze/media-services-portal-analyze001.png)
3. Vyberte datový zdroj, který chcete analyzovat, a stiskněte tlačítko **Analyzovat.**
   
    ![Analýza videí](./media/media-services-portal-analyze/media-services-portal-analyze002.png)
4. V okně **Zpracovat mediální prostředek s aplikací Media Analytics** vyberte procesor. 
   
    Zbytek článku vysvětluje, proč a jak používat každý procesor. 
5. Stisknutím **klávesy Vytvořit** spustíte úlohu.

## <a name="azure-media-indexer"></a>Azure Media Indexer
Mediální procesor **Azure Media Indexer** umožňuje prohledávat mediální soubory a obsah a také generovat stopy skrytých titulků. Tato část obsahuje některé podrobnosti o možnostech, které zadáte pro tento MP.

![Analýza videí](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Jazyk
Přirozený jazyk, který má být rozpoznán v multimediálním souboru. Například angličtina nebo španělština. 

### <a name="captions"></a>Popisky
Můžete zvolit formát titulku, který bude generován z vašeho obsahu. Úloha indexování může generovat soubory skrytých titulků v následujících formátech:  
 
* **TTML**
* **WebVTT**

Soubory s uzavřeným titulkem (CC) v těchto formátech lze použít k tomu, aby byly zvukové soubory a video soubory přístupné osobám se sluchovým postižením.

### <a name="keywords"></a>Klíčová slova
Tuto možnost vyberte, pokud chcete vygenerovat soubor XML klíčových slov. Tento soubor obsahuje klíčová slova extrahovaná z obsahu řeči s informacemi o frekvenci a posunu.

### <a name="job-name"></a>Název úlohy
Popisný název, který umožňuje identifikovat úlohu. [Tento](media-services-portal-check-job-progress.md) článek popisuje, jak můžete sledovat průběh úlohy. 

### <a name="output-file"></a>Výstupní soubor
Popisný název, který umožňuje identifikovat výstupní obsah. 

### <a name="speed"></a>Rychlost
Určete rychlost, s jakou chcete zrychlit vstupní video. Výstupem je stabilizované a časově zaokalené ztvárnění vstupního videa.

### <a name="job-name"></a>Název úlohy
Popisný název, který umožňuje identifikovat úlohu. [Tento](media-services-portal-check-job-progress.md) článek popisuje, jak můžete sledovat průběh úlohy. 

### <a name="output-file"></a>Výstupní soubor
Popisný název, který umožňuje identifikovat výstupní obsah. 

## <a name="azure-media-face-detector"></a>Azure Media Face Detector
Mediální procesor **Azure Media Face Detector** (MP) umožňuje počítat, sledovat pohyby a dokonce měřit účast publika a reakci pomocí výrazů obličeje. Tato služba obsahuje dvě funkce: 

* **Detekce obličeje**
  
    Detekce obličeje vyhledá a sleduje lidské tváře ve videu. Více tváří lze zjistit a následně sledovat, jak se pohybují, s časem a umístění metadata vrácena v souboru JSON. Během sledování se pokusí poskytnout konzistentní ID na stejnou tvář, zatímco osoba se pohybuje na obrazovce, i když jsou blokovány nebo krátce opustit rám.
  
  > [!NOTE]
  > Tyto služby neprovádějí rozpoznávání obličeje. Jedinec, který opustí rám nebo se stane překážkou příliš dlouho, dostane při návratu nové ID.
  > 
  > 
* **Detekce emocí**
  
    Detekce emocí je volitelná součást editoru médií pro detekci obličeje, která vrací analýzu několika emočních atributů z detekovaných tváří, včetně štěstí, smutku, strachu, hněvu a dalších. 

![Analýza videí](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Detekční režim
Procesor může použít jeden z následujících režimů:

* detekce obličeje
* detekce emocí na obličej
* souhrnná detekce emocí

### <a name="job-name"></a>Název úlohy
Popisný název, který umožňuje identifikovat úlohu. [Tento](media-services-portal-check-job-progress.md) článek popisuje, jak můžete sledovat průběh úlohy. 

### <a name="output-file"></a>Výstupní soubor
Popisný název, který umožňuje identifikovat výstupní obsah. 

## <a name="azure-media-motion-detector"></a>Azure Media Motion Detector
Mediální procesor **Azure Media Motion Detector** (MP) umožňuje efektivně identifikovat části zájmu v rámci jinak dlouhého a bezproblémového videa. Detekci pohybu lze použít na statických záběrech kamery k identifikaci částí videa, kde dochází k pohybu. Generuje soubor JSON obsahující metadata s časovými razítky a ohraničující oblasti, kde došlo k události.

Tato technologie je zaměřena na bezpečnostní videozáznamy a je schopna kategorizovat pohyb do relevantních událostí a falešných poplachů, jako jsou stíny a změny osvětlení. To vám umožní generovat výstrahy zabezpečení z kamerových kanálů, aniž by byl spams nekonečné irelevantní události, a zároveň je schopen extrahovat okamžiky zájmu z extrémně dlouhých sledovacích videí.

![Analýza videí](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Azure Media Video Thumbnails
Tento procesor vám pomůže vytvořit souhrny dlouhých videí automatickým výběrem zajímavých úryvků ze zdrojového videa. To je užitečné, pokud chcete poskytnout rychlý přehled o tom, co můžete očekávat v dlouhém videu. Podrobné informace a příklady najdete [v tématu Použití miniatur videa médií Azure k vytvoření souhrnu videa.](media-services-video-summarization.md)

![Analýza videí](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Název úlohy
Popisný název, který umožňuje identifikovat úlohu. [Tento](media-services-portal-check-job-progress.md) článek popisuje, jak můžete sledovat průběh úlohy. 

### <a name="output-file"></a>Výstupní soubor
Popisný název, který umožňuje identifikovat výstupní obsah. 

## <a name="azure-media-content-moderator"></a>Moderátor mediálního obsahu Azure
Tento procesor vám pomůže odhalit potenciální obsah pro dospělé a pikantní obsah ve videích. Procesor automaticky detekuje snímky a klíčové snímky ve videu. Zkóruje klíčové snímky pro možný obsah pro dospělé nebo pikantní obsah a navrhuje recenze na základě výchozích prahových hodnot. Podrobné informace a příklady najdete [v tématu Moderování videí pomocí moderátoru mediálního obsahu Azure](media-services-content-moderation.md)

![Moderování videí](./media/media-services-portal-analyze/media-services-portal-analyze-content-moderator.PNG)

### <a name="version"></a>Version 
Použijte "2.0".

### <a name="mode"></a>Mode
Verze 2.0 `Mode` ignoruje nastavení.

## <a name="next-steps"></a>Další kroky
Zobrazit studijní cesty služby Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
