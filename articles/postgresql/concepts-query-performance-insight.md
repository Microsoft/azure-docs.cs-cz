---
title: Query Performance Insight v Azure Database for PostgreSQL
description: Tento článek popisuje funkci Query Performance Insight ve službě Azure Database for PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: cc041104169ca8c4344b9d3de597283d122e63db
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47394756"
---
# <a name="query-performance-insight"></a>Query Performance Insight 

**Platí pro:** Azure Database for PostgreSQL 9.6 a 10

> [!IMPORTANT]
> Query Performance Insight funkce je ve verzi Public Preview v omezeném počtu oblastí. 

Query Performance Insight vám umožní rychle zjistit, co nejdéle probíhající dotazy se, jak se v průběhu času měnit a jaké čeká ovlivňují.

## <a name="permissions"></a>Oprávnění
**Vlastník** nebo **Přispěvatel** oprávnění nutná k zobrazení textu dotazů v Query Performance Insight. **Čtečka** může zobrazit grafů a tabulek, ale není text dotazu.

## <a name="prerequisites"></a>Požadavky
Pro Query Performance Insight k funkci, musí existovat data [Query Store](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Informace o zobrazení výkonu
[Query Performance Insight](concepts-query-performance-insight.md) zobrazení na webu Azure Portal, bude přinášet vizualizace na informace o klíči z Query Store. 

Na stránce portálu vašeho serveru Azure Database for postgresql – výběr **přehled o výkonu dotazů** pod **podpora a řešení potíží** části řádku nabídek.

![Query Performance Insight dlouho běžící dotazy](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

**Dlouho běžící dotazy** karta zobrazuje 5 nejčastějších dotazů podle průměrné doby trvání na spuštění, agregují v intervalech 15 minut. Další dotazy můžete zobrazit tak, že vyberete **číslo dotazy** rozevírací seznam. Barvy grafu může změnit pro konkrétní ID dotazu při této akci.

Můžete kliknout a přetáhnout v grafu můžete zúžit na konkrétní časové okno. Můžete také použijte funkce zvětšení ani výstupní ikony zobrazíte menší nebo větší časový úsek, v uvedeném pořadí.

V tabulce pod grafem jsou uvedené další podrobnosti o dlouho běžící dotazy v tomto časovém období.

Vyberte **statistiky čekání** kartu k zobrazení odpovídající vizualizace na čeká na serveru.

![Query Performance Insight počkejte statistiky](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Další postup
- Další informace o [monitorování a optimalizace](concepts-monitoring.md) ve službě Azure Database for PostgreSQL.


