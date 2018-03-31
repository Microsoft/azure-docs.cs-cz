---
title: Azure Mobile Engagement implementace pro herní aplikace
description: Scénář herní aplikace k implementaci Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 2cafc044-4902-4058-8037-49399bf6bf7f
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 96e827b57e804cc91798859bc906e49046decede
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="implement-mobile-engagement-with-gaming-app"></a>Implementace s herní aplikace Mobile Engagement
## <a name="overview"></a>Přehled
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

Spuštění herní má spuštění nové aplikace herní play role, strategie rybolovu na základě. Hry byl spuštěný a funkční po dobu 6 měsíců. Tato hra je velký úspěšné a má miliony souborů ke stažení a uchovávání je velmi vysoká ve srovnání s jinými aplikacemi herní spuštění. Na zasedání čtvrtletně zkontrolujte souhlas zúčastněným v prostředí, že které potřebují ke zvýšení průměrný výnos na uživatele. Premium ve hře balíčky jsou k dispozici jako speciální nabídky. Tyto sady pro herní umožňují uživatelům vzhled a výkonu svých rybářských řádky a lures nebo tackles ve hře upgradu. Balíček prodeje jsou však velmi nízké. Proto se rozhodnou nejprve k analýze zkušeností zákazníků se nástroj pro analýzu a vyvíjet engagement program a zvyšte prodeje pomocí pokročilé segmentace.

Na základě [Azure Mobile Engagement – Příručka Začínáme s osvědčenými postupy](mobile-engagement-getting-started-best-practices.md) vytváří strategii zapojení.

## <a name="objectives-and-kpis"></a>Cíle a klíčové ukazatele výkonu.
Splňovat klíčových zúčastněných stran pro příslušnou hru. Všechny ke shodě o jeden hlavní cíl - zvýšit prodej balíček premium 15 %. Vytvoří obchodní klíčové ukazatele výkonu (KPI) k měření a disku tohoto cíle

* Na kterou úroveň ve hře jsou tyto balíčky zakoupili?
* Co je tržby na uživatele, na relaci, za týden a za měsíc?
* Jaké jsou typy Oblíbené nákupu?

Část 1 / [– Příručka Začínáme](mobile-engagement-getting-started-best-practices.md) vysvětluje, jak k definování cílů a klíčových ukazatelů výkonu. 

U obchodních klíčových ukazatelů výkonu nyní definována vytvoří správce produktu Mobile klíčové ukazatele zapojení k určení nového uživatele trendy a jejich uchovávání.

* Monitorování uchovávání a použít v rámci následující intervaly: každý den, každý 2 dní, každý týden, měsíc a každé 3 měsíce
* Počty aktivního uživatele
* Hodnocení aplikace v úložišti

Na základě doporučení od týmu IT, byly přidány následující technické klíčové ukazatele výkonu zodpovědět následující otázky:

* Co je cesta k uživatele (je navštívené stránky, která, kolik času stráví na něm)
* Počet havárií nebo oznámení chyb došlo na relaci
* Jaké verze operačního systému jsou mé uživatelé používají?
* Jaká je průměrná velikost obrazovky pro svoje uživatele?
* Jaký druh připojení k Internetu Moji uživatelé mají?

Pro každý ukazatel KPI Mobile produktu Manager určuje data Jana potřebuje, a kde se nachází v jeho scénářem.

## <a name="engagement-program-and-integration"></a>Integrace produktů a program zapojení
Před vytvořením program k pokročilé zapojení, ředitel projektu Mobile starosti projekt by měl mít hluboké znalosti, jak a kdy produkty jsou spotřebovávají uživatele.

Po 3 měsíce ředitel projektu Mobile shromáždil dostatek dat ke zvýšení jeho prodej v aplikaci nabízená oznámení. Dozví, který:

* První nákup obvykle dochází na úrovni 14. Pro 90 % těchto případech je nákupu nové Legendární zbraní $3.
* V těchto případech 80 % uživatelé, kteří provedli nákup, pokračovat s tímto produktem a nastavit další uzavře.
* Uživatelé, kteří uplynulo úrovně 20, spustit zatěžovat více než 10 za týden.
* Uživatelé zpravidla koupit premium balíčky na úrovni 16, 24 a 32.

Díky této analýze ředitel projektu Mobile rozhodne vytvořit konkrétní nabízená oznámení pořadí k nárůstu prodeje aplikace. Vytvoří tři sekvencí nabízených oznámení, které mu volá: Vítejte program, Program prodeje a neaktivní programu. Další informace najdete v části [Playbooks](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks) ![][1]

<!--Image references-->

[1]: ./media/mobile-engagement-game-scenario/notification-scenario.png

<!--Link references-->
