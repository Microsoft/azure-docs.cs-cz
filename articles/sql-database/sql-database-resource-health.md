---
title: Monitorování stavu databáze pomocí Azure Resource Health
description: Pomocí Azure Resource Health umožňuje monitorovat stav SQL Database, pomáhá diagnostikovat a získáním podpory v případě, že problém Azure ovlivňuje vaše prostředky SQL.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 02/26/2019
ms.openlocfilehash: 9e19e904b47d69444b491dd88ffe49ff812aafc3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79208869"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database"></a>Řešení potíží s připojením pro službu Azure SQL Database pomocí Resource Health

## <a name="overview"></a>Přehled

[Resource Health](../service-health/resource-health-overview.md#get-started) pro SQL Database vám pomůže s diagnostikou a získáním podpory v případě, že problém Azure ovlivňuje vaše prostředky SQL. Informuje o aktuálním a dřívějším stavu prostředků a pomáhá zmírnit problémy. Resource Health poskytuje technickou podporu, když potřebujete pomoc při potížích se službami Azure.

![Přehled](./media/sql-database-resource-health/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Kontroly stavu

Služba Resource Health Určuje stav prostředek SQL tím, že kontroluje úspěšné a neúspěšné přihlášení k prostředku. Stav prostředků pro prostředek databáze SQL v současné době zkontroluje pouze neúspěšná přihlášení z důvodu chyby systému a není chyba uživatele. Stav Resource Health je aktualizován každé 1-2 minuty.

## <a name="health-states"></a>Stavy

### <a name="available"></a>K dispozici

Stav **k dispozici** znamená, že Resource Health nezjistila selhání přihlášení kvůli chybám systému v prostředku SQL.

![K dispozici](./media/sql-database-resource-health/sql-resource-health-available.jpg)

### <a name="degraded"></a>Snížený výkon

Stav **Degradovaný** znamená, že služba Resource Health zjistila většinu úspěšných přihlášení, ale také několik selhání. Toto jsou pravděpodobně přechodné přihlášení chyby. Chcete-li snížit dopad problémů s připojením způsobených přechodnými chybami přihlášení, implementujte prosím v kódu [logiku opakování](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) .

![Snížený výkon](./media/sql-database-resource-health/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Nedostupný

Stav **nedostupný** znamená, že Resource Health zjistila konzistentní selhání přihlášení k vašemu prostředku SQL. Pokud váš prostředek zůstane v tomto stavu delší dobu, kontaktujte prosím podporu.

![Nedostupný](./media/sql-database-resource-health/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Neznámé

Stav **Neznámý** znamená, že Resource Health nedostaly informace o tomto prostředku po dobu více než 10 minut. Přestože tento stav není úplným a rozhodujícím údaj o stavu prostředku, je důležitý datový bod v procesu odstraňování potíží. Pokud prostředek běží podle očekávání, stav prostředku se změní na dostupný za pár minut. Pokud dojde k problémům s prostředkem, neznámý stav může naznačovat, že událost platformy ovlivňuje prostředku.

![Neznámé](./media/sql-database-resource-health/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Historické informace

Dostanete až 14 dní historie stavu v části historie stavu Resource Health. V části, bude obsahovat také z důvodu výpadek (Pokud je k dispozici) pro výpadky hlášených Resource Health. Azure v současné době ukazuje výpadek pro prostředek databáze SQL ve dvouminutových intervalech. Skutečná délka výpadku je pravděpodobně méně než minuty – průměrná je 8s.

### <a name="downtime-reasons"></a>Z důvodů výpadek

Když vaši službu SQL Database dojde k výpadku, provedení analýzy určit důvod. Pokud je k dispozici, v části historie stavu Resource Health hlásí z důvodu výpadek. Důvody výpadků se obvykle publikují 30 minut po události.

#### <a name="planned-maintenance"></a>Plánovaná údržba

Infrastruktura Azure pravidelně provede plánovanou údržbu – upgrade hardwarové nebo softwarové komponenty v datovém centru. Zatímco databáze při údržbě, SQL může ukončit některé existující připojení a odmítnout nové značky. Neúspěšné přihlášení, ke kterým došlo během plánované údržby, je obvykle přechodný a [logika opakování](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) pomáhá snižovat dopad. Pokud budete nadále dochází k chybám přihlášení, kontaktujte prosím podporu.

#### <a name="reconfiguration"></a>Rekonfigurace

Rekonfigurací se považují za přechodné podmínky. proto se očekává, že čas od času. Tyto události mohou být spouštěny zavádění vyrovnávání nebo softwaru a hardwaru. Všechny klientské aplikace, které se připojují ke cloudové databázi, by měly implementovat robustní [logiku opakování](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)připojení, protože by to mohlo přispět k zmírnění těchto situací a měla by obecně být chybně transparentní pro koncového uživatele.

## <a name="next-steps"></a>Další kroky

- Další informace o [logice opakování pro přechodné chyby](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
- [Oprava, diagnostika a prevence chyb připojení SQL](./sql-database-connectivity-issues.md)
- Další informace o [konfiguraci výstrah Resource Health](../service-health/resource-health-alert-arm-template-guide.md)
- Získání přehledu o [Resource Health](../service-health/resource-health-overview.md)
- [Nejčastější dotazy k Resource Health](../service-health/resource-health-faq.md)
