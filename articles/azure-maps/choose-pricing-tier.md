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
ms.openlocfilehash: 366637274a8006455f05702f47c02f505a615820
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063229"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Zvolte cenovou úroveň ve službě Azure Maps vpravo

Azure Maps nabízí dva cenové úrovně. Cílem tohoto článku je k vám pomůžou zvolit cenovou úroveň pro vaše potřeby vpravo. Pomoc při výběru doprava cenovou úroveň, položte si následující dvě otázky.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Jaké geoprostorové funkce plánu použít?
Cenová úroveň S0 je pro vás vhodná, pokud splňují vaše požadavky na služby geoprostorové základní rozhraní API. Pokud chcete další pokročilé funkce pro vaši aplikaci, vezměte v úvahu vyžadují pro cenovou úroveň S1. Příklad možnosti jsou areal plus hybridní snímky, rozsah trasy a geografické kódování služby batch. **Cenové úrovně možnosti** tabulka poskytuje lepší představu potřeby vaší aplikace. Pomáhá také zvolte cenovou úroveň pro vaši aplikaci nejvhodnější.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Tom, kolik souběžných uživatelů je plán podpory? 
Cenové úrovně S0 a S1 zpracovávat různé množství dat propustnosti. Než se rozhodnete Azure Maps cenovou úroveň, položte si otázku: některé dotazy. Příkladem je "kolik souběžných uživatelů můžu přejete podporovat?" Cenová úroveň S0 zpracovává až **50 dotazů za sekundu**. Obslužné rutiny cenovou úroveň S1 **více než 50 dotazů za sekundu**.

### <a name="pricing-tier-capabilities"></a>Cenová úroveň funkce

| Schopnost                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Search                                  |        ✓           |     ✓    |
| Směrování                                 |        ✓           |     ✓    |
| Vykreslování                                  |        ✓           |     ✓    |
| Provoz                                 |        ✓           |     ✓    |
| Časová pásma                              |        ✓           |     ✓    |
| * Trénováním plus hybridní snímky (preview)  |        ✓           |     ✓    |
| * Trasy rozsahu (preview)                  |        ✓           |     ✓    |
| * Umístění IP 2 (preview)                |        ✓           |     ✓    |
| * Mnohoúhelníky z vyhledávání (preview)         |        ✓           |     ✓    |
| * Batch geokódování (preview)              |        ✓           |     ✓    |
| * Batch směrování (preview)                |        ✓           |     ✓    |
| * Matice směrování (preview)               |        ✓           |     ✓    |

\* Přístup k těmto funkcím z S0 cenová úroveň se přestanou používat po 4. února 2019.

Tyto další datové body stojí za zvážení:
* Jaký druh enterprise máte?
* Jak důležité je při sestavování aplikace?

Najdete v článku **cenová úroveň cílové zákazníkům** tabulka, která má získat lepší představu o S0 a S1 cenové úrovně. Další informace najdete v tématu [ceny Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/). 

### <a name="pricing-tier-targeted-customers"></a>Cenová úroveň cílové zákazníků

| Cenová úroveň  |     Cílové zákazníků                                                                |
|---------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>Cenová úroveň S0 je pro zákazníky, kteří jsou malé a střední podniky. Je pravé cenovou úroveň pro vás, pokud neočekáváte velké objemy souběžných uživatelů. Je také vhodné Pokud geoprostorové základní rozhraní API je znázorněno v předchozí tabulce splňují vaše požadavky na služby. Tato úroveň je obecně dostupná. Funguje pro aplikace ve všech fázích produkční: od testování konceptu vývoje a testování do produkčních aplikací a nasazení rané fázi.<p>|
| S1            |    <p>Cenovou úroveň S1 je určená pro zákazníky, které potřebují podporu pro enterprise ve velkém měřítku, důležité podnikové aplikace nebo vysokých objemů souběžných uživatelů. Je také pro zákazníky, kteří vyžadují pokročilé geoprostorových služeb.</p>|

## <a name="next-steps"></a>Další postup

Další informace o tom, jak zobrazení a změna cenové úrovně:

> [!div class="nextstepaction"] 
> [Správa cenovou úroveň.](how-to-manage-pricing-tier.md)
