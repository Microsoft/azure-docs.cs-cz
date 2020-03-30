---
title: Přehled zpracování médií škálování | Dokumenty společnosti Microsoft
description: Toto téma je přehled škálování zpracování médií pomocí Azure Media Services.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2019
ms.author: juliako
ms.openlocfilehash: 780d3ab5047bff321d0c554880ba2995bcf25524
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70102916"
---
# <a name="scaling-media-processing-overview"></a>Přehled škálování zpracování médií 
Tato stránka poskytuje přehled o tom, jak a proč škálovat zpracování médií. 

## <a name="overview"></a>Přehled
Účet Media Services je přidružený k typu rezervované jednotky, který určuje rychlost zpracování vašich úloh zpracování médií. Můžete si vybrat mezi následujícími typy rezervovaných jednotek: **S1**, **S2** nebo **S3**. Například stejná úloha kódování bude rychlejší, když použijete typ rezervované jednotky **S2**, než kdybyste použili typ **S1**. Další informace naleznete v [tématu Rezervované typy jednotek](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/).

Kromě určení typu rezervované jednotky můžete zadat zřízení účtu s rezervovanými jednotkami. Počet zřízených rezervovaných jednotek určuje počet úloh médií, které je možné v daném účtu zpracovávat současně. Pokud má například váš účet pět rezervovaných jednotek, bude současně spuštěno pět úloh médií, pokud budou zpracovány úlohy. Zbývající úkoly budou čekat ve frontě a budou po dokončení spuštěné úlohy vyzvednuty pro zpracování postupně. Pokud účet nemá žádné rezervované jednotky zřízena, pak úkoly budou vyzvednuty postupně. V takovém případě bude čekací doba mezi dokončením jednoho úkolu a dalším zahájením záviset na dostupnosti prostředků v systému.

## <a name="choosing-between-different-reserved-unit-types"></a>Výběr mezi různými typy rezervovaných jednotek
Následující tabulka vám pomůže rozhodnout při výběru mezi různými rychlostmi kódování. Poskytuje také několik případů [benchmarku](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) na videu, které si můžete stáhnout k provedení vlastních testů:

|Typ ŽP|Scénář|Příklad y pro [video 7 min 1080p](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Kódování jednoho přenosové rychlosti. <br/>Soubory na SD nebo pod rozlišení, není časově citlivé, nízké náklady.|Kódování do jednoho souboru MP4 s přenosovým tokem SD pomocí "H264 Single Bitrate SD 16x9" trvá přibližně 7 minut.|
| **S2**|Kódování s jedním přenosovou rychlostí a vícenásobnou přenosovou rychlostí.<br/>Normální použití pro kódování SD i HD.|Kódování s přednastavením "H264 Single Bitrate 720p" trvá přibližně 6 minut.<br/><br/>Kódování s přednastavením "H264 Multiple Bitrate 720p" trvá přibližně 12 minut.|
| **S3**|Kódování s jedním přenosovou rychlostí a vícenásobnou přenosovou rychlostí.<br/>Videa s rozlišením Full HD a 4K. Časově citlivé, rychlejší kódování obratu.|Kódování s přednastavením "H264 Single Bitrate 1080p" trvá přibližně 3 minuty.<br/><br/>Kódování s přednastavením "H264 Multiple Bitrate 1080p" trvá přibližně 8 minut.|

## <a name="considerations"></a>Požadavky
> [!IMPORTANT]
> Zkontrolujte aspekty popsané v této části.  
> 
> 

* Pro úlohy analýzy zvuku a analýzy videa, které jsou spuštěny službou Media Services v3 nebo Video Indexer, je typ jednotky S3 vysoce doporučeno.
* Pokud používáte sdílený fond, to znamená bez rezervovaných jednotek, pak vaše úlohy kódování mají stejný výkon jako u jednotek Ru S1. Neexistuje však žádná horní mez času, který mohou úkoly strávit ve stavu ve frontě, a v daném okamžiku bude spuštěna maximálně jedna úloha.

## <a name="billing"></a>Fakturace

Poplatky vám budou účtovány na základě počtu minut, po které jsou rezervované jednotky médií zřízeny ve vašem účtu. K tomu dochází nezávisle na tom, zda jsou ve vašem účtu spuštěny nějaké úlohy. Podrobné vysvětlení naleznete v části Nejčastější dotazy na stránce [s cenami mediálních služeb.](https://azure.microsoft.com/pricing/details/media-services/)   

## <a name="quotas-and-limitations"></a>Kvóty a omezení
Informace o kvótách a omezeních a o tom, jak otevřít lístek podpory, naleznete v [tématu Kvóty a omezení](media-services-quotas-and-limitations.md).

## <a name="next-step"></a>Další krok
Dosáhněte úlohy zpracování médií škálování pomocí jedné z těchto technologií: 

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portál](media-services-portal-scale-media-processing.md)
> * [Odpočinku](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 

> [!NOTE]
> Pokud chcete získat nejnovější verzi sady Java SDK a začít s vývojem v jazyce Java, přečtěte si článek [Začínáme s klientskou sadou Java SDK pro Media Services](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Pokud si chcete stáhnout nejnovější sadu PHP SDK pro službu Media Services, najděte si v [úložišti Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7) balíček Microsoft/WindowsAzure verze 0.5.7.  

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

