---
title: Co je Azure SQL Database samostatná databáze | Microsoft Docs
description: Další informace o izolovaných databázích v Azure SQL Database
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
ms.openlocfilehash: c5de4277915e71763f034596d372554e4f88e204
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316661"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>Co je jediná databáze v Azure SQL Database

Možnost nasazení jedné databáze vytvoří databázi v Azure SQL Database s vlastní sadou prostředků a je spravovaná pomocí SQL Databaseho serveru. V případě izolované databáze je každá databáze izolovaná od ostatních a přenosných počítačů, z nichž každá má svou vlastní úroveň služby v rámci [nákupního modelu založeného na DTU](sql-database-service-tiers-dtu.md) nebo v [nákupním modelu založeném na Vcore](sql-database-service-tiers-vcore.md) a zaručené výpočetní velikosti.

> [!IMPORTANT]
> Jedna databáze je jednou ze tří možností nasazení Azure SQL Database. Další dvě jsou [elastické fondy](sql-database-elastic-pool.md) a [spravovaná instance](sql-database-managed-instance.md).
> [!NOTE]
> Glosář termínů v Azure SQL Database naleznete v tématu [SQL Database terms Glosář](sql-database-glossary-terms.md)

## <a name="dynamic-scalability"></a>Dynamická škálovatelnost

V rámci výpočetní úrovně bez serveru (Preview) nebo malé výpočetní velikosti v zřízené výpočetní úrovni můžete vytvořit svou první aplikaci s malou jedinou databází za nízkou cenu. [Výpočetní kapacitu nebo úroveň služby](sql-database-single-database-scale.md) můžete změnit ručně nebo programově, aby vyhovovaly potřebám vašeho řešení. Můžete upravit úroveň výkonu bez přerušení provozu aplikace a bez dopadu na vaše zákazníky. Dynamická škálovatelnost umožňuje databázím transparentně reagovat na rychle se měnící požadavky na prostředky a vy díky tomu platíte pouze za prostředky, které potřebujete, když je potřebujete.

## <a name="single-databases-and-elastic-pools"></a>Izolované databáze a elastické fondy

Pro sdílení prostředků lze přesunout jednu databázi do [elastického fondu](sql-database-elastic-pool.md) nebo z něj. Řadě firem a aplikací stačí, že může vytvářet izolované databáze a nastavovat větší nebo menší výkon na vyžádání, zejména při relativně předvídatelném způsobu používání. Ale pokud vaše vzorce používání předvídatelné nejsou, může být správa nákladů a údržba obchodního modelu velmi těžká. Elastické fondy jsou navržené pro vyřešení tohoto problému. Princip je jednoduchý. Prostředky výkonu přidělíte místo individuální databáze a platíte za celkový výkon fondu, nikoli pro jednotlivé databáze.

## <a name="monitoring-and-alerting"></a>Monitorování a upozorňování

V kombinaci s hodnocením výkonu použijete integrované [nástroje](sql-database-insights-alerts-portal.md)pro [monitorování výkonu](sql-database-performance.md) a upozorňování. Pomocí těchto nástrojů můžete rychle posoudit dopad vertikálního navýšení nebo snížení kapacity v závislosti na stávajících nebo předpokládaných požadavcích. Kromě toho může SQL Database [generovat metriky a diagnostické protokoly](sql-database-metrics-diag-logging.md) pro snazší monitorování.

## <a name="availability-capabilities"></a>Možnosti dostupnosti

Jednotlivé databáze, elastické fondy a spravované instance poskytují mnoho vlastností dostupnosti. Informace najdete v tématu věnovaném [vlastnostem dostupnosti](sql-database-technical-overview.md#availability-capabilities).

## <a name="transact-sql-differences"></a>Rozdíly v Transact-SQL

Většina funkcí Transact-SQL, které aplikace používá, je plně podporovaná v Microsoft SQL Server i Azure SQL Database. Například základní komponenty SQL, jako jsou datové typy, operátory, řetězce, aritmetické funkce, logické a kurzorové funkce, fungují stejně v SQL Server a SQL Database. Existuje však několik rozdílů v jazyce T-SQL v prvcích DDL (Data-Definition Language) a DML (data pro manipulaci s daty), které jsou výsledkem příkazů T-SQL a dotazů, které jsou pouze částečně podporovány (což se zabývá dále v tomto článku).
Kromě toho existují některé funkce a syntaxe, které nejsou vůbec podporovány, protože Azure SQL Database slouží k izolaci funkcí ze závislostí v hlavní databázi a v operačním systému. Proto většina aktivit na úrovni serveru není pro SQL Database vhodná. Příkazy T-SQL a možnosti nejsou k dispozici, pokud konfigurují možnosti na úrovni serveru, součásti operačního systému nebo určují konfiguraci systému souborů. Pokud jsou tyto možnosti požadovány, je vhodná alternativa často k dispozici jiným způsobem než SQL Database nebo z jiné funkce nebo služby Azure.

Další informace najdete v tématu [řešení rozdílů v jazyce Transact-SQL během migrace na SQL Database](sql-database-transact-sql-information.md).

## <a name="security"></a>Zabezpečení

SQL Database poskytuje řadu integrovaných funkcí [zabezpečení a dodržování předpisů](sql-database-security-overview.md) , které vaší aplikaci pomůžou splnit různé požadavky na zabezpečení a dodržování předpisů.

> [!IMPORTANT]
> Azure SQL Database (všechny možnosti nasazení) byly certifikovány na základě řady standardů dodržování předpisů. Další informace najdete v [Centru zabezpečení Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , kde můžete najít nejaktuálnější seznam SQL Database certifikace dodržování předpisů.

## <a name="next-steps"></a>Další kroky

- Pokud chcete rychle začít pracovat s izolovanou databází, začněte s [jednou příručkou pro rychlý Start databáze](sql-database-single-database-quickstart-guide.md).
- Další informace o migraci databáze SQL Server do Azure najdete v tématu [migrace na Azure SQL Database](sql-database-single-database-migrate.md).
- Informace o podporovaných funkcích najdete v tématu [Funkce](sql-database-features.md).
