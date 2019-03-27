---
title: Query Performance Insight v Azure Database for PostgreSQL
description: Tento článek popisuje funkci Query Performance Insight ve službě Azure Database for PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/26/2019
ms.openlocfilehash: 69963f34cb49482cc7eae25320a6a3a5f176f8dd
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2019
ms.locfileid: "58486575"
---
# <a name="query-performance-insight"></a>Query Performance Insight 

**Platí pro:** Azure Database for PostgreSQL 9.6 a 10

Query Performance Insight vám umožní rychle zjistit, co nejdéle probíhající dotazy se, jak se v průběhu času měnit a jaké čeká ovlivňují.

## <a name="permissions"></a>Oprávnění
K zobrazení textu dotazů v Query Performance Insight jsou nutná oprávnění **vlastníka** nebo **přispěvatele**. **Čtenář** může zobrazit grafy a tabulky, ale ne text dotazu.

## <a name="prerequisites"></a>Požadavky
Pro Query Performance Insight k funkci, musí existovat data [Query Store](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Informace o zobrazení výkonu
Zobrazení [Query Performance Insight](concepts-query-performance-insight.md) na portálu Azure, bude přinášet vizualizace o klíčových informacích z Query Storu. 

Na stránce portálu vašeho serveru Azure Database for postgresql – výběr **přehled o výkonu dotazů** pod **podpora a řešení potíží** části řádku nabídek.

![Query Performance Insight dlouho běžící dotazy](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

Karta **Dlouho běžících dotazů** zobrazuje 5 nejčastějších dotazů podle průměrné doby trvání na spuštění, agregované do 15minutových intervalů. Další dotazy můžete zobrazit tak, že v rozevíracím seznamu vyberete **Počet dotazů**. Barvy grafu se mohou při této akci změnit pro konkrétní ID dotazu.

Můžete kliknout a přetáhnout v grafu, abyste zmenšili konkrétní časové okno. Můžete také použijte funkce zvětšení ani výstupní ikony zobrazíte menší nebo větší časový úsek, v uvedeném pořadí.

V tabulce pod grafem jsou uvedené další podrobnosti o dlouho běžící dotazy v tomto časovém období.

Vyberte kartu **Statistiky čekání** k zobrazení odpovídající vizualizace týkající se čekání na serveru.

![Statistiky čekání Query Performance Insight](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Další postup
- Další informace o [sledování a ladění ](concepts-monitoring.md) ve službě Azure Database for PostgreSQL.


