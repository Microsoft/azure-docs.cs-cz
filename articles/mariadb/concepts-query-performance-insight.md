---
title: Přehled výkonu dotazů – databáze Azure pro MariaDB
description: Tento článek popisuje funkci Přehled výkonu dotazu v Azure Database for MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 88777ee44551ed6abdb7a6c7c909d6bf55db48c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527840"
---
# <a name="query-performance-insight-in-azure-database-for-mariadb"></a>Query Performance Insight ve službě Azure Database for MariaDB

**Platí pro:** Databáze Azure pro MariaDB 10.2

Přehled výkonu dotazů vám pomůže rychle zjistit, jaké jsou vaše nejdéle spuštěné dotazy, jak se v průběhu času mění a co je ovlivňuje.

## <a name="common-scenarios"></a>Obvyklé scénáře

### <a name="long-running-queries"></a>Dlouho běžící dotazy

- Identifikace nejdelších spuštěných dotazů za posledních X hodin
- Identifikace nejvyšších n dotazů, které čekají na prostředky
 
### <a name="wait-statistics"></a>Statistiky čekání

- Principy povahy čekání na dotaz
- Principy trendů čekání prostředků a místa, kde existují konflikty prostředků

## <a name="permissions"></a>Oprávnění

K zobrazení textu dotazů v Query Performance Insight jsou nutná oprávnění **vlastníka** nebo **přispěvatele**. **Čtenář** může zobrazit grafy a tabulky, ale ne text dotazu.

## <a name="prerequisites"></a>Požadavky

Aby přehled výkonu dotazu fungoval, musí data existovat v [úložišti dotazů](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Zobrazení přehledů o výkonu

Zobrazení [Query Performance Insight](concepts-query-performance-insight.md) na portálu Azure, bude přinášet vizualizace o klíčových informacích z Query Storu.

Na portálové stránce serveru Azure Database for MariaDB vyberte **přehled výkonu dotazu** v části **Inteligentní výkon** na řádku nabídek.

### <a name="long-running-queries"></a>Dlouho běžící dotazy

Tabulka **Dlouhé spuštěné dotazy** zobrazuje prvních 5 dotazů podle průměrné doby trvání na spuštění, agregované v 15minutových intervalech. Další dotazy můžete zobrazit výběrem z **rozevíracího přehledu Počet dotazů.** Barvy grafu se mohou při této akci změnit pro konkrétní ID dotazu.

Můžete kliknout a přetáhnout v grafu, abyste zmenšili konkrétní časové okno. Případně můžete pomocí ikon přiblížení a oddálení zobrazit menší nebo větší časové období.

![Dlouho běžící dotazy na přehled výkonu dotazů](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

### <a name="wait-statistics"></a>Statistiky čekání 

> [!NOTE]
> Statistiky čekání jsou určeny pro řešení problémů s výkonem dotazu. Doporučujeme zapnout pouze pro účely řešení potíží. <br>Pokud se zobrazí chybová zpráva na webu Azure Portal "*Problém zjištěný pro 'Microsoft.DBforMariaDB'; nemůže splnit požadavek. Pokud tento problém přetrvává nebo je neočekávaný, obraťte se na podporu s těmito informacemi.*" při prohlížení statistik čekání použijte menší časové období.

Wait statistiky poskytuje zobrazení události čekání, ke kterým dochází při provádění konkrétnídotaz. Další informace o typech událostí čekání naleznete v [dokumentaci k modulu MySQL](https://go.microsoft.com/fwlink/?linkid=2098206).

Vyberte kartu **Statistiky čekání** k zobrazení odpovídající vizualizace týkající se čekání na serveru.

Dotazy zobrazené v zobrazení statistiky čekání jsou seskupeny podle dotazů, které vykazují největší čekání během zadaného časového intervalu.

![Přehled výkonu dotazu čeká statistiky](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Další kroky

- Další informace o [monitorování a ladění](concepts-monitoring.md) v Azure Database pro MariaDB.