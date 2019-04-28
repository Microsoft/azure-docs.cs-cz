---
title: Doporučení k výkonu ve službě Azure Database for PostgreSQL
description: Tento článek popisuje funkce výkonu doporučení ve službě Azure Database for PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/28/2018
ms.openlocfilehash: c5324618eeda90b4ef1a512385fb2f14bf391215
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564429"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql"></a>Doporučení k výkonu ve službě Azure Database for PostgreSQL

**Platí pro:** Azure Database for PostgreSQL 9.6 a 10

Doporučení k výkonu funkce analyzuje vaše databáze pro vytvoření přizpůsobené návrhy pro zlepšení výkonu. K vytvoření doporučení, analýza zjistí různé vlastnosti databáze, včetně schémat. Povolit [Query Store](concepts-query-store.md) na serveru plně využívat funkci doporučení k výkonu. Po implementaci jakékoli výkonu doporučení, měli byste otestovat výkon a vyhodnotit její dopad tyto změny. 

## <a name="permissions"></a>Oprávnění
Pro spuštění funkce analýzy za použití funkce Doporučení k výkonu potřebujete oprávnění **vlastníka** nebo **přispěvatele**.

## <a name="performance-recommendations"></a>Doporučení k výkonu
Funkce [Doporučení k výkonu](concepts-performance-recommendations.md) analyzuje úlohy na serveru, aby identifikoval indexy a případně zlepšil výkon.

Otevřít **doporučení k výkonu** z **inteligentní výkonu** části řádku nabídek na stránce portálu Azure pro váš server PostgreSQL.

![Úvodní stránka Doporučení k výkonu](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Vyberte **analyzovat** a zvolte databázi, která začne analýza. V závislosti na velikosti pracovní zátěže th analýzy může trvat několik minut. Po dokončení analýzy se zobrazí oznámení na portálu. Analýza provádí podrobné zkoumání vaší databáze. Doporučujeme že provádět analýzy obdobích mimo špičku. 

**Doporučení** okna se zobrazí seznam doporučení, pokud některá.

![Nová stránka doporučení výkonu](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Doporučení se automaticky nepoužijí. Na jednotlivá doporučení použít, zkopírujte text dotazu a spusťte ze svého klienta podle výběru. Nezapomeňte otestovat a monitorování pro vyhodnocení doporučení. 

## <a name="recommendation-types"></a>Doporučení typy

V současné době podporuje dva typy doporučení: *Vytvořit Index* a *Drop Index*.

### <a name="create-index-recommendations"></a>Vytvoření doporučení indexu
*Vytvořit Index* doporučení navrhnout novou indexy pro urychlení nejčastěji spuštění nebo časově náročné dotazů v úloze. Tento typ doporučení vyžaduje [Query Store](concepts-query-store.md) povolit. Query Store shromažďuje informace o dotazu a poskytuje podrobné dotazů modulu runtime a četnost statistické údaje, které využívá analýzy, aby doporučení.

### <a name="drop-index-recommendations"></a>Vyřadit doporučení indexu
Kromě zjišťování chybějících indexů, analyzuje – Azure Database for postgresql – výkon stávající indexy. Pokud indexu je zřídka používané nebo redundantní, analyzátor doporučuje zastaví.


## <a name="next-steps"></a>Další postup
- Další informace o [sledování a ladění ](concepts-monitoring.md) ve službě Azure Database for PostgreSQL.

