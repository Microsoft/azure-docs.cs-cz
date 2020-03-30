---
title: Redigovat tváře s návodem k Azure Media Analytics | Dokumenty společnosti Microsoft
description: Toto téma ukazuje krok za krokem pokyny, jak spustit pracovní postup úplné redigování pomocí Průzkumníka médií Azure Media Services (AMSE) a Azure Media Redactor Visualizer (open source nástroj).
services: media-services
documentationcenter: ''
author: Lichard
manager: femila
editor: ''
ms.assetid: d6fa21b8-d80a-41b7-80c1-ff1761bc68f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: ril
ms.reviewer: juliako
ms.openlocfilehash: a8db8de6ef062dcf757f3d264379677d6550ea3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69997676"
---
# <a name="redact-faces-with-azure-media-analytics-walkthrough"></a>Redigovat tváře s návodem k Azure Media Analytics

## <a name="overview"></a>Přehled

**Azure Media Redactor** je mediální procesor [Azure Media Analytics](media-services-analytics-overview.md) (MP), který nabízí škálovatelnou redigování tváří v cloudu. Redigování obličeje umožňuje upravit video tak, aby rozmazalo tváře vybraných jedinců. Službu redigování obličeje můžete použít ve scénářích veřejné bezpečnosti a zpravodajských médií. Několik minut záznamu, který obsahuje více tváří, může trvat hodiny, než se ručně rediguje, ale s touto službou bude proces redigování obličeje vyžadovat jen několik jednoduchých kroků. Další informace naleznete v [tomto](https://azure.microsoft.com/blog/azure-media-redactor/) blogu.

Podrobnosti o **azure media redactor**najdete v tématu [face redigování přehled.](media-services-face-redaction.md)

Toto téma ukazuje krok za krokem pokyny, jak spustit pracovní postup úplné redigování pomocí Průzkumníka médií Azure Media Services (AMSE) a Azure Media Redactor Visualizer (open source nástroj).

Další informace naleznete v [tomto](https://azure.microsoft.com/blog/redaction-preview-available-globally) blogu.

## <a name="azure-media-services-explorer-workflow"></a>Pracovní postup Průzkumníka Médií Azure

Nejjednodušší způsob, jak začít s Redactor je použít open source Nástroj AMSE na GitHub. Pokud nepotřebujete přístup k obrázkům json nebo face jpg, můžete zjednodušený pracovní postup spustit v **kombinovaném** režimu.

### <a name="download-and-setup"></a>Stažení a nastavení

1. Stáhněte si nástroj AMSE pro AMS v2 [zde](https://aka.ms/amseforv2).
1. Přihlaste se ke svému účtu Mediálníslužby pomocí klíče služby.

    Pokud chcete získat název účtu a informace o klíči, přejděte na [Azure Portal](https://portal.azure.com/) a vyberte svůj účet AMS. Potom vyberte Nastavení > kláves. Zobrazí se okno Správa klíčů, které ukazuje název účtu a primární a sekundární klíče. Zkopírujte hodnoty názvu účtu a primárního klíče.

![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough001.png)

### <a name="first-pass--analyze-mode"></a>První průchod – režim analýzy

1. Nahrajte mediální soubor prostřednictvím služby Asset –> Nahrát nebo přetažením. 
1. Klikněte pravým tlačítkem myši a zpracujte mediální soubor pomocí služby Media Analytics – > režimu Azure Media Redactor – >. 


![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough002.png)

![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough003.png)

Výstup bude obsahovat poznámky json soubor s daty umístění obličeje, stejně jako jpg každé zjištěné plochy. 

![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough004.png)

### <a name="second-pass--redact-mode"></a>Druhý průchod – režim redigování

1. Nahrajte původní video datový zdroj do výstupu z prvního průchodu a nastavte jako primární datový zdroj. 

    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough005.png)

2. (Nepovinné) Nahrajte soubor Dance_idlist.txt, který obsahuje nový seznam id, která chcete redigovat. 

    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough006.png)

3. (Nepovinné) Proveďte všechny úpravy souboru Anotations.json, například zvýšení hranic ohraničovacího rámečku. 
4. Klikněte pravým tlačítkem myši na výstupní datový zdroj z prvního průchodu, vyberte redactor a spusťte s režimem **Redact.** 

    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough007.png)

5. Stáhněte nebo sdílejte konečný redigovaný výstupní datový zdroj. 

    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough008.png)

## <a name="azure-media-redactor-visualizer-open-source-tool"></a>Nástroj Open source visualismu Azure Media Redactor Visualizer

Nástroj [pro vizualizaci](https://github.com/Microsoft/azure-media-redactor-visualizer) s otevřeným zdrojovým kódem je navržen tak, aby vývojářům pomohl začít s formátem poznámky s analýzou a použitím výstupu.

Po klonování repo, aby bylo možné spustit projekt, budete muset stáhnout FFMPEG z jejich [oficiálních stránek](https://ffmpeg.org/download.html).

Pokud jste vývojář, který se pokouší analyzovat data poznámky JSON, podívejte se do části Models.MetaData příklady ukázkového kódu.

### <a name="set-up-the-tool"></a>Nastavení nástroje

1.  Stáhněte si a sestavte celé řešení. 

    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough009.png)

2.  Stáhnout FFMPEG [zde](https://ffmpeg.org/download.html). Tento projekt byl původně vyvinut s verzí be1d324 (2016-10-04) se statickým propojením. 
3.  Zkopírujte ffmpeg.exe a ffprobe.exe do stejné výstupní složky jako AzureMediaRedactor.exe. 

    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough010.png)

4. Spusťte soubor AzureMediaRedactor.exe. 

### <a name="use-the-tool"></a>Použití nástroje

1. Zpracujte své video v účtu Azure Media Services pomocí režimu Redactor MP on Analyze. 
2. Stáhněte si původní video soubor a výstup úlohy Redigování - analyzovat. 
3. Spusťte aplikaci vizualizéru a zvolte výše uvedené soubory. 

    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough011.png)

4. Náhled souboru. Vyberte, které plochy chcete rozmazat přes postranní panel vpravo. 
    
    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough012.png)

5.  Dolní textové pole se aktualizuje s ID plochy. Vytvořte soubor s názvem "idlist.txt" s těmito ID jako seznam emitovaný novým řádkem. 

    >[!NOTE]
    > Soubor idlist.txt by měl být uložen v ansi. Poznámkový blok můžete použít k uložení v ANSI.
    
6.  Nahrajte tento soubor do výstupního datového zdroje z kroku 1. Nahrajte také původní video do tohoto datového zdroje a nastavte jej jako primární datový zdroj. 
7.  Spusťte úlohu redigování tohoto datového zdroje v režimu "Redact", abyste získali konečné redigované video. 

## <a name="next-steps"></a>Další kroky 

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Související odkazy
[Přehled analýzy Mediálních služeb Azure](media-services-analytics-overview.md)

[Ukázky Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

[Oznámení redigování obličeje pro Azure Media Analytics](https://azure.microsoft.com/blog/azure-media-redactor/)
