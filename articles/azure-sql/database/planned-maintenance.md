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
ms.reviewer: sstein
ms.date: 1/21/2021
ms.openlocfilehash: d38ac9731959cf9a23052753b09c9e7819846705
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101664113"
---
# <a name="plan-for-azure-maintenance-events-in-azure-sql-database-and-azure-sql-managed-instance"></a>Plánování událostí údržby Azure v Azure SQL Database a spravované instanci Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Přečtěte si, jak připravit na plánované události údržby ve vaší databázi v Azure SQL Database a v Azure SQL Managed instance.

## <a name="what-is-a-planned-maintenance-event"></a>Co je plánovaná událost údržby?

Aby bylo možné zajistit Azure SQL Database a služby Azure SQL Managed instance zabezpečené, kompatibilní, stabilní a výkonné, provedou se komponenty služby skoro nepřetržitě. Díky moderní a robustní architektuře služeb a inovativním technologiím, jako jsou [Hot patching](https://aka.ms/azuresqlhotpatching), je většina aktualizací plně transparentní a bez dopadu na dostupnost služeb. Stále se u několika typů aktualizací nezpůsobí krátká přerušení služeb a vyžadují zvláštní zpracování. 

Pro každou databázi, Azure SQL Database a Azure SQL Managed instance udržují kvorum replik databáze, kde jedna replika je primární. V každém okamžiku musí být primární replika online obsluha a aspoň jedna sekundární replika musí být v pořádku. Během plánované údržby se členové kvora databáze po jednom přejdou do offline režimu, s cílem zajistit, aby existovala jedna primární replika a aspoň jedna sekundární replika online, aby nedocházelo k výpadkům klientů. Když se primární replika musí převést do režimu offline, dojde k rekonfiguraci nebo procesu převzetí služeb při selhání, kdy se jedna sekundární replika stane novou primární.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Co očekávat během plánované události údržby

Událost údržby může způsobit jedno nebo několik převzetí služeb při selhání v závislosti na Constellation primárních a sekundárních replik na začátku události údržby. V průměru 1,7 dojde k převzetí služeb při selhání za plánovanou událost údržby. Rekonfigurace/převzetí služeb při selhání se většinou dokončí do 30 sekund. Průměrná hodnota je osm sekund. Pokud už je vaše aplikace připojená, musí se znovu připojit k nové primární replice vaší databáze. Pokud dojde k pokusu o nové připojení, zatímco se databáze předá novou primární replikou v online režimu, zobrazí se chyba 40613 (databáze není k dispozici): *"databáze {DatabaseName} na serveru {ServerName}" není aktuálně k dispozici. Zkuste prosím připojení znovu později. "* Pokud má vaše databáze dlouhotrvající dotaz, bude tento dotaz během opakované konfigurace přerušen a bude nutné ho restartovat.

## <a name="how-to-simulate-a-planned-maintenance-event"></a>Postup simulace plánované události údržby

Zajištění odolnosti klientských aplikací proti událostem údržby před nasazením do produkčního prostředí vám pomůže zmírnit riziko chyb aplikací a přispěje k dostupnosti aplikací pro koncové uživatele. Při provádění [ručního převzetí služeb při selhání](https://aka.ms/mifailover-techblog) prostřednictvím PowerShellu, CLI nebo REST API můžete testovat chování klientské aplikace během plánovaných událostí údržby. Vytvoří stejné chování jako událost údržby při převedení primární repliky do offline režimu.

## <a name="retry-logic"></a>Logika opakování

Každá provozní aplikace klienta, která se připojuje ke cloudové databázové službě, by měla implementovat robustní [logiku opakování](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)připojení. To pomůže zajistit, aby převzetí služeb při selhání bylo transparentní pro koncové uživatele nebo alespoň v negativních dopadech.

## <a name="resource-health"></a>Stav prostředků

Pokud v databázi dochází k chybám přihlášení, podívejte se do okna [Resource Health](../../service-health/resource-health-overview.md#get-started) v [Azure Portal](https://portal.azure.com) aktuálního stavu. Část historie stavu obsahuje důvod výpadku každé události (Pokud je dostupná).

## <a name="maintenance-window-feature"></a>Funkce časového období údržby

Funkce časového období údržby umožňuje konfiguraci předvídatelných plánů oken údržby pro oprávněné databáze SQL Azure a spravované instance SQL. Další informace najdete v časovém [intervalu pro správu a údržbu](maintenance-window.md) .

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Resource Health](resource-health-to-troubleshoot-connectivity.md) pro Azure SQL Database a Azure SQL Managed instance.
- Další informace o logice opakování najdete v tématu [logika opakování pro přechodné chyby](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors).
- Nakonfigurujte plány časových období údržby pomocí funkce časový interval pro správu a [údržbu](maintenance-window.md) .
