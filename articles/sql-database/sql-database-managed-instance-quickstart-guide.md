---
title: Úvodní příručka – instance spravovaná sql
description: Zjistěte, jak rychle začít s Azure SQL Database – spravovanou instancí
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
ms.date: 07/11/2019
ms.openlocfilehash: 602de3e23eb5419958f84b071e2220550d1d04d0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "73821724"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>Začínáme se spravovanou instancí Azure SQL Database

Možnost nasazení [spravované instance](sql-database-managed-instance-index.yml) vytvoří databázi s téměř 100% kompatibilitou s nejnovějším místním databázovým strojem SQL Server (Enterprise Edition), který poskytuje implementaci nativní virtuální [sítě (VNet),](../virtual-network/virtual-networks-overview.md) která řeší běžné problémy se zabezpečením, a [obchodní model](https://azure.microsoft.com/pricing/details/sql-database/) příznivý pro místní zákazníky SQL Serveru. V tomto článku se dozvíte, jak rychle nakonfigurovat a vytvořit spravovanou instanci a migrovat databáze.

## <a name="quickstart-overview"></a>Rychlé starty – přehled

Následující rychlé starty umožňují rychle vytvořit spravovanou instanci, nakonfigurovat virtuální počítač nebo přejděte na síť `.bak` připojení VPN pro klientskou aplikaci a obnovit databázi do nové spravované instance pomocí souboru.

### <a name="configure-environment"></a>Konfigurace prostředí

Jako první krok budete muset vytvořit první spravovanou instanci v síťovém prostředí, kde bude umístěna, a povolit připojení z počítače nebo virtuálního počítače, kde provádíte dotazy na spravovanou instanci. Můžete použít následující vodítka:

- [Vytvořte spravovanou instanci pomocí portálu Azure](sql-database-managed-instance-get-started.md). Na webu Azure Portal nakonfigurujete potřebné parametry (uživatelské jméno/heslo, počet jader a maximální velikost úložiště) a automaticky vytvoříte síťové prostředí Azure bez nutnosti vědět o podrobnostech sítě a požadavcích na infrastrukturu. Jen se ujistěte, že máte [typ předplatného,](sql-database-managed-instance-resource-limits.md#supported-subscription-types) který je aktuálně povoleno vytvořit spravovanou instanci. Pokud máte vlastní síť, kterou chcete použít, nebo chcete síť přizpůsobit, přečtěte si informace [o konfiguraci existující instance spravované virtuální sítě pro Azure SQL Database](sql-database-managed-instance-configure-vnet-subnet.md) nebo vytvoření instance spravované virtuální sítí pro Azure SQL [Database](sql-database-managed-instance-create-vnet-subnet.md).
- Spravovaná instance se vytvoří ve vlastní virtuální síti bez veřejného koncového bodu. Pro přístup klientských aplikací můžete buď **vytvořit virtuální počítač ve stejné virtuální síti (jiné podsíti),** nebo vytvořit připojení VPN s bodem k **lokalitě k virtuální síti z klientského počítače** pomocí jednoho z těchto rychlých startů:
  - Povolte [veřejný koncový bod](sql-database-managed-instance-public-endpoint-configure.md) ve spravované instanci, abyste měli přístup k datům přímo z vašeho prostředí.
  - Vytvořte [virtuální počítač Azure ve virtuální síti spravované instance](sql-database-managed-instance-configure-vm.md) pro připojení klientských aplikací, včetně SQL Server Management Studio.
  - Nastavte [připojení VPN z bodu do sítě ke spravované instanci](sql-database-managed-instance-configure-p2s.md) z klientského počítače, ve kterém máte sql server Management Studio a další aplikace pro připojení klientů. Toto je další ze dvou možností připojení ke spravované instanci a k její virtuální síti.

  > [!NOTE]
  > Můžete také použít expresní trasu nebo připojení k lokalitě z místní sítě, ale tyto přístupy jsou mimo rozsah těchto rychlých startů.

Jako alternativu k ručnímu vytváření spravované instance můžete tento proces skriptovat a automatizovat pomocí [PowerShellu](scripts/sql-database-create-configure-managed-instance-powershell.md), [PowerShellu se šablonou Správce prostředků](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)nebo [Azure CLI.](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)

### <a name="migrate-your-databases"></a>Migrace databází

Po vytvoření spravované instance a konfiguraci přístupu můžete začít migrovat databáze z místního SQL Serveru nebo virtuálních aplikací Azure. Migrace se nezdaří, pokud máte některé nepodporované funkce ve zdrojové databázi, kterou chcete migrovat. Chcete-li se vyhnout chybám a zkontrolovat kompatibilitu, můžete nainstalovat [Pomocníka pro migraci dat (DMA),](https://www.microsoft.com/download/details.aspx?id=53595) který analyzuje databáze na serveru SQL Server a vyhledá jakýkoli problém, který by mohl blokovat migraci na spravovanou instanci, jako je například existence [FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) nebo více souborů protokolu. Pokud tyto problémy vyřešíte, jsou databáze připraveny k migraci do spravované instance. [Pomocník pro experimentování s databází](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/) je další užitečný nástroj, který může zaznamenávat vaše úlohy na serveru SQL Server a přehrát jej na spravované instanci, takže můžete určit, že budou existovat nějaké problémy s výkonem, pokud migrujete do spravované instance.

Jakmile jste si jisti, že můžete migrovat databázi do spravované instance, můžete použít nativní `.bak` sql server obnovení schopnosti obnovit databázi do spravované instance ze souboru. Tuto metodu můžete použít k migraci databází z databázového stroje SQL Server nainstalovaného místně nebo virtuálního počítače Azure. Úvodní příručka najdete v tématu [Obnovení ze zálohy na spravovanou instanci](sql-database-managed-instance-get-started-restore.md). V tomto rychlém startu můžete `.bak` obnovit ze souboru uloženého v úložišti objektů Blob Azure pomocí příkazu `RESTORE` Transact-SQL.

> [!TIP]
> Pokud chcete `BACKUP` pomocí příkazu Transact-SQL vytvořit zálohu databáze v úložišti objektů Blob Azure, [přečtěte](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)si část zálohování serveru SQL Server na adresu URL .

Tyto rychlé starty umožňují rychle vytvářet, konfigurovat a obnovovat zálohování databáze do spravované instance. V některých případech budete muset přizpůsobit nebo automatizovat nasazení spravovaných instancí a požadované síťové prostředí. Tyto scénáře budou popsány níže.

## <a name="customize-network-environment"></a>Přizpůsobení síťového prostředí

I když virtuální síť/podsíť může být automaticky nakonfigurován při vytvoření instance pomocí [portálu Azure](sql-database-managed-instance-get-started.md), může být dobré ji vytvořit před zahájením vytváření spravovaných instancí, protože můžete nakonfigurovat parametry virtuální sítě a podsítě. Nejjednodušší způsob, jak vytvořit a nakonfigurovat síťové prostředí, je použít [šablonu nasazení prostředků Azure,](sql-database-managed-instance-create-vnet-subnet.md) která vytvoří a nakonfiguruje síť a podsíť, kde bude instance umístěna. Stačí stisknout tlačítko nasazení Azure Resource Manager a naplnit formulář s parametry.

Jako alternativu můžete také použít tento [skript prostředí PowerShell](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) k automatizaci vytváření sítě.

Pokud už máte virtuální síť a podsíť, kde chcete nasadit spravovanou instanci, musíte se ujistit, že vaše virtuální síť a podsíť splňují [požadavky na síť](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Pomocí tohoto [skriptu prostředí PowerShell ověřte, zda je podsíť správně nakonfigurována](sql-database-managed-instance-configure-vnet-subnet.md). Tento skript ověří vaši síť a hlásí všechny problémy a řekne vám, co by se mělo změnit, a pak nabídne provést potřebné změny ve vaší virtuální síti nebo podsíti. Pokud nechcete nakonfigurovat virtuální síť/podsíť ručně, spusťte tento skript. Můžete jej také spustit po jakékoli větší rekonfiguraci síťové infrastruktury. Pokud chcete vytvořit a nakonfigurovat vlastní síť, přečtěte si [architekturu připojení](sql-database-managed-instance-connectivity-architecture.md) a tento [konečný návod pro vytváření a konfiguraci prostředí spravované instance](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01).

## <a name="migrate-to-a-managed-instance"></a>Migrace na spravovanou instanci

Články v těchto rychlých startech umožňují rychle nastavit spravovanou `RESTORE` instanci a přesunout databáze pomocí nativních funkcí. To to je dobrý výchozí bod, pokud chcete dokončit rychlé ověření konceptů a ověřte, že vaše řešení může pracovat na spravované instanci. 

Chcete-li však migrovat produkční databázi nebo dokonce dev/test databáze, které chcete použít pro některé test výkonu, budete muset zvážit použití některých dalších technik, jako jsou:
- Testování výkonu – měli byste měřit výkon směrného plánu na zdrojové instanci serveru SQL Server a porovnat je s výkonem v cílové spravované instanci, kam jste databázi migrovali. Další informace o [doporučených postupech pro porovnání výkonu](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).
- Migrace online – `RESTORE` s nativní popsané v tomto článku, budete muset počkat na databáze, které mají být obnoveny (a zkopírovat do úložiště objektů blob Azure, pokud již nejsou uloženy tam). To způsobí, že některé prostoje aplikace zejména pro větší databáze. Chcete-li přesunout produkční databázi, použijte [službu migrace dat (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json) k migraci databáze s minimálními prostoji. DMS toho dosahuje postupným odesíláním změn provedených ve zdrojové databázi do obnovené databáze spravovaných instancí. Tímto způsobem můžete rychle přepnout aplikaci ze zdrojové do cílové databáze s minimálními prostoji.

Přečtěte si další informace o [doporučeném procesu migrace](sql-database-managed-instance-migrate.md).

## <a name="next-steps"></a>Další kroky

- Zde najdete [seznam podporovaných funkcí na vysoké úrovni ve spravované instanci](sql-database-features.md) a [podrobnosti a známé problémy zde](sql-database-managed-instance-transact-sql-information.md).
- Informace o [technických vlastnostech spravované instance](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).
- Najděte pokročilejší způsoby použití v [tom, jak používat spravovanou instanci v Azure SQL Database](sql-database-howto-managed-instance.md).
- [Identifikujte správnou skladovou položku azure sql databáze/spravované instance pro místní databázi](/sql/dma/dma-sku-recommend-sql-db/).
