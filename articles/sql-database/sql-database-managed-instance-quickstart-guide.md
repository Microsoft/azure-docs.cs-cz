---
title: Rychlý Start-Azure SQL Database spravovaná instance | Microsoft Docs
description: Naučte se, jak rychle začít s Azure SQL Databaseem – spravovaná instance
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
ms.openlocfilehash: 97f7393622311103d94d3067205f902e3eec6843
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567350"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>Začínáme s Azure SQL Database Managed instance

Možnost nasazení [spravované instance](sql-database-managed-instance-index.yml) vytvoří databázi s téměř 100% kompatibilitou s nejnovějším databázovým strojem SQL Server místně (Enterprise Edition), který poskytuje implementaci nativní [virtuální sítě (VNET)](../virtual-network/virtual-networks-overview.md) , která řeší běžné bezpečnostní obavy a [obchodní model](https://azure.microsoft.com/pricing/details/sql-database/) , který je pro místní SQL Server zákazníky příznivý. V tomto článku se dozvíte, jak rychle nakonfigurovat a vytvořit spravovanou instanci a migrovat databáze.

## <a name="quickstart-overview"></a>Přehled rychlého startu

Následující rychlé starty vám umožní rychle vytvořit spravovanou instanci, nakonfigurovat virtuální počítač nebo Ukázat připojení VPN na lokalitu pro klientskou aplikaci a obnovit databázi do nové spravované instance pomocí `.bak` souboru.

### <a name="configure-environment"></a>Konfigurace prostředí

Jako první krok byste museli vytvořit svou první spravovanou instanci v síťovém prostředí, kde bude umístěna, a povolit připojení z počítače nebo virtuálního počítače, ve kterém spouštíte dotazy do spravované instance. Můžete použít následující příručky:

- [Vytvořte spravovanou instanci pomocí Azure Portal](sql-database-managed-instance-get-started.md). V Azure Portal nakonfigurujete potřebné parametry (uživatelské jméno/heslo, počet jader a maximální velikost úložiště) a automaticky vytvoříte síťové prostředí Azure bez nutnosti znát podrobnosti o síti a požadavky na infrastrukturu. Stačí se ujistit, že máte [typ](sql-database-managed-instance-resource-limits.md#supported-subscription-types) předplatného, který aktuálně povoluje vytvoření spravované instance. Pokud máte vlastní síť, kterou chcete použít, nebo chcete síť přizpůsobit, přečtěte si téma [Konfigurace existující virtuální sítě pro Azure SQL Database spravovanou instanci](sql-database-managed-instance-configure-vnet-subnet.md) nebo [vytvoření virtuální sítě pro Azure SQL Database spravovanou instanci](sql-database-managed-instance-create-vnet-subnet.md).
- Spravovaná instance je vytvořená ve vlastní virtuální síti bez veřejného koncového bodu. Pro klientský přístup k aplikacím můžete buď **vytvořit virtuální počítač ve stejné virtuální síti (jinou podsíť)** , nebo **vytvořit připojení VPN typu Point-to-site k virtuální síti z klientského počítače** pomocí některého z těchto rychlých startů:
  - Povolením [veřejného koncového bodu](sql-database-managed-instance-public-endpoint-configure.md) na spravované instanci získáte přístup k datům přímo z vašeho prostředí.
  - Vytvořte [virtuální počítač Azure v rámci virtuální sítě spravované instance](sql-database-managed-instance-configure-vm.md) pro připojení klientské aplikace, včetně SQL Server Management Studio.
  - Nastavte [připojení VPN typu Point-to-site ke spravované instanci](sql-database-managed-instance-configure-p2s.md) z klientského počítače, na kterém máte SQL Server Management Studio a další aplikace pro připojení klienta. Jedná se o další dvě možnosti připojení ke spravované instanci a její virtuální síti.

  > [!NOTE]
  > Z místní sítě můžete také použít expresní směrování nebo připojení typu Site-to-site, ale tyto přístupy jsou z rozsahu těchto rychlých startů.

Jako alternativu k ručnímu vytvoření spravované instance můžete použít [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md), [PowerShell s správce prostředků šablonou](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)nebo [Azure CLI](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) pro vytvoření skriptu a automatizaci tohoto procesu.

### <a name="migrate-your-databases"></a>Migrace databází

Po vytvoření spravované instance a konfiguraci přístupu můžete začít s migrací databází z SQL Server místních nebo virtuálních počítačů Azure. Pokud ve zdrojové databázi, kterou chcete migrovat, nejsou k dispozici některé nepodporované funkce, migrace se nezdařila. Aby nedocházelo k chybám a kontrolovali kompatibilitu, můžete nainstalovat [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) , který analyzuje vaše databáze na SQL Server a vyhledá případné potíže, které by mohly blokovat migraci do spravované instance, jako je existence [FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) nebo více souborů protokolu. Pokud tyto problémy vyřešíte, budou vaše databáze připravené k migraci do spravované instance. [Pomocník pro experimentování s databázemi](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/) je další užitečný nástroj, který může zaznamenat vaše zatížení na SQL Server a přehrát je ve spravované instanci, abyste zjistili, že při migraci na spravovanou instanci dojde k problémům s výkonem.

Jakmile si jste jisti, že můžete migrovat databázi do spravované instance, můžete použít možnosti obnovení nativního SQL Server k obnovení databáze do spravované instance ze `.bak` souboru. Tuto metodu můžete použít k migraci databází z SQL Server databázového stroje nainstalovaného místně nebo Azure VM. Rychlý Start najdete v tématu [obnovení ze zálohy do spravované instance](sql-database-managed-instance-get-started-restore.md). V tomto rychlém startu obnovíte `.bak` soubor uložený v úložišti objektů BLOB v Azure `RESTORE` pomocí příkazu Transact-SQL.

> [!TIP]
> Pokud chcete použít `BACKUP` příkaz Transact-SQL k vytvoření zálohy databáze v úložišti objektů BLOB v Azure, přečtěte si téma [SQL Server zálohování na adresu URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Tyto rychlé starty vám umožní rychle vytvořit, nakonfigurovat a obnovit zálohu databáze do spravované instance. V některých scénářích je třeba přizpůsobit nebo automatizovat nasazování spravovaných instancí a požadovaného síťového prostředí. Tyto scénáře budou popsány níže.

## <a name="customize-network-environment"></a>Přizpůsobení síťového prostředí

I když se virtuální síť nebo podsíť dá automaticky nakonfigurovat při vytvoření instance pomocí [Azure Portal](sql-database-managed-instance-get-started.md), může být vhodné ji vytvořit ještě předtím, než začnete vytvářet spravované instance, protože můžete nakonfigurovat parametry virtuální sítě a podsítě. Nejjednodušší způsob, jak vytvořit a nakonfigurovat síťové prostředí, je použít šablonu [nasazení prostředků Azure](sql-database-managed-instance-create-vnet-subnet.md) , která vytvoří a nakonfiguruje síť a podsíť, do které se instance umístí. Stačí stisknout tlačítko Azure Resource Manager nasadit a naplnit formulář parametry.

Jako alternativu můžete k automatizaci vytváření sítě použít [skript prostředí PowerShell](https://www.powershellmagazine.com/20../../configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) .

Alternativně můžete tento [skript PowerShellu](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) použít také k automatizaci vytváření sítě.

Pokud už máte virtuální síť a podsíť, do které chcete nasadit spravovanou instanci, musíte zajistit, aby vaše virtuální síť a podsíť splňovaly [požadavky na síť](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Pomocí tohoto [skriptu PowerShell ověříte, že je vaše podsíť správně nakonfigurovaná](sql-database-managed-instance-configure-vnet-subnet.md). Tento skript ověří vaši síť a nahlásí všechny problémy a poskytne vám informace o tom, co se má změnit, a pak nabídne potřebné změny ve vaší virtuální síti nebo podsíti. Pokud nechcete konfigurovat virtuální síť nebo podsíť ručně, spusťte tento skript. Můžete ji také spustit po jakékoli zásadní rekonfiguraci síťové infrastruktury. Pokud chcete vytvořit a nakonfigurovat vlastní síť, načtěte si [architekturu připojení](sql-database-managed-instance-connectivity-architecture.md) a tuto ucelenou [příručku pro vytváření a konfiguraci prostředí spravované instance](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01).

## <a name="migrate-to-a-managed-instance"></a>Migrace do spravované instance

Články v těchto rychlých startech vám umožní rychle nastavit spravovanou instanci a přesunout databáze pomocí nativní `RESTORE` schopnosti. Toto je dobrý výchozí bod, pokud chcete kompletní kontrolu konceptů a ověřit, jestli vaše řešení může fungovat na spravované instanci. 

Chcete-li však migrovat provozní databázi nebo dokonce vývoj/testovací databáze, které chcete použít pro některý test výkonnosti, je nutné zvážit použití některých dalších technik, například:
- Testování výkonu – měli byste změřit základní výkon instance SQL Server instanci a porovnat je s výkonem v cílové spravované instanci, kde jste migrovali databázi. Přečtěte si další informace o [osvědčených postupech pro porovnání výkonu](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).
- Online migrace – s nativním `RESTORE` popsaným v tomto článku musíte počkat, až budou databáze obnoveny (a zkopírovány do úložiště objektů BLOB v Azure, pokud se tam ještě neuložily). To způsobí určité výpadky aplikace, zejména pro větší databáze. K přesunu provozní databáze použijte [službu migrace dat (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json) k migraci databáze s minimálními prostoji. DMS to provede přírůstkovým vložením změn provedených ve zdrojové databázi do obnovované databáze spravované instance. Tímto způsobem můžete rychle přepnout aplikaci ze zdrojového do cílové databáze s minimálními výpadky.

Přečtěte si další informace o [doporučeném procesu migrace](sql-database-managed-instance-migrate.md).

## <a name="next-steps"></a>Další kroky

- Tady najdete [seznam podporovaných funkcí ve spravované instanci](sql-database-features.md) a [Podrobnosti a známé problémy](sql-database-managed-instance-transact-sql-information.md).
- Seznamte se [s technickými charakteristikami spravované instance](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits).
- Najděte si pokročilejší postupy, [Jak používat spravovanou instanci v Azure SQL Database](sql-database-howto-managed-instance.md).
- [Identifikujte správnou Azure SQL Database/SKU spravované instance pro vaši místní databázi](/sql/dma/dma-sku-recommend-sql-db/).
