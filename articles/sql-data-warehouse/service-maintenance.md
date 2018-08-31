---
title: Plánovaná údržba – Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Zjistěte, jak připravit pro události plánované údržby pro Azure SQL Data Warehouse.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/19/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 8b5b94c959ae9820cab338f3ee3317fb3ee166d6
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286597"
---
# <a name="planning-for-maintenance-on-your-azure-sql-data-warehouse"></a>Plánování údržby ve službě Azure SQL data warehouse

Zjistěte, jak připravit pro události plánované údržby ve službě Azure SQL data warehouse.

## <a name="what-is-a-planned-maintenance-event"></a>Co je události plánované údržby?
Události plánované údržby je okno dobu, kdy váš datový sklad musí být v režimu offline pro operace údržby. Tyto události jsou příležitosti pro použití služby upgrady, nové funkce nebo opravy. 

## <a name="frequency"></a>Frekvence
V průměru alespoň jeden plánované údržby dojde k události každý měsíc. 

## <a name="notifications-and-downtime"></a>Oznámení a výpadky
Zobrazí se oznámení před každé události plánované údržby. Události údržby zruší všechny spuštěné dotazy a váš datový sklad převede do režimu offline. Očekávaný výpadek pro každý datový sklad je přibližně 30 minut. Oznámení můžete očekávat při dokončení údržby. 

## <a name="setting-up-alerts"></a>Nastavení výstrah

Doporučujeme používat [Azure Monitor](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md) nastavit plánované údržby upozornění protokolů. Výstrahy můžete naplánovat může požadovaná Údržba, chcete-li minimalizovat dopad na vaši firmu. 

Nastavení oznámení, použijte tyto [protokolu upozornění pokyny](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md). 

## <a name="next-steps"></a>Další postup
Další informace o monitorování najdete v tématu [monitorování vaší úlohy](sql-data-warehouse-manage-monitor.md).
