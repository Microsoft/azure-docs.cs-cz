---
title: Zvolte cenovou úroveň pro službu Azure Maps právo | Dokumentace Microsoftu
description: Další informace o cenových úrovních, které nabízí Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 01/02/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 028a227eefb319d19c9274082626a007bd9161de
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "53993956"
---
# <a name="choosing-the-right-pricing-tier-in-azure-maps"></a>Volba správného cenové úrovně ve službě Azure Maps

Azure Maps nabízí dva cenové úrovně. Cílem tohoto článku je k vám pomůžou zvolit cenovou úroveň pro vaše potřeby vpravo. Pomoc při výběru doprava cenovou úroveň, položte si otázku: dvě otázky:

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Jaké geoprostorové funkce plánu použít?
Pokud si myslíte, že splňuje vaše požadavky na služby geoprostorové základní rozhraní API a cenovou úroveň S0 je pro vás nejvhodnější. Pokud chcete další pokročilé funkce pro vaši aplikaci, jako je například areal + hybridní snímky, získávání trasy rozsah batch geokódování atd., zvažte, že pro cenovou úroveň S1. V následující tabulce s **cenové úrovně možnosti** poskytují lepší představu o potřeby vaší aplikace, který se bude také nápovědy, zvolte cenovou úroveň pro vaši aplikaci nejvhodnější.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Tom, kolik souběžných uživatelů je plán podpory? 
S0 a cenových úrovní S1 může zpracovávat různé množství dat propustnosti. Před výběrem Azure Maps cenovou úroveň, zkuste se zeptat sami otázky, jako je počet souběžných uživatelů, kteří si přejete podporovat? S0 cenovou úroveň můžete zpracovávat až **50 dotazů za sekundu** a dokáže zpracovat cenovou úroveň S1 **více než 50 dotazů za sekundu**.

### <a name="pricing-tier-capabilities"></a>Cenová úroveň funkce

| Schopnost                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Search                                  |        ✓           |     ✓    |
| Směrování                                 |        ✓           |     ✓    |
| Vykreslování                                  |        ✓           |     ✓    |
| Provoz                                 |        ✓           |     ✓    |
| Časová pásma                              |        ✓           |     ✓    |
| * Trénováním + hybridní snímky (preview)     |        ✓           |     ✓    |
| * Trasy rozsahu (Preview)                  |        ✓           |     ✓    |
| * Poloha IP adresy 2 (preview)                |        ✓           |     ✓    |
| * Mnohoúhelníky z vyhledávání (Preview)         |        ✓           |     ✓    |
| * Batch Geokódování (preview)              |        ✓           |     ✓    |
| * Batch směrování (preview)                |        ✓           |     ✓    |
| * Směrování matice (preview)               |        ✓           |     ✓    |

> [!Note]
> Přístup k těmto funkcím z S0 cenová úroveň se přestanou používat po 4. února 2019.

Jsou některé další datové body, které stojí za to, vzhledem k tomu, jaký druh enterprise máte nebo jak kritické je aplikace vytváří?

Viz tabulku s **cenová úroveň cílové zákazníkům** získat lepší představu o S0 a S1 cenové úrovně. Další informace o cenách Azure Maps, naleznete v části, [ceny služby Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/). 

### <a name="pricing-tier-targeted-customers"></a>Cenová úroveň cílové zákazníků

| Cenová úroveň  |        Cílové zákazníků                                                                |
|---------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>Cenová úroveň S0 je pro zákazníky, kteří jsou malé a střední podniky. Je pravé cenovou úroveň pro vás, pokud neočekáváte velké objemy souběžných uživatelů a splňuje vaše požadavky na služby geoprostorové základní rozhraní API, jak je uvedeno ve výše uvedené tabulce. Tato úroveň je obecně dostupná a platí pro aplikace ve všech fázích výroby od testování konceptu vývoje a rané testování do produkčních aplikací a nasazení.<p>|
| S1            |    <p>Cenovou úroveň S1 je určená pro zákazníky, které potřebují podporu pro enterprise ve velkém měřítku, kritické podnikové procesy, velký objem souběžných uživatelů a vyžaduje pokročilé geoprostorových služeb.</p>|

## <a name="next-steps"></a>Další postup

Další informace o zobrazení a změna cenové úrovně:

> [!div class="nextstepaction"]
> [Správa cenová úroveň](how-to-manage-pricing-tier.md)
