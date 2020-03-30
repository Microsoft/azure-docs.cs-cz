---
title: Co je jedna databáze
description: Informace o jedné databázi v Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 04/08/2019
ms.openlocfilehash: fc63de4057def632d3ac1980e8cb3eaedbff2175
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500761"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>Co je jedna databáze v Azure SQL Database

Možnost nasazení jedné databáze vytvoří databázi v Azure SQL Database s vlastní sadou prostředků a spravuje se přes databázový server SQL. S jednou databází je každá databáze izolovaná od sebe navzájem a přenosná, každá s vlastní úrovní služeb v rámci [nákupního modelu založeného na DTU](sql-database-service-tiers-dtu.md) nebo [nákupního modelu založeného na virtuálních jádrech](sql-database-service-tiers-vcore.md) a zaručené výpočetní velikosti.

> [!IMPORTANT]
> Jedna databáze je jednou ze tří možností nasazení pro Azure SQL Database. Další dva jsou [elastické fondy](sql-database-elastic-pool.md) a [spravované instance](sql-database-managed-instance.md).
> [!NOTE]
> Glosář termínů v Azure SQL Database najdete v tématu [Glosář termínů databáze SQL.](sql-database-glossary-terms.md)

## <a name="dynamic-scalability"></a>Dynamická škálovatelnost

První aplikaci můžete vytvořit na malé, jediné databázi s nízkými náklady na výpočetní úrovni bez serveru nebo malé výpočetní velikosti v zřízené výpočetní vrstvě. Výpočetní [nebo servisní vrstvu](sql-database-single-database-scale.md) můžete kdykoli změnit ručně nebo programově, aby vyhovovaly potřebám vašeho řešení. Můžete upravit úroveň výkonu bez přerušení provozu aplikace a bez dopadu na vaše zákazníky. Dynamická škálovatelnost umožňuje databázím transparentně reagovat na rychle se měnící požadavky na prostředky a vy díky tomu platíte pouze za prostředky, které potřebujete, když je potřebujete.

## <a name="single-databases-and-elastic-pools"></a>Izolované databáze a elastické fondy

Jednu databázi lze přesunout do nebo z [elastického fondu](sql-database-elastic-pool.md) pro sdílení prostředků. Řadě firem a aplikací stačí, že může vytvářet izolované databáze a nastavovat větší nebo menší výkon na vyžádání, zejména při relativně předvídatelném způsobu používání. Ale pokud vaše vzorce používání předvídatelné nejsou, může být správa nákladů a údržba obchodního modelu velmi těžká. Elastické fondy jsou navržené k řešení tohoto problému. Princip je jednoduchý. Prostředky výkonu přidělujete do fondu, nikoli do jednotlivé databáze, a platíte za prostředky kolektivního výkonu fondu, nikoli za výkon jedné databáze.

## <a name="monitoring-and-alerting"></a>Monitorování a upozorňování

Používáte integrované nástroje [pro sledování výkonu](sql-database-performance-guidance.md) a [upozorňování](sql-database-insights-alerts-portal.md)v kombinaci s hodnocením výkonu. Pomocí těchto nástrojů můžete rychle posoudit dopad vertikálního navýšení nebo snížení kapacity v závislosti na stávajících nebo předpokládaných požadavcích. Kromě toho SQL Database můžete [vyzařovat metriky a protokoly prostředků](sql-database-metrics-diag-logging.md) pro snadnější monitorování.

## <a name="availability-capabilities"></a>Možnosti dostupnosti

Jediné databáze, elastické fondy a spravované instance poskytují mnoho charakteristik dostupnosti. Další informace naleznete v [tématu Charakteristiky dostupnosti](sql-database-technical-overview.md#availability-capabilities).

## <a name="transact-sql-differences"></a>Rozdíly v transakt-SQL

Většina funkcí Transact-SQL, které aplikace používají, je plně podporována v Microsoft SQL Serveru i Azure SQL Database. Například základní součásti SQL, jako jsou datové typy, operátory, řetězec, aritmetické, logické a kurzorové funkce, fungují stejně v SQL Serveru a databázi SQL. Existuje však několik T-SQL rozdíly v DDL (jazyk definice dat) a DML (jazyk pro manipulaci s daty) prvky, které mají za následek T-SQL příkazy a dotazy, které jsou podporovány pouze částečně (které budeme diskutovat dále v tomto článku).
Kromě toho existují některé funkce a syntaxe, která není podporována vůbec, protože Azure SQL Database je navržen tak, aby izolovat funkce od závislostí na hlavní databázi a operační systém. Jako takové většina aktivit na úrovni serveru jsou nevhodné pro sql database. Příkazy a možnosti T-SQL nejsou k dispozici, pokud konfigurují možnosti na úrovni serveru, součásti operačního systému nebo určují konfiguraci systému souborů. Pokud jsou takové funkce požadovány, je vhodná alternativa často dostupná jiným způsobem z databáze SQL nebo z jiné funkce nebo služby Azure.

Další informace naleznete [v tématu Řešení rozdílů transakt-SQL během migrace do databáze SQL](sql-database-transact-sql-information.md).

## <a name="security"></a>Zabezpečení

SQL Database poskytuje řadu integrovaných funkcí [zabezpečení a dodržování předpisů,](sql-database-security-overview.md) které vaší aplikaci pomáhají splnit různé požadavky na zabezpečení a dodržování předpisů.

> [!IMPORTANT]
> Azure SQL Database (všechny možnosti nasazení) byla certifikována podle řady standardů dodržování předpisů. Další informace najdete v [Centru zabezpečení Microsoft Azure,](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) kde najdete nejnovější seznam certifikací dodržování předpisů databáze SQL Database.

## <a name="next-steps"></a>Další kroky

- Chcete-li rychle začít s jednou databází, začněte pomocí [průvodce rychlým startem databáze .](sql-database-single-database-quickstart-guide.md)
- Další informace o migraci databáze SQL Serveru do Azure najdete v tématu [Migrace do Azure SQL Database](sql-database-single-database-migrate.md).
- Informace o podporovaných funkcích najdete v tématu [Funkce](sql-database-features.md).
