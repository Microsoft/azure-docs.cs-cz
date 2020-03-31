---
title: Doporučení pro výkon – databáze Azure pro PostgreSQL – jeden server
description: Tento článek popisuje funkci doporučení výkonu v Azure Database for PostgreSQL – single server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: a30af0c8bef47a37fe3439e885d3895a2c826225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768465"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql---single-server"></a>Doporučení pro výkon v databázi Azure pro PostgreSQL – jeden server

**Platí pro:** Databáze Azure pro PostgreSQL – verze s jedním serverem 9.6, 10, 11

Funkce Doporučení výkonu analyzuje databáze a vytváří přizpůsobené návrhy pro zlepšení výkonu. Chcete-li vytvořit doporučení, analýza se zabývá různými charakteristikami databáze včetně schématu. Povolte [úložiště dotazů](concepts-query-store.md) na serveru, abyste mohli plně využívat funkci Doporučení pro výkon. Po implementaci jakékoli doporučení výkonu, měli byste otestovat výkon vyhodnotit dopad těchto změn. 

## <a name="permissions"></a>Oprávnění
Pro spuštění funkce analýzy za použití funkce Doporučení k výkonu potřebujete oprávnění **vlastníka** nebo **přispěvatele**.

## <a name="performance-recommendations"></a>Doporučení k výkonu
Funkce [Doporučení k výkonu](concepts-performance-recommendations.md) analyzuje úlohy na serveru, aby identifikoval indexy a případně zlepšil výkon.

Otevřete **doporučení výkonu** z části **Inteligentní výkon** na panelu nabídek na stránce portálu Azure pro váš postgreSQL server.

![Úvodní stránka Doporučení k výkonu](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Vyberte **Analyzovat** a zvolte databázi, která zahájí analýzu. V závislosti na vaší pracovní zátěži může dokončení analýzy trvat několik minut. Po dokončení analýzy se zobrazí oznámení na portálu. Analýza provádí hloubkové prozkoumání databáze. Doporučujeme provádět analýzy mimo špičku. 

V okně **Doporučení** se zobrazí seznam doporučení, pokud byly nalezeny.

![Doporučení pro zvýšení výkonu nová stránka](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Doporučení nejsou automaticky použita. Chcete-li použít doporučení, zkopírujte text dotazu a spusťte jej z vybraného klienta. Nezapomeňte otestovat a sledovat vyhodnotit doporučení. 

## <a name="recommendation-types"></a>Typy doporučení

V současné době jsou podporovány dva typy doporučení: *Vytvořit index* a *Drop Index*.

### <a name="create-index-recommendations"></a>Vytvořit doporučení rejstříku
*Vytvořit index* doporučení navrhnout nové indexy urychlit nejčastěji spuštěné nebo časově náročné dotazy v zatížení. Tento typ doporučení vyžaduje, aby bylo povoleno [úložiště dotazů.](concepts-query-store.md) Úložiště dotazů shromažďuje informace o dotazu a poskytuje podrobné statistiky běhu dotazu a frekvence, které analýza používá k doporučení.

### <a name="drop-index-recommendations"></a>Doporučení pro drop index
Kromě zjišťování chybějícíindexy, Azure Database pro PostgreSQL analyzuje výkon existujících indexů. Pokud index je zřídka používá nebo redundantní, analyzátor doporučuje ho přetažení.

## <a name="considerations"></a>Požadavky
* Doporučení výkonu není k dispozici pro [repliky pro čtení](concepts-read-replicas.md).
## <a name="next-steps"></a>Další kroky
- Další informace o [sledování a ladění ](concepts-monitoring.md) ve službě Azure Database for PostgreSQL.

