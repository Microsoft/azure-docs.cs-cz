---
title: Doporučení pro výkon – Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje funkci doporučení výkonu na serveru Azure Database for PostgreSQL-Single.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: d547844671e6485e71be7dd8c355de08f3dec5e6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91710562"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql---single-server"></a>Doporučení týkající se výkonu v Azure Database for PostgreSQL – jeden server

**Platí pro:** Azure Database for PostgreSQL – jeden server verze 9,6, 10, 11

Funkce doporučení k výkonu analyzuje vaše databáze a vytváří přizpůsobené návrhy pro zlepšení výkonu. Při vytváření doporučení analyzuje tato analýza různé charakteristiky databáze, včetně schématu. Povolením [úložiště dotazů](concepts-query-store.md) na serveru můžete plně využít funkci doporučení pro výkon. Po implementaci jakéhokoli doporučení výkonu byste měli testovat výkon a vyhodnotit dopad těchto změn. 

## <a name="permissions"></a>Oprávnění
Pro spuštění funkce analýzy za použití funkce Doporučení k výkonu potřebujete oprávnění **vlastníka** nebo **přispěvatele**.

## <a name="performance-recommendations"></a>Doporučení k výkonu
Funkce [Doporučení k výkonu](concepts-performance-recommendations.md) analyzuje úlohy na serveru, aby identifikoval indexy a případně zlepšil výkon.

Otevřete **doporučení pro výkon** z části **inteligentní výkon** na panelu nabídek na stránce Azure Portal pro server PostgreSQL.

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-page.png" alt-text="Úvodní stránka Doporučení k výkonu":::

Vyberte možnost **analyzovat** a zvolte databázi, která bude začínat analýzou. V závislosti na vašem zatížení může trvat několik minut, než se dokončí analýza. Po dokončení analýzy se zobrazí oznámení na portálu. Analýza provede důkladné přezkoumání vaší databáze. Doporučujeme, abyste provedli analýzu v době mimo špičku. 

V okně **doporučení** se zobrazí seznam doporučení, pokud byla nalezena kterákoli z nich.

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-result.png" alt-text="Nová stránka s doporučeními pro výkon":::

Doporučení se nepoužívají automaticky. Pokud chcete doporučení použít, zkopírujte text dotazu a spusťte ho z vašeho klienta podle vlastního výběru. Nezapomeňte otestovat a monitorovat, abyste vyhodnotili doporučení. 

## <a name="recommendation-types"></a>Typy doporučení

V současné době jsou podporovány dva typy doporučení: *Create index* a *drop index*.

### <a name="create-index-recommendations"></a>Vytvořit doporučení indexu
Doporučení *vytvořit index* návrhy nových indexů vám umožní zrychlit nejčastěji spouštěné nebo časově náročné dotazy v zatížení. Tento typ doporučení vyžaduje, aby [úložiště dotazů](concepts-query-store.md) bylo povolené. Úložiště dotazů shromažďuje informace o dotazech a poskytuje podrobné běhové dotazy a statistické údaje o četnosti, které analýza používá, aby provedla doporučení.

### <a name="drop-index-recommendations"></a>Vyřadit doporučení indexu
Kromě detekce chybějících indexů Azure Database for PostgreSQL analyzuje výkon existujících indexů. Pokud je index buď zřídka používaný, nebo redundantní, analyzátor ho doporučuje odstranit.

## <a name="considerations"></a>Požadavky
* Doporučení k výkonu nejsou pro [repliky čtení](concepts-read-replicas.md)k dispozici.
## <a name="next-steps"></a>Další kroky
- Další informace o [sledování a ladění ](concepts-monitoring.md) ve službě Azure Database for PostgreSQL.

