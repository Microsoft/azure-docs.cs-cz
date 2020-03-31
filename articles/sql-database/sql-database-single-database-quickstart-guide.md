---
title: Úvodní příručka - jednotlivé databáze
description: Zjistěte, jak rychle začít s jednotlivými databázemi v Azure SQL Database
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
ms.openlocfilehash: 6070b53e5f906bc378402d98275b8f798f57b505
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78674433"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Začínáme s jednotlivými databázemi v Azure SQL Database

[Jedna databáze](sql-database-single-index.yml) je plně spravovaná databáze PaaS jako služba (DbaaS), která je ideálním úložným strojem pro moderní aplikace, které se narodily v cloudu. V této části se dozvíte, jak rychle nakonfigurovat a vytvořit jednu databázi v databázi SQL.

## <a name="quickstart-overview"></a>Rychlé starty – přehled

V této části se zobrazí přehled dostupných článků, které vám pomohou rychle začít s jednotlivými databázemi. Následující rychlé starty umožňují rychle vytvořit jednu databázi, nakonfigurovat pravidlo brány firewall databázového `.bacpac` serveru a potom importovat databázi do nové jediné databáze pomocí souboru:

- [Vytvořte jednu databázi pomocí portálu Azure](sql-database-single-database-get-started.md).
- Po vytvoření databáze je nutné [zabezpečit databázi konfigurací pravidel brány firewall](sql-database-server-level-firewall-rule.md).
- Pokud máte existující databázi na SQL Server, který chcete migrovat do Azure, měli byste nainstalovat [Pomocníka pro migraci dat (DMA),](https://www.microsoft.com/download/details.aspx?id=53595) který bude analyzovat databáze na SQL Serveru a najít jakýkoli problém, který by mohl blokovat migraci na možnost nasazení jedné databáze. Pokud nenajdete žádný problém, můžete exportovat `.bacpac` databázi jako soubor a [importovat ji pomocí portálu Azure nebo SqlPackage](sql-database-import.md).

## <a name="automating-management-operations"></a>Automatizace operací správy

Pomocí PowerShellu nebo rozhraní příkazového příkazu Azure můžete vytvořit, nakonfigurovat a škálovat databázi.

- [Vytvoření a konfigurace jedné databáze pomocí PowerShellu](scripts/sql-database-create-and-configure-database-powershell.md) nebo [Azure CLI](scripts/sql-database-create-and-configure-database-cli.md)
- [Aktualizace jediné databáze a škálování prostředků pomocí PowerShellu](scripts/sql-database-monitor-and-scale-database-powershell.md) nebo [Azure CLI](scripts/sql-database-monitor-and-scale-database-cli.md)

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Migrace do jedné databáze s minimálními prostoji

Tyto rychlé starty umožňují rychle vytvořit nebo importovat databázi do Azure pomocí souboru. `.bacpac` Soubory `.bacpac` a `.dacpac` soubory jsou však navrženy tak, aby rychle přesouvat databáze v různých verzích SQL Serveru a možnosti nasazení v rámci Azure SQL Database nebo implementovat průběžnou integraci v kanálu DevOps. Tato metoda však není určena pro migraci produkčních databází s minimálníprostožně, protože budete muset přestat `.bacpac` přidávat nová data, čekat na dokončení exportu zdrojové databáze do souboru a pak čekat na dokončení importu do Azure SQL Database. To vše čekání výsledky prostoje aplikace, zejména pro velké databáze. Chcete-li přesunout produkční databázi, potřebujete lepší způsob migrace, který zaručuje minimální prostoje migrace. Za tímto účelem použijte [službu migrace dat (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json) k migraci databáze s minimálními prostoji.. DMS toho dosáhne postupným odesíláním změn provedených ve zdrojové databázi do jedné obnovované databáze. Tímto způsobem můžete rychle přepnout aplikaci ze zdrojové do cílové databáze s minimálními prostoji.

## <a name="hands-on-learning-modules"></a>Praktické výukové moduly

Následující moduly Microsoft Learn vám pomůžou zdarma se učit o Azure SQL Database.

- [Zřízení databáze Azure SQL pro ukládání dat aplikací](https://docs.microsoft.com/learn/modules/provision-azure-sql-db/)
- [Vývoj a konfigurace ASP.NET aplikace, která se dotazuje na azure SQL databázi](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/)
- [Zabezpečení databáze Azure SQL](https://docs.microsoft.com/learn/modules/secure-your-azure-sql-database/)

## <a name="next-steps"></a>Další kroky

- Najděte [podrobný seznam podporovaných funkcí v Azure SQL Database](sql-database-features.md).
- Přečtěte si, jak [zabezpečit databázi](sql-database-security-tutorial.md).
- Najděte pokročilejší návody v [tom, jak používat jednu databázi v Azure SQL Database](sql-database-howto-single-database.md).
- Další ukázkové skripty napsané v [PowerShellu](sql-database-powershell-samples.md) a [Azure CLI](sql-database-cli-samples.md).
- Přečtěte si další informace o [rozhraní API pro správu,](sql-database-single-databases-manage.md) které můžete použít ke konfiguraci databází.
- [Identifikujte správnou skladovou položku azure sql databáze/spravované instance pro místní databázi](/sql/dma/dma-sku-recommend-sql-db/).
