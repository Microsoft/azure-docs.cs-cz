---
title: Plánovaná údržba – Azure Database for MySQL – flexibilní Server
description: Tento článek popisuje funkci naplánované údržby v Azure Database for MySQL-flexibilním serveru.
author: niklarin
ms.author: nlarin
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: a2e99440a7c8f33eee9d3c9fe2276ac3868ff4b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91331756"
---
# <a name="scheduled-maintenance-in-azure-database-for-mysql--flexible-server"></a>Plánovaná údržba na flexibilním serveru Azure Database for MySQL

Azure Database for MySQL – flexibilní server provádí pravidelnou údržbu, aby vaše spravovaná databáze zůstala zabezpečená, stabilní a aktuální. Během údržby server získá nové funkce, aktualizace a opravy.

> [!IMPORTANT]
> Azure Database for MySQL – flexibilní Server je ve verzi Preview.

## <a name="select-a-maintenance-window"></a>Vybrat časový interval pro správu a údržbu

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

Nastavení plánování můžete kdykoli aktualizovat. Pokud je naplánovaná údržba pro váš flexibilní Server a aktualizujete Předvolby plánování, aktuální událost bude pokračovat podle plánu a změna nastavení plánování vstoupí v platnost po úspěšném dokončení.

Ve výjimečných případech může být událost údržby zrušena systémem nebo se její úspěšné dokončení může selhat. V takovém případě systém vytvoří oznámení o události zrušení nebo selhání údržby v uvedeném pořadí. Další pokus o provedení údržby bude naplánován podle aktuálního nastavení plánování a oznámení se vám bude nacházet za pět dní předem.

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak [změnit plán údržby](how-to-maintenance-portal.md)
* Naučte se [dostávat oznámení o nadcházející údržbě](../../service-health/service-notifications.md) pomocí Azure Service Health
* Přečtěte si, jak [nastavit výstrahy o nadcházejících událostech plánované údržby](../../service-health/resource-health-alert-monitor-guide.md) .
