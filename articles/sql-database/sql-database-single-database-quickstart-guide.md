---
title: Rychlý start – izolované databáze ve službě Azure SQL Database | Dokumentace Microsoftu
description: Zjistěte, jak rychle začít používat izolované databáze ve službě Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: 0dc9d18ea4dae6ea0f45d1a91392eb1994e6e80e
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55757406"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Začínáme s izolované databáze ve službě Azure SQL Database

[Izolované databáze](sql-database-single-index.yml) je plně spravovaná databáze PaaS jako služba (DbaaS), která je ideální úložiště modul moderních cloudových aplikací. V této části se dozvíte, jak rychle nakonfigurovat a vytvoření izolované databáze ve službě SQL Database.

## <a name="quickstart-overview"></a>Rychlý start – přehled

V této části zobrazí přehled dostupných články, které vám umožňují rychle začít používat izolované databáze. Následujících šablon rychlý start vám umožňují rychle vytvářet izolované databáze, konfigurace pravidla brány firewall serveru databáze a pak importovat databázi do nové izolované databáze pomocí `.bacpac` souboru:

- [Vytvoření izolované databáze pomocí webu Azure portal](sql-database-single-database-get-started.md).
- Po vytvoření databáze, je třeba [zabezpečení databáze pomocí konfigurace pravidla brány firewall](sql-database-server-level-firewall-rule.md).
- Pokud máte existující databázi na SQL Server, který chcete migrovat do Azure, měli byste nainstalovat [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) , která analyzuje vaše databáze na SQL serveru a najít všechny problémy, které by mohly blokovat migrace do jedné možnost nasazení databáze. Pokud nenajdete žádné potíže, můžete exportovat databázi jako `.bacpac` souboru a [naimportujete pomocí webu Azure portal nebo SqlPackage](sql-database-import.md).

## <a name="automating-management-operations"></a>Automatizace operací správy

Vytvořte, nakonfigurujte a škálujte databázi, můžete použít PowerShell nebo rozhraní příkazového řádku Azure.

- [Vytvoření a konfigurace izolované databáze pomocí prostředí PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)
- [Vytvoření a konfigurace izolované databáze pomocí rozhraní příkazového řádku Azure](scripts/sql-database-create-and-configure-database-cli.md)
- [Aktualizace jedné databáze a škálování prostředků pomocí Powershellu](scripts/sql-database-monitor-and-scale-database-powershell.md)
- [Aktualizace jedné databáze a škálování prostředků pomocí Azure CLI](scripts/sql-database-monitor-and-scale-database-cli.md)

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Migrace na izolované databáze s minimálními výpadky

V těchto rychlých startech umožňují rychle vytvořit nebo importovat databázi do Azure s využitím `.bacpac` souboru. Ale `.bacpac` a `.dacpac` souborů jsou navržené tak, jak rychle přesunout databáze v různých verzích systému SQL Server a možnosti nasazení v rámci Azure SQL Database nebo implementaci nepřetržité integrace do vašeho kanálu DevOps. Však tato metoda není určená pro migraci vašich provozních databází s minimálními výpadky, protože je třeba zastavit přidávání nových dat, počkejte export zdrojové databáze do `.bacpac` souborů k dokončení a potom počkejte importu do Azure SQL Database k dokončení. Všechna tato čekací za následek výpadek aplikace, zejména u velkých databází. Chcete-li přesunout provozní databázi, budete potřebovat lepší způsob, jak migrovat, které zaručují minimálními prostoji migrace. V takovém případě použijte [Data Migration Service (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json) migrace databáze s minimálními prostoji... DMS dosahuje tak, že postupně doručením (push) změny provedené ve zdrojové databázi izolované databáze obnovena. Díky tomu můžete rychle přepínat aplikace ze zdroje do cílové databáze s minimálními výpadky.

## <a name="next-steps"></a>Další postup

- Najít [podrobný seznam podporovaných funkcí ve službě Azure SQL Database](sql-database-features.md).
- Naučte se, aby vaše [databáze bezpečnější](sql-database-security-tutorial.md).
- Jak najít pokročilejší-pro uživatele [použití izolované databáze ve službě Azure SQL Database](sql-database-howto-single-database.md).
- Najít další ukázkové skripty, které jsou napsané v [PowerShell](sql-database-powershell-samples.md) a [rozhraní příkazového řádku Azure](sql-database-cli-samples.md).
- Další informace o [rozhraní API pro správu](sql-database-single-databases-manage.md) , můžete použít ke konfiguraci vašich databází.
