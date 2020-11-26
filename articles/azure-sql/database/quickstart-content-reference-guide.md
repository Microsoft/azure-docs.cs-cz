---
title: Referenční informace k obsahu pro rychlý Start pro izolovanou databázi
description: Seznamte se s obsahem všech rychlých startů, které vám pomůžou rychle začít s izolovanými databázemi v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/29/2019
ms.openlocfilehash: 392f361f6fe487a15bdd63c2f3281ccc78b2f86d
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184412"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Začínáme s izolovanými databázemi v Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Jediná databáze](../index.yml) je plně spravovaná databáze PaaS (Platform as a Service) jako služba (DbaaS), která je ideálním modulem úložiště pro moderní aplikace v cloudu. V této části se dozvíte, jak rychle nakonfigurovat a vytvořit izolovanou databázi v Azure SQL Database.

## <a name="quickstart-overview"></a>Rychlé starty – přehled

V této části uvidíte přehled dostupných článků, které vám pomohou rychle začít s izolovanými databázemi. Následující rychlé starty vám umožní rychle vytvořit izolovanou databázi, nakonfigurovat pravidlo brány firewall na úrovni serveru a pak importovat databázi do nové izolované databáze pomocí `.bacpac` souboru:

- [Vytvořte jednu databázi pomocí Azure Portal](single-database-create-quickstart.md).
- Po vytvoření databáze bude nutné [zabezpečit databázi konfigurací pravidel brány firewall](firewall-create-server-level-portal-quickstart.md).
- Pokud máte v SQL Server existující databázi, kterou chcete migrovat na Azure SQL Database, měli byste nainstalovat [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) , která bude analyzovat vaše databáze na SQL Server, a najít případné potíže, které by mohly blokovat migraci. Pokud nějaký problém nenajdete, můžete databázi exportovat jako `.bacpac` soubor a [importovat ji pomocí Azure Portal nebo SqlPackage](database-import.md).


## <a name="automating-management-operations"></a>Automatizace operací správy

K vytvoření, konfiguraci a škálování databáze můžete použít PowerShell nebo Azure CLI.

- [Vytvoření a konfigurace izolované databáze pomocí prostředí PowerShell](scripts/create-and-configure-database-powershell.md) nebo rozhraní příkazového [řádku Azure](scripts/create-and-configure-database-cli.md)
- [Aktualizace izolované databáze a škálování prostředků pomocí PowerShellu](scripts/monitor-and-scale-database-powershell.md) nebo rozhraní příkazového [řádku Azure](scripts/monitor-and-scale-database-cli.md)

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Migrace na izolovanou databázi s minimálními výpadky

Tyto rychlé starty vám umožní rychle vytvořit nebo importovat databázi do Azure pomocí `.bacpac` souboru. `.bacpac` `.dacpac` Soubory a jsou ale navržené k rychlému přesunu databází v různých verzích SQL Server a v rámci Azure SQL nebo k implementaci průběžné integrace do kanálu DevOps. Tato metoda není navržená pro migraci provozních databází s minimálními výpadky, protože byste museli ukončit přidávání nových dat, počkat na dokončení exportu zdrojové databáze do `.bacpac` souboru a pak počkat na dokončení importu do Azure SQL Database. Všechny tyto čekající výsledky v důsledku výpadku vaší aplikace, zejména pro velké databáze. Pro přesun provozní databáze potřebujete lepší způsob migrace, která zaručuje minimální výpadky migrace. K tomu je potřeba použít [službu migrace dat (DMS)](../../dms/tutorial-sql-server-to-azure-sql.md?toc=%2fazure%2fsql-database%2ftoc.json) k migraci databáze s minimálními výpadky. DMS to provede přírůstkovým vložením změn provedených ve zdrojové databázi do databáze, která se obnovuje. Tímto způsobem můžete rychle přepnout aplikaci ze zdrojového do cílové databáze s minimálními výpadky.

## <a name="hands-on-learning-modules"></a>Praktické výukové moduly

Následující Microsoft Learn moduly vám pomůžou naučit se zdarma o Azure SQL Database.

- [Zřízení databáze v SQL Database k ukládání dat aplikací](/learn/modules/provision-azure-sql-db/)
- [Vývoj a konfigurace aplikace ASP.NET, která se dotazuje databáze v Azure SQL Database](/learn/modules/develop-app-that-queries-azure-sql/)
- [Zabezpečení databáze v Azure SQL Database](/learn/modules/secure-your-azure-sql-database/)

## <a name="next-steps"></a>Další kroky

- [Seznam podporovaných funkcí v Azure SQL Database najdete na nejvyšší úrovni](features-comparison.md).
- Přečtěte si, jak zvýšit [zabezpečení databáze](secure-database-tutorial.md).
- Najděte si pokročilejší postupy, [Jak používat izolovanou databázi v Azure SQL Database](how-to-content-reference-guide.md).
- Najděte si další ukázkové skripty napsané v [PowerShellu](powershell-script-content-guide.md) a v [Azure CLI](az-cli-script-samples-content-guide.md).
- Přečtěte si další informace o [rozhraní API pro správu](single-database-manage.md) , které můžete použít ke konfiguraci databází.
- [Identifikujte správnou Azure SQL Database nebo skladovou jednotku spravované instance SQL Azure pro vaši místní databázi](/sql/dma/dma-sku-recommend-sql-db/).