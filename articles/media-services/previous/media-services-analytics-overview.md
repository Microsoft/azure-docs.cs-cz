---
title: Media Analytics na Media Services platformě | Microsoft Docs
description: Přehled veřejné verze Preview Media Analytics, kolekce služeb pro rozpoznávání řeči a počítačové vidění na úrovni podniku, dodržování předpisů, zabezpečení a globálním dosahu
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
ms.openlocfilehash: 66edbc872fe5f1d4320107aa21ed2792bdd4fecc
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083890"
---
# <a name="media-analytics-on-the-media-services-platform"></a>Media Analytics na Media Services platformě 

## <a name="retirement-plans"></a>Plány vyřazení

> [!IMPORTANT]
> Následující starší verze mediálních procesorů se budou vyřadit v 2020. Další informace najdete v následující tabulce. 

|Název procesoru médií|Datum vyřazení|Další poznámky|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)| Od 1. ledna 2020|Tento procesor médií bude nahrazen [Azure Media Services video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Další informace najdete v tématu [migrace z Azure Media Indexer 2 do Azure Media Services video indexer](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|1\. října z 2020|Tento procesor médií bude nahrazen [Azure Media Services video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Další informace najdete v tématu [migrace z Azure Media Indexer do Azure Media Services video indexer](migrate-indexer-v1-v2.md)
 
## <a name="overview"></a>Přehled

Víc organizací používá video jako preferované médium ke školení svých zaměstnanců, zapojení svých zákazníků a dokumentaci obchodních funkcí. Cloud Computing poskytuje způsob, jak ukládat, streamovat a přistupovat k těmto velkým mediálním souborům. Ale podniková knihovna obsahu videa roste, potřebuje stejně efektivní způsob extrakce přehledů z obsahu. 

Pro vyřešení tohoto rostoucího potřeb Azure Media Services nabídky Azure Media Analytics. Media Analytics je kolekce řečových a vizuálních komponent, které organizacím a podnikům umožňují, aby ze svých videosouborů odvodily prakticky využitelné informace. V rámci sestavování s využitím základních Media Services komponent platformy mohou Media Analytics zpracovávat zpracování médií ve velkém měřítku na jednom dni.

Díky Media Analytics můžou vývojáři rychle využít pokročilé funkce videa do aplikací. Poskytuje podniková prostředí s úplným škálováním, dodržováním předpisů, zabezpečením a globálním dosahem vyžadovaným velkými organizacemi.

Následující diagram ukazuje Media Analytics a další hlavní části Media Services platformy. 

![Pracovní postup videa na vyžádání (VoD)](./media/media-services-analytics-overview/media-services-analytics-overview01.png)

Procesory médií z Media Analytics vytvářejí soubory MP4 nebo soubory JSON. Pokud procesor médií vytvoří soubor MP4, můžete ho postupně stáhnout. Pokud procesor médií vytvoří soubor JSON, můžete ho stáhnout z úložiště objektů BLOB v Azure. 

## <a name="media-analytics-services"></a>Služby Media Analytics

### <a name="indexer"></a>Indexovací modul
Pomocí Azure Media Indexer můžete provádět hledání obsahu a generovat stopy s titulky. Ve srovnání s předchozí verzí Azure Media Indexer 2 Preview rychlejší indexování a širší podporu jazyků. Mezi podporované jazyky patří angličtina, španělština, francouzština, němčina, italština, čínština, portugalština a arabština. Podrobné informace a příklady najdete v tématu [zpracování videí pomocí Azure Media Indexer 2](media-services-process-content-with-indexer2.md).
### <a name="motion-detector"></a>Detektor pohybu
Pomocí snímače pohybu můžete detekovat pohyb videa pomocí stacionárních pozadí. Díky tomu je možné kontrolovat falešně pozitivní události na událostech pohybu zjištěných sledovacími kamerami. Podrobné informace a příklady najdete v tématu [detekce pohybu pro Azure Media Analytics](media-services-motion-detection.md).
### <a name="face-detector"></a>Detektor tváří
Pomocí rozpoznávání tváře můžete detekovat plošky uživatelů a jejich emocey, včetně štěstí, smutek a neočekávaně. Má několik užitečných průmyslových aplikací, které jsou popsány později, včetně agregace a analýzy reakcí lidí, kteří se účastní události. Podrobné informace a příklady naleznete v tématu [detekce obličeje a emoce pro Azure Media Analytics](media-services-face-and-emotion-detection.md).
### <a name="video-summarization"></a>Shrnutí videa
Shrnutí videa vám může přispět k vytváření souhrnů dlouhých videí tím, že automaticky vybere zajímavé fragmenty ze zdrojového videa. Tato možnost je užitečná, když chcete poskytnout rychlý přehled o tom, co se má na dlouhém videu očekávat. Podrobné informace a příklady najdete v tématu [použití Azure Media Video Thumbnails k vytvoření shrnutí videa](media-services-video-summarization.md).
### <a name="optical-character-recognition"></a>Optické rozpoznávání znaků
Pomocí Azure Media OCR (optické rozpoznávání znaků) můžete převést textový obsah v videosouborech na upravitelný a prohledávatelný digitální text. Pak můžete automatizovat extrakci smysluplných metadat z obrazového signálu vašeho média.
### <a name="scalable-face-redaction"></a>Škálovatelné redigování obličeje
Azure Media Redactor je Media Analytics multimediální procesor, který nabízí škálovatelné redigování tváře v cloudu. Když použijete redigování, můžete upravit video a rozostřit plochy vybraných jednotlivců. Můžete chtít použít službu redigování obličeje na nosiči oznámení nebo v případě, že se jedná o veřejné zabezpečení. Několik minut záběrů, které obsahují více plošek, může trvat hodiny až při redigování ručně, ale s touto službou vynásobení redigování čelí pouze několika jednoduchým krokům. Další informace najdete v článku o [aplikaci Azure Media Analytics redigování](media-services-face-redaction.md) .
### <a name="content-moderation"></a>Moderování obsahu
Azure Content Moderator umožňuje používat pro vaše videa moderování s asistencí počítače. Ve videích například můžete chtít detekovat potenciálně nevhodný obsah nebo obsah pro dospělé a označený obsah nechat zkontrolovat týmy lidského moderování. Ruční moderování videí pro nežádoucí obsah je časově náročná a náročná úloha. Díky této službě a přidruženým nástrojům pro kontrolu se kombinuje moderování počítačů s funkcemi, které jsou v rámci smyčky, díky funkcím, které umožňují efektivně a efektivně úspornou efektivitu. Další informace najdete v článku [zpracování videí pomocí Azure Content moderator](media-services-content-moderation.md) .

## <a name="common-scenarios"></a>Obvyklé scénáře
Media Analytics mohou organizacím a podnikům pomáhat glean nové poznatky z videa a efektivněji spravovat velké objemy obsahu videa. Tady je několik scénářů:

* **Centra volání**. I když se nástupem na sociálních médiích, vaše zákaznická centra volání stále usnadňují vysoké procento transakcí služeb zákazníkům. Kódovaný v těchto zvukových datech je velké množství informací o zákaznících, které je možné analyzovat za účelem dosažení vyšší spokojenosti zákazníků. Pomocí Media Indexer mohou organizace extrahovat text a sestavit indexy a řídicí panely hledání. Pak můžou extrahovat inteligentní stížnosti, zdroje stížností a další relevantní data.
* **Moderování obsahu generovaného uživatelem** Z médií z novinek do policejních oddělení má mnoho organizací veřejné portály, které přijímají uživatelem generovaná média, jako jsou videa a obrázky. Objem obsahu může prošpička kvůli neočekávaným událostem. V těchto scénářích je obtížné provést efektivní ruční přezkoumání obsahu pro zajištění vhodnosti. Zákazníci se můžou spolehnout na službu Moderování obsahu a soustředit se na obsah, který je vhodný.

## <a name="media-analytics-media-processors"></a>Procesory Media Analytics Media
Tato část obsahuje seznam Media Analyticsch procesorů médií a ukazuje, jak použít rozhraní .NET nebo REST k získání objektu procesoru médií (MP).

### <a name="mp-names"></a>Názvy MP
* Azure Media Indexer 2 Preview
* Azure Media Indexer
* Azure Media Face Detector
* Azure Media Motion Detector
* Azure Media Video Thumbnails
* Azure Media OCR
* Azure Media Content Moderator

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
    x-ms-version: 2.12
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

## <a name="demos"></a>Ukázky
Viz [ukázky Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html).

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Související články
Viz téma [Media Services Analytics – oznámení](https://azure.microsoft.com/blog/introducing-azure-media-analytics/).

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png

## <a name="next-steps"></a>Další kroky
Prohlédněte si mapy kurzů k Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
