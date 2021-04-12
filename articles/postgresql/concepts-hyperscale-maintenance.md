---
title: Plánovaná údržba – Azure Database for PostgreSQL – škálovatelné (Citus)
description: Tento článek popisuje funkci naplánované údržby v Azure Database for PostgreSQL-Citus (škálování).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: dee7257a296f523dbc9040d65fe68c14edf928f6
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106781"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--hyperscale-citus"></a>Naplánovaná údržba v Azure Database for PostgreSQL – Citus (škálování)

Azure Database for PostgreSQL – Citus (škálování na více bitů) provádí pravidelnou údržbu, aby vaše spravovaná databáze zůstala zabezpečená, stabilní a aktuální.  Během údržby získají všechny uzly ve skupině serverů nové funkce, aktualizace a opravy.

Klíčovou funkcí naplánované údržby pro Citus () jsou tyto:

* Aktualizace se na všech uzlech ve skupině serverů aplikují na stejnou dobu.
* Oznámení o nadcházející údržbě se účtují Azure Service Health pět dní předem.
* Obvykle je mezi úspěšnými událostmi údržby pro skupinu serverů aspoň 30 dní.
* Upřednostňovaný den v týdnu a časový interval pro zahájení údržby lze pro každou skupinu serverů definovat jednotlivě.

## <a name="selecting-a-maintenance-window-and-notification-about-upcoming-maintenance"></a>Výběr časového období údržby a oznámení o nadcházející údržbě

Údržbu můžete naplánovat během konkrétního dne v týdnu a časového okna v daném dni. Nebo můžete nechat systém vybrat den a časové okno automaticky. V obou případech systém upozorní pět dní před spuštěním jakékoli údržby. Systém vám také informuje o tom, kdy je údržba spuštěná a kdy se úspěšně dokončila.

Oznámení o nadcházející plánované údržbě se publikují na Azure Service Health a můžou být:

* E-mail na konkrétní adresu
* Odeslání e-mailu do role Azure Resource Manager
* Odesílá se v textové zprávě (SMS) do mobilních zařízení.
* Nabízené oznámení v aplikaci Azure
* Hlasová zpráva

Při zadávání předvoleb pro plán údržby můžete vybrat den v týdnu a časové okno. Pokud nezadáte, systém vybírá časy mezi 23:00 a 7am v čase v oblasti skupiny serverů. V rámci vašeho předplatného Azure můžete definovat různé plány pro každou skupinu serverů Citus.

> [!IMPORTANT]
> V případě úspěšné plánované události údržby pro skupinu serverů je obvykle k dispozici alespoň 30 dní.
>
> V případě důležité nouzové aktualizace, například kvůli vážnému ohrožení zabezpečení, však můžete oznámení obdržet méně než pět dnů předem. Důležitá aktualizace se na váš server může nainstalovat i v případě, že během posledních 30 dnů úspěšně proběhla plánovaná údržba.

Nastavení plánování můžete kdykoli aktualizovat. Pokud je naplánována údržba skupiny serverů Citus () a aktualizujete plán, existující události budou i nadále naplánovaly. Změna nastavení se projeví po úspěšném dokončení stávajících událostí.

Pokud údržba selže nebo se zruší, systém vytvoří oznámení.
Znovu se pokusí o údržbu podle aktuálního nastavení plánování a upozorní vás pět dní před další událostí údržby.

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak [změnit plán údržby](howto-hyperscale-maintenance.md)
* Naučte se [dostávat oznámení o nadcházející údržbě](../service-health/service-notifications.md) pomocí Azure Service Health
* Přečtěte si, jak [nastavit výstrahy o nadcházejících událostech plánované údržby](../service-health/resource-health-alert-monitor-guide.md) .
