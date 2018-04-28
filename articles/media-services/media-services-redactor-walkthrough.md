---
title: Redigovat tyto řezy v Průvodci Azure Media Analytics | Microsoft Docs
description: Toto téma ukazuje podrobné informace o tom, jak spustit workflow úplné redigování pomocí Azure Media Services Explorer (AMSE) a Azure Media Redactor vizualizér (nástroj s otevřeným zdrojem).
services: media-services
documentationcenter: ''
author: Lichard
manager: cfowler
editor: ''
ms.assetid: d6fa21b8-d80a-41b7-80c1-ff1761bc68f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/03/2017
ms.author: rli; juliako;
ms.openlocfilehash: d287206d30c05289679c83636ba5f968a5fcbcbe
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="redact-faces-with-azure-media-analytics-walkthrough"></a>Redigovat tyto řezy v Průvodci Azure Media Analytics

## <a name="overview"></a>Přehled

**Azure Media Redactor** je [Azure Media Analytics](media-services-analytics-overview.md) procesor médií (PP), která nabízí redigování škálovatelné řez v cloudu. Vzhled redigování umožní vám upravit videa k rozostření řezy vybrané jednotlivce. Můžete použít službu redigování řez ve veřejné scénáře zabezpečení a média zprávy. Pár minut záznamů, která obsahuje více řezy může trvat hodiny redigovat ručně, ale s touto službou proces redigování řez bude vyžadovat několika jednoduchých kroků. Další informace najdete v tématu [to](https://azure.microsoft.com/blog/azure-media-redactor/) blogu.

Podrobnosti o **Azure Media Redactor**, najdete v článku [vzhled redigování přehled](media-services-face-redaction.md) tématu.

Toto téma ukazuje podrobné informace o tom, jak spustit workflow úplné redigování pomocí Azure Media Services Explorer (AMSE) a Azure Media Redactor vizualizér (nástroj s otevřeným zdrojem).

Další informace najdete v tématu [to](https://azure.microsoft.com/blog/redaction-preview-available-globally) blogu.

## <a name="azure-media-services-explorer-workflow"></a>Azure Media Services Explorer pracovního postupu

Nejjednodušší způsob, jak začít pracovat s Redactor je použít nástroj AMSE open source na githubu. Můžete spustit zjednodušené pracovního postupu prostřednictvím **kombinaci** režim, pokud nepotřebujete přístup k json poznámky nebo obrázků jpg řez.

### <a name="download-and-setup"></a>Stažení a instalaci

1. Stáhněte si nástroj AMSE z [zde](https://github.com/Azure/Azure-Media-Services-Explorer).
1. Přihlaste se k vašemu účtu Media Services pomocí klíč služby.

    Pokud chcete získat název účtu a informace o klíči, přejděte na [Azure Portal](https://portal.azure.com/) a vyberte svůj účet AMS. Zvolte Nastavení > klíče. Zobrazí se okno Správa klíčů, které ukazuje název účtu a primární a sekundární klíče. Zkopírujte hodnoty názvu účtu a primárního klíče.

![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough001.png)

### <a name="first-pass--analyze-mode"></a>První předání – analyzovat režimu

1. Nahrávání souborů médií prostřednictvím Asset –> nahrávání, nebo prostřednictvím přetažení. 
1. Klikněte pravým tlačítkem a zpracování souboru média pomocí Media Analytics –> Azure Media Redactor –> analyzovat režimu. 


![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough002.png)

![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough003.png)

Výstup bude obsahovat soubor json poznámky se data o umístění řez, jakož i jpg každý zjištěný řezu. 

![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough004.png)

### <a name="second-pass--redact-mode"></a>Druhý předat – redigovat režimu

1. Nahrát asset videa původní k výstupu z první fáze a nastavit jako primární asset. 

    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough005.png)

2. (Volitelné) Nahrajte soubor 'Dance_idlist.txt', který zahrnuje nový řádek oddělený seznam ID chcete redigovat. 

    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough006.png)

3. (Volitelné) Proveďte veškeré úpravy souboru annotations.json například zvýšení ohraničující hranice pole. 
4. Klikněte pravým tlačítkem na výstupní asset z první fáze, vyberte Redactor a spustit se **Redact** režimu. 

    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough007.png)

5. Stažení nebo sdílet konečné zredigované výstupní asset. 

    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough008.png)

## <a name="azure-media-redactor-visualizer-open-source-tool"></a>Nástroj Azure vizualizér Redactor médií s otevřeným zdrojem

Typu open source [vizualizér nástroj](https://github.com/Microsoft/azure-media-redactor-visualizer) pomáhá vývojářům právě spouští s formátem poznámky s analýzou a pomocí výstup.

Po naklonujte úložiště, aby bylo možné spustit projekt, budete muset stáhnout FFMPEG z jejich [oficiální web](https://ffmpeg.org/download.html).

Pokud jste vývojář pokusu o analýzu poznámky data JSON, podívejte se do Models.MetaData příklady ukázkový kód.

### <a name="set-up-the-tool"></a>Nastavení nástroje

1.  Stáhněte si a sestavte celé řešení. 

    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough009.png)

2.  Stáhněte si FFMPEG z [zde](https://ffmpeg.org/download.html). Tento projekt byl původně vytvořen s verze be1d324 (2016-10-04) s statické propojení. 
3.  Zkopírujte ffmpeg.exe a ffprobe.exe do stejné složky jako AzureMediaRedactor.exe výstup. 

    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough010.png)

4. Spusťte AzureMediaRedactor.exe. 

### <a name="use-the-tool"></a>Pomocí nástroje

1. Zpracovat videa v účtu Azure Media Services s Redactor MP na režimu analyzovat. 
2. Stáhněte si původní soubor videa a výstup redigování – analyzovat úlohy. 
3. Spusťte aplikaci vizualizér a vyberte soubory výše. 

    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough011.png)

4. Zobrazte náhled souboru. Vyberte řezy, které byste chtěli rozostření prostřednictvím na bočním panelu na pravé straně. 
    
    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough012.png)

5.  Písmo ID aktualizuje dolní textové pole. Vytvořte soubor s názvem "idlist.txt" v těchto ID jako seznam oddělený nový řádek. 

    >[!NOTE]
    > Idlist.txt má být uložen v ANSI. Poznámkový blok můžete uložit v ANSI.
    
6.  Tento soubor nahrajte na výstupní asset z kroku 1. Pro tento prostředek také nahrát původní video a nastavit jako primární asset. 
7.  Spustit úlohu redigování na tento prostředek s režimem "Redact" získat konečné redigovaný video. 

## <a name="next-steps"></a>Další postup 

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Související odkazy
[Azure Media Services Analytics – přehled](media-services-analytics-overview.md)

[Ukázky služby Azure Media Analytics](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

[Uvedení vzhled redigování pro Azure Media Analytics](https://azure.microsoft.com/blog/azure-media-redactor/)
