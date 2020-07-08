---
title: Analyzujte své médium pomocí Azure Portal | Microsoft Docs
description: Toto téma popisuje, jak zpracovat média pomocí Media Analyticsch procesorů médií (MPs) pomocí Azure Portal.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "74462615"
---
# <a name="analyze-your-media-using-the-azure-portal"></a>Analýza médií pomocí Azure Portal 

> [!IMPORTANT]
> Prohlédněte si [plány vyřazení](media-services-analytics-overview.md#retirement-plans) některých multimediálních procesorů.

## <a name="overview"></a>Přehled
Azure Media Services Analytics je kolekce funkcí pro řeč a vize (na úrovni podniku, dodržování předpisů, zabezpečení a globální dosah), která organizacím a podnikům umožňuje odvodit užitečné poznatky z jejich videosouborů. Podrobnější přehled Azure Media Services Analytics najdete v [tomto](media-services-analytics-overview.md) tématu. 

Toto téma popisuje, jak zpracovat média pomocí Media Analyticsch procesorů médií (MPs) pomocí Azure Portal. Media Analytics soubory MP4 nebo soubory JSON sady MPs. Pokud procesor médií vytvořil soubor MP4, budete ho postupně stahovat. Pokud procesor médií vytvořil soubor JSON, stáhnete ho z úložiště objektů BLOB v Azure. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Vyberte Asset, který chcete analyzovat.
1. Na webu [Azure Portal](https://portal.azure.com/) zvolte účet Azure Media Services.
2. V okně **Nastavení** vyberte **Assety**.  
   
    ![Analýza videí](./media/media-services-portal-analyze/media-services-portal-analyze001.png)
3. Vyberte Asset, který chcete analyzovat, a stiskněte tlačítko **analyzovat** .
   
    ![Analýza videí](./media/media-services-portal-analyze/media-services-portal-analyze002.png)
4. V okně **zpracovat prostředek média pomocí Media Analytics** vyberte procesor. 
   
    Zbytek článku vysvětluje, proč a jak používat jednotlivé procesory. 
5. Stisknutím tlačítka **vytvořit** spusťte úlohu.

## <a name="azure-media-indexer"></a>Azure Media Indexer
Procesor **Azure Media Indexer** médií umožňuje vytvářet soubory médií a obsah prohledávatelné a také generovat skryté titulky. V této části jsou uvedeny některé podrobnosti o možnostech, které pro tuto sadu MP zadáte.

![Analýza videí](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Jazyk
Přirozený jazyk, který se má rozpoznat v multimediálním souboru Například angličtina nebo španělština. 

### <a name="captions"></a>Popisky
Můžete zvolit formát titulku, který se vygeneruje z vašeho obsahu. Úloha indexování může generovat soubory titulků v následujících formátech:  
 
* **TTML**
* **WebVTT**

Soubory skrytého titulku (CC) v těchto formátech lze použít k zpřístupnění zvukových a videosouborů uživatelům s postižením sluchu.

### <a name="keywords"></a>Klíčová slova
Tuto možnost vyberte, pokud chcete vygenerovat soubor XML s klíčovými slovy. Tento soubor obsahuje klíčová slova extrahovaná z obsahu řeči s informacemi o četnosti a posunu.

### <a name="job-name"></a>Název úlohy
Popisný název, který vám umožní identifikovat úlohu. [Tento](media-services-portal-check-job-progress.md) článek popisuje, jak můžete monitorovat průběh úlohy. 

### <a name="output-file"></a>Výstupní soubor
Popisný název, který vám umožní identifikovat výstupní obsah. 

### <a name="speed"></a>Rychlost
Zadejte rychlost, s jakou se má zrychlit vstupní video. Výstupem je stabilizovaná a časově časová verze vstupního videa.

### <a name="job-name"></a>Název úlohy
Popisný název, který vám umožní identifikovat úlohu. [Tento](media-services-portal-check-job-progress.md) článek popisuje, jak můžete monitorovat průběh úlohy. 

### <a name="output-file"></a>Výstupní soubor
Popisný název, který vám umožní identifikovat výstupní obsah. 

## <a name="azure-media-face-detector"></a>Azure Media Face Detector
Procesor **Azure Media Face Detector** Media (MP) umožňuje počítat, sledovat pohyby a dokonce i vyhodnocovat podíl a reakci v cílové skupině prostřednictvím výrazů obličeje. Tato služba obsahuje dvě funkce: 

* **Rozpoznávání tváře**
  
    Detekce tváře vyhledává a sleduje lidské obličeje ve videu. Je možné detekovat více plošek a následně je sledovat při jejich přesunu s metadaty pro čas a umístění vrácenými v souboru JSON. Během sledování se pokusí dát stejnému snímku stejné ID, zatímco se osoba pohybuje na obrazovce, a to i v případě, že jsou zablokované nebo stručně opouští rámeček.
  
  > [!NOTE]
  > Tato služba neprovádí rozpoznávání obličeje. Jednotlivá osoba, která opustí rámec nebo se může zablokovat příliš dlouho, bude při návratu na ni předána novému ID.
  > 
  > 
* **Detekce emoce**
  
    Detekce emoce je volitelná součást procesoru Rozpoznávání tvářech médií, která vrací analýzu více atributů emocionálních ze zjištěných ploch, včetně štěstí, smutek, obav, hněv a dalších. 

![Analýza videí](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Režim detekce
Procesor může použít jeden z následujících režimů:

* rozpoznávání tváře
* detekce emoce podle obličeje
* zjištění agregované emoce

### <a name="job-name"></a>Název úlohy
Popisný název, který vám umožní identifikovat úlohu. [Tento](media-services-portal-check-job-progress.md) článek popisuje, jak můžete monitorovat průběh úlohy. 

### <a name="output-file"></a>Výstupní soubor
Popisný název, který vám umožní identifikovat výstupní obsah. 

## <a name="azure-media-motion-detector"></a>Azure Media Motion Detector
Procesor **Azure Media Motion Detector** Media (MP) umožňuje efektivně identifikovat oddíly zájmu v jiném dlouhodobém a nenáročném videu. Detekce pohybu se dá použít ve statických záběrech fotoaparátu k identifikaci částí videa, kde dochází k pohybu. Vygeneruje soubor JSON obsahující metadata s časovými razítky a ohraničující oblastí, kde došlo k události.

Tato technologie je zaměřená na kanály videa o zabezpečení, takže je možné kategorizovat pohyb do relevantních událostí a falešně pozitivních změn, jako jsou například stíny a osvětlení. To vám umožní vygenerovat výstrahy zabezpečení z kanálů kamery, aniž by byly zasílány proti spamu s nepodstatnými nepodstatnými událostmi, a současně může extrahovat moment zájmu z extrémně dlouhých videí o monitorování.

![Analýza videí](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Azure Media Video Thumbnails
Tento procesor vám může přispět k vytváření souhrnů dlouhých videí tím, že automaticky vybere zajímavé fragmenty ze zdrojového videa. To je užitečné v případě, že chcete poskytnout rychlý přehled toho, co na dlouhém videu očekáváte. Podrobné informace a příklady najdete v tématu [použití Azure Media Video Thumbnails k vytvoření shrnutí videa](media-services-video-summarization.md) .

![Analýza videí](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Název úlohy
Popisný název, který vám umožní identifikovat úlohu. [Tento](media-services-portal-check-job-progress.md) článek popisuje, jak můžete monitorovat průběh úlohy. 

### <a name="output-file"></a>Výstupní soubor
Popisný název, který vám umožní identifikovat výstupní obsah. 

## <a name="azure-media-content-moderator"></a>Azure Media Content Moderator
Tento procesor pomáhá detekovat potenciální obsah pro dospělé a pikantní ve videích. Procesor automaticky detekuje snímky a klíčové snímky ve videu. Vyhodnotí klíčové snímky pro možný obsah pro dospělé nebo pikantní a navrhne kontroly na základě výchozích prahových hodnot. Podrobné informace a příklady najdete v tématu [použití Azure Media Content moderator pro střední videa](media-services-content-moderation.md) .

![Střední videa](./media/media-services-portal-analyze/media-services-portal-analyze-content-moderator.PNG)

### <a name="version"></a>Verze 
Použijte "2,0".

### <a name="mode"></a>Mode
Verze 2,0 ignoruje `Mode` nastavení.

## <a name="next-steps"></a>Další kroky
Zobrazení Media Servicesch cest výuky.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
