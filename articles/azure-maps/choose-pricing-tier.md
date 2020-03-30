---
title: Zvolte správnou cenovou úroveň | Mapy Microsoft Azure
description: V tomto článku se dozvíte o cenových úrovních nabízených Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: a8bf7ff9bacd4fe84ee5b64d0aed5cb271ce06f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335665"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Výběr správné cenové úrovně v Azure Maps

Azure Maps nabízí dvě cenové úrovně, S0 a S1. Účelem tohoto článku je pomoci vám vybrat správnou cenovou úroveň pro vaše potřeby. Chcete-li vybrat správnou cenovou úroveň, zeptejte se sami sebe na následující dvě otázky.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Kolik souběžných uživatelů mám v plánu podporovat? 
Cenové úrovně S0 a S1 zpracovávají různé objemy propustností dat. Cenová úroveň S0 zpracovává až **50 dotazů za sekundu**. Vzhledem k tomu, že úroveň S1 zpracovává **více než 50 dotazů za sekundu**.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Jaké geoprostorové funkce mám v plánu použít?
Cenová úroveň S0 je pro vás ta pravá, pokud základní geoprostorová api splňují vaše požadavky na služby. Pokud chcete pokročilejší funkce pro vaši aplikaci, zvažte možnost i pro cenovou úroveň S1. Pokročilé funkce zahrnují: Letecké a hybridní snímky, získání rozsahu trasy a dávkové geokódování. Projděte si tabulku **možností cenové úrovně** a vyberte cenovou úroveň, která je pro vaši aplikaci nejvhodnější.

### <a name="pricing-tier-capabilities"></a>Možnosti cenové úrovně

| Schopnost                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Vykreslení mapy                              | ✓                   | ✓       |
| Satelitní snímky                       |                     | ✓        |
| Search                                  | ✓                    | ✓        |
| Dávkové vyhledávání                            |                     | ✓        |
| Trasa                                   | ✓                    |✓        |
| Dávkové směrování                            |                    | ✓        |
| Směrování matice                          |                     | ✓        |
| Rozsah trasy (Isochrony)                |                     | ✓        |
| Provoz                                |✓                    |✓        |
| Časové pásmo                               |✓                    |✓        |
| Geolokace (náhled)                    |✓                   |✓        |
| Prostorové operace                        |                    |✓        |
| Geofencing (monitorování geografických zón)                                |                    |✓        |
| Data map Azure (preview)                |                     | ✓        |
| Mobilita (náhled)                       |                     | ✓        |
| Počasí (náhled)                        |✓                    |✓        |

Zvažte tyto další body:
* Jaký typ podniku máte?
* Jak důležitá je vaše aplikace?

### <a name="pricing-tier-targeted-customers"></a>Cílení zákazníci cenové úrovně

Podívejte se na tabulku zákazníků cílených na **cenové úrovni,** abyste získali lepší představu o cenových úrovních S0 a S1. Další informace najdete v tématu [Ceny Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/). 

| Cenová úroveň  |     Cílení zákazníci                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>Cenová úroveň S0 funguje pro aplikace ve všech fázích výroby: od vývoje proof of concept a testování v rané fázi až po produkční a nasazení aplikací. Tato úroveň je však určen pro vývoj v malém měřítku nebo zákazníky s nízkými souběžnými uživateli nebo obojí. <p>|
| S1            |    <p>Cenová úroveň S1 je určen pro zákazníky s rozsáhlými podnikovými aplikacemi, důležitými aplikacemi nebo velkými objemy souběžných uživatelů. Je to také pro ty zákazníky, kteří vyžadují pokročilé geoprostorové služby.</p>|

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak zobrazit a změnit cenové úrovně:

> [!div class="nextstepaction"] 
> [Správa cenové úrovně](how-to-manage-pricing-tier.md)
