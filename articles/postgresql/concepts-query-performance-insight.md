---
title: Přehled výkonu dotazů – databáze Azure pro PostgreSQL – jeden server
description: Tento článek popisuje funkci Přehled výkonu dotazu v Azure Database for PostgreSQL – jeden server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: dd5b4ec53d82421ddd9d680ca41e48eeecc43c2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768380"
---
# <a name="query-performance-insight"></a>Query Performance Insight 

**Platí pro:** Databáze Azure pro PostgreSQL – verze s jedním serverem 9.6, 10, 11

Přehled výkonu dotazů vám pomůže rychle zjistit, jaké jsou vaše nejdéle spuštěné dotazy, jak se v průběhu času mění a co je ovlivňuje.

## <a name="permissions"></a>Oprávnění
K zobrazení textu dotazů v Query Performance Insight jsou nutná oprávnění **vlastníka** nebo **přispěvatele**. **Čtenář** může zobrazit grafy a tabulky, ale ne text dotazu.

## <a name="prerequisites"></a>Požadavky
Aby přehled výkonu dotazu fungoval, musí data existovat v [úložišti dotazů](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Zobrazení přehledů o výkonu
Zobrazení [Query Performance Insight](concepts-query-performance-insight.md) na portálu Azure, bude přinášet vizualizace o klíčových informacích z Query Storu. 

Na portálové stránce serveru Azure Database for PostgreSQL vyberte **přehled výkonu dotazu** v části **Inteligentní výkon** na řádku nabídek.

![Dlouho běžící dotazy na přehled výkonu dotazů](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

Tabulka **Dlouhé spuštěné dotazy** zobrazuje prvních pět dotazů podle průměrné doby trvání na spuštění, agregované v 15minutových intervalech. Další dotazy můžete zobrazit tak, že v rozevíracím seznamu vyberete **Počet dotazů**. Barvy grafu se mohou při této akci změnit pro konkrétní ID dotazu.

Můžete kliknout a přetáhnout v grafu, abyste zmenšili konkrétní časové okno. Případně můžete pomocí ikon přiblížení a oddálení zobrazit menší nebo větší časové období.

Tabulka pod grafem poskytuje další podrobnosti o dlouhotrvající dotazy v tomto časovém okně.

Vyberte kartu **Statistiky čekání** k zobrazení odpovídající vizualizace týkající se čekání na serveru.

![Přehled výkonu dotazu čeká statistiky](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="considerations"></a>Požadavky
* Přehled výkonu dotazu není k dispozici pro [repliky pro čtení](concepts-read-replicas.md).

## <a name="next-steps"></a>Další kroky
- Další informace o [sledování a ladění ](concepts-monitoring.md) ve službě Azure Database for PostgreSQL.


