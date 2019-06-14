---
title: Query Performance Insight v Azure Database for MySQL
description: Tento článek popisuje funkci Query Performance Insight ve službě Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: MySQL
ms.topic: conceptual
ms.date: 06/05/2019
ms.openlocfilehash: 1243ae8ae20d08ea643661606639abedbc56ab9c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078778"
---
# <a name="query-performance-insight-in-azure-database-for-mysql"></a>Query Performance Insight v Azure Database for MySQL

**Platí pro:**  Azure Database for MySQL 5.7

> [!NOTE]
> Query Performance Insight je ve verzi preview. Podpora pro Query Performance Insight na webu Azure Portal se nasazuje a ještě možná není k dispozici ve vaší oblasti.

Query Performance Insight vám umožní rychle zjistit, co nejdéle probíhající dotazy se, jak se v průběhu času měnit a jaké čeká ovlivňují.

## <a name="permissions"></a>Oprávnění

**Vlastník** nebo **Přispěvatel** oprávnění nutná k zobrazení textu dotazů v Query Performance Insight. ** Čtečka** může zobrazit grafů a tabulek, ale není text dotazu.

## <a name="prerequisites"></a>Požadavky

Pro Query Performance Insight k funkci, musí existovat data [Query Store](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Informace o zobrazení výkonu

Zobrazení [Query Performance Insight](concepts-query-performance-insight.md) na portálu Azure, bude přinášet vizualizace o klíčových informacích z Query Storu.

Na stránce portálu vašeho serveru Azure Database for MySQL, vyberte **Query Performance Insight** pod **inteligentní výkonu** části řádku nabídek.

![Query Performance Insight dlouho běžící dotazy](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png) 

 **Dlouho běžící dotazy** karta zobrazuje 5 nejčastějších dotazů podle průměrné doby trvání na spuštění, agregují v intervalech 15 minut. Další dotazy můžete zobrazit tak, že vyberete **číslo dotazy** rozevírací seznam. Barvy grafu se mohou při této akci změnit pro konkrétní ID dotazu.

Můžete kliknout a přetáhnout v grafu, abyste zmenšili konkrétní časové okno. Můžete také použijte funkce zvětšení ani výstupní ikony zobrazíte menší nebo větší časové období v uvedeném pořadí.

Vyberte **statistiky čekání** kartu k zobrazení odpovídající vizualizace na čeká na serveru.

Dotazy v zobrazení statistiky čekání jsou seskupené v dotazech, které vykazují ta největší čeká během zadaného časového intervalu.

![Query Performance Insight čeká statistiky](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Další postup

- Další informace o [monitorování a optimalizace](concepts-monitoring.md) ve službě Azure Database for MySQL.