---
title: Co je jediná databáze?
description: Přečtěte si o typu jednoho databázového prostředku v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 04/08/2019
ms.openlocfilehash: 8f92fe8e4a4ebbc2d970bf28e415859249b9f67c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "84343315"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>Co je jediná databáze v Azure SQL Database?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Typ prostředku jediné databáze vytvoří v Azure SQL Database databázi s vlastní sadou prostředků a spravuje ji prostřednictvím [serveru](logical-servers.md). U jediné databáze je každá databáze izolovaná a přenosná. Každý má svou vlastní úroveň služby v rámci [nákupního modelu založeného na DTU](service-tiers-dtu.md) nebo na [základě Vcore](service-tiers-vcore.md) a zaručené výpočetní velikosti.

> [!IMPORTANT]
> Jedna databáze je jedním z typů prostředků Azure SQL Database. Druhá je [elastické fondy](elastic-pool-overview.md).

## <a name="dynamic-scalability"></a>Dynamická škálovatelnost

V rámci výpočetní úrovně bez serveru nebo malé výpočetní velikosti v zřízené výpočetní úrovni můžete vytvořit svou první aplikaci s malou jedinou databází za nízkou cenu. [Výpočetní kapacitu nebo úroveň služby](single-database-scale.md) můžete změnit ručně nebo programově, aby vyhovovaly potřebám vašeho řešení. Můžete upravit úroveň výkonu bez přerušení provozu aplikace a bez dopadu na vaše zákazníky. Dynamická škálovatelnost umožňuje databázím transparentně reagovat na rychle se měnící požadavky na prostředky a vy díky tomu platíte pouze za prostředky, které potřebujete, když je potřebujete.

## <a name="single-databases-and-elastic-pools"></a>Izolované databáze a elastické fondy

Pro sdílení prostředků lze přesunout jednu databázi do [elastického fondu](elastic-pool-overview.md) nebo z něj. Řadě firem a aplikací stačí, že může vytvářet izolované databáze a nastavovat větší nebo menší výkon na vyžádání, zejména při relativně předvídatelném způsobu používání. Ale pokud vaše vzorce používání předvídatelné nejsou, může být správa nákladů a údržba obchodního modelu velmi těžká. Elastické fondy jsou navržené k řešení tohoto problému. Princip je jednoduchý. Prostředky výkonu přidělíte místo individuální databáze a platíte za celkový výkon fondu, nikoli pro jednotlivé databáze.

## <a name="monitoring-and-alerting"></a>Monitorování a upozorňování

V kombinaci s hodnocením výkonu použijete integrované nástroje pro [monitorování výkonu](performance-guidance.md) a [upozorňování](alerts-insights-configure-portal.md). Pomocí těchto nástrojů můžete rychle posoudit dopad vertikálního navýšení nebo snížení kapacity v závislosti na stávajících nebo předpokládaných požadavcích. Kromě toho SQL Database možné [vygenerovat metriky a protokoly prostředků](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) pro snazší monitorování.

## <a name="availability-capabilities"></a>Možnosti dostupnosti

Izolované databáze a elastické fondy poskytují mnoho vlastností dostupnosti. Informace najdete v tématu věnovaném [vlastnostem dostupnosti](sql-database-paas-overview.md#availability-capabilities).

## <a name="transact-sql-differences"></a>Rozdíly v Transact-SQL

Většina funkcí Transact-SQL, které aplikace používá, je plně podporovaná v Microsoft SQL Server i Azure SQL Database. Například základní komponenty SQL, jako jsou datové typy, operátory, řetězce, aritmetické funkce, logické a kurzorové funkce, fungují stejně v SQL Server a SQL Database. Existuje však několik rozdílů v jazyce T-SQL v prvcích DDL (Data-Definition Language) a DML (data pro manipulaci s daty), které jsou výsledkem příkazů T-SQL a dotazů, které jsou pouze částečně podporovány (což se zabývá dále v tomto článku).

Kromě toho existují některé funkce a syntaxe, které nejsou podporované, protože Azure SQL Database slouží k izolaci funkcí ze závislostí v hlavní databázi a v operačním systému. Proto většina aktivit na úrovni serveru není pro SQL Database vhodná. Příkazy T-SQL a možnosti nejsou k dispozici, pokud konfigurují možnosti na úrovni serveru, konfigurují součásti operačního systému nebo určují konfiguraci systému souborů. Pokud jsou tyto možnosti požadovány, je vhodná alternativa často k dispozici jiným způsobem než SQL Database nebo z jiné funkce nebo služby Azure.

Další informace najdete v tématu [řešení rozdílů v jazyce Transact-SQL během migrace na SQL Database](transact-sql-tsql-differences-sql-server.md).

## <a name="security"></a>Zabezpečení

SQL Database poskytuje řadu [integrovaných funkcí zabezpečení a dodržování předpisů](security-overview.md) , které vaší aplikaci pomůžou splnit různé požadavky na zabezpečení a dodržování předpisů.

> [!IMPORTANT]
> Azure SQL Database bylo certifikováno pro určitý počet standardů dodržování předpisů. Další informace najdete v [Centru zabezpečení Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), kde můžete najít nejaktuálnější seznam SQL Database certifikace dodržování předpisů.

## <a name="next-steps"></a>Další kroky

- Pokud chcete rychle začít pracovat s izolovanou databází, začněte s [jednou příručkou pro rychlý Start databáze](quickstart-content-reference-guide.md).
- Další informace o migraci databáze SQL Server do Azure najdete v tématu [migrace na Azure SQL Database](migrate-to-database-from-sql-server.md).
- Informace o podporovaných funkcích najdete v tématu [Funkce](features-comparison.md).
