---
title: Plánování událostí údržby Azure
description: Přečtěte si, jak připravit na plánované události údržby v Azure SQL Database a v Azure SQL Managed instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
ms.date: 01/30/2019
ms.openlocfilehash: 5bdc3eb8c118c19f90ce1fd92ac5ee156719dacd
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2020
ms.locfileid: "85987204"
---
# <a name="plan-for-azure-maintenance-events-in-azure-sql-database-and-azure-sql-managed-instance"></a>Plánování událostí údržby Azure v Azure SQL Database a spravované instanci Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Přečtěte si, jak připravit na plánované události údržby ve vaší databázi v Azure SQL Database a v Azure SQL Managed instance.

## <a name="what-is-a-planned-maintenance-event"></a>Co je plánovaná událost údržby?

Pro každou databázi, Azure SQL Database a Azure SQL Managed instance udržují kvorum replik databáze, kde jedna replika je primární. V každém okamžiku musí být primární replika online obsluha a aspoň jedna sekundární replika musí být v pořádku. Během plánované údržby se členové kvora databáze po jednom přejdou do offline režimu, s cílem zajistit, aby existovala jedna primární replika a aspoň jedna sekundární replika online, aby nedocházelo k výpadkům klientů. Když se primární replika musí převést do režimu offline, dojde k rekonfiguraci nebo procesu převzetí služeb při selhání, kdy se jedna sekundární replika stane novou primární.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Co očekávat během plánované události údržby

Rekonfigurace/převzetí služeb při selhání se většinou dokončí do 30 sekund. Průměr je 8 sekund. Pokud už je vaše aplikace připojená, musí se znovu připojit ke správné kopii nové primární repliky vaší databáze. Pokud dojde k pokusu o nové připojení, zatímco se databáze předá novou primární replikou v online režimu, zobrazí se chyba 40613 (databáze není k dispozici): "databáze {DatabaseName} na serveru {ServerName}" není aktuálně k dispozici. Opakujte pokus o připojení později.“ Pokud má vaše databáze dlouhotrvající dotaz, bude tento dotaz během opakované konfigurace přerušen a bude nutné ho restartovat.

## <a name="retry-logic"></a>Logika opakování

Každá provozní aplikace klienta, která se připojuje ke cloudové databázové službě, by měla implementovat robustní [logiku opakování](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)připojení. To vám pomůže zmírnit tyto situace a obvykle by měly být chyby transparentní pro koncového uživatele.

## <a name="frequency"></a>Frekvence

V průměru se v každém měsíci vyskytují plánované události údržby 1,7.

## <a name="resource-health"></a>Stav prostředků

Pokud v databázi dochází k chybám přihlášení, podívejte se do okna [Resource Health](../../service-health/resource-health-overview.md#get-started) v [Azure Portal](https://portal.azure.com) aktuálního stavu. Část historie stavu obsahuje důvod výpadku každé události (Pokud je dostupná).

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Resource Health](resource-health-to-troubleshoot-connectivity.md) pro Azure SQL Database a Azure SQL Managed instance.
- Další informace o logice opakování najdete v tématu [logika opakování pro přechodné chyby](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors).
