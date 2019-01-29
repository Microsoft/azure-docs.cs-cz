---
title: Rychlý start – Azure SQL Database Managed Instance | Dokumentace Microsoftu
description: Zjistěte, jak rychle začít s Azure SQL Database – Managed Instance
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
ms.openlocfilehash: b8eb6d483b9e7a92b50a269e5efbc8ca55946931
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55104214"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>Začínáme s Azure SQL Database Managed Instance

[Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) je plně spravovaná PaaS verzi systému SQL Server hostované v cloudu Azure a umístěn ve své vlastní virtuální síti s privátní IP adresu. V této části se dozvíte, jak rychle nakonfigurovat a vytvoření Managed Instance a migrace databází.

## <a name="quickstart-overview"></a>Rychlý start – přehled

V této části zobrazí přehled dostupných články, které vám umožňují rychle začít s spravovaných instancí. Nejjednodušší způsob, jak vytvořit první Managed Instance je použití [na webu Azure portal](sql-database-managed-instance-get-started.md) ve kterém můžete nakonfigurovat potřebné parametry (uživatelského jména a hesla, počet jader, maximální velikosti úložiště) a automaticky vytvoří Azure síťovém prostředí bez potřebujete vědět o sítě podrobnosti a požadavky na infrastrukturu. Ujistěte se, že máte [typ předplatného](sql-database-managed-instance-resource-limits.md#supported-subscription-types) , který může vytvořit instanci.

Pokud máte vlastní sítě, kterou chcete použít, nebo chcete přizpůsobit sítě naleznete v tématu Jak [konfigurace prostředí sítě](#configure-network-environment) pro Managed Instance.

Když vytvoříte Managed Instance, je třeba se připojit k instanci pomocí jedné z následujících postupů:
* Vytvoření [virtuálního počítače Azure](sql-database-managed-instance-configure-vm.md) s nainstalovaný SQL Server Management Studio a další aplikace, které lze použít pro přístup k Managed Instance v podsíti ve stejné virtuální síti, ve kterém je umístí Managed Instance. Virtuální počítač nemůže být ve stejné podsíti se vaše Managed instance.
* Nastavit [připojeníPoint-to-site](sql-database-managed-instance-configure-p2s.md) v počítači, který vám umožní připojit počítače k virtuální síti, ve kterém je umístí Managed Instance a použít Managed Instance jako žádný jiný SQL Server ve vaší síti.

Jako alternativu můžete použít expressroute nebo připojení site-to-site z místní sítě, ale tyto přístupy jsou mimo rozsah v těchto rychlých startech.

Když vytvoříte Managed Instance a konfigurovat přístup, můžete začít migrovat databáze umístěna na místní SQL Server nebo virtuální počítače Azure. Všimněte si, že migrace selže, pokud máte některé nepodporované funkce ve zdrojové databázi, kterou chcete migrovat. Pokud chcete zabránit selhání a zkontrolovat kompatibilitu, můžete nainstalovat [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) , která analyzuje vaše databáze na SQL serveru a najít všechny problémy, které by mohly blokovat migraci do spravované Instance, jako je například existence Nebo více souborů protokolu FileStream. Pokud tyto problémy vyřešit, vaše databáze jsou připraveni přejít k Managed Instance. [Databáze služby experimentování ve službě pomocníka](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/) je další užitečná nástroj, který může zaznamenávat vaše úlohy na serveru SQL Server a opětovného přehrání ho ve službě Managed Instance, aby mohl určit chystají možné problémy s výkonem, pokud provádíte migraci do spravované Instance.

Jakmile jste si jisti, že můžete migrovat databáze do Managed Instance, můžete použít [nativní obnovení](sql-database-managed-instance-get-started-restore.md) funkce, které vám umožní vytvořit zálohu vaší databáze pomocí příkazu jazyka Transact-SQL, nahrajte ho do služby Azure blob storage a OBNOVENÍ databáze z úložiště objektů blob pomocí příkazu jazyka Transact-SQL.

V těchto rychlých startech umožňují rychle konfigurovat, vytvořit a umístit na vašich spravovaných instancí databáze. V některých případech je třeba přizpůsobit nebo automatizovat nasazení Managed Instance a požadované síťové prostředí. Tyto scénáře budou popsány níže.

## <a name="customizing-network-environment"></a>Přizpůsobení prostředí sítě

I když se virtuální síť/podsíť může automaticky nakonfiguruje při vytvoření instance pomocí [na webu Azure portal](sql-database-managed-instance-get-started.md), může být vhodné vytvořte ho, než můžete začít vytvářet spravovaných instancí, protože můžete nakonfigurovat parametry sítě vnet a podsítě. Nejjednodušší způsob, jak vytvořit a konfigurovat síťové prostředí je použití [nasazení prostředků Azure](sql-database-managed-instance-create-vnet-subnet.md) šablonu, která vytvoří a nakonfiguruje je síť a podsíť, kde budou umístěné na instanci. Stačí stisknout klávesu Azure Resource Manageru nasadit tlačítko a vyplnit formulář s parametry. Jako alternativu můžete použít [skript prostředí PowerShell](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) k automatizaci vytváření sítě.

Pokud již máte virtuální síť a podsíť kde byste chtěli nasadit Managed Instance, je třeba zajistit, že splňují vaše virtuální síť a podsíť [požadavky na síť](sql-database-managed-instance-connectivity-architecture.md#network-requirements). To byste měli použít [skript Powershellu, který ověřte, že je správně nakonfigurována vaše podsíť](sql-database-managed-instance-configure-vnet-subnet.md). Tento skript nejen ověření vaší sítě a hlášení problémů – to zjistíte, co by měl být změněn a navíc nabízí můžete provést potřebné změny ve vaší virtuální síti nebo podsíti. Pokud nechcete konfigurovat vaše virtuální síť/podsíť ručně, spusťte tento skript a také měli byste ji spouštět po jakékoli hlavní změny konfigurace síťové infrastruktury. Pokud chcete vytvořit a nakonfigurovat vlastní sítě, přečtěte si [dokumentace ke službě Managed Instance](sql-database-managed-instance-connectivity-architecture.md) a [Tato příručka](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01).

## <a name="automating-creation-of-managed-instance"></a>Automatizace vytvoření spravované Instance

 Pokud jste ještě nevytvořili síťové prostředí, jak je popsáno v předchozím kroku, na webu Azure portal může udělat pro vás – pouze nevýhodou je skutečnost, že ho nakonfigurujete pomocí některé výchozí parametry, které nelze později změnit. Jako alternativu můžete použít [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/), [Powershellu pomocí šablony Resource Manageru](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md), nebo [rozhraní příkazového řádku Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/11/14/create-azure-sql-managed-instance-using-azure-cli/).

## <a name="migrating-to-managed-instance-with-minimal-downtime"></a>Migrací do spravované Instance s minimálními výpadky

Články v těchto rychlých startech umožňují rychle nastavit Managed Instance a přesun databází. Však nativní obnovení, které je třeba čekat databázi, kterou chcete obnovit, což by vedlo výpadkům vaší aplikace zejména v případě, že databáze je větší. Při přesunutí provozní databáze, je třeba pravděpodobně lepší způsob, jak migrovat, který zaručí minimálními prostoji migrace. [Data Migration service](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json) migrace služba, která můžete migrovat databáze s minimálními prostoji formou přírůstkové změny provedené ve zdrojové databázi k databázi, která se obnovení k Managed Instance. Díky tomu můžete rychle přepínat aplikace ze zdroje do cílové databáze s minimálními výpadky.

## <a name="next-steps"></a>Další postup

* Najít [podrobný seznam podporovaných funkcí ve spravované instanci zde](sql-database-features.md) a [podrobnosti a známé problémy většinou neřeší](sql-database-managed-instance-transact-sql-information.md). 
* Další informace o [technické vlastnosti Managed Instance](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits). 
* Najít další pokročilé kurzy v [jak části](sql-database-howto-managed-instance.md). 
