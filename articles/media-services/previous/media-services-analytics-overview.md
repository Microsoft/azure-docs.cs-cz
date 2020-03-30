---
title: Media Analytics na platformě Mediálních služeb | Dokumenty společnosti Microsoft
description: Přehled veřejné verze Preview služby Media Analytics, kolekce služeb pro rozpoznávání řeči a počítačového vidění v podnikovém měřítku, dodržování předpisů, zabezpečení a globální dosah
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: c56e3781-8510-4f7f-b5ff-a218c1bb6f4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/13/2019
ms.author: juliako
ms.reviewer: milanga; johndeu
ms.openlocfilehash: ab1eba3de474d9ff985e62f491c24fa63be0fa63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77069621"
---
# <a name="media-analytics-on-the-media-services-platform"></a>Media Analytics na platformě Mediálních služeb 

## <a name="retirement-plans"></a>Penzijní plány

> [!IMPORTANT]
> Některé mediální procesory jsou vyřazeny. Data odchodu do důchodu a další informace naleznete v tématu [starších součástí.](legacy-components.md) 

## <a name="overview"></a>Přehled

Další organizace používají video jako preferované médium pro školení svých zaměstnanců, zapojení svých zákazníků a dokumentování obchodních funkcí. Cloud computing poskytuje způsob ukládání, streamování a přístupu k těmto velkým mediálním souborům. Ale jak roste firemní knihovna videoobsahu, potřebuje stejně účinný způsob, jak získat poznatky z obsahu. 

K řešení této rostoucí potřeby nabízí Azure Media Services Azure Media Analytics. Media Analytics je kolekce řečových a vizuálních komponent, které organizacím a podnikům umožňují, aby ze svých videosouborů odvodily prakticky využitelné informace. Media Analytics, vytvořená pomocí základních komponent platformy Media Services, dokáže zpracovávat zpracování médií ve velkém měřítku první den.

S aplikací Media Analytics mohou vývojáři rychle přenést pokročilé funkce videa do aplikací. Poskytuje podnikovým prostředím plné škálování, dodržování předpisů, zabezpečení a globální dosah vyžadované velkými organizacemi.

Následující diagram znázorňuje analýzu médií a další hlavní části platformy Mediálních služeb. 

![Pracovní postup videa na vyžádání (VoD)](./media/media-services-analytics-overview/media-services-analytics-overview01.png)

Procesory médií z Media Analytics vytvářejí soubory MP4 nebo soubory JSON. Pokud mediální procesor vytvoří soubor MP4, můžete jej postupně stáhnout. Pokud mediální procesor vytvoří soubor JSON, můžete soubor stáhnout z úložiště objektů Blob Azure. 

## <a name="media-analytics-services"></a>Služby Media Analytics

### <a name="indexer"></a>Indexer
Pomocí Azure Media Indexer můžete obsah prohledávat a generovat stopy s uzavřenými titulky. Podrobné informace a příklady najdete [v tématu Indexování mediálních souborů pomocí Azure Media Indexer](media-services-index-content.md).

### <a name="motion-detector"></a>Detektor pohybu
Detektor pohybu můžete použít k detekci pohybu ve videu se stacionárním pozadím. To umožňuje kontrolovat falešně pozitivní výsledky událostí pohybu zjištěných bezpečnostními kamerami. Podrobné informace a příklady najdete [v tématu Detekce pohybu pro Azure Media Analytics](media-services-motion-detection.md).

### <a name="face-detector"></a>Detektor tváří
Pomocí detektoru obličeje můžete detekovat tváře lidí a jejich emoce, včetně štěstí, smutku a překvapení. To má několik užitečných oborových aplikací, popsaných později, včetně agregace a analýzy reakcí lidí, kteří se účastní události. Podrobné informace a příklady najdete v tématu [Detekce tváří a emocí pro Azure Media Analytics](media-services-face-and-emotion-detection.md).

### <a name="video-summarization"></a>Shrnutí videa
Shrnutí videa vám pomůže vytvořit souhrny dlouhých videí automatickým výběrem zajímavých úryvků ze zdrojového videa. Tato možnost je užitečná, pokud chcete poskytnout rychlý přehled o tom, co můžete očekávat v dlouhém videu. Podrobné informace a příklady najdete [v tématu Použití miniatur videa médií Azure k vytvoření souhrnu videa](media-services-video-summarization.md).
### <a name="optical-character-recognition"></a>Optické rozpoznávání znaků
Pomocí Azure Media OCR (optické rozpoznávání znaků) můžete převést textový obsah v souborech videa na upravitelný digitální text, který lze prohledávat. Potom můžete automatizovat extrakci smysluplných metadat z video signálu média.
### <a name="scalable-face-redaction"></a>Škálovatelná redigování plochy
Azure Media Redactor je mediální procesor Media Analytics, který nabízí škálovatelnou redigování tváří v cloudu. Pomocí redigování obličeje můžete upravit video tak, aby rozmazalo tváře vybraných jedinců. Možná budete chtít použít službu redigování obličeje ve sdělovacích prostředcích nebo v případě, že se jedná o veřejnou bezpečnost. Několik minut záznamu, který obsahuje více tváří, může trvat hodiny, než se rediguje ručně, ale s touto službou trvá redigování obličeje jen několika jednoduchými kroky. Další informace najdete v článku [Redact faces with Azure Media Analytics.](media-services-face-redaction.md)

### <a name="content-moderation"></a>Moderování obsahu
Azure Content Moderator umožňuje pro vaše videa používat usitovanou moderování pomocí počítače. Ve videích například můžete chtít detekovat potenciálně nevhodný obsah nebo obsah pro dospělé a označený obsah nechat zkontrolovat týmy lidského moderování. Ruční moderování videí pro nežádoucí obsah je časově náročný a nákladný úkol. S touto službou a přidruženými nástroji pro kontrolu kombinujete moderování s pomocí strojů s funkcemi human-in-the-loop pro dosažení nejlepších výsledků efektivně a nákladově efektivním způsobem. Další informace najdete v článku Zpracování videí pomocí azure [content moderatoru.](media-services-content-moderation.md)

## <a name="common-scenarios"></a>Obvyklé scénáře
Služba Media Analytics může organizacím a podnikům pomoci získat nové poznatky z videa a efektivněji spravovat velké objemy videoobsahu. Zde je několik scénářů:

* **Call centra**. Dokonce i s příchodem sociálních médií zákaznická call centra stále usnadňují velké procento transakcí se zákaznickými službami. V těchto zvukových datech je zakódováno velké množství informací o zákaznících, které lze analyzovat, aby se dosáhlo vyšší spokojenosti zákazníků. Pomocí media Indexer, organizace mohou extrahovat text a vytvářet indexy vyhledávání a řídicí panely. Pak mohou získat informace o běžných stížnostech, zdrojích stížností a dalších relevantních údajích.
* **Moderování obsahu generovaného uživatelem**. Od sdělovacích prostředků až po policejní oddělení, mnoho organizací má veřejné portály, které přijímají uživatelsky generovaná média, jako jsou videa a obrázky. Objem obsahu může zvýšit v důsledku neočekávaných událostí. V těchto scénářích je obtížné provádět účinné ruční kontroly obsahu pro vhodnost. Zákazníci se mohou spolehnout na službu moderování obsahu, aby se zaměřili na vhodný obsah.

## <a name="media-analytics-media-processors"></a>Mediální procesory Media Analytics
V této části jsou uvedeny mediální procesory Media Analytics a ukazuje, jak pomocí rozhraní .NET nebo REST získat objekt mediálního procesoru (MP).

### <a name="mp-names"></a>Názvy MP

* Azure Media Indexer
* Azure Media Face Detector
* Azure Media Motion Detector
* Azure Media Video Thumbnails
* Azure Media OCR
* Moderátor mediálního obsahu Azure

### <a name="net"></a>.NET
Následující funkce přebírá jeden ze zadaných názvů MP a vrátí objekt MP.

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
            .Where(p => p.Name == mediaProcessorName)
            .ToList()
            .OrderBy(p => new Version(p.Version))
            .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }


### <a name="rest"></a>REST
Požadavek:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20OCR' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.19
    Host: media.windows.net

Odpověď:

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:074c3899-d9fb-448f-9ae1-4ebcbe633056",
             "Description":"Azure Media OCR",
             "Name":"Azure Media OCR",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

## <a name="demos"></a>Dema
Podívejte se [na ukázky Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html).

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Související články
Viz [Oznámení analýzy mediálních služeb](https://azure.microsoft.com/blog/introducing-azure-media-analytics/).

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png

## <a name="next-steps"></a>Další kroky
Prohlédněte si mapy kurzů k Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
