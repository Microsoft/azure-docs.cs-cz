---
title: Výběr správné cenové úrovně pro mapy Microsoft Azure
description: Přečtěte si o Azure Maps cenové úrovně. Podívejte se, které funkce jsou nabízené na úrovních a zda si chcete zobrazit klíčové důležité informace pro výběr cenové úrovně.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cb57e424642141e73588a61d026d1ff2f1a8b096
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96905294"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Výběr správné cenové úrovně v Azure Maps

Azure Maps nabízí dvě cenové úrovně: S0 a S1. Účelem tohoto článku je pomáhat s výběrem správné cenové úrovně pro vaše potřeby. Pokud chcete zvolit správnou cenovou úroveň, položte si následující dvě otázky.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Kolik souběžných uživatelů Plánujem podporu?

Cenové úrovně S0 a S1 zpracovávají různé objemy propustnosti dat. Cenová úroveň S0 zpracovává až **50 dotazů za sekundu**. Zatímco úroveň S1 zpracovává **více než 50 dotazů za sekundu**.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Jaké geoprostorové možnosti mám v plánu použít?

Pokud základní geoprostorové rozhraní API splňuje požadavky vaší služby, vyberte cenovou úroveň S0. Pokud chcete pro aplikaci využít pokročilejší možnosti, zvažte možnost výběru cenové úrovně S1. Mezi pokročilé funkce patří: letecká a hybridní satelitní obrázek, získání rozsahu směrování a geografické kódování dávky. Pokud chcete vybrat cenovou úroveň vhodnou pro vaši aplikaci, přečtěte si následující tabulka **možností cenové úrovně** :

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
|  Tvůrce (Preview)                         |                   |✓        |
|  Zvýšení úrovně oprávnění (Preview)                        |                   |✓        |

Vezměte v úvahu tyto další body:

* Jaký typ firmy máte?
* Jak kritická je vaše aplikace?

### <a name="pricing-tier-targeted-customers"></a>Cílené zákazníky cenové úrovně

Podívejte se na tabulku **cílené zákazníky s cenovou úrovní** a získejte lepší představu o cenových úrovních S0 a S1. Další informace najdete v tématu [Azure Maps ceny](https://azure.microsoft.com/pricing/details/azure-maps/). 

| Cenová úroveň  |     Cíloví zákazníci                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    Cenová úroveň S0 funguje pro aplikace ve všech fázích produkce: od testování konceptu a testování v rané fázi při výrobě a nasazování aplikací. Tato úroveň je však navržena pro vývoj v malém měřítku nebo pro zákazníky s nízkými souběžnými uživateli nebo obojí. 
| S1            |    Cenová úroveň S1 je určena pro zákazníky, kteří mají rozsáhlé podnikové aplikace, klíčové aplikace nebo velké objemy souběžných uživatelů. Také pro zákazníky, kteří vyžadují pokročilé geoprostorové služby.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak zobrazit a změnit cenové úrovně:

> [!div class="nextstepaction"]
> [Správa cenové úrovně](how-to-manage-pricing-tier.md)
