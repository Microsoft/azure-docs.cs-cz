---
title: Škálování zpracování médií přehled | Dokumentace Microsoftu
description: V tomto tématu je uveden přehled škálování zpracování médií pomocí Azure Media Services.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 780ef5c2-3bd6-4261-8540-6dee77041387
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2018
ms.author: juliako
ms.openlocfilehash: 698a85244d5341224dd9f513c5617b9086e36844
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033069"
---
# <a name="scaling-media-processing-overview"></a>Přehled škálování zpracování médií
Tato stránka poskytuje přehled toho, jak a proč ke škálování zpracování médií. 

## <a name="overview"></a>Přehled
Účet Media Services je přidružený k typu rezervované jednotky, který určuje rychlost zpracování vašich úloh zpracování médií. Můžete si vybrat mezi následujícími typy rezervovaných jednotek: **S1**, **S2** nebo **S3**. Například stejná úloha kódování bude rychlejší, když použijete typ rezervované jednotky **S2**, než kdybyste použili typ **S1**. Další informace najdete v tématu [typy rezervovaných jednotek](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/).

Kromě určení typu rezervované jednotky, můžete zadat účet zřídit s rezervovaných jednotek. Počet zřízených rezervovaných jednotek určuje počet úloh médií, které je možné v daném účtu zpracovávat současně. Například pokud má váš účet pak pět média úlohy poběží současně po dobu pěti rezervovaných jednotek, jako jsou úkoly ke zpracování. Ve zbývajících úkolech počká ve frontě a bude získat, vyberou se pro zpracování postupně, kdy spuštěná úloha dokončí. Pokud účet nemá žádné rezervované jednotky zřízené, pak úlohy neexistoval, použije postupně. V takovém případě dobu čekání mezi dokončení úkolů a další nazve spuštění bude záviset na dostupnost prostředků v systému.

## <a name="choosing-between-different-reserved-unit-types"></a>Volba mezi jednotku rezervovanou pro různé typy
V následující tabulce umožňuje rozhodování při výběru mezi různými rychlostmi kódování. Poskytuje také několik případů srovnávacích testů na [video, které si můžete stáhnout](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) provádět vlastní testy:

| Scénáře | **S1** | **S2** | **S3** |
| --- | --- | --- | --- |
| Zamýšlený případ použití |Kódování s jednou přenosovou rychlostí. <br/>Soubory na SD nebo nižší rozlišení, není čas citlivé, s nízkými náklady. |S jednou přenosovou rychlostí a více s přenosovou rychlostí s kódováním.<br/>Normální využívání SD a HD, High Density kódování. |S jednou přenosovou rychlostí a více s přenosovou rychlostí s kódováním.<br/>Úplné HD, High Density a 4 kB videa s rozlišením. Čas, po vyřízení citlivé a rychlejší kódování. |
| Srovnávací test pro video 7 minut |Kódování s jednou přenosovou rychlostí soubor MP4, ve stejném rozlišení trvá přibližně 5 minut. |Kódování pomocí "H264 s jednou přenosovou rychlostí 720p" přednastavení trvá přibližně 8 minut.<br/><br/>Kódování pomocí "H264 Multiple Bitrate 720p" přednastavení trvá přibližně 16.8 minut. |Kódování pomocí "H264 s jednou přenosovou rychlostí 1080p" přednastavení trvá přibližně 4 minuty.<br/><br/>Kódování pomocí "H264 Multiple Bitrate 1080p" přednastavení trvá přibližně 8 minut. |


## <a name="considerations"></a>Požadavky
> [!IMPORTANT]
> Prostudujte si důležité informace, které jsou popsané v této části.  
> 
> 

* Analýza zvuku a videa analýzy úloh, které jsou aktivovány Media Services v3 nebo Video Indexer důrazně doporučujeme typ jednotky S3.
* Pokud používáte sdílený fond, to znamená, bez jakékoli rezervovaných jednotek, pak vaše úkoly kódovat mít stejný výkon stejně jako u jednotek ru S1. Však neexistuje žádná horní mez do doby, kdy vaše úlohy můžete využít ve stavu zařazení do fronty, a v daném okamžiku bude spuštěna maximálně pouze jeden úkol.

## <a name="billing"></a>Fakturace

Budou se vám účtovat skutečné minuty využití rezervovaných jednotek médií. Podrobné vysvětlení najdete v tématu v části Nejčastější dotazy [ceny služby Media Services](https://azure.microsoft.com/pricing/details/media-services/) stránky.   

## <a name="quotas-and-limitations"></a>Kvóty a omezení
Informace o omezení a kvóty a tom, jak vytvořit lístek podpory najdete v tématu [kvóty a omezení](media-services-quotas-and-limitations.md).

## <a name="next-step"></a>Další krok
Zajištění škálování úloh zpracování médií s jedním z těchto technologií: 

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
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

