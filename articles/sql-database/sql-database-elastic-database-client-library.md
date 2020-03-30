---
title: Vytváření škálovatelných cloudových databází
description: Vytváření škálovatelných databázových aplikací .NET s klientskou knihovnou elastické databáze
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/25/2018
ms.openlocfilehash: ae26f669ddbe2cc2c5b6e25a9c1c0229e88dc2e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823911"
---
# <a name="building-scalable-cloud-databases"></a>Vytváření škálovatelných cloudových databází

Škálování databází lze snadno provést pomocí škálovatelných nástrojů a funkcí pro Azure SQL Database. Zejména můžete použít **klientskou knihovnu elastické databáze** k vytvoření a správě databází s horizontálním navýšením kapacity. Tato funkce umožňuje snadno vyvíjet roztřesené aplikace pomocí stovek nebo dokonce tisíců databází Azure SQL.

Stažení:

* Java verze knihovny, viz [Maven Centrální úložiště](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* Verze knihovny .NET, naleznete v tématu [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="documentation"></a>Dokumentace

1. [Začínáme s nástroji Elastic Database](sql-database-elastic-scale-get-started.md)
2. [Funkce elastické databáze](sql-database-elastic-scale-introduction.md)
3. [Správa mapování horizontálních oddílů](sql-database-elastic-scale-shard-map-management.md)
4. [Migrace existujících databází za účelem horizontálního navýšení kapacity](sql-database-elastic-convert-to-use-elastic-tools.md)
5. [Směrování závislé na datech](sql-database-elastic-scale-data-dependent-routing.md)
6. [Dotazy s několika horizontálními oddíly](sql-database-elastic-scale-multishard-querying.md)
7. [Přidání šimetu pomocí nástrojů elastické databáze](sql-database-elastic-scale-add-a-shard.md)
8. [Aplikace s více klienty s elastickými databázovými nástroji a zabezpečením na úrovni řádků](sql-database-elastic-tools-multi-tenant-row-level-security.md)
9. [Upgrade aplikací klientské knihovny](sql-database-elastic-scale-upgrade-client-library.md) 
10. [Přehled elastických dotazů](sql-database-elastic-query-overview.md)
11. [Glosář nástrojů elastické databáze](sql-database-elastic-scale-glossary.md)
12. [Klientská knihovna elastická databáze s rozhraním Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Klientská knihovna elastické databáze s aplikací Dapper](sql-database-elastic-scale-working-with-dapper.md)
14. [Nástroj pro sloučení rozdělení](sql-database-elastic-scale-overview-split-and-merge.md)
15. [Čítače výkonu pro správce mapování horizontálních oddílů](sql-database-elastic-database-client-library.md) 
16. [Nejčastější dotazy k nástrojům elastické databáze](sql-database-elastic-scale-faq.md)

## <a name="client-capabilities"></a>Možnosti klienta

Horizontální navýšení kapacity představuje výzvy pro vývojáře i pro správce. *sharding* Klientská knihovna zjednodušuje úlohy správy tím, že poskytuje nástroje, které umožňují vývojářům i správcům spravovat škálované databáze. V typickém příkladu existuje mnoho databází, označované jako "střepy", které chcete spravovat. Zákazníci jsou umístěny společně ve stejné databázi a existuje jedna databáze na zákazníka (schéma jednoho klienta). Klientská knihovna obsahuje tyto funkce:

- **Správa střepů**: Je vytvořena speciální databáze nazvaná "správce mapy syrových bitových žpon). Správa mapy sítkem je možnost pro aplikaci spravovat metadata o jeho úlomky. Vývojáři mohou tuto funkci použít k registraci databází jako úlomků, popisují mapování jednotlivých klíčů syrovým rozdílem nebo rozsahy klíčů pro tyto databáze a udržují tato metadata, protože počet a složení databází se vyvíjí tak, aby odrážely změny kapacity. Bez klientské knihovny elastické databáze byste museli strávit spoustu času psaním kódu správy při implementaci oddílu je obtížné. Podrobnosti naleznete v tématu [Správa map svižniny svižné](sql-database-elastic-scale-shard-map-management.md).

- **Směrování závislé na datech**: Představte si požadavek přicházející do aplikace. Na základě hodnoty klíče s ráždí požadavku aplikace potřebuje určit správnou databázi na základě hodnoty klíče. Potom otevře připojení k databázi ke zpracování požadavku. Směrování závislé na datech poskytuje možnost otevřít připojení s jedním snadným voláním do mapy střepů aplikace. Směrování závislé na datech byla další oblast kódu infrastruktury, která je nyní pokryta funkcemi v klientské knihovně elastické databáze. Podrobnosti naleznete v [tématu Směrování závislé na datech](sql-database-elastic-scale-data-dependent-routing.md).
- **Vícestřešné dotazy (MSQ):** Dotazování s více oddíly funguje, když požadavek zahrnuje několik (nebo všechny) oddíly. Dotaz s více oddíly spustí stejný kód T-SQL na všech šmejdů nebo sadu šmejdů. Výsledky ze zúčastněných úlomků jsou sloučeny do celkové sady výsledků pomocí sémantiky UNION ALL. Funkce, které jsou vystaveny prostřednictvím klientské knihovny, zpracovává mnoho úloh, včetně: správa připojení, správa vláken, zpracování chyb a zpracování zprostředkujících výsledků. Služba MSQ může dotazovat až stovky úlomků. Podrobnosti naleznete [v tématu Dotazování s více oddíly](sql-database-elastic-scale-multishard-querying.md).

Obecně platí, že zákazníci, kteří používají nástroje elastické databáze můžete očekávat, že získat úplné funkce T-SQL při odesílání operací shard místní na rozdíl od operací mezi oddíly, které mají vlastní sémantiku.



## <a name="next-steps"></a>Další kroky

- Klientská knihovna elastická databáze ([Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22), [.NET](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)) - **pro stažení** knihovny.

- [Můžete začít s elastické databázové nástroje](sql-database-elastic-scale-get-started.md) – vyzkoušet **ukázkovou aplikaci,** která ukazuje funkce klienta.

- GitHub ([Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md), [.NET](https://github.com/Azure/elastic-db-tools)) - aby se příspěvky ke kódu.
- [Přehled elastického dotazu Azure SQL Database](sql-database-elastic-query-overview.md) – použití elastických dotazů.

- [Přesouvání dat mezi škálovatelovými cloudovými databázemi](sql-database-elastic-scale-overview-split-and-merge.md) – pokyny k použití **nástroje pro sloučení rozdělení**.



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

