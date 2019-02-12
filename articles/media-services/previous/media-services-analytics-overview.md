---
title: Analýzy mediálních služeb na platformě Media Services | Dokumentace Microsoftu
description: Přehled verze public preview služby Media Analytics, kolekce pro zpracování řeči a počítače služby pro zpracování obrazu v podnikovém měřítku, dodržování předpisů, zabezpečením a globálním dosahem
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
ms.date: 02/08/2019
ms.author: milanga;juliako;johndeu
ms.openlocfilehash: c60fd90adda4c362b15fe2e324aa55a581c9e59a
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56003760"
---
# <a name="media-analytics-on-the-media-services-platform"></a>Analýzy mediálních služeb na platformě Media Services 

## <a name="overview"></a>Přehled
Další organizace využívají videa jako střední preferované trénování svým zaměstnancům zapojit zákazníky a dokumentu obchodní funkce. Cloud computingu, poskytuje způsob, jak ukládat, streamování a přístup k těmto souborům velké média. Ale s růstem vaší společnosti knihovny videoobsahu, které potřebuje stejně efektivní způsob extrahování přehledů z obsahu. 

Tyto rostoucí potřeby řeší Azure Media Services nabízí službu Azure Media Analytics. Media Analytics je kolekce řečových a vizuálních komponent, které organizacím a podnikům umožňují, aby ze svých videosouborů odvodily prakticky využitelné informace. Vytvořené s použitím základní součásti platformy Media Services, Media Analytics dokáže zpracovat ve velkém měřítku na začátku zpracování médií.

Pomocí Media Analytics vývojáři rychle přenést pokročilé funkce videa do aplikací. Podniková prostředí umožňuje plném rozsahu, dodržování předpisů, zabezpečení a globální dosah vyžadují velké organizace.

Následující diagram znázorňuje analýzy mediálních služeb a další hlavní části platformy Media Services. 

![Pracovní postup videa na vyžádání (VoD)](./media/media-services-analytics-overview/media-services-analytics-overview01.png)

Procesory médií z Media Analytics vytvářejí soubory MP4 nebo soubory JSON. Pokud procesor médií vytvoří soubor MP4, můžete ho progresivně stahovat. Pokud procesor médií vytvoří soubor JSON, si můžete stáhnout soubor z úložiště objektů Blob v Azure. 

## <a name="media-analytics-services"></a>Media Analytics services

### <a name="indexer"></a>Indexovací modul
S Azure Media Indexer měli prohledávatelný obsah a generovat stopy titulků. Ve srovnání s předchozí verzí, má Azure Media Indexer 2 Preview language rychleji indexování a širší podporu. Mezi podporované jazyky patří angličtina, španělština, francouzština, němčina, italština, čínština, portugalské a arabština. Podrobné informace a příklady najdete v tématu [zpracování videí pomocí Azure Media Indexer 2](media-services-process-content-with-indexer2.md).
### <a name="hyperlapse"></a>Hyperlapse
Microsoft Hyperlapse kombinuje stabilizaci videa a časové závislosti schopnost vytvářet rychlá, použitelné videa ze svého obsahu dlouhá. Kromě vytvoření časové závislosti videa, můžete vytvořit stabilní videa z roztřesených videí zachycené přes mobilní telefony a videokamer Hyperlapse. Podrobné informace a příklady najdete v tématu [Hyperlapse mediálních souborů pomocí Azure Media Hyperlapse](media-services-hyperlapse-content.md).
### <a name="motion-detector"></a>Detektor pohybu
Detektor pohybu můžete použít k detekci pohybu ve videu s nehybným. To umožňuje zkontrolovat počet falešně pozitivních výsledků v pohybu událostí detekovaných službou bezpečnostních kamer. Podrobné informace a příklady najdete v tématu [pro Azure Media Analytics detekce pohybu](media-services-motion-detection.md).
### <a name="face-detector"></a>Detektor tváří
Pomocí Face Detectoru může zjistit tváří lidí a jejich emocí, včetně štěstí, smutek a překvapení. To má několik užitečných oboru aplikace, je popsáno dále, včetně agregaci a analýzu reakcí s palcem lidí účast na akci v události. Podrobné informace a příklady najdete v tématu [rozpoznávání tváří a emocí pro Azure Media Analytics](media-services-face-and-emotion-detection.md).
### <a name="video-summarization"></a>Souhrn videa
Souhrn videa můžete vytvořit souhrny z dlouhých videí automaticky výběrem zajímavé fragmenty kódu ze zdrojového videa. Tato možnost je užitečná, pokud byste chtěli poskytnout rychlý přehled toho, co očekávat při dlouhé videa. Podrobné informace a příklady najdete v tématu [Video Thumbnails použití Azure Media k vytvoření videosouhrn](media-services-video-summarization.md).
### <a name="optical-character-recognition"></a>Optické rozpoznávání znaků
S Azure Media OCR (optické rozpoznávání znaků) můžete převést textový obsah v videosoubory lze upravovat, prohledávatelné digitální textu. Pak můžete automatizovat extrakce smysluplná metadata z videa signál médií.
### <a name="scalable-face-redaction"></a>Škálovatelné face redactoru
Azure Media Redactor je procesor multimédií Media Analytics, která nabízí škálovatelné face redactoru v cloudu. Pomocí face redactoru můžete upravovat vaše video k rozostření tváří vybraných jedinců. Můžete chtít použít službu rozmazání tváří v sdělovacích nebo pokud se jedná o veřejný pořádek. Pár minut záběrů, který obsahuje více ploch může trvat hodiny zákona o svobodném přístupu ručně, ale s touto službou rozmazání obličejů trvá jenom pár jednoduchých kroků. Další informace najdete v tématu [zákona o svobodném přístupu tváří pomocí Azure Media Analytics](media-services-face-redaction.md) článku.
### <a name="content-moderation"></a>Moderování obsahu
Azure Content Moderator umožňuje používat moderování s podporou počítače pro svá videa. Můžete například zjistit možné obsahu pro dospělé nebo pikantního obsahu ve videích a zkontrolovat obsah s příznakem vaše týmy lidské moderování. Ručně moderování videa pro nežádoucí obsah je nákladné a časově náročné úlohy. Pomocí této služby a nástroje pro recenze přidružené kombinovat moderování s podporou počítače s funkcemi lidských v the smyčky pro dosažení co nejlepších výsledků efektivně a s nízkými náklady. Další informace najdete v tématu [zpracování videa s využitím Azure Content Moderator](media-services-content-moderation.md) článku.

## <a name="common-scenarios"></a>Obvyklé scénáře
Media Analytics může pomoct organizacím a podnikům glean nové poznatky z videa a informace efektivně spravovat velké objemy obsahu videa. Tady je několik scénářů:

* **Volání centra**. I s nástupem sociálních médií stále zákazníka volání Centra usnadnění vysoké procento transakce služby zákazníkům. Kódování v této zvuková data je velké množství informací o zákaznících, které můžou být analyzována s cílem dosáhnout vyšší spokojenost zákazníků. Pomocí Media Indexer organizace můžete extrahovat text a vytváření vyhledávacích indexů a řídicí panely. Potom extrahovat intelligence kolem častých stížností zdroje stížností a další relevantní data.
* **Moderování obsahu uživatelem generovaný**. Z sdělovacích výstupy pro oddělení policejních jednotek řada organizací má veřejnou portály, které přijímají uživatelem generovaný médií, jako jsou videa a obrázků. Množství obsahu může vystoupat z důvodu neočekávané události. V těchto scénářích je obtížné provádět efektivní ruční kontroly obsahu pro vhodnost. Zákazníci můžou využívají službu moderování obsahu a zaměřte se na obsah, který je vhodný.
* **Sledování**. S nárůstem v použití kamer IP obsahuje rostoucí seznam sledování videa. Ruční kontrolu dozoru video je čas náročné a náchylné k lidským chybám. Media Analytics poskytuje služby, jako je detekce pohybu, rozpoznávání tváří a Hyperlapse pro proces kontroly, Správa a vytváření odvozené jednodušší.

## <a name="media-analytics-media-processors"></a>Analýzy mediálních služeb procesory médií
Tato část uvádí procesory médií Media Analytics a ukazuje, jak získat objekt procesoru (PP) médií pomocí .NET nebo REST.

### <a name="mp-names"></a>Názvy sady Management Pack
* Azure Media Indexer 2 Preview
* Azure Media Indexer
* Azure Media Hyperlapse
* Azure Media Face Detector
* Azure Media Motion Detector
* Azure Media Video Thumbnails
* Azure Media OCR
* Azure Media Content Moderator

### <a name="net"></a>.NET
Následující funkce vezme jeden z názvů zadané sady Management Pack a vrátí objekt sady Management Pack.

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
Zobrazit [ukázek Azure Media Analytics](http://azuremedialabs.azurewebsites.net/demos/Analytics.html).

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Související články
Zobrazit [Media Services Analytics oznámení](https://azure.microsoft.com/blog/introducing-azure-media-analytics/).

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png

## <a name="next-steps"></a>Další postup
Prohlédněte si mapy kurzů k Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
