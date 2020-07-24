---
title: Výběr správné cenové úrovně | Mapy Microsoft Azure
description: V tomto článku se dozvíte o cenových úrovních nabízených službou Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 4b32706822ff16780388f4acb5d59bf3d91e6d91
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133329"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Výběr správné cenové úrovně v Azure Maps

Azure Maps nabízí dvě cenové úrovně S0 a S1. Účelem tohoto článku je pomáhat s výběrem správné cenové úrovně pro vaše potřeby. Pokud chcete zvolit správnou cenovou úroveň, položte si následující dvě otázky.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Kolik souběžných uživatelů Plánujem podporu? 
Cenové úrovně S0 a S1 zpracovávají různé objemy propustnosti dat. Cenová úroveň S0 zpracovává až **50 dotazů za sekundu**. Zatímco úroveň S1 zpracovává **více než 50 dotazů za sekundu**.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Jaké geoprostorové možnosti mám v plánu použít?
Cenová úroveň S0 je pro vás nejvhodnější, pokud základní geoprostorové rozhraní API splňuje požadavky vaší služby. Pokud chcete pro aplikaci využít pokročilejší možnosti, zvažte možnost použít cenovou úroveň S1. Mezi pokročilé funkce patří: letecká a hybridní satelitní obrázek, získání rozsahu směrování a geografické kódování dávky. Zkontrolujte tabulku **schopností cenové úrovně** a vyberte cenovou úroveň, která je pro vaši aplikaci nejvhodnější.

### <a name="pricing-tier-capabilities"></a>Možnosti cenové úrovně

| Schopnost                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Vykreslování mapy                              | ✓                   | ✓       |
| Satelitních snímků                       |                     | ✓        |
| Hledat                                  | ✓                    | ✓        |
| Dávkové vyhledávání                            |                     | ✓        |
| Trasa                                   | ✓                    |✓        |
| Směrování Batch                            |                    | ✓        |
| Směrování matice                          |                     | ✓        |
| Rozsah trasy (Izochronů)                |                     | ✓        |
| Provoz                                |✓                    |✓        |
| Časové pásmo                               |✓                    |✓        |
| Geografická poloha (Preview)                    |✓                   |✓        |
| Prostorové operace                        |                    |✓        |
| Geofencing (monitorování geografických zón)                                |                    |✓        |
| Data Azure Maps (Preview)                |                     | ✓        |
| Mobilita (Preview)                       |                     | ✓        |
| Počasí (Preview)                        |✓                    |✓        |

Vezměte v úvahu tyto další body:
* Jaký typ firmy máte?
* Jak kritická je vaše aplikace?

### <a name="pricing-tier-targeted-customers"></a>Cílené zákazníky cenové úrovně

Podívejte se na tabulku **cílené zákazníky s cenovou úrovní** a získejte lepší představu o cenových úrovních S0 a S1. Další informace najdete v tématu [Azure Maps ceny](https://azure.microsoft.com/pricing/details/azure-maps/). 

| Cenová úroveň  |     Cíloví zákazníci                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>Cenová úroveň S0 funguje pro aplikace ve všech fázích produkce: od testování konceptu a testování v rané fázi při výrobě a nasazování aplikací. Tato úroveň je však navržena pro vývoj v malém měřítku nebo pro zákazníky s nízkými souběžnými uživateli nebo obojí. <p>|
| S1            |    <p>Cenová úroveň S1 je určena pro zákazníky, kteří mají rozsáhlé podnikové aplikace, klíčové aplikace nebo velké objemy souběžných uživatelů. Také pro zákazníky, kteří vyžadují pokročilé geoprostorové služby.</p>|

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak zobrazit a změnit cenové úrovně:

> [!div class="nextstepaction"] 
> [Správa cenové úrovně](how-to-manage-pricing-tier.md)
