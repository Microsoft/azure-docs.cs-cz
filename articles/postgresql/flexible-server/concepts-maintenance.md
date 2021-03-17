---
title: Plánovaná údržba – Azure Database for PostgreSQL – flexibilní Server
description: Tento článek popisuje funkci naplánované údržby v Azure Database for PostgreSQL-flexibilním serveru.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: ffee15776a48b6495f78b6becf81c620e1dc4d69
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336305"
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
* Vložené jako oznámení do aplikace Azure
* Doručení jako hlasová zpráva
 
Při zadávání předvoleb pro plán údržby můžete vybrat den v týdnu a časové okno. Pokud ho nezadáte, server vybere časový interval od 23:00 do 7:00 v oblasti vašeho serveru. Můžete definovat různé plány pro každý flexibilní Server v rámci vašeho předplatného Azure. 
 
> [!IMPORTANT]
> Mezi událostmi úspěšné plánované údržby serveru je obvykle alespoň 30 dnů.
>
> V případě důležité nouzové aktualizace, například kvůli vážnému ohrožení zabezpečení, však můžete oznámení obdržet méně než pět dnů předem. Důležitá aktualizace se na váš server může nainstalovat i v případě, že během posledních 30 dnů úspěšně proběhla plánovaná údržba.

Nastavení plánování můžete kdykoli aktualizovat. Pokud je naplánovaná údržba pro váš flexibilní Server a aktualizujete Předvolby plánování, aktuální událost bude pokračovat podle plánu a změna nastavení plánování vstoupí v platnost po úspěšném dokončení. 

Pokud systém zruší událost údržby nebo se nepodaří úspěšně dokončit, systém vytvoří oznámení o události zrušení nebo selhání údržby v uvedeném pořadí. Další pokus o provedení údržby bude naplánován podle aktuálního nastavení plánování a oznámení se vám bude nacházet za pět dní předem.
 
## <a name="next-steps"></a>Další kroky
 
* Informace o tom, jak [změnit plán údržby](how-to-maintenance-portal.md)
* Naučte se [dostávat oznámení o nadcházející údržbě](../../service-health/service-notifications.md) pomocí Azure Service Health
* Přečtěte si, jak [nastavit výstrahy o nadcházejících událostech plánované údržby](../../service-health/resource-health-alert-monitor-guide.md) .
