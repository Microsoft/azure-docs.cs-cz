---
title: Rezervované jednotky médií – Azure | Microsoft Docs
description: Rezervované jednotky médií umožňují škálovat proces média a určovat rychlost úloh zpracování médií.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: df691f5b9e74432f683e52fbb896dd4d44efe2aa
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91622058"
---
# <a name="media-reserved-units"></a>Rezervované jednotky médií

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services vám umožní škálovat zpracování médií správou rezervovaných jednotek médií (MRUs). MRU poskytuje další výpočetní kapacitu potřebnou pro kódování médií. Počet MRUs určuje rychlost, s jakou jsou zpracovávány vaše mediální úlohy a kolik multimediálních úloh lze v účtu souběžně zpracovat. Pokud má váš účet například pět MRUs a existují úkoly, které je potřeba zpracovat, pak může běžet pět mediálních úloh současně. Všechny zbývající úkoly budou zařazeny do fronty a bude možné je po dokončení probíhající úlohy vyzvednout pro zpracování. Každé naposledy zřízené zařízení má za následek rezervaci kapacity, ale neposkytuje vyhrazené prostředky. V době extrémně vysokého požadavku se MRUs nemusí okamžitě zahájit zpracování.

Úkol je individuální operace práce na prostředku, např. Adaptivní kódování streamování. Když odešlete úlohu, Media Services postará o rozdělení úlohy na jednotlivé operace (tj. úkoly), které pak budou přidruženy k samostatným MRUsům.

## <a name="choosing-between-different-reserved-unit-types"></a>Volba mezi různými typy rezervovaných jednotek

Následující tabulka vám pomůže při rozhodování o tom, jak určit různé rychlosti kódování.  Zobrazuje dobu trvání kódování po dobu 7 minut, 1080p v závislosti na použitém použitém seznamu.

|Typ RU|Scénář|Příklady výsledků pro video o 7 min. |
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

Účtují se vám poplatky podle počtu minut, po které jsou rezervované jednotky médií zřízené ve vašem účtu, bez ohledu na to, jestli jsou spuštěné nějaké úlohy. Podrobné vysvětlení najdete v části Nejčastější dotazy stránky s [cenami Media Services](https://azure.microsoft.com/pricing/details/media-services/) .

## <a name="next-step"></a>Další krok
[Škálování rezervovaných jednotek médií pomocí](media-reserved-units-cli-how-to.md) rozhraní PŘÍKAZového řádku 
 [Analýza videí](analyze-videos-tutorial-with-api.md)

## <a name="see-also"></a>Viz také:

* [Kvóty a omezení](limits-quotas-constraints.md)
