---
title: Query Performance Insight – Azure Database for MySQL
description: Tento článek popisuje funkci Query Performance Insight v Azure Database for MySQL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 5/12/2020
ms.openlocfilehash: 30457b950e6400c799f35924e7b2ee90d868a465
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94535176"
---
# <a name="query-performance-insight-in-azure-database-for-mysql"></a>Query Performance Insight ve službě Azure Database for MySQL

**Platí pro:** Azure Database for MySQL 5,7, 8,0

Query Performance Insight vám pomůže rychle zjistit, co nejdéle běží dotazy, jak se v průběhu času mění a co se na ně budou jejich vlivovat.

## <a name="common-scenarios"></a>Obvyklé scénáře

### <a name="long-running-queries"></a>Dlouho běžící dotazy

- Identifikace nejdelších spuštěných dotazů za posledních X hodin
- Identifikace prvních N dotazů, které čekají na prostředky
 
### <a name="wait-statistics"></a>Statistiky čekání

- Principy čekací povahy pro dotaz
- Porozumění trendům pro čekání na prostředky a na tom, kde existuje spor prostředků

## <a name="permissions"></a>Oprávnění

K zobrazení textu dotazů v Query Performance Insight jsou nutná oprávnění **vlastníka** nebo **přispěvatele**. **Čtenář** může zobrazit grafy a tabulky, ale ne text dotazu.

## <a name="prerequisites"></a>Předpoklady

Aby Query Performance Insight fungoval, musí existovat data v [úložišti dotazů](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Zobrazení přehledů výkonu

Zobrazení [Query Performance Insight](concepts-query-performance-insight.md) na portálu Azure, bude přinášet vizualizace o klíčových informacích z Query Storu.

Na stránce portálu Azure Database for MySQL serveru vyberte **Query Performance Insight** v části **inteligentní výkon** na řádku nabídek.

### <a name="long-running-queries"></a>Dlouho běžící dotazy

Karta **dlouho běžící dotazy** zobrazuje prvních 5 dotazů podle průměrné doby trvání za běhu, agregované v intervalech 15 minut. Další dotazy můžete zobrazit výběrem z rozevírací nabídky **počet dotazů** . Barvy grafu se mohou při této akci změnit pro konkrétní ID dotazu.

Můžete kliknout a přetáhnout v grafu, abyste zmenšili konkrétní časové okno. Případně můžete použít ikony přiblížení a ven k zobrazení kratšího nebo většího časového období.

:::image type="content" source="./media/concepts-query-performance-insight/query-performance-insight-landing-page.png" alt-text="Query Performance Insight dlouho běžící dotazy"::: 

### <a name="wait-statistics"></a>Statistiky čekání

> [!NOTE]
> Pro řešení potíží s výkonem dotazů je určena Statistika čekání. Doporučuje se zapnout jenom pro účely řešení potíží. <br>Pokud se zobrazí chybová zpráva v Azure Portal "*problém byl zjištěn pro ' Microsoft. DBforMySQL '; požadavek nelze splnit. Pokud tento problém přetrvává nebo je neočekávaný, obraťte se prosím na podporu s těmito informacemi.*" Při prohlížení statistik čekání použijte kratší časové období.

Statistika čekání poskytuje zobrazení událostí čekání, ke kterým dojde během provádění konkrétního dotazu. Přečtěte si další informace o typech událostí čekání v [dokumentaci k modulu MySQL](https://go.microsoft.com/fwlink/?linkid=2098206).

Vyberte kartu **Statistiky čekání** k zobrazení odpovídající vizualizace týkající se čekání na serveru.

Dotazy zobrazené v zobrazení statistiky čekání jsou seskupené podle dotazů, které vykazují největší čekání během zadaného časového intervalu.

:::image type="content" source="./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png" alt-text="Query Performance Insight čeká na statistiku":::

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [monitorování a ladění](concepts-monitoring.md) v Azure Database for MySQL.