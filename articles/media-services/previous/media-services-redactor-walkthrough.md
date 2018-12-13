---
title: Zákona o svobodném přístupu tváře s názorným postupem Azure Media Analytics | Dokumentace Microsoftu
description: Toto téma ukazuje podrobné pokyny o tom, jak spouštět úplné redigování pracovního postupu pomocí Azure Media Services Explorer (AMSE) a Azure Media Redactor Visualizer (open source nástroj).
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
ms.openlocfilehash: 81618446930a23a7ea713da19bb7c63a06d135ed
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53162429"
---
# <a name="redact-faces-with-azure-media-analytics-walkthrough"></a>Zákona o svobodném přístupu tváře s názorným postupem Azure Media Analytics

## <a name="overview"></a>Přehled

**Azure Media Redactor** je [Azure Media Analytics](media-services-analytics-overview.md) procesor médií (PP), která nabízí škálovatelné face redactoru v cloudu. Rozmazání obličejů umožňuje upravovat vaše video, aby bylo možné rozostření tváří vybraných jedinců. Můžete chtít použít službu rozmazání tváří v veřejného scénáře a sdělovací. Pár minut záběrů, který obsahuje více ploch může trvat hodiny zákona o svobodném přístupu ručně, ale s touto službou procesu rozmazání tváří vyžaduje jenom pár jednoduchých kroků. Další informace najdete v tématu [to](https://azure.microsoft.com/blog/azure-media-redactor/) blogu.

Podrobnosti o **Azure Media Redactor**, najdete v článku [přehled rozmazání tváří](media-services-face-redaction.md) tématu.

Toto téma ukazuje podrobné pokyny o tom, jak spouštět úplné redigování pracovního postupu pomocí Azure Media Services Explorer (AMSE) a Azure Media Redactor Visualizer (open source nástroj).

Další informace najdete v tématu [to](https://azure.microsoft.com/blog/redaction-preview-available-globally) blogu.

## <a name="azure-media-services-explorer-workflow"></a>Azure Media Services Explorer pracovního postupu

Nejjednodušší způsob, jak začít pracovat s Redactor se má používat open source nástroj AMSE na Githubu. Můžete spustit zjednodušený pracovní postup prostřednictvím **kombinovat** režim, pokud už nepotřebujete přístup k json poznámky nebo jpg obrázků pro rozpoznávání tváře.

### <a name="download-and-setup"></a>Stažení a instalace

1. Stáhněte si nástroj AMSE z [tady](https://github.com/Azure/Azure-Media-Services-Explorer).
1. Přihlaste se k účtu Media Services pomocí klíče služby.

    Pokud chcete získat název účtu a informace o klíči, přejděte na [Azure Portal](https://portal.azure.com/) a vyberte svůj účet AMS. Vyberte nastavení > klíče. Zobrazí se okno Správa klíčů, které ukazuje název účtu a primární a sekundární klíče. Zkopírujte hodnoty názvu účtu a primárního klíče.

![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough001.png)

### <a name="first-pass--analyze-mode"></a>První předání – analýza režimu

1. Nahrávání souborů médií prostřednictvím Asset –> nahrávat, nebo pomocí přetažení. 
1. Klikněte pravým tlačítkem myši a zpracování souboru média pomocí Media Analytics –> Azure Media Redactor –> analyzovat režimu. 


![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough002.png)

![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough003.png)

Výstup bude obsahovat soubor json poznámky s daty umístění pro rozpoznávání tváře, jakož i jpg pro každou zjištěnou plochu. 

![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough004.png)

### <a name="second-pass--redact-mode"></a>Za druhé předat – zákona o svobodném přístupu režimu

1. Nahrát asset videa původní do výstupu z první fáze a nastavit jako primární asset. 

    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough005.png)

2. (Volitelné) Odešlete soubor "Dance_idlist.txt", která zahrnuje nového řádku oddělený seznam identifikátorů chcete zákona o svobodném přístupu. 

    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough006.png)

3. (Volitelné) Proveďte veškeré úpravy k souboru annotations.json například zvýšení ohraničující hranice pole. 
4. Klikněte pravým tlačítkem myši klikněte na výstupní asset z první fáze, vyberte Redactor a spustit s **Redact** režimu. 

    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough007.png)

5. Stáhnout nebo sdílet konečné zrevidovaně výstupního prostředku. 

    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough008.png)

## <a name="azure-media-redactor-visualizer-open-source-tool"></a>Azure Media Redactor Vizualizéru open source nástroj

Open source [nástroj vizualizátor](https://github.com/Microsoft/azure-media-redactor-visualizer) je navržené pro pomoc vývojářům jenom od verze formátu poznámky se analýza kódu a pomocí výstupu.

Po naklonování úložiště, chcete-li spustit projekt, budete muset stáhnout aplikaci FFMPEG z jejich [oficiální web](https://ffmpeg.org/download.html).

Pokud jste vývojář pokusu o analýzu dat JSON poznámky, vyhledejte uvnitř Models.MetaData příklady kódu pro vzorku.

### <a name="set-up-the-tool"></a>Nastavení nástroje

1.  Stáhněte si a sestavte celé řešení. 

    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough009.png)

2.  Stáhněte si aplikaci FFMPEG z [tady](https://ffmpeg.org/download.html). Tento projekt, byla původně vyvinuta be1d324 verze (2016-10-04) s statické propojování. 
3.  Zkopírujte ffmpeg.exe a ffprobe.exe do stejné složky jako AzureMediaRedactor.exe výstup. 

    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough010.png)

4. Spusťte AzureMediaRedactor.exe. 

### <a name="use-the-tool"></a>Pomocí nástroje

1. Zpracování videa ve vašem účtu Azure Media Services s redactor ve verzi sady Management Pack režim analyzovat. 
2. Stažení původního videa souboru a výstup redigování – analýza úlohy. 
3. Spusťte aplikaci vizualizéru a vyberte výše uvedené soubory. 

    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough011.png)

4. Zobrazit náhled souboru. Vyberte tváří, které byste chtěli rozostření prostřednictvím na bočním panelu na pravé straně. 
    
    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough012.png)

5.  Dolní části textového pole se aktualizuje face ID. Vytvořte soubor s názvem "idlist.txt" se tyto identifikátory jako seznam oddělený znaku nového řádku. 

    >[!NOTE]
    > Idlist.txt uložit v ANSI. Můžete použít Poznámkový blok uložte v ANSI.
    
6.  Tento soubor nahrajte do prostředku výstupu z kroku 1. Nahrání původního videa do tohoto prostředku i a nastavit jako primární asset. 
7.  Spustit úlohu redigování na tento prostředek s režimem "Redact" získat poslední redigovaný videa. 

## <a name="next-steps"></a>Další postup 

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Související odkazy
[Azure Media Services Analytics – přehled](media-services-analytics-overview.md)

[Ukázky Azure Media Analytics](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

[Oznamujeme vydání rozmazání Obličejů pro Azure Media Analytics](https://azure.microsoft.com/blog/azure-media-redactor/)
