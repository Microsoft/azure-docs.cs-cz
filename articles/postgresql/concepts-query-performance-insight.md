---
title: Query Performance Insight – Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje funkci Query Performance Insight v rámci Azure Database for PostgreSQL na jednom serveru.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: babf2c8208732a194184549dfa5ed3228b376d0f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91710256"
---
# <a name="query-performance-insight"></a>Query Performance Insight 

**Platí pro:** Azure Database for PostgreSQL – jeden server verze 9,6, 10, 11

Query Performance Insight vám pomůže rychle zjistit, co nejdéle běží dotazy, jak se v průběhu času mění a co se na ně budou jejich vlivovat.

## <a name="permissions"></a>Oprávnění
K zobrazení textu dotazů v Query Performance Insight jsou nutná oprávnění **vlastníka** nebo **přispěvatele**. **Čtenář** může zobrazit grafy a tabulky, ale ne text dotazu.

## <a name="prerequisites"></a>Požadavky
Aby Query Performance Insight fungoval, musí existovat data v [úložišti dotazů](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Zobrazení přehledů výkonu
Zobrazení [Query Performance Insight](concepts-query-performance-insight.md) na portálu Azure, bude přinášet vizualizace o klíčových informacích z Query Storu. 

Na stránce portálu vašeho serveru Azure Database for PostgreSQL v části **inteligentní výkon** v řádku nabídek vyberte **Query Performance Insight** .

:::image type="content" source="./media/concepts-query-performance-insight/query-performance-insight-landing-page.png" alt-text="Query Performance Insight dlouho běžící dotazy":::

Karta **dlouho běžící dotazy** zobrazuje prvních pět dotazů na základě průměrné doby trvání za běhu, agregované v intervalech 15 minut. Další dotazy můžete zobrazit tak, že v rozevíracím seznamu vyberete **Počet dotazů**. Barvy grafu se mohou při této akci změnit pro konkrétní ID dotazu.

Můžete kliknout a přetáhnout v grafu, abyste zmenšili konkrétní časové okno. Alternativně můžete použít ikony přiblížení a ven k zobrazení kratšího nebo většího časového období.

Tabulka pod grafem obsahuje další podrobnosti o dlouhotrvajících dotazech v daném časovém intervalu.

Vyberte kartu **Statistiky čekání** k zobrazení odpovídající vizualizace týkající se čekání na serveru.

:::image type="content" source="./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png" alt-text="Query Performance Insight čeká na statistiku":::

## <a name="considerations"></a>Požadavky
* Query Performance Insight není pro [repliky čtení](concepts-read-replicas.md)k dispozici.

## <a name="next-steps"></a>Další kroky
- Další informace o [sledování a ladění ](concepts-monitoring.md) ve službě Azure Database for PostgreSQL.


