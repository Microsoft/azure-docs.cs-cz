---
title: Monitorování stavu SQL Database pomocí Azure Resource Health
description: Pomocí Azure Resource Health můžete monitorovat stav SQL Database, pomůže vám diagnostikovat a získat podporu v případě, že problém Azure ovlivňuje vaše prostředky SQL.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 02/26/2019
ms.openlocfilehash: 10ce2da8ae8bcd8a12f0972d437a84a37a8390b9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687505"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database"></a>Řešení potíží s připojením pro Azure SQL Database pomocí Resource Health

## <a name="overview"></a>Přehled

[Resource Health](../service-health/resource-health-overview.md#get-started) pro SQL Database vám pomůže s diagnostikou a získáním podpory v případě, že problém Azure ovlivňuje vaše prostředky SQL. Informuje o aktuálním a dřívějším stavu prostředků a pomáhá zmírnit problémy. Resource Health poskytuje technickou podporu, když potřebujete pomoc při potížích se službami Azure.

![Přehled](./media/sql-database-resource-health/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Kontroly stavu

Resource Health určuje stav prostředku SQL tím, že prozkoumá úspěch a neúspěch přihlášení k prostředku. V současné době Resource Health pro váš prostředek SQL DB kontroluje pouze selhání přihlášení z důvodu chyby systému a nejedná se o chybu uživatele. Stav Resource Health se aktualizuje každých 1-2 minut.

## <a name="health-states"></a>Stav

### <a name="available"></a>K dispozici.

Stav **k dispozici** znamená, že Resource Health nezjistila selhání přihlášení kvůli chybám systému v prostředku SQL.

![K dispozici.](./media/sql-database-resource-health/sql-resource-health-available.jpg)

### <a name="degraded"></a>Snížený výkon

Stav **Degradovaný** znamená, že služba Resource Health zjistila většinu úspěšných přihlášení, ale také několik selhání. Jedná se o nejpravděpodobnější chyby při přechodných přihlášeních. Chcete-li snížit dopad problémů s připojením způsobených přechodnými chybami přihlášení, implementujte prosím v kódu [logiku opakování](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) .

![Snížený výkon](./media/sql-database-resource-health/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Neaktivní

Stav **nedostupný** znamená, že Resource Health zjistila konzistentní selhání přihlášení k vašemu prostředku SQL. Pokud prostředek zůstane v tomto stavu delší dobu, obraťte se prosím na podporu.

![Neaktivní](./media/sql-database-resource-health/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Není známo

Stav **Neznámý** znamená, že Resource Health nedostaly informace o tomto prostředku po dobu více než 10 minut. I když tento stav není konečným náznakem stavu prostředku, jedná se o důležitý datový bod v procesu řešení potíží. Pokud je prostředek spuštěný podle očekávání, stav prostředku se změní na k dispozici po několika minutách. Pokud máte problémy s prostředkem, neznámý stav může navrhnout, aby daný prostředek ovlivnila událost na platformě.

![Není známo](./media/sql-database-resource-health/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Historické informace

V části Historie stavu Resource Health můžete přistupovat až ke 14 dnům historie stavu. Oddíl bude obsahovat také důvod výpadku (Pokud je k dispozici) pro výpadky hlášené Resource Health. V současné době Azure ukazuje výpadky vašeho prostředku databáze SQL ve dvouminutových intervalech. Ve skutečnosti výpadky pravděpodobně netrvaly ani minutu – průměr je 8 s.

### <a name="downtime-reasons"></a>Důvody výpadku

Když se SQL Database výpadky, provede se analýza, která určí důvod. V případě, že je k dispozici, je důvod výpadku uveden v části Historie stavu Resource Health. Důvody výpadků se obvykle publikují 30 minut po události.

#### <a name="planned-maintenance"></a>Plánovaná údržba

Infrastruktura Azure pravidelně provádí plánovanou údržbu – upgrade hardwarových nebo softwarových součástí v datacentru. I když probíhá údržba databáze, SQL může ukončit některá existující připojení a odmítat nové. Neúspěšné přihlášení, ke kterým došlo během plánované údržby, je obvykle přechodný a [logika opakování](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) pomáhá snižovat dopad. Pokud budete pokračovat v práci s chybami přihlášení, obraťte se prosím na podporu.

#### <a name="reconfiguration"></a>Rekonfigurace

Rekonfigurace se považují za přechodné podmínky a očekává se čas od času. Tyto události se můžou aktivovat vyrovnáváním zatížení nebo selháním softwaru nebo hardwaru. Všechny klientské aplikace, které se připojují ke cloudové databázi, by měly implementovat robustní [logiku opakování](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)připojení, protože by to mohlo přispět k zmírnění těchto situací a měla by obecně být chybně transparentní pro koncového uživatele.

## <a name="next-steps"></a>Další kroky

- Další informace o [logice opakování pro přechodné chyby](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
- [Oprava, diagnostika a prevence chyb připojení SQL](./sql-database-connectivity-issues.md)
- Další informace o [konfiguraci výstrah Resource Health](../service-health/resource-health-alert-arm-template-guide.md)
- Získání přehledu o [Resource Health](../service-health/resource-health-overview.md)
- [Nejčastější dotazy k Resource Health](../service-health/resource-health-faq.md)
