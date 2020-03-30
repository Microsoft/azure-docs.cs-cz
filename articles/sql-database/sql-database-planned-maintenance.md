---
title: Plánování událostí údržby Azure
description: Zjistěte, jak se připravit na plánované události údržby do databáze Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
ms.date: 01/30/2019
ms.openlocfilehash: ba882176fbe17f7b74c786f421dde8fadd58d9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821321"
---
# <a name="planning-for-azure-maintenance-events-in-azure-sql-database"></a>Plánování událostí údržby Azure ve službě Azure SQL Database

Zjistěte, jak se připravit na plánované události údržby v databázi Azure SQL.

## <a name="what-is-a-planned-maintenance-event"></a>Co je plánovaná událost údržby

Pro každou databázi Udržuje Azure SQL DB kvorum repliky databáze, kde jedna replika je primární. Primární replika musí být vždy online a alespoň jedna sekundární replika musí být v pořádku. Během plánované údržby členové kvora databáze přejde do režimu offline jeden po druhém, s úmyslem, že existuje jedna odpověď primární repliky a alespoň jednu sekundární repliku online, aby bylo zajištěno, že žádný prostoje klienta. Když primární replika musí být uvedena do režimu offline, dojde k procesu změny konfigurace/převzetí služeb při selhání, ve kterém se jedna sekundární replika stane novou primární.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Co očekávat během plánované akce údržby

Rekonfigurace/převzetí služeb při selhání se obvykle dokončí během 30 sekund – průměr je 8 sekund. Pokud již připojen, aplikace musí znovu připojit k funkční kopie nové primární repliky databáze. Pokud dojde k pokusu o nové připojení, zatímco databáze prochází rekonfigurací před novou primární replikou online, zobrazí se chyba 40613 (Databáze není k dispozici): "Databáze {databasename}' na serveru {název_serveru}' není aktuálně k dispozici. Zopakujte připojení později.". Pokud databáze obsahuje dlouho běžící dotaz, bude tento dotaz během rekonfigurace přerušen a bude nutné je restartovat.

## <a name="retry-logic"></a>Logika pro opakování

Každá klientská produkční aplikace, která se připojuje ke službě cloudové databáze, by měla implementovat robustní [logiku opakování](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)připojení . To pomůže zmírnit tyto situace a obecně by měly chyby transparentní pro koncového uživatele.

## <a name="frequency"></a>Frequency

V průměru se každý měsíc uskuteční 1,7 plánovaných událostí údržby.

## <a name="resource-health"></a>Resource Health

Pokud vaše databáze SQL dochází k selhání přihlášení, zkontrolujte okno [Stav prostředků](../service-health/resource-health-overview.md#get-started) na webu [Azure portálu](https://portal.azure.com) aktuální stav. Část Historie stavu obsahuje důvod prostoje pro každou událost (je-li k dispozici).


## <a name="next-steps"></a>Další kroky

- Další informace o [stavu prostředků](sql-database-resource-health.md) pro databázi SQL
- Další informace o logice opakování naleznete v tématu [Logika opakování pro přechodné chyby](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
