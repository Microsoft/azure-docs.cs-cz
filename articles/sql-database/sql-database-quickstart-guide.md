---
title: Rychlý start – izolované databáze ve službě Azure SQL Database | Dokumentace Microsoftu
description: Zjistěte, jak rychle začít používat izolované databáze ve službě Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 72ffdb357b5469b71da7655104add1135bf114fb
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55464803"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Začínáme s izolované databáze ve službě Azure SQL Database

[Izolované databáze](sql-database-single-index.yml) ve službě Azure SQL Database je plně spravovaná databáze PaaS jako služba (DbaaS), která je ideální úložiště modul moderních cloudových aplikací. V této části se dozvíte, jak rychle nakonfigurovat a vytvoření databáze SQL.

## <a name="quickstart-overview"></a>Rychlý start – přehled

V této části zobrazí přehled dostupných články, které vám umožňují rychle začít používat izolované databáze. Nejjednodušší způsob, jak vytvořit svoji první databázi SQL je použití [na webu Azure portal](sql-database-get-started-portal.md) ve kterém můžete nakonfigurovat potřebné parametry.
Po vytvoření je potřeba [zabezpečení databáze pomocí konfigurace pravidla brány firewall](sql-database-get-started-portal-firewall.md). 

Pokud máte existující databázi na SQL Server, který chcete migrovat do Azure, měli byste nainstalovat [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) , která analyzuje vaše databáze na SQL serveru a najít všechny problémy, které by mohly blokovat migrace na jednom Databáze. Pokud nenajdete žádné potíže, můžete exportovat databázi jako `.bacpac` souboru a [naimportujete pomocí webu Azure portal nebo SqlPackage](sql-database-import.md).

V těchto rychlých startech umožňují rychle konfigurovat, vytvořit a importovat vaše databáze v cloudu Azure.

## <a name="automating-management-operations"></a>Automatizace operací správy

Na webu Azure portal umožňuje snadno vytvářet a upravovat izolované databáze. Jako alternativu můžete použít [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md), nebo [rozhraní příkazového řádku Azure](scripts/sql-database-create-and-configure-database-cli.md) k vytváření databází.
Můžete také aktualizovat jedné databáze a škálování prostředků pomocí [PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md) nebo [rozhraní příkazového řádku Azure](scripts/sql-database-monitor-and-scale-database-cli.md).

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Migrace na izolované databáze s minimálními výpadky

Články v těchto rychlých startech umožňují rychle vytvořit nebo importovat databázi do Azure s využitím `.bacpac`. Ale `.bacpac` a `.dacpack` souborů jsou navržené tak, jak rychle přesunout databáze v různých verzích systému SQL Server a Azure SQL Database (izolované databáze, elastický fond nebo spravované Instance), jak implementovat průběžné integrace ve vaší DevOps kanál. Však tato metoda není určená pro migraci vašich provozních databází s minimálními výpadky, protože by bylo nutné čekat na export zdrojové databáze jako `.bacpac` soubor a naimportujte ho do Azure SQL Database, která způsobí výpadek z vaší aplikace, zejména v případě, že databáze je větší. Při přesunutí provozní databáze, je třeba pravděpodobně lepší způsob, jak migrovat, který zaručí minimálními prostoji migrace. [Data Migration Service](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json) je služba, která můžete migrovat databáze s minimálními výpadky. Díky tomu můžete rychle přepínat aplikace ze zdroje do cílové databáze s minimálními výpadky.

## <a name="next-steps"></a>Další postup

* Najít [podrobný seznam podporovaných funkcí ve službě Azure SQL Database](sql-database-features.md). 
* Naučte se, aby vaše [databáze bezpečnější](sql-database-security-tutorial.md). 
* Najít další pokročilé kurzy v [jak části](sql-database-howto-single-database.md). 
* Najít další ukázkové skripty, které jsou napsané v [PowerShell](sql-database-powershell-samples.md) a [rozhraní příkazového řádku Azure](sql-database-cli-samples.md). 
* Další informace o [rozhraní API pro správu](sql-database-single-databases-manage.md) , můžete použít ke konfiguraci vašich databází. 
