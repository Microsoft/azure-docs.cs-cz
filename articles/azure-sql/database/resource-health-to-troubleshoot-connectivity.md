---
title: Monitorování stavu databáze pomocí Azure Resource Health
description: Pomocí Azure Resource Health můžete monitorovat stav služby Azure SQL Database a Azure SQL Managed instance, což vám pomůže diagnostikovat a získat podporu v případě, že problém Azure ovlivňuje vaše prostředky SQL.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: wiassaf, sstein
ms.date: 03/24/2021
ms.openlocfilehash: 4434c796a41126444b8785440427123e88e0b528
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107784"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database-and-azure-sql-managed-instance"></a>Řešení potíží s připojením pro Azure SQL Database a spravovanou instanci SQL Azure pomocí Resource Health
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[Resource Health](../../service-health/resource-health-overview.md#get-started) pro Azure SQL Database a Azure SQL Managed instance pomáhají diagnostikovat a získat podporu v případě, že problém Azure ovlivňuje vaše prostředky SQL. Informuje o aktuálním a dřívějším stavu prostředků a pomáhá zmírnit problémy. Resource Health poskytuje technickou podporu, když potřebujete pomoc s problémy se službami Azure.

![Přehled](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Kontroly stavu

Resource Health určuje stav prostředku SQL tím, že prozkoumá úspěch a neúspěch přihlášení k prostředku. V současné době Resource Health pro prostředek SQL Database prozkoumávat pouze selhání přihlášení z důvodu chyby systému a chyby uživatele. Stav Resource Health se aktualizuje každých 1 až 2 minut.

## <a name="health-states"></a>Stav

### <a name="available"></a>K dispozici

Stav **k dispozici** znamená, že Resource Health nezjistila selhání přihlášení kvůli chybám systému v prostředku SQL.

![K dispozici.](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-available.jpg)

### <a name="degraded"></a>Snížený výkon

Stav **Degradovaný** znamená, že služba Resource Health zjistila většinu úspěšných přihlášení, ale také několik selhání. Jedná se o nejpravděpodobnější chyby při přechodných přihlášeních. Chcete-li snížit dopad problémů s připojením způsobených přechodnými chybami přihlášení, implementujte v kódu [logiku opakování](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) .

![Snížený výkon](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Neaktivní

Stav **nedostupný** znamená, že Resource Health zjistila konzistentní selhání přihlášení k vašemu prostředku SQL. Pokud prostředek zůstane v tomto stavu delší dobu, obraťte se na podporu.

![Neaktivní](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Neznámý

Stav **Neznámý** znamená, že Resource Health nedostaly informace o tomto prostředku po dobu více než 10 minut. I když tento stav není konečným náznakem stavu prostředku, jedná se o důležitý datový bod v procesu řešení potíží. Pokud je prostředek spuštěný podle očekávání, stav prostředku se změní na k dispozici po několika minutách. Pokud máte problémy s prostředkem, neznámý stav může navrhnout, aby daný prostředek ovlivnila událost na platformě.

![Neznámý](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Historické informace

V části Historie stavu Resource Health můžete přistupovat až ke 14 dnům historie stavu. Oddíl bude obsahovat také důvod výpadku (Pokud je k dispozici) pro výpadky hlášené Resource Health. V současné době Azure zobrazuje výpadky vašeho databázového prostředku na základě dvou minut. Ve skutečnosti výpadky pravděpodobně netrvaly ani minutu. Průměr je 8 sekund.

### <a name="downtime-reasons"></a>Důvody výpadku

Když dojde k výpadku databáze, provede se analýza a určí se důvod. V případě, že je k dispozici, je důvod výpadku uveden v části Historie stavu Resource Health. Důvody výpadku jsou obvykle publikovány během 45 minut po události.

#### <a name="planned-maintenance"></a>Plánovaná údržba

Infrastruktura Azure pravidelně provádí plánovanou údržbu – upgrade hardwarových nebo softwarových součástí v datacentru. I když probíhá údržba databáze, může Azure SQL ukončit některá existující připojení a odmítat nové. Neúspěšné přihlášení, ke kterým došlo během plánované údržby, je obvykle přechodný a [logika opakování](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) pomáhá snižovat dopad. Pokud budete pokračovat v práci s chybami přihlášení, obraťte se na podporu.

#### <a name="reconfiguration"></a>Rekonfigurace

Rekonfigurace se považují za přechodné podmínky a očekává se čas od času. Tyto události se můžou aktivovat vyrovnáváním zatížení nebo selháním softwaru nebo hardwaru. Všechny klientské aplikace, které se připojují ke cloudové databázi, by měly implementovat robustní [logiku opakování](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)připojení, protože by to mohlo přispět k zmírnění těchto situací a měla by obecně být chybně transparentní pro koncového uživatele.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [logice opakování pro přechodné chyby](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors).
- [Řešení potíží, diagnostika a prevence chyb připojení SQL](troubleshoot-common-connectivity-issues.md)
- Přečtěte si další informace o [konfiguraci výstrah Resource Health](../../service-health/resource-health-alert-arm-template-guide.md).
- Získejte přehled o [Resource Health](../../service-health/resource-health-overview.md).
- Přečtěte si [Resource Health Nejčastější dotazy](../../service-health/resource-health-faq.md).
