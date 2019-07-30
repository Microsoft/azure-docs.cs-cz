---
title: Rychlý Start – jednotlivé databáze v Azure SQL Database | Microsoft Docs
description: Naučte se, jak rychle začít s izolovanými databázemi v Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
ms.date: 07/29/2019
ms.openlocfilehash: 45b0b2bc7a2ef5c3cb6ca801668f7b5be7c8ac73
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639997"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Začínáme s izolovanými databázemi v Azure SQL Database

[Jediná databáze](sql-database-single-index.yml) je plně spravovaná databáze PaaS jako služba (DbaaS), která je ideálním modulem úložiště pro moderní aplikace v cloudu. V této části se dozvíte, jak rychle nakonfigurovat a vytvořit izolovanou databázi v SQL Database.

## <a name="quickstart-overview"></a>Přehled rychlého startu

V této části se zobrazí přehled dostupných článků, které vám pomohou rychle začít s izolovanými databázemi. Následující rychlé starty vám umožní rychle vytvořit izolovanou databázi, nakonfigurovat pravidlo brány firewall databázového serveru a pak importovat databázi do nové izolované databáze pomocí `.bacpac` souboru:

- [Vytvořte jednu databázi pomocí Azure Portal](sql-database-single-database-get-started.md).
- Po vytvoření databáze bude nutné [zabezpečit databázi konfigurací pravidel brány firewall](sql-database-server-level-firewall-rule.md).
- Pokud máte v SQL Server existující databázi, kterou chcete migrovat do Azure, měli byste nainstalovat [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) , která bude analyzovat vaše databáze na SQL Server, a najít případné potíže, které by mohly blokovat migraci do nasazení jediné databáze. nastavení. Pokud nějaký problém nenajdete, můžete databázi exportovat jako `.bacpac` soubor a [importovat ji pomocí Azure Portal nebo SqlPackage](sql-database-import.md).

## <a name="automating-management-operations"></a>Automatizace operací správy

K vytvoření, konfiguraci a škálování databáze můžete použít PowerShell nebo Azure CLI.

- [Vytvoření a konfigurace izolované databáze pomocí prostředí PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)
- [Vytvoření a konfigurace izolované databáze pomocí Azure CLI](scripts/sql-database-create-and-configure-database-cli.md)
- [Aktualizace databáze a škálování prostředků pomocí PowerShellu](scripts/sql-database-monitor-and-scale-database-powershell.md)
- [Aktualizujte svou databázi a škálujte prostředky pomocí Azure CLI.](scripts/sql-database-monitor-and-scale-database-cli.md)

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Migrace na izolovanou databázi s minimálními výpadky

Tyto rychlé starty vám umožní rychle vytvořit nebo importovat databázi do Azure pomocí `.bacpac` souboru. `.bacpac` Soubory a `.dacpac` jsou ale navržené k rychlému přesunu databází napříč různými verzemi SQL Server a možností nasazení v rámci Azure SQL Database nebo k implementaci průběžné integrace do kanálu DevOps. Tato metoda není navržená pro migraci provozních databází s minimálními prostoji, protože byste museli ukončit přidávání nových dat, počkat na dokončení exportu zdrojové databáze do `.bacpac` souboru a pak počkat na import do Azure SQL Database k dokončení. Všechny tyto čekající výsledky v důsledku výpadku vaší aplikace, zejména pro velké databáze. Pro přesun provozní databáze potřebujete lepší způsob migrace, která zaručuje minimální výpadky migrace. K tomu je potřeba použít [službu migrace dat (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json) k migraci databáze s minimálními výpadky. DMS to provede přírůstkovým vložením změn provedených ve zdrojové databázi do databáze, která se obnovuje. Tímto způsobem můžete rychle přepnout aplikaci ze zdrojového do cílové databáze s minimálními výpadky.

## <a name="hands-on-learning-modules"></a>Praktické výukové moduly

Následující Microsoft Learn moduly vám pomůžou naučit se zdarma o Azure SQL Database.

- [Zřízení databáze SQL Azure pro ukládání dat aplikací](https://docs.microsoft.com/learn/modules/provision-azure-sql-db/)
- [Vývoj a konfigurace aplikace ASP.NET, která se dotazuje na Azure SQL Database](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/)
- [Zabezpečení Azure SQL Database](https://docs.microsoft.com/learn/modules/secure-your-azure-sql-database/)

## <a name="next-steps"></a>Další kroky

- [Seznam podporovaných funkcí v Azure SQL Database najdete na nejvyšší úrovni](sql-database-features.md).
- Přečtěte si, jak zvýšit [zabezpečení databáze](sql-database-security-tutorial.md).
- Najděte si pokročilejší postupy, [Jak používat izolovanou databázi v Azure SQL Database](sql-database-howto-single-database.md).
- Najděte si další ukázkové skripty napsané v [PowerShellu](sql-database-powershell-samples.md) a [Azure CLI](sql-database-cli-samples.md).
- Přečtěte si další informace o [rozhraní API pro správu](sql-database-single-databases-manage.md) , které můžete použít ke konfiguraci databází.
- [Identifikujte správnou Azure SQL Database/SKU spravované instance pro vaši místní databázi](/sql/dma/dma-sku-recommend-sql-db/).
