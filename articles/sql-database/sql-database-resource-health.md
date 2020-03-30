---
title: Monitorování stavu databáze pomocí funkce Azure Resource Health
description: Pomocí Azure Resource Health můžete monitorovat stav databáze SQL, pomůže vám diagnostikovat a získat podporu, když problém Azure ovlivní vaše prostředky SQL.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208869"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database"></a>Řešení potíží s připojením ke službě Azure SQL Database s využitím služby Resource Health

## <a name="overview"></a>Přehled

[Stav prostředků](../service-health/resource-health-overview.md#get-started) pro SQL Database vám pomůže diagnostikovat a získat podporu, když problém Azure ovlivní vaše prostředky SQL. Informuje o aktuálním a dřívějším stavu prostředků a pomáhá zmírnit problémy. Resource Health poskytuje technickou podporu, když potřebujete pomoc při potížích se službami Azure.

![Přehled](./media/sql-database-resource-health/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Kontroly stavu

Stav prostředků určuje stav prostředku SQL kontrolou úspěch a selhání přihlášení k prostředku. V současné době stav prostředků pro prostředek SQL DB zkoumá pouze selhání přihlášení z důvodu systémové chyby a není chyba uživatele. Stav prostředku je aktualizován každých 1-2 minuty.

## <a name="health-states"></a>Zdravotní státy

### <a name="available"></a>K dispozici.

Stav **K dispozici** znamená, že stav prostředků nezjistil chyby přihlášení z důvodu systémových chyb v prostředku SQL.

![K dispozici.](./media/sql-database-resource-health/sql-resource-health-available.jpg)

### <a name="degraded"></a>Snížený výkon

Stav **degradované** znamená, že resource health zjistil většinu úspěšných přihlášení, ale také některé chyby. Jedná se s největší pravděpodobností o přechodné chyby přihlášení. Chcete-li snížit dopad problémů s připojením způsobených přechodovými chybami přihlášení, implementujte [logiku opakování](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) ve vašem kódu.

![Snížený výkon](./media/sql-database-resource-health/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Neaktivní

Stav **Nedostupné** znamená, že stav prostředků zjistil konzistentní selhání přihlášení k prostředku SQL. Pokud váš prostředek zůstane v tomto stavu po delší dobu, obraťte se na podporu.

![Neaktivní](./media/sql-database-resource-health/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Není známo

Stav **Neznámý** označuje, že stav prostředků neobdržel informace o tomto prostředku více než 10 minut. Přestože tento stav není definitivní označení stavu prostředku, je důležitým datovým bodem v procesu řešení potíží. Pokud je prostředek spuštěn podle očekávání, stav prostředku se po několika minutách změní na K dispozici. Pokud dochází k problémům s prostředkem, neznámý stav může naznačovat, že událost na platformě ovlivňuje prostředek.

![Není známo](./media/sql-database-resource-health/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Historické informace

V části Historie stavu v části Zdraví prostředků můžete získat přístup až 14 dní historie stavu. Část bude také obsahovat důvod prostojů (pokud je k dispozici) pro prostoje hlášené službou Resource Health. V současné době Azure ukazuje výpadky vašeho prostředku databáze SQL ve dvouminutových intervalech. Ve skutečnosti výpadky pravděpodobně netrvaly ani minutu – průměr je 8 s.

### <a name="downtime-reasons"></a>Důvody prostoje

Když databáze SQL dojde k prostojům, provede se analýza, která určí důvod. Pokud je k dispozici, důvod prostojů je uveden v části Historie stavu v oblasti Zdraví prostředků. Důvody výpadků se obvykle publikují 30 minut po události.

#### <a name="planned-maintenance"></a>Plánovaná údržba

Infrastruktura Azure pravidelně provádí plánovanou údržbu – upgrade hardwarových nebo softwarových komponent v datovém centru. Zatímco databáze prochází údržbou, SQL může ukončit některá existující připojení a odmítnout nové. Selhání přihlášení během plánované údržby jsou obvykle přechodné a [logika opakování](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) pomáhá snížit dopad. Pokud se nadále setkáte s chybami přihlášení, obraťte se na podporu.

#### <a name="reconfiguration"></a>Rekonfigurace

Rekonfigurace jsou považovány za přechodné podmínky a jsou očekávány čas od času. Tyto události mohou být vyvolány vyrovnáváním zatížení nebo selháním softwaru/hardwaru. Všechny klientské produkční aplikace, která se připojuje ke cloudové databázi by měl implementovat robustní [logiku opakování](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)připojení , protože by pomohlo zmírnit tyto situace a obecně by měly chyby transparentní pro koncového uživatele.

## <a name="next-steps"></a>Další kroky

- Další informace o [logice opakování přechodných chyb](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
- [Oprava, diagnostika a prevence chyb připojení SQL](./sql-database-connectivity-issues.md)
- Další informace o [konfiguraci výstrah stavu prostředků](../service-health/resource-health-alert-arm-template-guide.md)
- Získejte přehled o [stavu zdrojů](../service-health/resource-health-overview.md)
- [Nejčastější dotazy k funkci Resource Health](../service-health/resource-health-faq.md)
