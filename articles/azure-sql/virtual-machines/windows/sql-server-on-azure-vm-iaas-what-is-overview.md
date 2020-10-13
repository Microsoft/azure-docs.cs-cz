---
title: Přehled SQL Serveru na virtuálních počítačích Azure s Windows | Dokumentace Microsoftu
description: Naučte se spouštět úplné edice SQL Server v Azure Virtual Machines v cloudu, aniž byste museli spravovat jakýkoli místní hardware.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/27/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ae00703805bef6f5e9758bafcb781eda91cd93f9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91272153"
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>Co je SQL Server ve službě Azure Virtual Machines (Windows)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)

[SQL Server v Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/) umožňují používat v cloudu úplné verze SQL Server, aniž byste museli spravovat jakýkoli místní hardware. SQL Server virtuální počítače také zjednodušují náklady na licencování při průběžných platbách.

Virtuální počítače Azure běží v mnoha různých [geografických oblastech](https://azure.microsoft.com/regions/) po celém světě. Také nabízejí celou řadu [velikostí počítačů](../../../virtual-machines/windows/sizes.md). Galerie imagí virtuálních počítačů umožňuje vytvoření virtuálního počítače s SQL Serverem, který má správnou verzi, vydání i operační systém. To umožňuje virtuálním počítačům dobrou možnost pro mnoho různých SQL Server úloh.

Pokud s SQL Server na virtuálních počítačích Azure začínáte, podívejte se na video o *SQL Server na virtuálním počítači Azure* z naší podrobné sady [videí Azure SQL](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner):
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/SQL-Server-on-Azure-VM-Overview-4-of-61/player]

## <a name="automated-updates"></a>Automatizované aktualizace

SQL Server v Azure Virtual Machines můžou používat [automatizované opravy](automated-patching.md) k naplánování okna údržby pro instalaci důležitých oken a SQL Server aktualizace automaticky.

## <a name="automated-backups"></a>Automatizované zálohy

SQL Server v Azure Virtual Machines můžou využívat výhod [automatizovaného zálohování](automated-backup.md), které pravidelně vytváří zálohy databáze do úložiště objektů BLOB. Tento postup můžete použít také ručně. Další informace najdete v tématu [Použití služby Azure Storage pro zálohování a obnovování SQL Serveru](azure-storage-sql-server-backup-restore-use.md).

Azure také nabízí řešení zálohování na podnikové úrovni pro SQL Server běžící na virtuálních počítačích Azure. Plně spravované řešení zálohování podporuje skupiny dostupnosti Always On, dlouhodobé uchovávání, obnovení k určitému bodu v čase a centrální správu a monitorování. Další informace najdete v tématu [Azure Backup pro SQL Server ve virtuálních počítačích Azure](https://docs.microsoft.com/azure/backup/backup-azure-sql-database).
  

## <a name="high-availability"></a>Vysoká dostupnost

Pokud požadujete vysokou dostupnost, doporučujeme nakonfigurovat skupiny dostupnosti SQL Serveru. To zahrnuje více instancí SQL Server v Azure Virtual Machines ve virtuální síti. Řešení s vysokou dostupností můžete nakonfigurovat ručně, nebo můžete použít šablony v Azure Portal pro automatickou konfiguraci. Přehled všech možností vysoké dostupnosti najdete v tématu [Vysoká dostupnost a zotavení po havárii pro SQL Server v Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="performance"></a>Výkon

Virtuální počítače Azure nabízejí různé kapacity, takže splňují různé požadavky na pracovní zatížení. SQL Server virtuální počítače také poskytují automatickou konfiguraci úložiště, která je optimalizovaná pro vaše požadavky na výkon. Další informace o konfiguraci úložiště pro virtuální počítače s SQL Server najdete v tématu [Konfigurace úložiště pro SQL Server virtuální počítače](storage-configuration.md). Pokud chcete vyladit výkon, přečtěte si téma [osvědčené postupy výkonu pro SQL Server v Azure Virtual Machines](performance-guidelines-best-practices.md).

## <a name="get-started-with-sql-server-vms"></a>Začínáme s SQL Servermi virtuálními počítači

Chcete-li začít, zvolte image virtuálního počítače s SQL Serverem s požadovanou verzí, vydáním a operačním systémem. Následující oddíly poskytují přímé odkazy na image v galerii virtuálních počítačů s SQL Serverem na webu Azure Portal.

> [!TIP]
> Další informace o cenách SQL Server imagí najdete v tématu [cenové poradenství pro SQL Server v Azure Virtual Machines](pricing-guidance.md). 

### <a name="pay-as-you-go"></a><a id="payasyougo"></a> Průběžné platby
Následující tabulka obsahuje matici imagí SQL Serveru s průběžnými platbami.

| Verze | Operační systém | Edice |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019enterprise), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019standard), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019web), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019sqldev) | 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2ExpressWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

Pokud chcete zobrazit dostupné image virtuálních počítačů SQL Server on Linux, přečtěte si téma [přehled SQL Server v Azure Virtual Machines (Linux)](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md).

> [!NOTE]
> Nyní je možné změnit licenční model s platbami podle využití SQL Server virtuálního počítače na používání vlastní licence. Další informace najdete v tématu [Změna licenčního modelu pro SQL Server virtuální počítač](licensing-model-azure-hybrid-benefit-ahb-change.md). 

### <a name="bring-your-own-license"></a><a id="BYOL"></a> Přineste si vlastní licenci
Můžete také používat vlastní licenci (BYOL). V tomto scénáři zaplatíte jenom za virtuální počítač bez jakýchkoli dalších poplatků za licencování SQL Serveru.  Používáním vlastní licence můžete časem ušetřit peníze za nepřetržité produkční úlohy. Požadavky k použití této možnosti najdete v tématu [Doprovodné materiály k cenám pro virtuální počítače Azure s SQL Serverem](pricing-guidance.md#byol).

Pokud chcete využít vlastní licenci, můžete buď převést existující SQL Server virtuální počítač s platbami za použití, nebo můžete nasadit image s předem opraveným **{BYOL}**. Další informace o přepínání licenčního modelu mezi platbami podle využití a BYOL najdete v článku [Změna licenčního modelu pro SQL Server virtuální počítač](licensing-model-azure-hybrid-benefit-ahb-change.md). 

| Verze | Operační systém | Edice |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolenterprise), [Standard BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolstandard)| 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4StandardWindowsServer2012R2) |

Je možné nasadit starší bitovou kopii SQL Server, která není k dispozici v Azure Portal pomocí prostředí PowerShell. Pokud chcete zobrazit všechny dostupné Image pomocí PowerShellu, použijte následující příkaz:

  ```powershell
  Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
  ```

Další informace o nasazení SQL Server virtuálních počítačů pomocí prostředí PowerShell najdete v tématu [jak zřídit SQL Server virtuálních počítačů pomocí Azure PowerShell](create-sql-vm-powershell.md).


### <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači
Po vytvoření virtuálního počítače s SQL Serverem se k němu připojte z aplikací nebo nástrojů, jako je například SQL Server Management Studio (SSMS). Pokyny najdete v tématu [připojení k virtuálnímu počítači s SQL Server v Azure](ways-to-connect-to-sql.md).

### <a name="migrate-your-data"></a>Migrace dat
Pokud máte existující databázi, budete ji chtít přesunout na nově zřízený SQL Server virtuální počítač. Seznam možností migrace a pokyny najdete v části [Migrace databáze do SQL Serveru ve virtuálním počítači Azure](migrate-to-vm-from-sql-server.md).

## <a name="create-and-manage-azure-sql-resources-with-the-azure-portal"></a>Vytváření a Správa prostředků Azure SQL pomocí Azure Portal

Azure Portal poskytuje jednu stránku, kde můžete spravovat [všechny prostředky Azure SQL](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Sql%2Fazuresql) , včetně vašich virtuálních počítačů SQL.

Pokud chcete získat přístup k **prostředkům Azure SQL** , vyberte v nabídce Azure Portal položku **Azure SQL** , nebo vyhledejte a na libovolné stránce vyberte **Azure SQL** .

![Hledání Azure SQL](./media/sql-server-on-azure-vm-iaas-what-is-overview/search-for-azure-sql.png)

> [!NOTE]
> Azure SQL nabízí rychlý a snadný způsob, jak získat přístup ke všem databázím SQL Azure, elastickým fondům, logickým serverům, spravovaným instancím a virtuálním počítačům. Azure SQL není služba nebo prostředek. 

Pokud chcete spravovat existující prostředky, vyberte požadovanou položku v seznamu. Pokud chcete vytvořit nové prostředky Azure SQL, vyberte **+ Přidat**. 

![Vytvoření prostředku Azure SQL](./media/sql-server-on-azure-vm-iaas-what-is-overview/create-azure-sql-resource.png)

Po výběru možnosti **+ Přidat**zobrazte další informace o různých možnostech výběrem možnosti **Zobrazit podrobnosti** na libovolné dlaždici.

![Podrobnosti dlaždice databáze](./media/sql-server-on-azure-vm-iaas-what-is-overview/sql-vm-details.png)

Podrobnosti najdete tady:

- [Vytvoření izolované databáze](../../database/single-database-create-quickstart.md)
- [Vytvoření elastického fondu](../../database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)
- [Vytvoření spravované instance](../../managed-instance/instance-create-quickstart.md)
- [Vytvoření virtuálního počítače s SQL Server](sql-vm-create-portal-quickstart.md)

## <a name="sql-server-vm-image-refresh-policy"></a><a id="lifecycle"></a> Zásada aktualizace image virtuálního počítače SQL Server
Azure pro každou podporovanou kombinaci operačního systému, jeho verze a edice udržuje pouze jednu image virtuálního počítače. To znamená, že se image v průběhu času aktualizují a staré image se odebírají. Další informace najdete v části **Image** tématu [Nejčastější dotazy k virtuálním počítačům s SQL Serverem](frequently-asked-questions-faq.md#images).

## <a name="customer-experience-improvement-program-ceip"></a>Program Zlepšování softwaru a služeb na základě zkušeností uživatelů (CEIP)
Program Zlepšování softwaru a služeb na základě zkušeností uživatelů (CEIP) je ve výchozím nastavení povolen. Ten pravidelně odesílá zprávy do Microsoftu s cílem přispět k vylepšování SQL Serveru. S programem CEIP není spojená žádná nutná úloha správy – za předpokladu, že ho nechcete po zřízení zakázat. Tento program můžete přizpůsobit nebo vypnout připojením k virtuálnímu počítači pomocí vzdálené plochy. Pak spusťte nástroj **pro zasílání zpráv o chybách a vytváření sestav využití SQL Serveru**. Podle pokynů zakažte generování sestav. Další informace o shromažďování dat najdete v [Prohlášení o zásadách ochrany osobních údajů pro SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-privacy).

## <a name="related-products-and-services"></a>Související produkty a služby
### <a name="windows-virtual-machines"></a>Virtuální počítače s Windows
* [Přehled služby Azure Virtual Machines](../../../virtual-machines/windows/overview.md)

### <a name="storage"></a>Storage
* [Úvod do Microsoft Azure Storage](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Sítě
* [Přehled Virtual Network](../../../virtual-network/virtual-networks-overview.md)
* [IP adresy v Azure](../../../virtual-network/public-ip-addresses.md)
* [Vytvoření plně kvalifikovaného názvu domény (FQDN) na webu Azure Portal](../../../virtual-machines/linux/portal-create-fqdn.md)

### <a name="sql"></a>SQL
* [Dokumentace k SQL Server](https://docs.microsoft.com/sql/index)
* [Porovnání Azure SQL Database](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>Další kroky

Začínáme s SQL Server v Azure Virtual Machines:

* [Vytvoření virtuálního počítače s SQL Serverem na webu Azure Portal](sql-vm-create-portal-quickstart.md)

Získejte odpovědi na nejčastější dotazy týkající se SQL Server virtuálních počítačů:

* [SQL Server na Azure Virtual Machines – nejčastější dotazy](frequently-asked-questions-faq.md)

Zobrazení referenčních architektur pro spouštění N-vrstvých aplikací na SQL Server v IaaS

* [N-vrstvá aplikace Windows v Azure s SQL Server](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [Spuštění N-vrstvé aplikace v několika oblastech Azure pro zajištění vysoké dostupnosti](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
