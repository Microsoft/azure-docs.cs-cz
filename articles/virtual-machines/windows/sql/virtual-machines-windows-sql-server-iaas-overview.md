---
title: Přehled SQL Serveru na virtuálních počítačích Azure s Windows | Dokumentace Microsoftu
description: Můžete se dozvědět, jak spouštět úplné edice SQL Serveru virtuálních počítačích Azure.
services: virtual-machines-windows
documentationcenter: ''
author: rothja
manager: craigg
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2018
ms.author: jroth
ms.openlocfilehash: 416d1c7d45902735f58e397dd9195185ba0867df
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53315243"
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>Co je SQL Server na virtuálních počítačích Azure? (Windows)

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)

[SQL Server na virtuálních počítačích Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) umožňuje používat plné verze SQL Serveru v cloudu, aniž by bylo potřeba spravovat jakýkoli místní hardware. Pokud platíte průběžně, pro virtuální počítače s SQL Serverem se také zjednoduší náklady na licencování.

Virtuální počítače Azure běží v mnoha různých [geografických oblastech](https://azure.microsoft.com/regions/) po celém světě. Také nabízejí celou řadu [velikostí počítačů](../sizes.md). Galerie imagí virtuálních počítačů umožňuje vytvoření virtuálního počítače s SQL Serverem, který má správnou verzi, vydání i operační systém. Díky tomu jsou virtuální počítače vhodné pro mnoho různých úloh SQL Serveru.

## <a name="automated-updates"></a>Automatizované aktualizace

Virtuální počítače Azure s SQL Serverem mohou používat [Automatické opravy](virtual-machines-windows-sql-automated-patching.md) k naplánování okna údržby pro automatickou instalaci důležitých oken a aktualizace SQL serveru.

## <a name="automated-backups"></a>Automatizované zálohy

Virtuální počítače Azure s SQL Serverem mohou využívat výhod [automatizovaného zálohování](virtual-machines-windows-sql-automated-backup-v2.md), které pravidelně vytváří zálohy databáze do Blob Storage. Tento postup můžete použít také ručně. Další informace najdete v tématu [Použití služby Azure Storage pro zálohování a obnovování SQL Serveru](virtual-machines-windows-use-storage-sql-server-backup-restore.md).

## <a name="high-availability"></a>Vysoká dostupnost

Pokud požadujete vysokou dostupnost, doporučujeme nakonfigurovat skupiny dostupnosti SQL Serveru. Znamená to využívat více virtuálních počítačů Azure s SQL Serverem ve virtuální síti. Řešení vysoké dostupnosti můžete nakonfigurovat ručně, nebo můžete použít šablony na webu Azure Portal pro automatickou konfiguraci. Přehled možností vysoké dostupnosti najdete v tématu [Vysoká dostupnost a zotavení po havárii pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="performance"></a>Výkon

Virtuální počítače Azure nabízejí různé kapacity, takže splňují různé požadavky na pracovní zatížení. Virtuální počítače SQL také poskytují automatickou konfiguraci úložiště, která je optimalizovaná pro vaše požadavky na výkon. Další informace o konfiguraci úložiště pro virtuální počítače na serveru SQL najdete v tématu [Konfigurace úložiště pro virtuální počítače SQL Serveru](virtual-machines-windows-sql-server-storage-configuration.md). Pokud budete chtít ladit výkon, přečtěte si [Osvědčené postupy z hlediska výkonu pro SQL Server na Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

## <a name="get-started-with-sql-vms"></a>Začínáme s virtuálními počítači SQL

Chcete-li začít, zvolte image virtuálního počítače s SQL Serverem s požadovanou verzí, vydáním a operačním systémem. Následující oddíly poskytují přímé odkazy na image v galerii virtuálních počítačů s SQL Serverem na webu Azure Portal.

> [!TIP]
> Další informace pro lepší pochopení cen imagí SQL najdete v [doprovodných materiálech k cenám pro virtuální počítače Azure s SQL Serverem](virtual-machines-windows-sql-server-pricing-guidance.md). 

### <a id="payasyougo"></a> Průběžné platby
Následující tabulka obsahuje matici imagí SQL Serveru s průběžnými platbami.

| Verze | Operační systém | Edice |
| --- | --- | --- |
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2ExpressWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

Dostupné image virtuálních počítačů s Linuxem a SQL Serverem najdete v tématu [Přehled SQL Serveru na virtuálních počítačích Azure (Linux)](../../linux/sql/sql-server-linux-virtual-machines-overview.md).

> [!NOTE]
> Nyní je možné změnit licenční model plateb za využití virtuálního počítače SQL serveru používat vlastní licenci. Další informace najdete v tématu [jak změnit licenční model virtuálního počítače SQL](virtual-machines-windows-sql-ahb.md). 

### <a id="BYOL"></a> Používání vlastní licence
Můžete také používat vlastní licenci (BYOL). V tomto scénáři zaplatíte jenom za virtuální počítač bez jakýchkoli dalších poplatků za licencování SQL Serveru.  Používáním vlastní licence můžete časem ušetřit peníze za nepřetržité produkční úlohy. Požadavky k použití této možnosti najdete v tématu [Doprovodné materiály k cenám pro virtuální počítače Azure s SQL Serverem](virtual-machines-windows-sql-server-pricing-guidance.md#byol).

Používání vlastní licence, můžete buď převést existující virtuální počítač SQL platby za využití nebo můžete nasadit bitovou kopii s předponou **{BYOL}**. Další informace o přepínání mezi platby za využití a BYOL váš licenční model, najdete v části [jak změnit licenční model virtuálního počítače SQL](virtual-machines-windows-sql-ahb.md). 

| Verze | Operační systém | Edice |
| --- | --- | --- |
| **SQL Server 2017** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4StandardWindowsServer2012R2) |


### <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači
Po vytvoření virtuálního počítače s SQL Serverem se k němu připojte z aplikací nebo nástrojů, jako je například SQL Server Management Studio (SSMS). Pokyny najdete v tématu [Připojení k virtuálnímu počítači SQL Serveru na Azure](virtual-machines-windows-sql-connect.md).

### <a name="migrate-your-data"></a>Migrace dat
Pokud máte existující databázi, bude ji nutné přesunout do nově zřízeného virtuálního počítače s SQL Serverem. Seznam možností migrace a pokyny najdete v části [Migrace databáze do SQL Serveru ve virtuálním počítači Azure](virtual-machines-windows-migrate-sql.md).

## <a id="lifecycle"></a>Zásady aktualizace imagí virtuálních počítačů SQL
Azure pro každou podporovanou kombinaci operačního systému, jeho verze a edice udržuje pouze jednu image virtuálního počítače. To znamená, že se image v průběhu času aktualizují a staré image se odebírají. Další informace najdete v části **Image** tématu [Nejčastější dotazy k virtuálním počítačům s SQL Serverem](virtual-machines-windows-sql-server-iaas-faq.md#images).

## <a name="customer-experience-improvement-program-ceip"></a>Program Zlepšování softwaru a služeb na základě zkušeností uživatelů (CEIP)
Program Zlepšování softwaru a služeb na základě zkušeností uživatelů (CEIP) je ve výchozím nastavení povolen. Ten pravidelně odesílá zprávy do Microsoftu s cílem přispět k vylepšování SQL Serveru. S programem CEIP není spojená žádná nutná úloha správy – za předpokladu, že ho nechcete po zřízení zakázat. Tento program můžete přizpůsobit nebo vypnout připojením k virtuálnímu počítači pomocí vzdálené plochy. Pak spusťte nástroj **pro zasílání zpráv o chybách a vytváření sestav využití SQL Serveru**. Podle pokynů zakažte generování sestav. Další informace o shromažďování dat najdete v [Prohlášení o zásadách ochrany osobních údajů pro SQL Server](https://docs.microsoft.com/sql/getting-started/microsoft-sql-server-privacy-statement).

## <a name="related-products-and-services"></a>Související produkty a služby
### <a name="windows-virtual-machines"></a>Windows Virtual Machines
* [Přehled služby Virtual Machines](../overview.md)

### <a name="storage"></a>Storage
* [Úvod do Microsoft Azure Storage](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Sítě
* [Přehled služby Virtual Network](../../../virtual-network/virtual-networks-overview.md)
* [IP adresy v Azure](../../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Vytvoření plně kvalifikovaného názvu domény (FQDN) na webu Azure Portal](../portal-create-fqdn.md)

### <a name="sql"></a>SQL
* [Dokumentace SQL Serveru](https://docs.microsoft.com/sql/index)
* [Porovnání Azure SQL Database](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)

## <a name="next-steps"></a>Další postup

Začínáme s SQL Serverem na virtuálních počítačích Azure:

* [Vytvoření virtuálního počítače s SQL Serverem na webu Azure Portal](quickstart-sql-vm-create-portal.md)

Odpovědi na nejčastější dotazy o virtuálních počítačích SQL:

* [SQL Server na Azure Virtual Machines – nejčastější dotazy](virtual-machines-windows-sql-server-iaas-faq.md)
