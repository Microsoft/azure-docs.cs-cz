---
title: Media Analytics na platformě Media Services | Microsoft Docs
description: Přehled verze public Preview Media Analytics, kolekce řečových a počítače služby vize na škálování enterprise, dodržování předpisů, zabezpečení a globální sítě
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: c56e3781-8510-4f7f-b5ff-a218c1bb6f4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2017
ms.author: milanga;juliako;johndeu
ms.openlocfilehash: 0ac8b9fad35267ceaec5b5acec4722b6005f68a9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788778"
---
# <a name="media-analytics-on-the-media-services-platform"></a>Media Analytics na platformě Media Services
## <a name="overview"></a>Přehled
Další organizace používají video jako upřednostňovaný střední cvičení svým zaměstnancům, zaujmout zákazníků a dokumentu podnikovým funkcím. Cloud computing poskytuje způsob, jak uložit, stream a přístup k tyto soubory médií velké. Ale s růstem společnosti knihovny obsahu videa, je nutné stejně účinný prostředek extrahování statistiky z obsahu. 

Chcete-li vyřešit tento rostoucí potřeby, Azure Media Services nabízí Azure Media Analytics. Media Analytics je kolekce řečových a vizuálních komponent, které organizacím a podnikům umožňují, aby ze svých videosouborů odvodily prakticky využitelné informace. Vytvořené pomocí platformy Media Services základní komponenty, Media Analytics může zpracovávat média zpracování škálované na jeden den.

Pomocí Media Analytics vývojáři rychle uveďte pokročilé funkce video do aplikace. Poskytne podnikových prostředích úplné škálování, dodržování předpisů, zabezpečení a globální reach potřebné ve velkých organizacích.

Následující diagram znázorňuje analýzy mediálních služeb a jiných hlavní části platformy Media Services. 

![Pracovní postup videa na vyžádání (VoD)](./media/media-services-analytics-overview/media-services-analytics-overview01.png)

Procesory médií z Media Analytics vytvářejí soubory MP4 nebo soubory JSON. Pokud procesor médií vytvoří soubor MP4, můžete progresivně stáhnout soubor. Pokud procesor médií vytvoří soubor JSON, můžete stáhnout soubor z Azure Blob storage. 

## <a name="media-analytics-services"></a>Služeb Media Analytics

### <a name="indexer"></a>Indexovací modul
S Azure Media Indexer můžete provést s možností vyhledávání obsahu a generovat titulky sleduje. Ve srovnání s předchozí verze, Azure Media Indexer 2 Preview má rychlejší indexování a širší jazykové podpory. Mezi podporované jazyky patří angličtina, španělština, francouzština, němčina, italština, čínština, portugalština a arabské. Podrobné informace a příklady naleznete v tématu [zpracování videí pomocí Azure Media Indexer 2](media-services-process-content-with-indexer2.md).
### <a name="hyperlapse"></a>Hyperlapse
Microsoft Hyperlapse kombinuje video ustálení a časové závislosti schopnost vytvořit rychlý, použití videa z dlouhých obsah webu. Kromě vytvoření časové závislosti video, můžete vytvořit stabilní videa z zobrazuje rozostřený videa zachytit přes mobilní telefony a videokamer Hyperlapse. Podrobné informace a příklady naleznete v tématu [Hyperlapse mediálních souborů pomocí Azure Media Hyperlapse](media-services-hyperlapse-content.md).
### <a name="motion-detector"></a>Detektor pohybu
Detektor pohybu můžete použít k detekci pohybu v video s stojící pozadí. To umožňuje kontrolovat falešně pozitivních na pohybu událostí detekovaných službou sledováním kamery. Podrobné informace a příklady naleznete v tématu [pro Azure Media Analytics detekce pohybu](media-services-motion-detection.md).
### <a name="face-detector"></a>Detektor tváří
Pomocí detektor vzhled, můžete zjistit řezy uživatelů a jejich emoce, včetně štěstí, sadness a neočekávaném. To má několik užitečné odvětví aplikace, popsané dál, včetně agregaci a analýzu reakce lidí, kteří se účastní událost. Podrobné informace a příklady naleznete v tématu [vzhled a rozpoznávání emocí úrovně zjišťování pro Azure Media Analytics](media-services-face-and-emotion-detection.md).
### <a name="video-summarization"></a>Videosouhrn
Videosouhrn vám pomůžou vytvořit souhrnných informací o dlouho videa automaticky výběrem zajímavé fragmenty kódu z zdroj videa. Tato možnost je užitečné, když chcete poskytovat rychlý přehled toho, co očekávat při dlouhé video. Podrobné informace a příklady naleznete v tématu [Video miniatur média pomocí Azure k vytvoření videosouhrn](media-services-video-summarization.md).
### <a name="optical-character-recognition"></a>Optické rozpoznávání znaků
S Azure Media rozpoznávání znaků (optické rozpoznávání znaků) můžete upravovat, vyhledávat digitální text převést textového obsahu v video soubory. Pak můžete automatizovat extrakce smysluplný metadata z video signál média.
### <a name="scalable-face-redaction"></a>Škálovatelné vzhled redigování
Azure Media Redactor je procesor Media Analytics médií, která nabízí redigování škálovatelné řez v cloudu. Pomocí redigování vzhled, můžete upravit videa na rozostření řezy vybrané jednotlivce. Můžete chtít používat službu redigování vzhled zprávy médiu nebo veřejné bezpečnosti je zahrnuta. Pár minut záznamů, která obsahuje více řezy může trvat hodiny redigovat ručně, ale s touto službou vzhled redigování trvá jenom pár jednoduchých kroků. Další informace najdete v tématu [redigovat řezy s Azure Media Analytics](media-services-face-redaction.md) článku.
### <a name="content-moderation"></a>Moderování obsahu
Azure obsahu moderátora umožňuje použít počítač s asistencí přerušování pro videa. Můžete například zjistit možné obsah pro dospělé a zájem v videa a zkontrolujte označení obsah týmy lidského přerušování. Ručně moderování videa pro nežádoucího obsahu je úloha je časově náročná a nákladná. Pomocí této služby a nástroje přidružené kontrolní kombinovat s asistencí počítač přerušování s možnostmi lidské v the smyčky nejlepších výsledků dosáhnete efektivní a cenově výhodnou. Další informace najdete v tématu [zpracujte videa s Azure obsahu moderátora](media-services-content-moderation.md) článku.

## <a name="common-scenarios"></a>Obvyklé scénáře
Media Analytics může pomoci organizacím a podnikům glean nové přehledy z video a další efektivně spravovat velké objemy obsahu videa. Tady je několik scénářů:

* **Volání centrech**. I s nástupem sociálních médií stále zákazníka telefonní centra usnadnění vysoké procento oddělení služeb zákazníkům transakce. V této zvuková data kódování je velké množství informace o zákazníkovi, který lze analyzovat k dosažení vyšší spokojenost zákazníků. Pomocí Media Indexer organizace rozbalte text a vytvářejte indexy vyhledávání a řídicí panely. Potom mohli extrahovat intelligence kolem častých stížností, zdroje stížností a další relevantní data.
* **Uživatelem generovaný obsah přerušování**. Z média zprávy výstupy k oddělení policie má řada organizací veřejné portály, které přijímají uživatelem generovaný média, například videa a obrázků. Z důvodu neočekávané události můžete špiček množstvím obsahu. V těchto scénářích je obtížné chování efektivní ruční recenze obsahu na základě vhodnosti. Zákazníci mohou spoléhají na službu přerušování obsah a zaměřit se na obsah, který je vhodný.
* **Sledováním**. S růstem používá kamery IP dodává rostoucí inventáře dohledu videa. Ruční kontrola sledováním video je čas náročné a náchylné k lidským chybám. Media Analytics poskytuje službám, jako je detekce pohybu, detekce vzhled a Hyperlapse proces projdete, Správa a vytváření odvozené konfigurace jednodušší.

## <a name="media-analytics-media-processors"></a>Procesory médií z Media Analytics
V této části jsou uvedeny procesory médií z Media Analytics a ukazuje, jak získat objekt média procesoru (PP) pomocí rozhraní .NET nebo REST.

### <a name="mp-names"></a>Názvy MP
* Azure Media Indexer 2 Preview
* Azure Media Indexer
* Azure Media Hyperlapse
* Azure Media Face Detector
* Azure Media Motion Detector
* Azure Media Video Thumbnails
* Azure Media OCR
* Azure Media obsahu moderátora

### <a name="net"></a>.NET
Následující funkce trvá některý zadaný název sady Management Pack a vrátí objekt sady Management Pack.

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
Žádost:

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
V tématu [ukázek Azure Media Analytics](http://azuremedialabs.azurewebsites.net/demos/Analytics.html).

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Související články
V tématu [Media Services Analytics oznámení](https://azure.microsoft.com/blog/introducing-azure-media-analytics/).

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png

## <a name="next-steps"></a>Další postup
Prohlédněte si mapy kurzů k Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
