---
title: Vytváření škálovatelných cloudových databází | Dokumentace Microsoftu
description: Vytvářet škálovatelné aplikace .NET databáze Klientská knihovna elastic database
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: 065b2511da568ee2cdc0a989b34905b4010ea32e
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55562483"
---
# <a name="building-scalable-cloud-databases"></a>Vytváření škálovatelných cloudových databází

Horizontální navýšení kapacity databáze můžete snadno udělat pomocí škálovatelné nástroje a funkce pro službu Azure SQL Database. Konkrétně můžete použít **Klientská knihovna Elastic Database** k vytvoření a Správa databází s horizontálním navýšením kapacity. Tato funkce vám umožní snadno vyvíjet horizontálně dělené aplikace pomocí stovky – nebo dokonce tisíců – databází Azure SQL. [Elastické úlohy](sql-database-elastic-jobs-powershell.md) je pak možné umožňující snadnou správu těchto databází.

Stažení:

* Verze Javy knihovny, najdete v článku [centrálního úložiště Maven](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* Rozhraní .NET verze knihovny, najdete v článku [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="documentation"></a>Dokumentace

1. [Začínáme s nástroji Elastic Database](sql-database-elastic-scale-get-started.md)
2. [Funkce elastic Database](sql-database-elastic-scale-introduction.md)
3. [Správa mapování horizontálních oddílů](sql-database-elastic-scale-shard-map-management.md)
4. [Migrace existujících databází pro horizontální navýšení kapacity](sql-database-elastic-convert-to-use-elastic-tools.md)
5. [Směrování závislé na datech](sql-database-elastic-scale-data-dependent-routing.md)
6. [Dotazy na více horizontálních oddílů](sql-database-elastic-scale-multishard-querying.md)
7. [Přidání horizontálního oddílu pomocí nástrojů pro elastické databáze](sql-database-elastic-scale-add-a-shard.md)
8. [Aplikace s více tenanty s nástroji elastic database a zabezpečení na úrovní řádků](sql-database-elastic-tools-multi-tenant-row-level-security.md)
9. [Upgrade klientské knihovny aplikace](sql-database-elastic-scale-upgrade-client-library.md) 
10. [Přehled elastické dotazy](sql-database-elastic-query-overview.md)
11. [Glosář nástrojů elastické databáze](sql-database-elastic-scale-glossary.md)
12. [Klientská knihovna elastic Database s Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Klientská knihovna elastic database s Dapperem](sql-database-elastic-scale-working-with-dapper.md)
14. [Dělení a slučování](sql-database-elastic-scale-overview-split-and-merge.md)
15. [Čítače výkonu pro správce mapování horizontálních oddílů](sql-database-elastic-database-client-library.md) 
16. [Nejčastější dotazy týkající se nástrojů Elastic database](sql-database-elastic-scale-faq.md)

## <a name="client-capabilities"></a>Možnosti klienta

Horizontální navýšení kapacity aplikace pomocí *horizontálního dělení* představuje výzev pro vývojáře i správce. Klientská knihovna zjednodušíte úlohy správy tím, že poskytuje nástroje, které umožní obou vývojáři a správci spravovat databází s horizontálním navýšením kapacity. V Typickým příkladem je mnoho databází, známé jako "horizontálních oddílů," pro správu. Zákazníci jsou umístěné ve stejné databázi a není (schéma jedním tenantem) zákazníkovi jednu databázi. Klientská knihovna zahrnuje tyto funkce:

- **Správa mapování horizontálních oddílů**: Volá se, "správce mapování horizontálních oddílů" zvláštní databáze se vytvoří. Správa mapování horizontálních oddílů je možnost pro aplikaci pro správu metadata o jeho horizontálních oddílů. Vývojáři můžou využívat tuto funkci k registraci databáze jako horizontální oddíly, popisují mapování jednotlivé horizontální dělení klíče nebo klíče oblastí pro tyto databáze a udržovat tato metadata jako číslo a vyvíjí složení databáze tak, aby odrážely změny kapacitu. Bez Klientská knihovna elastic database je třeba trávit mnoho času psaní kódu správu při implementaci horizontálního dělení. Podrobnosti najdete v tématu [správy mapování horizontálních oddílů](sql-database-elastic-scale-shard-map-management.md).

- **Směrování závislé na datech**: Představte si žádosti přicházející do aplikace. Založené na hodnotě klíče horizontálního dělení požadavku, aplikace potřebuje k určení správné databáze založené na hodnotě klíče. Potom otevře připojení k databázi pro zpracování žádosti. Směrování závislé na datech poskytuje možnost otevření připojení pomocí jediného jednoduché volání do mapy horizontálního dělení aplikace. Směrování závislé na datech byla infrastruktury kódu, která je teď předmětem funkce v klientské knihovně elastic database jiné oblasti. Podrobnosti najdete v tématu [směrování závislé na datech](sql-database-elastic-scale-data-dependent-routing.md).
- **Dotazy na více horizontálních oddílů (MSQ)**: Dotazování více horizontálních oddílů funguje, když požadavek zahrnuje několik (nebo všechny) horizontálních oddílů. Dotazování více horizontálních oddílů spouští stejný kód T-SQL pro všemi horizontálními oddíly nebo sadu horizontálních oddílů. Výsledky ze zúčastněných horizontální oddíly jsou sloučeny do celkový výsledek nastavit pomocí sémantiky UNION ALL. Funkce jako prostřednictvím Klientská knihovna zpracovává mnoho úloh, včetně: Správa připojení, správa vláken, zpracování chyb a mezilehlých výsledků zpracování. MSQ můžete dotazovat až na stovky horizontálních oddílů. Podrobnosti najdete v tématu [dotazování více horizontálních oddílů](sql-database-elastic-scale-multishard-querying.md).

Zákazníci, kteří používají nástroje pro elastické databáze obecně můžete očekávat zobrazíte všechny funkce jazyka T-SQL při odesílání operace horizontálního oddílu místní operace napříč horizontálními oddíly, které mají své vlastní sémantiku na rozdíl od.



## <a name="next-steps"></a>Další postup

- Klientská knihovna elastic Database ([Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22), [.NET](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)) – abychom **Stáhnout** knihovny.

- [Začínáme s nástroji elastic database](sql-database-elastic-scale-get-started.md) – zkuste **ukázkovou aplikaci** , která ukazuje funkce klienta.

- GitHub ([Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md), [.NET](https://github.com/Azure/elastic-db-tools)) – k přispívání do kódu.
- [Přehled služby Azure SQL Database elastic query](sql-database-elastic-query-overview.md) – Pokud chcete použít elastické dotazy.

- [Přesun dat mezi databázemi s horizontálním navýšením kapacity](sql-database-elastic-scale-overview-split-and-merge.md) – pokyny k používání **nástroj split-merge**.



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

