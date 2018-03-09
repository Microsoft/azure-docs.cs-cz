---
title: "Škálování zpracování média přehled | Microsoft Docs"
description: "Toto téma je přehled škálování zpracování média pomocí služby Azure Media Services."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 780ef5c2-3bd6-4261-8540-6dee77041387
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/04/2017
ms.author: juliako
ms.openlocfilehash: c80bddfe1896b0b99319ef007c25718b5a754005
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="scaling-media-processing-overview"></a>Škálování zpracování média – přehled
Tato stránka poskytuje přehled toho, jak a proč škálování zpracování média. 

## <a name="overview"></a>Přehled
Účet Media Services je přidružený k typu rezervované jednotky, který určuje rychlost zpracování vašich úloh zpracování médií. Můžete si vybrat mezi následujícími typy rezervovaných jednotek: **S1**, **S2** nebo **S3**. Například stejná úloha kódování bude rychlejší, když použijete typ rezervované jednotky **S2**, než kdybyste použili typ **S1**. Další informace najdete v tématu [vyhrazené typy jednotek](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/).

Určení typu jednotku rezervovanou, můžete kromě zadat poskytnutí jednotek rezervovaných pro váš účet. Počet zřízených rezervovaných jednotek určuje počet úloh médií, které je možné v daném účtu zpracovávat současně. Například pokud potom pět média úlohy bude běžet současně, pokud má váš účet jednotky rezervované pro pět, jako jsou úlohy, které mají být zpracovány. Ve zbývajících úkolech bude čekat ve frontě a bude získat zachyceny pro zpracování postupně po dokončení spuštěná úloha. Pokud účet nemá žádné jednotky rezervované pro zřízení, pak úlohy bude být zachyceny postupně. V takovém případě dobu čekání mezi jeden úkol nepřipojujte a další jeden počáteční bude záviset na dostupnost prostředků v systému.

## <a name="choosing-between-different-reserved-unit-types"></a>Vybrat jinou jednotku rezervovanou typy
V následující tabulce pomáhá zajistit rozhodnutí při výběru mezi různými rychlostmi kódování. Také poskytuje několik případů srovnávacího testu a poskytuje adresy URL SAS, který můžete použít ke stažení videa, na kterých můžete provádět vlastní testy:

| Scénáře | **S1** | **S2** | **S3** |
| --- | --- | --- | --- |
| Zamýšlený případ použití |Kódování jednou přenosovou rychlostí. <br/>Soubory SD nebo pod řešení, není čas citlivé, nízkými náklady. |Jednou přenosovou rychlostí a více kódování přenosovou rychlostí.<br/>Normálního využití pro SD i HD kódování. |Jednou přenosovou rychlostí a více kódování přenosovou rychlostí.<br/>Úplné HD a ovladači 4K videa řešení. Čas, citlivé a rychlejší vyřízení kódování. |
| Benchmark |[Vstupní soubor: 5 minut dlouhé 640x360p v 29,97 snímků za sekundu](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_360p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D).<br/><br/>Kódování určené k s jednou přenosovou rychlostí souboru MP4, ve stejném rozlišení, trvá přibližně 11 minut. |[Vstupní soubor: 5 minut dlouhé 1280x720p v 29,97 snímků za sekundu](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_720p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D)<br/><br/>S "H264 jednou přenosovou rychlostí 720p" přednastavení kódování trvá přibližně 5 minut.<br/><br/>Kódování s "H264 Multiple Bitrate 720p" přednastavených trvá přibližně 11.5 minut. |[Vstupní soubor: 5 minut dlouhé 1920x1080p v 29,97 snímků za sekundu](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_1080p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D). <br/><br/>S "H264 jeden Bitrate 1080p" přednastavení kódování trvá přibližně 2.7 minut.<br/><br/>Kódování s "H264 Multiple Bitrate 1080p" přednastavených trvá přibližně 5.7 minut. |

## <a name="considerations"></a>Požadavky
> [!IMPORTANT]
> Prostudujte si důležité informace, které jsou popsané v této části.  
> 
> 

* Jednotky rezervované pro fungovat pro paralelním prováděním veškeré zpracování médií, včetně indexování úloh pomocí Azure Media Indexer.  Ale na rozdíl od kódování se úlohy indexování s rychlejšími rezervovanými jednotkami nezpracovávají rychleji.
* Pokud používáte sdílený fond, to znamená, bez jakékoli jednotky rezervované pak úlohami kódovat mít stejného výkonu stejně jako u S1 RUs. Však neexistuje žádná horní mez do doby, kdy můžete tráví vaše úkoly ve stavu zařazených do fronty, a v každém okamžiku, bude spuštěna maximálně pouze jednu úlohu.

## <a name="billing"></a>Fakturace

Budou se vám účtovat skutečné minuty využití rezervovaných jednotek médií. Podrobné vysvětlení, najdete v části Nejčastější dotazy [Media Services ceny](https://azure.microsoft.com/pricing/details/media-services/) stránky.   

## <a name="quotas-and-limitations"></a>Kvóty a omezení
Informace o kvóty a omezení a jak otevřít lístek podpory najdete v tématu [kvóty a omezení](media-services-quotas-and-limitations.md).

## <a name="next-step"></a>Další krok
Dosáhnout škálování úlohu zpracování média s některou z těchto technologií: 

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Azure Portal](media-services-portal-scale-media-processing.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 

> [!NOTE]
> Pokud chcete získat nejnovější verzi sady Java SDK a začít s vývojem v jazyce Java, přečtěte si článek [Začínáme s klientskou sadou Java SDK pro Media Services](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Pokud si chcete stáhnout nejnovější sadu PHP SDK pro službu Media Services, najděte si v [úložišti Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7) balíček Microsoft/WindowsAzure verze 0.5.7.  

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

