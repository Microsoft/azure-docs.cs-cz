---
title: Plánování údržby Azure události – Azure SQL Database | Dokumentace Microsoftu
description: Zjistěte, jak připravit pro události plánované údržby pro Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
manager: craigg
ms.date: 01/30/2019
ms.openlocfilehash: 928338a911efae051df7164239dbd19f9317338a
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824045"
---
# <a name="planning-for-azure-maintenance-events-in-azure-sql-database"></a>Plánování údržby Azure událostí ve službě Azure SQL Database

Zjistěte, jak připravit pro události plánované údržby v Azure SQL database.

## <a name="what-is-a-planned-maintenance-event"></a>Co je události plánované údržby

Pro každou databázi Azure SQL DB zachová kvorum replik databáze, kde jedna replika je primární. Na celou dobu primární replika musí být online údržby a nejméně jedna sekundární replika musí být v pořádku. Během plánované údržby bude členy kvora databáze Přejít offline jeden po druhém, se záměrem je jedním reagovat primární repliky a nejméně jedna sekundární replika online zajistit bez výpadků klienta. Když primární repliky je potřeba uvést do offline režimu, Rekonfigurace/převzetí služeb při selhání procesu dojde, ve které jedna sekundární replika se stane nová primární.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Co můžete očekávat během plánované údržby

Rekonfigurací/převzetí služeb při selhání obecně dokončit během 30 sekundách – průměr je 8 sekund. Pokud již připojeni, aplikace musí znovu připojit k nové primární replice v pořádku kopie databáze. Nové připojení dojde k pokusu o, zatímco databáze prochází změny konfigurace před novou primární repliku je online, získáte chyby 40613 (databáze není k dispozici): "Databáze {databasename}' na serveru {servername} není aktuálně k dispozici. Opakujte pokus o připojení později. ". Pokud má vaše databáze dlouho probíhající dotazy, tento dotaz se odešle při změny konfigurace a bude nutné restartovat.

## <a name="retry-logic"></a>Logika opakování

Všechny klientské produkční aplikace, která se připojuje k Cloudová databázová služba by měla implementovat robustní připojení [Logika opakování](sql-database-connectivity-issues.md#retry-logic-for-transient-errors). To zmírnit tyto situace a by měly obecně chyby transparentní pro koncového uživatele.

## <a name="frequency"></a>Frekvence

V průměru 1.7 události plánované údržby dojít každý měsíc.

## <a name="resource-health"></a>Resource Health

Pokud vaše databáze SQL dochází k selhání přihlášení, zkontrolujte [Resource Health](../service-health/resource-health-overview.md#getting-started) okna [webu Azure portal](https://portal.azure.com) pro aktuální stav. Historie stavu oddíl obsahuje výpadek důvod pro každou jednotlivou událost (Pokud je k dispozici).


## <a name="next-steps"></a>Další postup

- Další informace o [Resource Health](sql-database-resource-health.md) pro službu SQL Database
- Další informace o logiku opakování, naleznete v tématu [Logika opakování pro přechodné chyby](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
