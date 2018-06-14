---
title: Plánované údržby – Azure SQL Data Warehouse | Microsoft Docs
description: Zjistěte, jak připravit pro události plánované údržby do Azure SQL Data Warehouse.
services: sql-data-warehouse
author: antvgski
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/19/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: a273ad4c256267865d3af324f0ef755a6cb75c5c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31800052"
---
# <a name="planning-for-maintenance-on-your-azure-sql-data-warehouse"></a>Plánování údržby v Azure SQL data warehouse

Zjistěte, jak připravit pro události plánované údržby na Azure SQL data warehouse.

## <a name="what-is-a-planned-maintenance-event"></a>Co je události plánované údržby?
Události plánované údržby je okno čas, kdy musí být offline pro operace údržby datového skladu. Tyto události jsou příležitosti pro použití služby upgrady, nové funkce nebo opravy. 

## <a name="frequency"></a>Frekvence
V průměru alespoň jeden události plánované údržby dojde každý měsíc. 

## <a name="notifications-and-downtime"></a>Oznámení a výpadky
Zobrazí se oznámení před každé události plánované údržby. Událost údržby zruší všechny spuštěné dotazy a uvede datového skladu do offline režimu. Očekávaná doba nedostupnosti pro každý datový sklad je přibližně 30 minut. Můžete očekávat oznámení po dokončení údržby. 

## <a name="setting-up-alerts"></a>Nastavení výstrah

Doporučujeme používat [Azure monitorování](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md) nastavit plánované údržby protokolu výstrahy. Výstrahy můžete naplánovat může požadovaná Údržba minimalizovat dopad k vaší firmě. 

Chcete-li nastavit oznámení, použijte tyto [protokolu výstrahy pokyny](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md). 

## <a name="next-steps"></a>Další postup
Další informace o monitorování najdete v tématu [sledovat vaše úlohy](sql-data-warehouse-manage-monitor.md).
