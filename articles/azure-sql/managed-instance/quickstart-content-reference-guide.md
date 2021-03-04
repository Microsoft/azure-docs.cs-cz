---
title: Odkaz na obsah Začínáme
titleSuffix: Azure SQL Managed Instance
description: 'Odkaz na obsah, který vám pomůže začít se službou Azure SQL Managed instance. '
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: davidtrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 07/11/2019
ms.openlocfilehash: c84c81fa81cf4ba9be454eea5782927a09c3252a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690953"
---
# <a name="getting-started-with-azure-sql-managed-instance"></a>Začínáme se službou Azure SQL Managed instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Pomocí [spravované instance Azure SQL](sql-managed-instance-paas-overview.md) se vytvoří databáze s téměř 100% kompatibilitou s nejnovějším databázovým strojem SQL Server (Enterprise Edition), který poskytuje nativní implementaci [virtuální sítě](../../virtual-network/virtual-networks-overview.md) , která řeší běžné bezpečnostní obavy, a [obchodní model](https://azure.microsoft.com/pricing/details/sql-database/) , který by měl být pro stávající SQL Server zákazníky příznivý.

V tomto článku najdete odkazy na obsah, který vás naučí, jak rychle nakonfigurovat a vytvořit spravovanou instanci SQL a migrovat databáze.

## <a name="quickstart-overview"></a>Rychlé starty – přehled

Následující rychlé starty vám umožní rychle vytvořit spravovanou instanci SQL, nakonfigurovat virtuální počítač nebo Ukázat připojení VPN na lokalitu pro klientskou aplikaci a obnovit databázi do nové spravované instance SQL pomocí `.bak` souboru.

### <a name="configure-environment"></a>Konfigurace prostředí

Jako první krok byste museli vytvořit svou první spravovanou instanci SQL pomocí síťového prostředí, kde bude umístěn, a povolit připojení z počítače nebo virtuálního počítače, ve kterém spouštíte dotazy do spravované instance SQL. Můžete použít následující příručky:

- [Pomocí Azure Portal vytvořit spravovanou instanci SQL](instance-create-quickstart.md). V Azure Portal nakonfigurujete potřebné parametry (uživatelské jméno/heslo, počet jader a maximální velikost úložiště) a automaticky vytvoříte síťové prostředí Azure bez nutnosti znát podrobnosti o síti a požadavky na infrastrukturu. Stačí se ujistit, že máte [Typ předplatného](resource-limits.md#supported-subscription-types) , který aktuálně povoluje vytvoření spravované instance SQL. Pokud máte vlastní síť, kterou chcete použít, nebo chcete síť přizpůsobit, přečtěte si téma [Konfigurace existující virtuální sítě pro spravovanou instanci SQL Azure](vnet-existing-add-subnet.md) nebo [vytvoření virtuální sítě pro SPRAVOVANOU instanci Azure SQL](virtual-network-subnet-create-arm-template.md).
- Spravovaná instance SQL se vytvoří ve vlastní virtuální síti bez veřejného koncového bodu. Pro klientský přístup k aplikacím můžete buď **vytvořit virtuální počítač ve stejné virtuální síti (jinou podsíť)** , nebo **vytvořit připojení VPN typu Point-to-site k virtuální síti z klientského počítače** pomocí některého z těchto rychlých startů:
  - Povolte [veřejný koncový bod](public-endpoint-configure.md) na spravované instanci SQL, abyste měli přístup k datům přímo z vašeho prostředí.
  - Vytvořte [virtuální počítač Azure ve virtuální síti spravované instance SQL](connect-vm-instance-configure.md) pro připojení klientské aplikace, včetně SQL Server Management Studio.
  - Nastavte [připojení VPN typu Point-to-site k spravované instanci SQL](point-to-site-p2s-configure.md) z klientského počítače, na kterém máte SQL Server Management Studio a další aplikace pro připojení klienta. Jedná se o další dvě možnosti připojení ke spravované instanci SQL a ke své virtuální síti.

  > [!NOTE]
  > - Z místní sítě můžete také použít expresní směrování nebo připojení typu Site-to-site, ale tyto přístupy jsou z rozsahu těchto rychlých startů.
  > - Pokud změníte dobu uchování z 0 (neomezené uchovávání) na jakoukoli jinou hodnotu, pamatujte na to, že doba uchovávání se bude vztahovat jenom na protokoly zapsané po změně hodnoty uchování (protokoly zapsané během období, kdy byla doba uchování nastavená na neomezený, se zachovají i po povolení uchování).

Jako alternativu k ručnímu vytvoření spravované instance SQL můžete použít [PowerShell](scripts/create-configure-managed-instance-powershell.md), [PowerShell s správce prostředků šablonou](./create-template-quickstart.md)nebo [Azure CLI](/cli/azure/sql/mi#az-sql-mi-create) pro vytvoření skriptu a automatizaci tohoto procesu.

### <a name="migrate-your-databases"></a>Migrace databází

Po vytvoření spravované instance SQL a konfiguraci přístupu můžete začít s migrací SQL Serverch databází. Pokud máte ve zdrojové databázi, kterou chcete migrovat, nepodporované funkce, může migrace selhat. Aby nedocházelo k chybám a kontrolovali kompatibilitu, můžete pomocí [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) analyzovat databáze na SQL Server a vyhledat všechny problémy, které by mohly blokovat migraci do spravované instance SQL, jako je například existence [FileStream](/sql/relational-databases/blob/filestream-sql-server) nebo více souborů protokolu. Pokud tyto problémy vyřešíte, vaše databáze jsou připravené k migraci do spravované instance SQL. [Pomocník pro experimentování s databázemi](/sql/dea/database-experimentation-assistant-overview) je další užitečný nástroj, který může zaznamenat vaše zatížení na SQL Server a přehrát je na spravované instanci SQL, abyste zjistili, že při migraci na SPRAVOVANOU instanci SQL dojde k problémům s výkonem.

Jakmile si jste jisti, že můžete migrovat databázi do spravované instance SQL, můžete použít možnosti obnovení nativního SQL Server k obnovení databáze do spravované instance SQL ze `.bak` souboru. Tuto metodu můžete použít k migraci databází z SQL Server databázového stroje nainstalovaného místně nebo Azure Virtual Machines. Rychlý Start najdete v tématu [obnovení ze zálohy do spravované instance SQL](restore-sample-database-quickstart.md). V tomto rychlém startu obnovíte `.bak` soubor uložený v úložišti objektů BLOB v Azure pomocí `RESTORE` příkazu Transact-SQL.

> [!TIP]
> Pokud chcete použít `BACKUP` příkaz Transact-SQL k vytvoření zálohy databáze v úložišti objektů BLOB v Azure, přečtěte si téma [SQL Server zálohování na adresu URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Tyto rychlé starty umožňují rychle vytvářet, konfigurovat a obnovovat zálohy databáze na spravovanou instanci SQL. V některých scénářích je třeba přizpůsobit nebo automatizovat nasazení spravované instance SQL a požadovaného síťového prostředí. Tyto scénáře budou popsány níže.

## <a name="customize-network-environment"></a>Přizpůsobení síťového prostředí

I když se virtuální síť nebo podsíť dá automaticky nakonfigurovat při vytvoření instance [pomocí Azure Portal](instance-create-quickstart.md), může být vhodné ji vytvořit ještě předtím, než začnete vytvářet instance spravované instance SQL, protože můžete nakonfigurovat parametry virtuální sítě a podsítě. Nejjednodušší způsob, jak vytvořit a nakonfigurovat síťové prostředí, je použít šablonu [nasazení prostředků Azure](virtual-network-subnet-create-arm-template.md) , která vytvoří a nakonfiguruje vaši síť a podsíť, do které se instance umístí. Stačí stisknout tlačítko Azure Resource Manager nasadit a naplnit formulář parametry.

Alternativně můžete tento [skript PowerShellu](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) použít také k automatizaci vytváření sítě.

Pokud už máte virtuální síť a podsíť, kde byste chtěli nasadit spravovanou instanci SQL, musíte zajistit, aby vaše virtuální síť a podsíť splňovaly [požadavky na síť](connectivity-architecture-overview.md#network-requirements). Pomocí tohoto [skriptu PowerShell ověříte, že je vaše podsíť správně nakonfigurovaná](vnet-existing-add-subnet.md). Tento skript ověří vaši síť a nahlásí všechny problémy, oznámí vám, co se má změnit, a pak nabídne, aby ve vaší virtuální síti nebo podsíti provedl potřebné změny. Pokud nechcete konfigurovat virtuální síť nebo podsíť ručně, spusťte tento skript. Můžete ji také spustit po jakékoli zásadní rekonfiguraci síťové infrastruktury. Pokud chcete vytvořit a nakonfigurovat vlastní síť, načtěte si [architekturu připojení](connectivity-architecture-overview.md) a tuto ucelenou [příručku pro vytváření a konfiguraci prostředí spravované instance SQL](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01).

## <a name="migrate-to-a-sql-managed-instance"></a>Migrace na spravovanou instanci SQL

Dříve zmíněné rychlé starty vám umožní rychle nastavit spravovanou instanci SQL a přesunout databáze pomocí nativní `RESTORE` schopnosti. Toto je dobrý výchozí bod, pokud chcete kompletní kontrolu konceptů a ověřit, jestli vaše řešení může fungovat na spravované instanci.

Chcete-li však migrovat provozní databázi nebo dokonce vývoj/testovací databáze, které chcete použít pro některý test výkonnosti, je nutné zvážit použití některých dalších technik, například:

- Testování výkonu – je třeba změřit metriky výkonu na zdrojovém SQL Server instanci a porovnat je s metrikami výkonu cílové spravované instance SQL, kam jste migrovali databázi. Přečtěte si další informace o [osvědčených postupech pro porovnání výkonu](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).
- Online migrace – s nativním `RESTORE` popsaným v tomto článku musíte počkat, až budou databáze obnoveny (a zkopírovány do úložiště objektů BLOB v Azure, pokud se tam ještě neuložily). To způsobí určité výpadky aplikace, zejména pro větší databáze. K přesunu provozní databáze použijte [službu migrace dat (DMS)](../../dms/tutorial-sql-server-to-managed-instance.md?toc=%2fazure%2fsql-database%2ftoc.json) k migraci databáze s minimálními prostoji. DMS to provede přírůstkovým vložením změn provedených ve zdrojové databázi do obnovované databáze spravované instance SQL. Tímto způsobem můžete rychle přepnout aplikaci ze zdroje do cílové databáze s minimálními výpadky.

Přečtěte si další informace o [doporučeném procesu migrace](migrate-to-instance-from-sql-server.md).

## <a name="next-steps"></a>Další kroky

- Tady najdete [seznam podporovaných funkcí v rámci spravované instance SQL](../database/features-comparison.md) , které najdete tady, a [Podrobnosti a známé problémy](transact-sql-tsql-differences-sql-server.md).
- Seznamte se [s technickými charakteristikami spravované instance SQL](resource-limits.md#service-tier-characteristics).
- Najděte si pokročilejší postupy, [Jak používat spravovanou instanci SQL](how-to-content-reference-guide.md).
- [Identifikujte správnou skladovou jednotku spravované instance SQL Azure pro vaši místní databázi](/sql/dma/dma-sku-recommend-sql-db/).