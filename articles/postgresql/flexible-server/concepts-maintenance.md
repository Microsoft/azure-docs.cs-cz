---
title: Plánovaná údržba – Azure Database for PostgreSQL – flexibilní Server
description: Tento článek popisuje funkci naplánované údržby v Azure Database for PostgreSQL-flexibilním serveru.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 50ef040f1cb7d8c533ec5ee31e9bffa2e6dca2f5
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478008"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--flexible-server"></a>Plánovaná údržba na flexibilním serveru Azure Database for PostgreSQL
 
Azure Database for PostgreSQL – flexibilní server provádí pravidelnou údržbu, aby vaše spravovaná databáze zůstala zabezpečená, stabilní a aktuální. Během údržby server získá nové funkce, aktualizace a opravy.
 
> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.
 
## <a name="selecting-a-maintenance-window"></a>Výběr časového období údržby
 
Údržbu můžete naplánovat během konkrétního dne v týdnu a časového okna v daném dni. Nebo můžete nechat systém vybrat den a časový interval pro automatické vyzvednutí. V obou případech systém upozorní pět dní před spuštěním jakékoli údržby. Systém vám také informuje o zahájení údržby a po úspěšném dokončení.
 
Oznámení o nadcházející plánované údržbě mohou být:
 
* E-mail na konkrétní adresu
* Odeslání e-mailu do role Azure Resource Manager
* Odesílá se v textové zprávě (SMS) do mobilních zařízení.
* Nabízené oznámení v aplikaci Azure
* Hlasová zpráva
 
Při zadávání předvoleb pro plán údržby můžete vybrat den v týdnu a časové okno. Pokud ho nezadáte, server vybere časový interval od 23:00 do 7:00 v oblasti vašeho serveru. Můžete definovat různé plány pro každý flexibilní Server v rámci vašeho předplatného Azure. 
 
> [!IMPORTANT]
> Mezi událostmi úspěšné plánované údržby serveru je obvykle alespoň 30 dnů.
>
> V případě důležité nouzové aktualizace, například kvůli vážnému ohrožení zabezpečení, však můžete oznámení obdržet méně než pět dnů předem. Důležitá aktualizace se na váš server může nainstalovat i v případě, že během posledních 30 dnů úspěšně proběhla plánovaná údržba.

Nastavení plánování můžete kdykoli aktualizovat. Pokud je u flexibilního serveru naplánovaná údržba a aktualizujete Předvolby plánování, aktuální zavedení bude pokračovat podle plánu a změna nastavení plánování vstoupí v platnost po úspěšném dokončení další naplánované údržby.

U každého flexibilního serveru v předplatném Azure můžete definovat plán spravovaný systémem nebo vlastní plán.  
* Pomocí vlastního plánu můžete pro server určit okno údržby, a to výběrem dne v týdnu a časového intervalu v jednom hodině.  
* V rámci plánu spravovaného systémem vybere systém v časovém rámci vašeho serveru v jednom hodinovém okně mezi 23:00 a 7am.  

V rámci provádění změn se tyto aktualizace aplikují na servery nakonfigurované s plánem spravovaným systémem, a to jako servery s vlastním plánem po minimálním rozpětí 7 dnů v dané oblasti. Pokud máte v úmyslu dostávat na loďstva serverů pro vývoj a testování nějaké prvotní aktualizace, doporučujeme pro servery používané ve vývojovém a testovacím prostředí nakonfigurovat plán spravovaný systémem. To vám umožní získat nejnovější aktualizaci jako první v prostředí pro vývoj/testování pro testování a vyhodnocení pro ověřování. Pokud narazíte na jakékoli chování nebo zásadní změny, budete je muset vyřešit ještě před tím, než bude stejná aktualizace nasazená na produkčních serverech s vlastním plánem spravovaným pomocí vlastního plánu. Aktualizace začne zavádět na vlastní a flexibilní servery, které jsou po 7 dnech a aplikovány na váš server v definovaném časovém období údržby. V současné době neexistuje možnost odložení aktualizace po odeslání oznámení. Vlastní plán se doporučuje jenom pro produkční prostředí. 

Ve výjimečných případech může být událost údržby zrušena systémem nebo se její úspěšné dokončení může selhat. Pokud se aktualizace nezdaří, aktualizace se vrátí a předchozí verze binárních souborů se obnoví. V takových neúspěšných scénářích aktualizace můžete během časového období údržby stále docházet k restartování serveru. Pokud je aktualizace zrušená nebo se nezdařila, systém vytvoří oznámení o události zrušení nebo selhání údržby, která vás upozorní. Další pokus o provedení údržby bude naplánován podle aktuálního nastavení plánování a oznámení se vám bude nacházet za pět dní předem. 

 
## <a name="next-steps"></a>Další kroky
 
* Informace o tom, jak [změnit plán údržby](how-to-maintenance-portal.md)
* Naučte se [dostávat oznámení o nadcházející údržbě](../../service-health/service-notifications.md) pomocí Azure Service Health
* Přečtěte si, jak [nastavit výstrahy o nadcházejících událostech plánované údržby](../../service-health/resource-health-alert-monitor-guide.md) .
