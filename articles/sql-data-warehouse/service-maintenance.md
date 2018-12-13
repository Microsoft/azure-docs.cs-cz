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
ms.openlocfilehash: c1e8f94a0131ace6354d070e932e414a1897260e
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53166298"
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

Doporučujeme používat [Azure Monitor](../azure-monitor/platform/alerts-activity-log-service-notifications.md) nastavit plánované údržby upozornění protokolů. Výstrahy můžete naplánovat může požadovaná Údržba, chcete-li minimalizovat dopad na vaši firmu. 

Nastavení oznámení, použijte tyto [protokolu upozornění pokyny](../azure-monitor/platform/alerts-activity-log-service-notifications.md). 

## <a name="next-steps"></a>Další postup
Další informace o monitorování najdete v tématu [monitorování vaší úlohy](sql-data-warehouse-manage-monitor.md).
