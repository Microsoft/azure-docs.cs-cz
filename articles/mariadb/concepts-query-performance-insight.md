---
title: Query Performance Insight ve službě Azure Database pro MariaDB
description: Tento článek popisuje funkci Query Performance Insight ve službě Azure Database pro MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 48ff1fdc08e0df463ec48fd1415c7b67d5beb744
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462098"
---
# <a name="query-performance-insight-in-azure-database-for-mariadb"></a>Query Performance Insight ve službě Azure Database pro MariaDB

**Platí pro:**  Azure Database pro MariaDB 10.2

> [!NOTE]
> Query Performance Insight je ve verzi preview.

Query Performance Insight vám umožní rychle zjistit, co nejdéle probíhající dotazy se, jak se v průběhu času měnit a jaké čeká ovlivňují.

## <a name="common-scenarios"></a>Obvyklé scénáře

### <a name="long-running-queries"></a>Dlouho běžící dotazy

- Identifikuje nejdelšího spouštění dotazů v minulosti X hodin
- Určení horních N dotazy, které čekají na prostředky
 
### <a name="wait-statistics"></a>Statistiky čekání

- Pochopení povahy čekání dotazu
- Principy trendy pro prostředek čeká a tam, kde existuje kolize prostředků

## <a name="permissions"></a>Oprávnění

**Vlastník** nebo **Přispěvatel** oprávnění nutná k zobrazení textu dotazů v Query Performance Insight. ** Čtečka** může zobrazit grafů a tabulek, ale není text dotazu.

## <a name="prerequisites"></a>Požadavky

Pro Query Performance Insight k funkci, musí existovat data [Query Store](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Informace o zobrazení výkonu

Zobrazení [Query Performance Insight](concepts-query-performance-insight.md) na portálu Azure, bude přinášet vizualizace o klíčových informacích z Query Storu.

Na stránce portálu Azure Database pro MariaDB server vyberte **Query Performance Insight** pod **inteligentní výkonu** části řádku nabídek.

### <a name="long-running-queries"></a>Dlouho běžící dotazy

 **Dlouho běžící dotazy** karta zobrazuje 5 nejčastějších dotazů podle průměrné doby trvání na spuštění, agregují v intervalech 15 minut. Další dotazy můžete zobrazit tak, že vyberete **číslo dotazy** rozevírací seznam. Barvy grafu se mohou při této akci změnit pro konkrétní ID dotazu.

Můžete kliknout a přetáhnout v grafu, abyste zmenšili konkrétní časové okno. Můžete také použijte funkce zvětšení ani výstupní ikony zobrazíte menší nebo větší časové období v uvedeném pořadí.

![Query Performance Insight dlouho běžící dotazy](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

### <a name="wait-statistics"></a>Statistiky čekání 

> [!NOTE]
> Statistiky čekání jsou určeny pro odstraňování problémů s výkonem dotazů. Doporučujeme zapnout pouze pro účely odstraňování potíží.

Statistiky čekání poskytuje přehled čekání události, ke kterým dochází při spuštění specifického dotazu. Další informace o typech události čekání v [dokumentaci k modulu MySQL](https://go.microsoft.com/fwlink/?linkid=2098206).

Vyberte **statistiky čekání** kartu k zobrazení odpovídající vizualizace na čeká na serveru.

Dotazy v zobrazení statistiky čekání jsou seskupené v dotazech, které vykazují ta největší čeká během zadaného časového intervalu.

![Query Performance Insight čeká statistiky](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Další postup

- Další informace o [monitorování a optimalizace](concepts-monitoring.md) ve službě Azure Database pro MariaDB.