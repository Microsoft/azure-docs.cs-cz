---
title: "Další informace o kodéry doporučení služby Azure Media Services | Microsoft Docs"
description: "Další informace o kodéry doporučená službou media services"
services: media-services
keywords: "kódování; kodéry; média"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: d0c5536d2339470eac058250cc14e1f250b86d90
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2017
---
# <a name="recommended-on-premises-encoders"></a>Doporučené místních kodérů
Když živé streamování pomocí služby Azure Media Services, můžete zadat, jak chcete, aby kanál pro příjem vstupního datového proudu. Pokud chcete použít místní kodér s živého kanálu kódování, kodér nabízenou datový proud s jednou přenosovou rychlostí vysoce kvalitní jako výstup. Pokud chcete použít místní kodér s průchozí kanál, kodér nabízenou datový proud s více přenosovými rychlostmi jako výstup pro všechny vlastnosti požadované výstup. Další informace najdete v tématu [živé streamování s místní kodéry](media-services-live-streaming-with-onprem-encoders.md).

Azure Media Services doporučuje pomocí jedné z následujících kodéry, které mají RTMP jako výstup:
- Média Adobe Flash Live kodér 3.2
- Haivision Makito X HEVC
- Kodér Telestream Wirecast 8.1
- Řez Teradek 756
- 8000 čase
- Malé čase HD – 4

Azure Media Services doporučuje pomocí jedné z následující kodéry, které mají více přenosovými rychlostmi technologie Smooth Streaming jako výstup:
- Ateme TITAN za provozu
- Kodér digitálního média Cisco 2200
- Elemental za provozu
- Envivio 4Caster C4 generace III
- Představte si Selenio MCP3 komunikace
- Média v aplikaci Excel hrdina za provozu

> [!NOTE]
> Za provozu encoder můžete odeslat datový proud s jednou přenosovou rychlostí průchozí kanál, ale tato konfigurace se nedoporučuje, protože není povolen pro adaptivní přenosové rychlosti streamování do klienta.

## <a name="how-to-become-an-on-prem-encoder-partner"></a>Postup stane partnerem místní kodér
Jako partner Azure Media Services místní kodéru Media Services podporuje svůj produkt doporučením kodér pro podnikové zákazníky. Stane partnerem místní kodér, je nutné ověřit kompatibilitu místní kodér pomocí služby Media Services. Uděláte to tak, proveďte následující ověření:

Předání kanál ověření
1. Vytvořit nebo navštivte účtu Azure Media Services
2. Vytvoření a spuštění **průchozí** kanálu
3. Nakonfigurujte kodér tak, aby nabízel živý datový proud s více přenosovými rychlostmi.
4. Vytvoření publikovaných živé události
5. Spuštění za provozu kodér přibližně 10 minut
6. Zastavit živé události
7. ID prostředku, záznamu, publikovaná streamovací adresa URL pro archiv za provozu a nastavení a verze použitá z provozu kodér
8. Resetování stavu kanálu po vytvoření jednotlivé vzorky
9. Opakujte kroky 3 až 8 pro všechny konfigurace nepodporuje kodér (s nebo bez ad signalizace nebo titulky nebo jiné kódování rychlost)

Za provozu kódování ověření kanálu
1. Vytvořit nebo navštivte účtu Azure Media Services
2. Vytvoření a spuštění **live kódování** kanálu
3. Nakonfigurujte kodér tak, aby nabízel datový proud s jednou přenosovou rychlostí.
4. Vytvoření publikovaných živé události
5. Spuštění za provozu kodér přibližně 10 minut
6. Zastavit živé události
7. ID prostředku, záznamu, publikovaná streamovací adresa URL pro archiv za provozu a nastavení a verze použitá z provozu kodér
8. Resetování stavu kanálu po vytvoření jednotlivé vzorky
9. Opakujte kroky 3 až 8 pro všechny konfigurace nepodporuje kodér (s nebo bez ad signalizace nebo titulky nebo různých kódování rychlost)

Ověření dlouhověkosti
1. Vytvořit nebo navštivte účtu Azure Media Services
2. Vytvoření a spuštění **průchozí** kanálu
3. Nakonfigurujte kodér tak, aby nabízel živý datový proud s více přenosovými rychlostmi.
4. Vytvoření publikovaných živé události
5. Spuštění za provozu kodér pro jeden týden nebo delší
6. Zastavit živé události
7. ID prostředku, záznamu, publikovaná streamovací adresa URL pro archiv za provozu a nastavení a verze použitá z provozu kodér

Nakonec odeslat zaznamenané nastavení a za chodu parametry archivu ke službě Media Services e-mailem na amsstreaming@microsoft.com. Po přijetí Media Services provádí ukázky z provozu kodér testech pro ověření. Obraťte se na Media Services s dotazy týkající se tohoto procesu.