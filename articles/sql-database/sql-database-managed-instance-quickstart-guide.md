---
title: Rychlý start – Azure SQL Database managed instance | Dokumentace Microsoftu
description: Zjistěte, jak rychle začít s Azure SQL Database – spravovaná instance
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: ec0007e2d53a3fd3cae158375b696379d923b4b3
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447775"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>Začínáme s Azure SQL Database managed instance

[Spravovanou instanci](sql-database-managed-instance-index.yml) možnost nasazení vytvoří databázi s téměř 100 % kompatibilitu s nejnovější systému SQL Server (Enterprise Edition) na místní databázový stroj, poskytuje nativní [virtuální síť (VNet)](../virtual-network/virtual-networks-overview.md) implementace, která řeší běžné problémy zabezpečení a [obchodního modelu](https://azure.microsoft.com/pricing/details/sql-database/) uspokojivým pro zákazníky v místním systému SQL Server. V tomto článku se dozvíte, jak rychle nakonfigurovat a vytvoření managed instance a migrace databází.

## <a name="quickstart-overview"></a>Rychlý start – přehled

Tyto rychlé starty vám umožní rychle vytvořit managed instance, konfigurace virtuálního počítače nebo přejděte na připojení VPN typu site pro klientskou aplikaci a obnovit databázi k nové spravované instance pomocí `.bak` souboru.

### <a name="configure-environment"></a>Konfigurace prostředí

Jako první krok je třeba vytvořit první Managed Instance pomocí síťového prostředí, kde budou umístěny a povolit připojení z počítače nebo virtuálního počítače, ve kterém se spouštěním dotazů do Managed Instance. Můžete použít následující příručky:

- [Vytvoření managed instance pomocí webu Azure portal](sql-database-managed-instance-get-started.md). Na webu Azure Portal nakonfigurujte potřebné parametry (uživatelského jména a hesla, počet jader a množství maximální velikosti úložiště) a automaticky vytvořit prostředí Azure sítě bez nutnosti znát podrobnosti o síti a požadavky na infrastrukturu. Můžete dejte pozor, abyste měli [typ předplatného](sql-database-managed-instance-resource-limits.md#supported-subscription-types) , který je aktuálně povoleno vytvoření managed instance. Pokud máte vlastní sítě, kterou chcete použít, nebo chcete přizpůsobit sítě naleznete v tématu [konfigurace existující virtuální sítě pro spravovanou instanci Azure SQL Database](sql-database-managed-instance-configure-vnet-subnet.md) nebo [vytvoření virtuální sítě pro službu Azure SQL Database spravovaná instance](sql-database-managed-instance-create-vnet-subnet.md).
- Spravovaná instance je vytvořena ve vlastní virtuální síť, bez veřejného koncového bodu. Pro přístup z aplikace klienta, můžete je buď **vytvoření virtuálního počítače ve stejné virtuální síti (jiné podsíti)** nebo **vytvořit připojení VPN typu point-to-site k virtuální síti z klientských počítačů** pomocí jedné z nich šablony rychlý start:

  - Vytvoření [virtuálního počítače Azure ve spravované instanci VNet](sql-database-managed-instance-configure-vm.md) připojením klienta aplikace, včetně SQL Server Management Studio.
  - Nastavit [připojení VPN typu point-to-site k vaší spravované instance](sql-database-managed-instance-configure-p2s.md) z klientského počítače, na kterých máte SQL Server Management Studio a další aplikace připojení klienta. Toto je druhý operand dvě možnosti pro připojení k vaší spravované instanci a k jeho virtuální síti.

  > [!NOTE]
  > Můžete také použít expressroute nebo připojení site-to-site z místní sítě, ale tyto přístupy jsou mimo rozsah v těchto rychlých startech.

### <a name="migrate-your-databases"></a>Migrace databází

Po vytvoření managed instance a konfigurovat přístup, můžete začít migrovat databáze z místního SQL serveru nebo virtuálních počítačích Azure. Migrace selže, pokud máte některé nepodporované funkce ve zdrojové databázi, kterou chcete migrovat. Pokud chcete zabránit selhání a zkontrolovat kompatibilitu, můžete nainstalovat [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) , která analyzuje vaše databáze na SQL serveru a najde některý problém, který může způsobit zablokování migraci do spravované instance, jako je například existence [FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) nebo víc souborů protokolů. Pokud tyto problémy vyřešit, vaše databáze jsou připravené k migraci do spravované instance. [Databáze služby experimentování ve službě pomocníka](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/) je další užitečná nástroj, který může zaznamenávat vaše úlohy na serveru SQL Server a opětovného přehrání ho ve službě managed instance, můžete určit chystají možné problémy s výkonem, pokud provádíte migraci do spravované instance.

Jakmile jste si jisti, že můžete migrovat databáze do spravované instance, nativní možnosti obnovení systému SQL Server můžete obnovit databázi do managed instance z `.bak` souboru. Tuto metodu můžete použít k migraci databází systému SQL Server nainstalovaný modul místní databáze nebo virtuální počítač Azure. Rychlý start, naleznete v tématu [obnovit ze zálohy do spravované instance](sql-database-managed-instance-get-started-restore.md). V tomto rychlém startu, můžete obnovit ze `.bak` souboru uloženého v Azure Blob storage pomocí `RESTORE` příkazu jazyka Transact-SQL.

> [!TIP]
> Použít `BACKUP` naleznete v tématu příkazu Transact-SQL ve službě Azure Blob storage, vytvořte zálohu vaší databáze [zálohování serveru SQL Server na adresu URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url).

V těchto rychlých startech umožňují rychle vytvářet, konfigurovat a obnovte zálohu databáze do spravované instance. V některých případech je třeba přizpůsobit nebo automatizovat nasazení spravované instance a požadované síťové prostředí. Tyto scénáře budou popsány níže.

## <a name="customize-network-environment"></a>Přizpůsobení prostředí sítě

I když se virtuální síť/podsíť může automaticky nakonfiguruje při vytvoření instance pomocí [na webu Azure portal](sql-database-managed-instance-get-started.md), může být vhodné vytvořte ho, než můžete začít vytvářet spravovaných instancí, protože můžete nakonfigurovat parametry sítě vnet a podsítě. Nejjednodušší způsob, jak vytvořit a konfigurovat síťové prostředí je použití [nasazení prostředků Azure](sql-database-managed-instance-create-vnet-subnet.md) šablonu, která vytvoří a nakonfiguruje je síť a podsíť, kde budou umístěné na instanci. Stačí stisknout klávesu Azure Resource Manageru nasadit tlačítko a vyplnit formulář s parametry.

Jako alternativu můžete použít [skript prostředí PowerShell](https://www.powershellmagazine.com/20../../configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) k automatizaci vytváření sítě.

Jako alternativu můžete použít také to [skript prostředí PowerShell](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) k automatizaci vytváření sítě.

Pokud již máte virtuální síť a podsíť kde byste chtěli nasadit spravované instance, potřebujete, abyste měli jistotu, že splňují vaše virtuální síť a podsíť [požadavky na síť](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Použijte tento [skript Powershellu, který ověřte, že je správně nakonfigurována vaše podsíť](sql-database-managed-instance-configure-vnet-subnet.md). Tento skript ověřuje vaší sítí a sestavy nějaké problémy a zjistíte, co by mělo být změněno a potom nabídky proveďte potřebné změny ve vaší virtuální síti nebo podsíti. Pokud nechcete konfigurovat vaše virtuální síť/podsíť ručně, spusťte tento skript. Můžete ho spustit také za jakékoli hlavní změny konfigurace síťové infrastruktury. Pokud chcete vytvořit a nakonfigurovat vlastní sítě, přečtěte si [architektura připojení](sql-database-managed-instance-connectivity-architecture.md) a to[základní příručku k vytváření a konfiguraci prostředí spravované instance](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01).

## <a name="automating-creation-of-a-managed-instance"></a>Automatizace vytvoření spravované instance

 Pokud jste ještě nevytvořili síťové prostředí, jak je popsáno v předchozím kroku, na webu Azure portal může udělat pro vás – pouze nevýhodou je skutečnost, že se konfiguruje s nějakou výchozí parametry, které nelze později změnit. Jako alternativu můžete použít:

- [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md)
- [Prostředí PowerShell pomocí šablony Resource Manageru](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)
- [Rozhraní příkazového řádku Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/11/14/create-azure-sql-managed-instance-using-azure-cli/).
- [Šablona Resource Manageru](sql-database-single-database-get-started-template.md)

## <a name="migrating-to-a-managed-instance-with-minimal-downtime"></a>Migrace na spravované instanci s minimálními výpadky

Články v těchto rychlých startech umožňují rychle vytvářet spravovaná instance a přesun databází pomocí nativní `RESTORE` funkce. Avšak v případě nativní `RESTORE`, budete muset počkat databázi, kterou chcete být obnoveny (a zkopírovat do úložiště objektů Blob v Azure není již uloženy). To způsobí, že výpadkům vaší aplikace, zejména u větších databází. Chcete-li přesunout provozní databázi, použijte [Data Migration service (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json) migrace databáze s minimálními výpadky. DMS dosahuje tak, že postupně doručením (push) změny provedené ve zdrojové databázi na spravované instanci databáze obnovena. Díky tomu můžete rychle přepínat aplikace ze zdroje do cílové databáze s minimálními výpadky.

## <a name="next-steps"></a>Další postup

- Najít [podrobný seznam podporovaných funkcí ve spravované instanci zde](sql-database-features.md) a [podrobnosti a známé problémy většinou neřeší](sql-database-managed-instance-transact-sql-information.md).
- Další informace o [technické vlastnosti spravovanou instanci](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits).
- Jak najít pokročilejší-pro uživatele [použití ve službě Azure SQL Database managed instance](sql-database-howto-managed-instance.md).
- [Určení správné Azure SQL Database nebo spravované Instance SKU pro místní databázi](/sql/dma/dma-sku-recommend-sql-db/).