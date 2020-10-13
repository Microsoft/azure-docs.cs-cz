---
title: Migrace databáze SQL Server do SQL Server na virtuálním počítači | Microsoft Docs
description: Přečtěte si, jak migrovat místní databázi uživatelů na SQL Server na virtuálním počítači Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.topic: how-to
ms.date: 08/18/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 83b29252038f88bf8b81299303442abd0cc36814
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91298656"
---
# <a name="migrate-a-sql-server-database-to-sql-server-on-an-azure-virtual-machine"></a>Migrace databáze SQL Server pro SQL Server na virtuálním počítači Azure

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Existuje několik způsobů, jak migrovat místní databázi SQL Server uživatelů do SQL Server na virtuálním počítači Azure (VM). Tento článek stručně popisuje různé metody a doporučuje nejlepší způsob pro různé scénáře.


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

  > [!NOTE]
  > SQL Server 2008 a SQL Server 2008 R2 se blíží [konci životního cyklu podpory](https://www.microsoft.com/sql-server/sql-server-2008) pro místní instance. Pokud chcete rozšířit podporu, můžete buď migrovat instanci SQL Server na virtuální počítač Azure, nebo si koupit rozšířené aktualizace zabezpečení, aby byly místní. Další informace najdete v tématu věnovaném [rozšiřování podpory SQL Server 2008 a 2008 R2 s Azure](sql-server-2008-extend-end-of-support.md) .

## <a name="what-are-the-primary-migration-methods"></a>Jaké jsou primární metody migrace?

Primární metody migrace jsou:

* Proveďte místní zálohování pomocí komprese a pak ručně zkopírujte záložní soubor do virtuálního počítače Azure.
* Proveďte zálohu na adresu URL a pak ji z adresy URL obnovte do virtuálního počítače Azure.
* Odpojte data a soubory protokolů, zkopírujte je do úložiště objektů BLOB v Azure a pak je připojte k SQL Server na virtuálním počítači Azure z adresy URL.
* Převeďte místní fyzický počítač na virtuální pevný disk Hyper-V, nahrajte ho do úložiště objektů BLOB v Azure a pak ho nasaďte jako nový virtuální počítač pomocí nahraného virtuálního pevného disku.
* Dodejte pevný disk pomocí služby Windows import/export.
* Pokud máte místní nasazení skupiny dostupnosti AlwaysOn, použijte [Průvodce přidáním repliky Azure](../../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) k vytvoření repliky v Azure, převzetí služeb při selhání a nasměrování uživatelů na instanci Azure Database.
* Pomocí SQL Server [transakční replikace](https://msdn.microsoft.com/library/ms151176.aspx) můžete nakonfigurovat instanci Azure SQL Server jako předplatitele, zakázat replikaci a nasměrovat uživatele na instanci databáze Azure.

> [!TIP]
> Stejné postupy můžete použít také k přesunu databází mezi SQL Server virtuálními počítači v Azure. Například neexistuje žádný podporovaný způsob, jak upgradovat galerii SQL Server – image virtuálního počítače z jedné verze nebo edice na jinou. V takovém případě byste měli vytvořit nový virtuální počítač s SQL Server s novou verzí nebo edicí a potom použít jeden z technik migrace v tomto článku k přesunutí vašich databází. 

## <a name="choose-a-migration-method"></a>Zvolit způsob migrace

Pro dosažení optimálního výkonu přenosu dat migrujte soubory databáze do virtuálního počítače Azure pomocí komprimovaného záložního souboru.

Pokud chcete během procesu migrace databáze minimalizovat prostoje, použijte možnost AlwaysOn nebo transakční replikaci.

Pokud není možné použít výše uvedené metody, migrujte databázi ručně. Obecně platí, že začnete se zálohováním databáze, postupujete s kopií zálohy databáze do Azure a pak obnovíte databázi. Soubory databáze můžete také zkopírovat do Azure a pak je připojit. Existuje několik metod, pomocí kterých můžete tento ruční proces migrace databáze do virtuálního počítače Azure provést.

> [!NOTE]
> Při upgradu na SQL Server 2014 nebo SQL Server 2016 ze starších verzí SQL Server byste měli zvážit, zda jsou potřeba změny. Doporučujeme, abyste všechny závislosti na funkcích, které nejsou podporovány v nové verzi SQL Server, vyřešíte jako součást projektu migrace. Další informace o podporovaných edicích a scénářích najdete v tématu [upgrade na SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

Následující tabulka uvádí jednotlivé metody primární migrace a popisuje, kdy je použití jednotlivých metod nejvhodnější.

| Metoda | Verze zdrojové databáze | Verze cílové databáze | Omezení velikosti zálohy zdrojové databáze | Poznámky |
| --- | --- | --- | --- | --- |
| [Provedení místní zálohy pomocí komprese a ručnímu zkopírování záložního souboru do virtuálního počítače Azure](#back-up-and-restore) |SQL Server 2005 nebo vyšší |SQL Server 2005 nebo vyšší |[Limit úložiště virtuálních počítačů Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) | Tato technika je pro přesun databází v různých počítačích jednoduchá a dobře testována. |
| [Proveďte zálohu na adresu URL a obnovte ji do virtuálního počítače Azure z adresy URL.](#backup-to-url-and-restore-from-url) |SQL Server 2012 SP1 CU2 nebo novější | SQL Server 2012 SP1 CU2 nebo novější | < 12,8 TB pro SQL Server 2016, jinak < 1 TB | Tato metoda je jenom dalším způsobem, jak přesunout záložní soubor do virtuálního počítače pomocí služby Azure Storage. |
| [Odpojení a následné zkopírování souborů dat a protokolů do úložiště objektů BLOB v Azure a připojení k SQL Server na virtuálním počítači Azure z adresy URL](#detach-and-attach-from-a-url) | SQL Server 2005 nebo vyšší |SQL Server 2014 nebo vyšší | [Limit úložiště virtuálních počítačů Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) | Tuto metodu použijte, když plánujete [ukládat tyto soubory pomocí služby Azure Blob Storage](https://msdn.microsoft.com/library/dn385720.aspx) a připojit je k SQL Server běžícímu na virtuálním počítači Azure, zejména u velmi rozsáhlých databází. |
| [Převod místního počítače na virtuální pevné disky Hyper-V, nahrání do úložiště objektů BLOB v Azure a následné nasazení nového virtuálního počítače pomocí nahraného virtuálního pevného disku](#convert-to-a-vm-upload-to-a-url-and-deploy-as-a-new-vm) |SQL Server 2005 nebo vyšší |SQL Server 2005 nebo vyšší |[Limit úložiště virtuálních počítačů Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Při migraci databáze, kterou spustíte ve starší verzi SQL Server nebo při migraci systémových a uživatelských databází v rámci migrace databáze závislé na jiných uživatelových databázích nebo systémových databázích, použijte [k uvedení vlastní licence SQL Server](../../../azure-sql/azure-sql-iaas-vs-paas-what-is-overview.md). |
| [Dodání pevného disku pomocí služby Windows import/export](#ship-a-hard-drive) |SQL Server 2005 nebo vyšší |SQL Server 2005 nebo vyšší |[Limit úložiště virtuálních počítačů Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Použijte [službu import/export systému Windows](../../../storage/common/storage-import-export-service.md) , pokud je metoda ručního kopírování příliš pomalá, například u velmi velkých databází. |
| [Použití Průvodce přidáním repliky Azure](../../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) |SQL Server 2012 nebo vyšší |SQL Server 2012 nebo vyšší |[Limit úložiště virtuálních počítačů Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Minimalizuje výpadek, používá se, když máte trvalé nasazení na pracovišti. |
| [Použití SQL Server transakční replikace](https://msdn.microsoft.com/library/ms151176.aspx) |SQL Server 2005 nebo vyšší |SQL Server 2005 nebo vyšší |[Limit úložiště virtuálních počítačů Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Použijte v případě, že potřebujete minimalizovat prostoje a nemáte trvalé nasazení v místním prostředí. |

## <a name="back-up-and-restore"></a>Zálohování a obnovení

Zálohujte databázi pomocí komprese, zkopírujte zálohu do virtuálního počítače a pak databázi obnovte. Je-li záložní soubor větší než 1 TB, je nutné vytvořit prokládanou sadu, protože maximální velikost disku virtuálního počítače je 1 TB. Pomocí následujících obecných kroků migrujte uživatelskou databázi pomocí této ruční metody:

1. Proveďte úplnou zálohu databáze do místního umístění.
2. Vytvořte nebo nahrajte virtuální počítač s požadovanou verzí SQL Server.
3. Nastavte připojení podle vašich požadavků. Viz [připojení k virtuálnímu počítači s SQL Server v Azure (Správce prostředků)](ways-to-connect-to-sql.md).
4. Zkopírujte záložní soubory do virtuálního počítače pomocí vzdálené plochy, Průzkumníka Windows nebo příkazu Kopírovat z příkazového řádku.

## <a name="backup-to-url-and-restore-from-url"></a>Zálohování na adresu URL a obnovení z adresy URL

Místo zálohování do místního souboru můžete použít [zálohování na adresu URL](https://msdn.microsoft.com/library/dn435916.aspx) a pak obnovit z adresy URL virtuálního počítače. SQL Server 2016 podporuje prokládané zálohovací sklady. Doporučuje se pro výkon a musí překročit omezení velikosti na jeden objekt BLOB. U velmi rozsáhlých databází se doporučuje použít [službu import/export systému Windows](../../../storage/common/storage-import-export-service.md) .

## <a name="detach-and-attach-from-a-url"></a>Odpojení a připojení od adresy URL

Odpojte databáze a soubory protokolů a přeneste je do [úložiště objektů BLOB v Azure](https://msdn.microsoft.com/library/dn385720.aspx). Pak databázi připojte z adresy URL na VIRTUÁLNÍm počítači Azure. Tuto metodu použijte, pokud chcete, aby se fyzické soubory databáze nacházely ve službě BLOB Storage, což může být užitečné pro velmi velké databáze. Pomocí následujících obecných kroků migrujte uživatelskou databázi pomocí této ruční metody:

1. Odpojí soubory databáze od místní instance databáze.
2. Zkopírujte odpojené databázové soubory do úložiště objektů BLOB v Azure pomocí [nástroje příkazového řádku AzCopy](../../../storage/common/storage-use-azcopy.md).
3. Připojte soubory databáze z adresy URL Azure k instanci SQL Server ve virtuálním počítači Azure.

## <a name="convert-to-a-vm-upload-to-a-url-and-deploy-as-a-new-vm"></a>Převod na virtuální počítač, nahrání na adresu URL a nasazení jako nového virtuálního počítače

Tato metoda slouží k migraci všech systémových a uživatelských databází v místní instanci SQL Server do virtuálního počítače Azure. K migraci celé SQL Server instance pomocí této ruční metody použijte následující obecné kroky:

1. Převeďte fyzické nebo virtuální počítače na virtuální pevné disky Hyper-V.
2. Nahrajte soubory VHD do Azure Storage pomocí [rutiny Add-AzureVHD](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx).
3. Nasaďte nový virtuální počítač pomocí nahraného virtuálního pevného disku.

> [!NOTE]
> K migraci celé aplikace zvažte použití [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)].

## <a name="ship-a-hard-drive"></a>Dodání pevného disku

Použijte [metodu služby Windows import/export](../../../storage/common/storage-import-export-service.md) k přenosu velkých objemů souborových dat do úložiště objektů BLOB v Azure v situacích, kdy je nahlašování přes síť denáročná nebo neproveditelná. Pomocí této služby odešlete jeden nebo více pevných disků obsahujících tato data do datového centra Azure, kam budou data odeslána do svého účtu úložiště.

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [přehled SQL Server v Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md).

> [!TIP]
> Pokud máte dotazy k virtuálním počítačům s SQL Serverem, přečtěte si [Nejčastější dotazy](frequently-asked-questions-faq.md).

Pokyny k vytvoření SQL Server na virtuálním počítači Azure ze zaznamenané bitové kopie najdete v tématu [tipy & štychy týkající se klonování virtuálních počítačů Azure SQL ze zachycených imagí](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) na blogu šablony CSS SQL Server technici.

