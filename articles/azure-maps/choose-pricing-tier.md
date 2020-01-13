---
title: Výběr správné cenové úrovně | Mapy Microsoft Azure
description: V tomto článku se dozvíte o cenových úrovních nabízených službou Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 01/02/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5ff774f9848db948058075a98504e6c13db5467a
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911750"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Výběr správné cenové úrovně v Azure Maps

Azure Maps nabízí dvě cenové úrovně. Účelem tohoto článku je pomáhat s výběrem správné cenové úrovně pro vaše potřeby. Pokud chcete zvolit správnou cenovou úroveň, položte si následující dvě otázky.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Jaké geoprostorové možnosti mám v plánu použít?
Cenová úroveň S0 je vhodná pro vás, pokud základní geoprostorové rozhraní API splňuje požadavky vaší služby. Pokud chcete pro aplikaci využít pokročilejší možnosti, zvažte možnost použít cenovou úroveň S1. Příkladem funkcí jsou Areal a hybridních snímků, získání rozsahu směrování a geografické kódování Batch. Následující tabulka **možností cenové úrovně** vám dává lepší představu o potřebách vaší aplikace. Také vám pomůže vybrat cenovou úroveň vhodnou pro vaši aplikaci.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Kolik souběžných uživatelů Plánujem podporu? 
Cenové úrovně S0 a S1 zpracovávají různé objemy propustnosti dat. Před výběrem Azure Maps cenové úrovně si položte nějaké otázky. Příklad: kolik souběžných uživatelů chcete podporovat? " Cenová úroveň S0 zpracovává až **50 dotazů za sekundu**. Cenová úroveň S1 zpracovává **více než 50 dotazů za sekundu**.

### <a name="pricing-tier-capabilities"></a>Možnosti cenové úrovně

| Schopnost                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Hledání (geografické kódování FWD/rev, body zájmu)  |        ✓           |     ✓    |
| Geografické kódování dávky (Preview)              |                   |     ✓    |
| Mnohoúhelníky ze služby Search          |                   |     ✓    |
| Plánování tras                                 |        ✓           |     ✓    |
| Rozsah trasy                    |                   |     ✓    |
| Směrování Batch (Náhled)                |                   |     ✓    |
| Směrování matice (Preview)               |                   |     ✓    |
| Vykreslování                                  |        ✓           |     ✓    |
| Obrázek a hybridní obrázek    |            |     ✓    |
| Provoz                                 |        ✓           |     ✓    |
| Časová pásma                              |        ✓           |     ✓    |
| Geografická poloha (Preview)                |        ✓           |     ✓    |
| Data (Preview)               |                   |     ✓    |
| Prostor (Preview)               |                   |     ✓    |
| Monitorování geografických zón (Preview)               |                   |     ✓    |



Je potřeba zvážit tyto další datové body:
* Jaký druh firmy máte?
* Jak kritická je aplikace sestavená?

Podívejte se na tabulku **cílené zákazníky s cenovou úrovní** a získejte lepší představu o cenových úrovních S0 a S1. Další informace najdete v tématu [Azure Maps ceny](https://azure.microsoft.com/pricing/details/azure-maps/). 

### <a name="pricing-tier-targeted-customers"></a>Cílené zákazníky cenové úrovně

| Cenová úroveň  |     Cíloví zákazníci                                                                |
|---------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>Cenová úroveň S0 je určena pro zákazníky, kteří jsou malými nebo středními podniky. Pokud neočekáváte velké objemy souběžných uživatelů, jedná se o správnou cenovou úroveň. Je to také v případě, že základní geoprostorové rozhraní API uvedená v předchozí tabulce splňují požadavky vaší služby. Tato úroveň je všeobecně dostupná. Funguje pro aplikace ve všech fázích produkce: od testování konceptu a testování v rané fázi při výrobě a nasazování aplikací.<p>|
| S1            |    <p>Cenová úroveň S1 je určena zákazníkům, kteří potřebují podporu pro rozsáhlé podnikové, klíčové aplikace nebo velké objemy souběžných uživatelů. Také pro zákazníky, kteří vyžadují pokročilé geoprostorové služby.</p>|

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak zobrazit a změnit cenové úrovně:

> [!div class="nextstepaction"] 
> [Správa cenové úrovně](how-to-manage-pricing-tier.md)
