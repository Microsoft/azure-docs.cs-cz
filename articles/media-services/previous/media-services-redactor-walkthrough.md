---
title: Seredigování obličeje pomocí Azure Media Analyticsho návodu | Microsoft Docs
description: V tomto tématu se dozvíte, jak spustit plný pracovní postup redigování pomocí Azure Media Services Exploreru (AMSE) a Azure Media Redactor Vizualizér (Open Source Tool).
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
ms.openlocfilehash: 5fdec829ceeefce2426a5fd08b4245e66bd0a08c
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016667"
---
# <a name="redact-faces-with-azure-media-analytics-walkthrough"></a>Azure Media Analytics návodu k redigování

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Přehled

**Azure Media redactor** je [Azure Media Analytics](./legacy-components.md) multimediální procesor (MP), který nabízí škálovatelné redigování tváře v cloudu. Naredigování obličeje umožňuje upravit vaše video, aby se rozostří plošky vybraných jednotlivců. Je možné, že budete chtít použít službu redigování obličeje ve scénářích veřejného zabezpečení a média pro příspěvky. Několik minut záběrů, které obsahují více plošek, může trvat hodiny na redigování ručně, ale v rámci této služby bude proces redigování obličeje vyžadovat jenom několik jednoduchých kroků. Další informace najdete v [tomto](https://azure.microsoft.com/blog/azure-media-redactor/) blogu.

Podrobnosti o  **Azure Media redactor**najdete v tématu [Přehled redigování obličeje](media-services-face-redaction.md) .

V tomto tématu se dozvíte, jak spustit plný pracovní postup redigování pomocí Azure Media Services Exploreru (AMSE) a Azure Media Redactor Vizualizér (Open Source Tool).

Další informace najdete v [tomto](https://azure.microsoft.com/blog/redaction-preview-available-globally) blogu.

## <a name="azure-media-services-explorer-workflow"></a>Pracovní postup Průzkumníka Azure Media Services

Nejjednodušší způsob, jak začít používat redigování, je použití nástroje Open Source AMSE na GitHubu. Zjednodušený pracovní postup můžete spustit pomocí **kombinovaného** režimu, pokud nepotřebujete přístup k obrázkům JSON poznámky nebo obličeje.

### <a name="download-and-setup"></a>Stažení a nastavení

1. Stáhněte si nástroj AMSE for AMS v2 z [tohoto místa](https://aka.ms/amseforv2).
1. Přihlaste se ke svému účtu Media Services pomocí klíče služby.

    Pokud chcete získat název účtu a informace o klíči, přejděte na [Azure Portal](https://portal.azure.com/) a vyberte svůj účet AMS. Pak vyberte nastavení > klíče. Zobrazí se okno Správa klíčů, které ukazuje název účtu a primární a sekundární klíče. Zkopírujte hodnoty názvu účtu a primárního klíče.

![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough001.png)

### <a name="first-pass--analyze-mode"></a>První Pass – režim analýzy

1. Nahrání souboru médií prostřednictvím Assetu – > nahrávání nebo přetažení. 
1. Klikněte pravým tlačítkem a zpracujte soubor média pomocí Media Analytics – > Azure Media Redactor – > režimu analýzy. 


![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough002.png)

![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough003.png)

Výstup bude obsahovat soubor JSON poznámky s daty o poloze obličeje a také jpg každého zjištěného obličeje. 

![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough004.png)

### <a name="second-pass--redact-mode"></a>Druhý průchod – režim redigování

1. Nahrajte původní video na výstup z první fáze a nastavte ho jako primární Asset. 

    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough005.png)

2. Volitelné Nahrajte soubor Dance_idlist.txt, který obsahuje seznam identifikátorů ID, které chcete vyhodnotit, na nový řádek. 

    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough006.png)

3. Volitelné Proveďte úpravy annotations.jsv souboru, například zvětšení hranic ohraničovacího rámečku. 
4. Klikněte pravým tlačítkem myši na výstupní prostředek od prvního průchodu, vyberte redigování a spusťte s režimem **redigování** . 

    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough007.png)

5. Stáhněte nebo sdílejte finální prostředek redigování výstupu. 

    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough008.png)

## <a name="azure-media-redactor-visualizer-open-source-tool"></a>Open source nástroj Vizualizér Azure Media Redactor

Nástroj open source [Vizualizér](https://github.com/Microsoft/azure-media-redactor-visualizer) je navržený tak, aby usnadnil vývojářům jednoduše začít s analýzou a používáním výstupu pomocí formátu poznámek.

Po klonování úložiště bude nutné stáhnout FFMPEG ze své [oficiální lokality](https://ffmpeg.org/download.html), aby bylo možné projekt spustit.

Pokud jste vývojář, který se pokouší analyzovat data anotace JSON, podívejte se do části modely. MetaData pro ukázkové příklady kódu.

### <a name="set-up-the-tool"></a>Nastavení nástroje

1.  Stáhněte si celé řešení a sestavte ho. 

    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough009.png)

2.  Stáhněte [si ffmpeg odsud.](https://ffmpeg.org/download.html) Tento projekt byl původně vyvinut s verzí be1d324 (2016-10-04) se statickým propojením. 
3.  Zkopírujte ffmpeg.exe a ffprobe.exe do stejné výstupní složky jako AzureMediaRedactor.exe. 

    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough010.png)

4. Spusťte AzureMediaRedactor.exe. 

### <a name="use-the-tool"></a>Použití nástroje

1. Zpracujte své video v účtu Azure Media Services pomocí nástroje pro naZobrazení redigování v režimu analýzy. 
2. Stáhněte si původní videosoubor i výstup úlohy redigování-Analyze. 
3. Spusťte aplikaci Vizualizér a vyberte soubory výše. 

    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough011.png)

4. Zobrazte náhled souboru. Vyberte, které plošky chcete Rozostřit přes postranní panel na pravé straně. 
    
    ![Rozmazání obličejů](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough012.png)

5.  Dolní textové pole se aktualizuje s ID tváře. Vytvoří soubor s názvem "idlist.txt" s těmito ID jako seznam s odděleným novým řádkem. 

    >[!NOTE]
    > idlist.txt by měl být uložen ve standardu ANSI. Pomocí poznámkového bloku můžete ušetřit v ANSI.
    
6.  Nahrajte tento soubor do výstupního prostředku z kroku 1. Nahrajte také původní video do tohoto assetu a nastavte ho jako primární Asset. 
7.  Spusťte úlohu redigování na tomto assetu pomocí režimu "redigování", abyste získali konečné navýšení videa. 

## <a name="next-steps"></a>Další kroky 

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Související odkazy
[Přehled analýzy Azure Media Services](./legacy-components.md)

[Ukázky Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

[Oznamujeme, že se redigování obličeje pro Azure Media Analytics](https://azure.microsoft.com/blog/azure-media-redactor/)