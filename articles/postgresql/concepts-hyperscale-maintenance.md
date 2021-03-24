---
title: Plánovaná údržba – Azure Database for PostgreSQL – škálovatelné (Citus)
description: Tento článek popisuje funkci naplánované údržby v Azure Database for PostgreSQL-Citus (škálování).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 0d11ec8815cc0f5082f95c5331321f043e86eece
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027568"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--hyperscale-citus"></a>Naplánovaná údržba v Azure Database for PostgreSQL – Citus (škálování)

Azure Database for PostgreSQL – Citus (škálování na více bitů) provádí pravidelnou údržbu, aby vaše spravovaná databáze zůstala zabezpečená, stabilní a aktuální.  Během údržby získají všechny uzly ve skupině serverů nové funkce, aktualizace a opravy.

Klíčovou funkcí naplánované údržby pro Citus () jsou tyto:

* Aktualizace se na všech uzlech ve skupině serverů aplikují na stejnou dobu.
* Oznámení o nadcházející údržbě se účtují Azure Service Health pět dní předem.
* Obvykle je mezi úspěšnými událostmi údržby pro skupinu serverů aspoň 30 dní.

## <a name="notification-about-upcoming-maintenance"></a>Oznámení o nadcházející údržbě

Oznámení o nadcházející plánované údržbě se publikují na Azure Service Health a můžou být:

* E-mail na konkrétní adresu
* Odeslání e-mailu do role Azure Resource Manager
* Odesílá se v textové zprávě (SMS) do mobilních zařízení.
* Nabízené oznámení v aplikaci Azure
* Hlasová zpráva

> [!IMPORTANT]
> V případě úspěšné plánované události údržby pro skupinu serverů je obvykle k dispozici alespoň 30 dní.
>
> V případě důležité nouzové aktualizace, například kvůli vážnému ohrožení zabezpečení, však můžete oznámení obdržet méně než pět dnů předem. Důležitá aktualizace se na váš server může nainstalovat i v případě, že během posledních 30 dnů úspěšně proběhla plánovaná údržba.

Pokud údržba selže nebo se zruší, systém vytvoří oznámení.
Znovu se pokusí o údržbu podle aktuálního nastavení plánování a upozorní vás pět dní před další událostí údržby.

## <a name="next-steps"></a>Další kroky

* Naučte se [dostávat oznámení o nadcházející údržbě](../service-health/service-notifications.md) pomocí Azure Service Health
* Přečtěte si, jak [nastavit výstrahy o nadcházejících událostech plánované údržby](../service-health/resource-health-alert-monitor-guide.md) .
