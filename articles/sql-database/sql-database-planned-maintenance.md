---
title: Plánování událostí údržby Azure – Azure SQL Database
description: Přečtěte si, jak připravit na plánované události údržby pro Azure SQL Database.
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
ms.openlocfilehash: 5598d5a465645ce20d9244011de6d9cef47ac3c4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687625"
---
# <a name="planning-for-azure-maintenance-events-in-azure-sql-database"></a>Plánování událostí údržby Azure v Azure SQL Database

Přečtěte si, jak připravit na plánované události údržby ve službě Azure SQL Database.

## <a name="what-is-a-planned-maintenance-event"></a>Co je plánovaná událost údržby

Pro každou databázi Azure SQL DB udržuje kvorum replik databáze, kde jedna replika je primární. V každé době musí být primární replika online obsluha a aspoň jedna sekundární replika musí být v pořádku. Během plánované údržby se členové kvora databáze po jednom přejdou do offline režimu, s cílem zajistit, aby existovala jedna primární replika a aspoň jedna sekundární replika online, aby nedocházelo k výpadkům klientů. Když se primární replika musí převést do režimu offline, dojde k rekonfiguraci nebo procesu převzetí služeb při selhání, kdy se jedna sekundární replika stane novou primární.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Co očekávat během plánované události údržby

Rekonfigurace/převzetí služeb při selhání se obvykle dokončuje do 30 sekund – průměr je 8 sekund. Pokud už je vaše aplikace připojená, musí se znovu připojit ke správné kopii nové primární repliky vaší databáze. Pokud dojde k pokusu o nové připojení, zatímco se databáze předá novou primární replikou v online režimu, zobrazí se chyba 40613 (databáze není k dispozici): "databáze {DatabaseName} na serveru {ServerName}" není aktuálně k dispozici. Zkuste prosím připojení znovu později. ". Pokud má databáze dlouho běžící dotaz, bude tento dotaz během opakované konfigurace přerušen a bude nutné jej restartovat.

## <a name="retry-logic"></a>Logika opakování

Každá provozní aplikace klienta, která se připojuje ke cloudové databázové službě, by měla implementovat robustní [logiku opakování](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)připojení. To vám pomůže zmírnit tyto situace a obvykle by měly být chyby transparentní pro koncového uživatele.

## <a name="frequency"></a>Frequency

V průměru se v každém měsíci vyskytují plánované události údržby 1,7.

## <a name="resource-health"></a>Resource Health

Pokud v databázi SQL dochází k chybám přihlášení, podívejte se do okna [Resource Health](../service-health/resource-health-overview.md#get-started) v [Azure Portal](https://portal.azure.com) aktuálního stavu. Část historie stavu obsahuje důvod výpadku každé události (Pokud je dostupná).


## <a name="next-steps"></a>Další kroky

- Další informace o [Resource Health](sql-database-resource-health.md) pro SQL Database
- Další informace o logice opakování najdete v tématu [logika opakování pro přechodné chyby](sql-database-connectivity-issues.md#retry-logic-for-transient-errors) .
