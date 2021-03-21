---
title: Přehled rezervovaných jednotek médií | Microsoft Docs
description: Tento článek představuje Přehled škálování zpracování multimédií pomocí Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: d0692996c27f969ffc90078db2ddcc849ee15ab1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012713"
---
# <a name="media-reserved-units"></a>Rezervované jednotky médií

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Azure Media Services vám umožní škálovat zpracování médií správou rezervovaných jednotek médií (MRUs). MRU poskytuje další výpočetní kapacitu potřebnou pro kódování médií. Počet MRUs určuje rychlost, s jakou jsou zpracovávány vaše mediální úlohy a kolik multimediálních úloh lze v účtu souběžně zpracovat. Pokud má váš účet například pět MRUs a existují úkoly, které je potřeba zpracovat, pak může běžet pět mediálních úloh současně. Všechny zbývající úkoly budou zařazeny do fronty a bude možné je po dokončení probíhající úlohy vyzvednout pro zpracování. Každé naposledy zřízené zařízení má za následek rezervaci kapacity, ale neposkytuje vyhrazené prostředky. V době extrémně vysokého požadavku se MRUs nemusí okamžitě zahájit zpracování.

## <a name="choosing-between-different-reserved-unit-types"></a>Volba mezi různými typy rezervovaných jednotek

Následující tabulka vám pomůže při rozhodování o tom, jak určit různé rychlosti kódování.  Zobrazuje dobu trvání kódování po dobu 7 minut, 1080p v závislosti na použitém použitém seznamu.

|Typ RU|Scenario|Příklady výsledků pro video o 7 min. |
|---|---|---|
| **S1**|Kódování s jednou přenosovou rychlostí. <br/>Soubory na SD nebo pod rozlišením, nezávislá na čase, nízké náklady.|Kódování souboru MP4 s jednou přenosovou rychlostí SD pomocí "H264 s jednou přenosovou rychlostí" 16x9 "trvá přibližně 7 minut.|
| **S2**|Jedna přenosová rychlost a s více přenosovými rychlostmi.<br/>Normální použití pro kódování SD i HD.|Kódování s přednastavenou H264 Single přenosovou rychlostí 720p trvá přibližně 6 minut.<br/><br/>Kódování s přednastaveným H264 Multiple přenosovou rychlostí 720p trvá přibližně 12 minut.|
| **S3**|Jedna přenosová rychlost a s více přenosovými rychlostmi.<br/>Kompletní videa o rozlišení HD a 4K. Kódování citlivé na čas, rychlejší zadoba vyřízení.|Kódování pomocí přednastavené H264 s jednou přenosovou rychlostí 1080p trvá přibližně 3 minuty.<br/><br/>Kódování s přednastavenou H264 s více přenosovými rychlostmi 1080p trvá přibližně 8 minut.|

> [!NOTE]
> Pokud jste pro svůj účet nezřídili, budou se vaše mediální úlohy zpracovávat s výkonem seznamu S1 a úlohy se budou postupně vyzvednout. Žádná kapacita zpracování není vyhrazená, takže čekací doba mezi dokončením jednoho úkolu a dalším počátkem bude záviset na dostupnosti prostředků v systému.

## <a name="considerations"></a>Požadavky

* Pro analýzy zvuku a úlohy analýzy videí, které se spouštějí Media Services V3 nebo Video Indexer, se důrazně doporučuje zřídit účet s deseti jednotkami S3. Pokud potřebujete více než 10 S3 MRUs, otevřete lístek podpory pomocí [Azure Portal](https://portal.azure.com/).
* Pro úlohy kódování, které nemají MRUs, není k dispozici horní mez doby, kterou mohou úlohy ve stavu zařazeny do fronty, a současně bude spuštěna pouze jedna úloha.

## <a name="billing"></a>Fakturace

Účtují se vám poplatky podle počtu minut, po které jsou rezervované jednotky médií zřízené ve vašem účtu. K tomu dochází nezávisle na tom, zda se ve vašem účtu spouštějí nějaké úlohy. Podrobné vysvětlení najdete v části Nejčastější dotazy stránky s [cenami Media Services](https://azure.microsoft.com/pricing/details/media-services/) .

## <a name="quotas-and-limitations"></a>Kvóty a omezení

Informace o kvótách a omezeních a o tom, jak otevřít lístek podpory, najdete v tématu [kvóty a omezení](media-services-quotas-and-limitations.md).

## <a name="next-steps"></a>Další kroky

Vyzkoušejte škálované zpracování multimédií pomocí jedné z těchto technologií:

[Rozhraní .NET](media-services-dotnet-encoding-units.md) 
 [Portál](media-services-portal-scale-media-processing.md) 
 [REST](/rest/api/media/operations/encodingreservedunittype) 
 [Jazyk Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples) 
 [Php](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)