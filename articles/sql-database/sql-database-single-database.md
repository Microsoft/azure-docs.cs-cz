---
title: Co je jedné databáze Azure SQL Database | Dokumentace Microsoftu
description: Další informace o jedné databáze ve službě Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: e8af73c955305c54efc478290608e627acb431ea
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56099322"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>Co je izolované databáze ve službě Azure SQL Database

Možnost nasazení izolovaná databáze vytvoří databázi ve službě Azure SQL Database s vlastní sadou prostředků a spravuje se přes server SQL Database. Každá databáze s izolovanou databázi, je izolovaná od všech ostatních a, každá má svou vlastní úroveň služby v rámci [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) nebo [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md) a garantovanou výpočty velikosti.

> [!IMPORTANT]
> Izolovaná databáze je jednou tři možnosti nasazení pro službu Azure SQL Database. Další dvě [elastické fondy](sql-database-elastic-pool.md) a [spravovanou instanci](sql-database-managed-instance.md).
> [!NOTE]
> Glosář termínů ve službě Azure SQL Database, najdete v části [SQL Database termíny glosáře](sql-database-glossary-terms.md)

## <a name="dynamic-scalabilty"></a>Dynamická škálovatelnost

Můžete sestavit svoji první aplikaci malou izolovanou databází s nízkými náklady za měsíc cena/výkon s nízkou úrovní služeb a pak [změnit úroveň služby](sql-database-single-database-scale.md) ručně nebo prostřednictvím kódu programu kdykoli thehigher ceny a výkonu služby úroveň podle potřeb vašeho řešení. Můžete upravit úroveň výkonu bez přerušení provozu aplikace a bez dopadu na vaše zákazníky. Dynamická škálovatelnost umožňuje databázím transparentně reagovat na rychle se měnící požadavky na prostředky a vy díky tomu platíte pouze za prostředky, které potřebujete, když je potřebujete.

## <a name="single-databases-and-elastic-pools"></a>Izolované databáze a elastických fondů

Izolované databáze můžete přesunout do nebo z [elastického fondu](sql-database-elastic-pool.md) pro sdílení prostředků. Řadě firem a aplikací stačí, že může vytvářet izolované databáze a nastavovat větší nebo menší výkon na vyžádání, zejména při relativně předvídatelném způsobu používání. Ale pokud vaše vzorce používání předvídatelné nejsou, může být správa nákladů a údržba obchodního modelu velmi těžká. Elastické fondy jsou navržené pro vyřešení tohoto problému. Princip je jednoduchý. Přidělit prostředky výkonu fondu, nikoli jednotlivé databáze a budete platit pro souhrnné prostředky výkonu fondu místo za výkon izolovaných databází.

## <a name="monitoring-and-alerting"></a>Monitorování a upozorňování

Použít integrovaný [performance monitoring pro aplikace](sql-database-performance.md) a [výstrahy nástroje](sql-database-insights-alerts-portal.md)kombinované s hodnocením výkonu. Pomocí těchto nástrojů můžete rychle posoudit dopad vertikálního navýšení nebo snížení kapacity v závislosti na stávajících nebo předpokládaných požadavcích. Kromě toho může SQL Database [generovat metriky a diagnostické protokoly](sql-database-metrics-diag-logging.md) pro snazší monitorování.

## <a name="availability-capabilities"></a>Možnosti dostupnosti

Izolované databáze, elastické fondy a spravované instance poskytují mnoho characterics dostupnosti. Informace najdete v tématu [charakteristika dostupnosti](sql-database-technical-overview.md#availability-capabilities).

## <a name="transact-sql-differences"></a>Rozdíly v Transact-SQL

Většina funkcí jazyka Transact-SQL, které používají aplikace jsou plně podporovány v systému Microsoft SQL Server a Azure SQL Database. Například hlavní součásti SQL, jako je například datové typy, operátory, řetězce, aritmetické, logické a ukazatel funkce, fungují stejně jako v systému SQL Server a SQL Database. Existují, ale několik rozdílů jazyka T-SQL v DDL (data definition language) a prvky DML (jazyk pro manipulaci dat), výsledkem je příkazy jazyka T-SQL a dotazy, které jsou podporovány jen částečně (která probereme později v tomto článku).
Kromě toho existují některé funkce a syntaxi, která není podporována na všech, protože Azure SQL Database je určena izolovala funkce od závislostí na hlavní databázi a operačním systému. V důsledku toho se většina aktivit na úrovni serveru pro SQL Database nevhodná. Příkazy jazyka T-SQL a možnosti nejsou k dispozici, je-li nakonfigurovat možnosti na úrovni serveru, součásti operačního systému nebo určují konfiguraci systému souborů. Když tyto možnosti jsou povinné, je často odpovídající alternativu k dispozici jiným způsobem ze služby SQL Database nebo z jiného Azure funkci nebo službu.

Další informace najdete v tématu [rozdíly řešení příkazů jazyka Transact-SQL během migrace do služby SQL Database](sql-database-transact-sql-information.md).

## <a name="security"></a>Zabezpečení

SQL Database nabízí celou řadu [integrované zabezpečení a dodržování předpisů](sql-database-security-overview.md) funkce, které pomohou vaší aplikace vyhovět různým nárokům na zabezpečení a dodržování předpisů.

## <a name="next-steps"></a>Další postup

- Pokud chcete rychle začít pracovat s izolovanou databázi, [jednotné guide.md rychlý start databáze](sql-database-single-database-quickstart-guide.md).
- Další informace o migraci databáze SQL serveru do Azure najdete v tématu [migrace do služby Azure SQL Database](sql-database-single-database-migrate.md).
- Informace o podporovaných funkcích najdete v tématu [Funkce](sql-database-features.md).
