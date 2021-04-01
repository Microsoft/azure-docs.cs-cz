---
title: Vytváření škálovatelných cloudových databází
description: Vytvářejte škálovatelné databázové aplikace .NET pomocí klientské knihovny Elastic Database.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/25/2018
ms.openlocfilehash: bfe5dc00ba0255520c04ea85157f0b8bdc71b590
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "84050223"
---
# <a name="building-scalable-cloud-databases"></a>Vytváření škálovatelných cloudových databází
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Škálování databází lze snadno provést pomocí škálovatelných nástrojů a funkcí pro Azure SQL Database. Konkrétně můžete pomocí **klientské knihovny elastic Database** vytvářet a spravovat databáze s horizontálním škálováním kapacity. Tato funkce umožňuje snadno vyvíjet aplikace horizontálně dělené s využitím stovek – nebo dokonce tisíců databází v Azure SQL Database.

Ke stažení:

* Verze Java knihovny, viz [centrální úložiště Maven](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* Verze rozhraní .NET knihovny, viz [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="documentation"></a>Dokumentace

1. [Začínáme s nástroji Elastic Database](elastic-scale-get-started.md)
2. [Elastic Database funkce](elastic-scale-introduction.md)
3. [Správa mapování horizontálních oddílů](elastic-scale-shard-map-management.md)
4. [Migrace existujících databází pro horizontální navýšení kapacity](elastic-convert-to-use-elastic-tools.md)
5. [Směrování závislé na datech](elastic-scale-data-dependent-routing.md)
6. [Dotazy s několika horizontálními oddíly](elastic-scale-multishard-querying.md)
7. [Přidání horizontálních oddílů pomocí nástrojů Elastic Database](elastic-scale-add-a-shard.md)
8. [Víceklientské aplikace s nástroji pro Elastic Database a zabezpečení na úrovni řádků](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)
9. [Upgradovat klientské aplikace knihovny](elastic-scale-upgrade-client-library.md) 
10. [Přehled elastických dotazů](elastic-query-overview.md)
11. [Glosář nástrojů pro Elastic Database](elastic-scale-glossary.md)
12. [Elastic Database klientské knihovny s Entity Framework](elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Klientská knihovna Elastic Database s Dapperem](elastic-scale-working-with-dapper.md)
14. [Nástroj pro dělení a slučování](elastic-scale-overview-split-and-merge.md)
15. [Čítače výkonu pro správce mapování horizontálních oddílů](elastic-database-client-library.md) 
16. [Nejčastější dotazy k nástrojům pro Elastic Database](elastic-scale-faq.md)

## <a name="client-capabilities"></a>Možnosti klienta

Horizontální navýšení kapacity aplikací pomocí *horizontálního dělení* představují výzvy pro vývojáře i správce. Klientská knihovna zjednodušuje úlohy správy tím, že poskytuje nástroje, které umožňují vývojářům i správcům spravovat databáze s horizontálním škálováním kapacity. V typickém příkladu je pro správu k dispozici mnoho databází, označovaných jako "horizontálních oddílů". Zákazníci jsou společně umístěni ve stejné databázi a jedna databáze je určena pro každého zákazníka (schéma s jedním tenantům). Knihovna klienta zahrnuje tyto funkce:

- **Správa map horizontálních oddílů**: je vytvořena speciální databáze nazývaná "horizontálních oddílů Map Manager". Správa map horizontálních oddílů je schopnost aplikace spravovat metadata o své horizontálních oddílů. Vývojáři mohou tuto funkci používat k registraci databází jako horizontálních oddílů, popisují mapování jednotlivých klíčů horizontálního dělení nebo rozsahů klíčů pro tyto databáze a spravovat tato metadata jako počet a složení databází, které se rozvíjejí, aby odrážely změny kapacity. Bez Elastic Database klientské knihovny byste při implementaci horizontálního dělení museli strávit spoustu času psaním kódu správy. Podrobnosti najdete v tématu [Správa map horizontálních oddílů](elastic-scale-shard-map-management.md).

- **Směrování závislé na datech**: Představte si, že žádost přichází do aplikace. Na základě hodnoty horizontálního dělení klíče požadavku musí aplikace určit správnou databázi na základě hodnoty klíče. Pak otevře připojení k databázi pro zpracování žádosti. Směrování závislé na datech poskytuje možnost otevřít připojení s jedním jednoduchým voláním do mapy horizontálních oddílů aplikace. Směrování závislé na datech bylo další oblastí kódu infrastruktury, která je teď pokrytá funkcemi v klientské knihovně Elastic Database. Podrobnosti najdete v tématu [Směrování závislé na datech](elastic-scale-data-dependent-routing.md).
- **Multi-horizontálních oddílů dotazy (MSQ)**: dotazování multi-horizontálních oddílů funguje, když požadavek zahrnuje několik (nebo vše) horizontálních oddílů. Dotaz s více horizontálních oddílůmi spouští stejný kód T-SQL ve všech horizontálních oddílů nebo sadě horizontálních oddílů. Výsledky z zúčastněných horizontálních oddílů se sloučí do celkové sady výsledků pomocí SJEDNOCENí všech sémantik. Funkce, která je vystavena prostřednictvím klientské knihovny, zpracovává mnoho úloh, včetně správy připojení, správy vláken, zpracování chyb a průběžného zpracování výsledků. MSQ se může dotazovat až na stovky horizontálních oddílů. Podrobnosti najdete v tématu [dotazování multi-horizontálních oddílů](elastic-scale-multishard-querying.md).

Obecně platí, že zákazníci, kteří používají Elastic Database nástroje, můžou očekávat plnou funkčnost T-SQL při odesílání horizontálních oddílů místních operací na rozdíl od operací mezi horizontálních oddílů, které mají svou vlastní sémantiku.



## <a name="next-steps"></a>Další kroky

- Knihovna klienta Elastic Database ([Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22), [.NET](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)) – ke **stažení** knihovny.

- [Začínáme s nástroji pro elastic Database](elastic-scale-get-started.md) – můžete vyzkoušet **ukázkovou aplikaci** , která předvádí klientské funkce.

- GitHub ([Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md), [.NET](https://github.com/Azure/elastic-db-tools)) – pro zajištění příspěvků kódu.
- [Přehled Azure SQL Database elastický dotaz](elastic-query-overview.md) – pro použití elastických dotazů.

- [Přesouvání dat mezi vysoce škálovatelnými cloudových databází](elastic-scale-overview-split-and-merge.md) – pokyny k používání **Nástroje pro dělení a slučování**.



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

