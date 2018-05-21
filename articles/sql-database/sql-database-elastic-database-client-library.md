---
title: Vytváření databází škálovatelné cloudové | Microsoft Docs
description: Vytvářet škálovatelné aplikace .NET databáze s knihovnou klienta elastické databáze
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 5a54980e866a31b576265487fdff128f53f30470
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="building-scalable-cloud-databases"></a>Vytváření škálovatelných cloudových databází
Škálování databáze dá snadno dosáhnout pomocí funkcí a nástrojů pro škálovatelná pro databázi SQL Azure. Konkrétně můžete použít **klientské knihovny pro elastické databáze** k vytváření a správě databází Škálováním. Tato funkce vám umožní snadno vyvíjet horizontálně dělené aplikace, které používají stovky – nebo dokonce tisíce – databází Azure SQL. [Elastické úlohy](sql-database-elastic-jobs-powershell.md) lze použít ke snadné správy těchto databází.

Stažení:
* Java verzi knihovny, najdete v části [Maven centrálním úložišti](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* verze rozhraní .NET knihovny, najdete v části [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="documentation"></a>Dokumentace
1. [Začínáme s nástroji Elastic Database](sql-database-elastic-scale-get-started.md)
2. [Funkce elastické databáze](sql-database-elastic-scale-introduction.md)
3. [Správa mapování horizontálních oddílů](sql-database-elastic-scale-shard-map-management.md)
4. [Migrovat existující databáze chcete škálovat.](sql-database-elastic-convert-to-use-elastic-tools.md)
5. [Směrování závislé na datech](sql-database-elastic-scale-data-dependent-routing.md)
6. [Víc horizontálních dotazy](sql-database-elastic-scale-multishard-querying.md)
7. [Přidání horizontálního oddílu pomocí nástroje elastické databáze](sql-database-elastic-scale-add-a-shard.md)
8. [Víceklientské aplikací pomocí nástroje elastické databáze a zabezpečení na úrovni řádků](sql-database-elastic-tools-multi-tenant-row-level-security.md)
9. [Upgrade klienta knihovny aplikace](sql-database-elastic-scale-upgrade-client-library.md) 
10. [Přehled elastické dotazy](sql-database-elastic-query-overview.md)
11. [Glosář nástrojů elastické databáze](sql-database-elastic-scale-glossary.md)
12. [Klientská knihovna pro elastické databáze s platformou Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Klientská knihovna pro elastické databáze s Dapper](sql-database-elastic-scale-working-with-dapper.md)
14. [Nástroj rozdělení sloučení](sql-database-elastic-scale-overview-split-and-merge.md)
15. [Čítače výkonu pro správce mapování horizontálních oddílů](sql-database-elastic-database-client-library.md) 
16. [Nejčastější dotazy týkající se nástroje elastické databáze](sql-database-elastic-scale-faq.md)

## <a name="client-capabilities"></a>Možnosti klienta
Škálování aplikací s použitím *horizontálního dělení* představuje výzvy pro vývojáře jak správce. Klientské knihovny zjednodušíte úlohy správy tím, že poskytuje nástroje, které umožní obou vývojáři a správci spravovat upraveným databáze. V Typickým příkladem existuje mnoho databází, označuje jako "horizontálních oddílů," ke správě. Zákazníci jsou společně umístění ve stejné databázi a je jednu databázi na jednu zákazníků (schéma jednoho klienta). Klientská knihovna obsahuje tyto funkce:

- **Správa mapování horizontálních**: speciální databáze názvem "Mapa správce horizontálního oddílu" byla vytvořena. Správa mapování horizontálního oddílu je možnost pro aplikaci pro správu metadata o jeho horizontálních oddílů. Vývojáři mohou pomocí této funkce zaregistrovat databáze jako horizontálních oddílů, popisují mapování jednotlivých horizontálního dělení klíče nebo klíče rozsahy pro tyto databáze a udržovat tato metadata jako číslo a složení databází zpracovaní tak, aby odrážely změny kapacity. Bez klientské knihovny pro elastické databáze museli byste tráví mnoho času psaní kódu správy při implementaci horizontálního dělení. Podrobnosti najdete v tématu [horizontálního oddílu mapy správu](sql-database-elastic-scale-shard-map-management.md).

- **Data závislé směrování**: Představte si žádost, než dorazí do aplikace. Na základě horizontálního dělení klíče hodnoty požadavku, aplikace musí určit správné databázi na základě hodnoty klíče. Potom otevře připojení k databázi a zpracovat žádost. Závislé směrování dat poskytuje schopnost otevřete připojení na základě jednoho volání snadno do mapy horizontálního oddílu aplikace. Data závislé směrování se jiné oblasti infrastruktury kód, který je nyní předmětem funkce v knihovně klienta elastické databáze. Podrobnosti najdete v tématu [závislé směrování dat](sql-database-elastic-scale-data-dependent-routing.md).
- **Víc horizontálních dotazy (MSQ)**: dotazování víc horizontálních funguje, když požadavek zahrnuje několik (nebo všechny) horizontálních oddílů. Dotaz s více horizontálních spustí stejný kód T-SQL na všechny horizontálních oddílů nebo sadu horizontálních oddílů. Výsledky z zúčastněných horizontálních oddílů jsou sloučeny do celkový výsledek nastavit pomocí sémantiky UNION ALL. Funkce jako vystavenou přes klientské knihovny zpracovává mnoho úloh, včetně: Správa připojení, správu přístup z více vláken, selhání zpracování a zpracování mezilehlých výsledků. MSQ můžete dotazovat až stovky horizontálních oddílů. Podrobnosti najdete v tématu [víc horizontálních dotazování](sql-database-elastic-scale-multishard-querying.md).

Obecně platí zákazníků týkající se použití nástroje elastické databáze můžete očekávat, že se při odesílání operace horizontálního oddílu místní oproti mezi horizontálních operace, které mají své vlastní sémantiku získat plnou funkčnost T-SQL.



## <a name="next-steps"></a>Další postup

- Klientská knihovna pro elastické databáze ([Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22), [.NET](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)) – na **Stáhnout** knihovny.

- [Začínáme s nástroje elastické databáze](sql-database-elastic-scale-get-started.md) – a zkuste to **ukázkovou aplikaci** který předvádí funkce klienta.

- GitHub ([Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md), [.NET](https://github.com/Azure/elastic-db-tools)) – přispívat ke kódu.
- [Přehled služby Azure SQL Database elastické dotazu](sql-database-elastic-query-overview.md) – Pokud chcete používat elastické dotazy.

- [Přesun dat mezi instancemi cloudu databázemi](sql-database-elastic-scale-overview-split-and-merge.md) – pokyny k používání **nástroji pro sloučení rozdělení**.



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

