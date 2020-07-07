---
title: Přehled škálování zpracování multimédií | Microsoft Docs
description: Toto téma představuje Přehled škálování zpracování multimédií pomocí Azure Media Services.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "70102916"
---
# <a name="scaling-media-processing-overview"></a>Přehled škálování zpracování médií 
Tato stránka poskytuje přehled o tom, jak a proč škálovat zpracování médií. 

## <a name="overview"></a>Přehled
Účet Media Services je přidružený k typu rezervované jednotky, který určuje rychlost zpracování vašich úloh zpracování médií. Můžete si vybrat mezi následujícími typy rezervovaných jednotek: **S1**, **S2** nebo **S3**. Například stejná úloha kódování bude rychlejší, když použijete typ rezervované jednotky **S2**, než kdybyste použili typ **S1**. Další informace najdete v tématu [rezervované typy jednotek](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/).

Kromě určení typu rezervované jednotky můžete zadat, aby se účet zřídil rezervovanými jednotkami. Počet zřízených rezervovaných jednotek určuje počet úloh médií, které je možné v daném účtu zpracovávat současně. Pokud má váš účet například pět rezervovaných jednotek, pak pět mediálních úloh bude spuštěno souběžně, dokud budou zpracovány úkoly. Zbývající úlohy budou čekat ve frontě a budou vyzvednuty pro zpracování po dokončení běžící úlohy. Pokud účet nemá zřízeny žádné rezervované jednotky, budou úkoly postupně vyzvednuty. V tomto případě bude doba čekání mezi dokončením jednoho úkolu a dalším počátkem záviset na dostupnosti prostředků v systému.

## <a name="choosing-between-different-reserved-unit-types"></a>Volba mezi různými typy rezervovaných jednotek
Následující tabulka vám pomůže při rozhodování o tom, jak určit různé rychlosti kódování. Poskytuje také několik případů srovnávacích testů na [videu, které můžete stáhnout,](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) abyste mohli provádět vlastní testy:

|Typ RU|Scénář|Příklady výsledků pro [video o 7 min](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) .|
|---|---|---|
| **S1**|Kódování s jednou přenosovou rychlostí. <br/>Soubory na SD nebo pod rozlišením, nezávislá na čase, nízké náklady.|Kódování souboru MP4 s jednou přenosovou rychlostí SD pomocí "H264 s jednou přenosovou rychlostí" 16x9 "trvá přibližně 7 minut.|
| **S2**|Jedna přenosová rychlost a s více přenosovými rychlostmi.<br/>Normální použití pro kódování SD i HD.|Kódování s přednastavenou H264 Single přenosovou rychlostí 720p trvá přibližně 6 minut.<br/><br/>Kódování s přednastaveným H264 Multiple přenosovou rychlostí 720p trvá přibližně 12 minut.|
| **S3**|Jedna přenosová rychlost a s více přenosovými rychlostmi.<br/>Kompletní videa o rozlišení HD a 4K. Kódování citlivé na čas, rychlejší zadoba vyřízení.|Kódování pomocí přednastavené H264 s jednou přenosovou rychlostí 1080p trvá přibližně 3 minuty.<br/><br/>Kódování s přednastavenou H264 s více přenosovými rychlostmi 1080p trvá přibližně 8 minut.|

## <a name="considerations"></a>Důležité informace
> [!IMPORTANT]
> Projděte si pokyny popsané v této části.  
> 
> 

* Pro analýzy zvuku a úlohy analýzy videí, které se aktivují Media Services V3 nebo Video Indexer, se důrazně doporučuje typ jednotky S3.
* Pokud používáte sdílený fond, to znamená, že bez rezervovaných jednotek, budou mít vaše úlohy kódování stejný výkon jako u ru S1. Není však k dispozici horní mez doby, kterou mohou úlohy ve stavu zařazeny do fronty, a v jednom okamžiku bude spuštěna pouze jedna úloha.

## <a name="billing"></a>Fakturace

Účtují se vám poplatky podle počtu minut, po které jsou rezervované jednotky médií zřízené ve vašem účtu. K tomu dochází nezávisle na tom, zda se ve vašem účtu spouštějí nějaké úlohy. Podrobné vysvětlení najdete v části Nejčastější dotazy stránky s [cenami Media Services](https://azure.microsoft.com/pricing/details/media-services/) .   

## <a name="quotas-and-limitations"></a>Kvóty a omezení
Informace o kvótách a omezeních a o tom, jak otevřít lístek podpory, najdete v tématu [kvóty a omezení](media-services-quotas-and-limitations.md).

## <a name="next-step"></a>Další krok
Pomocí jedné z těchto technologií zajistěte úlohu zpracování médií s měřítkem: 

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

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

