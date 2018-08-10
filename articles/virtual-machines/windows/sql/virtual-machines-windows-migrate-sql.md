---
title: Migrace databáze SQL serveru na SQL Server na virtuálním počítači | Dokumentace Microsoftu
description: Další informace o tom, jak migrovat databázi místní uživatele k systému SQL Server ve virtuálním počítači Azure.
services: virtual-machines-windows
documentationcenter: ''
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.devlang: na
ms.topic: article
ms.date: 08/18/2018
ms.author: jroth
ms.openlocfilehash: 0677faa90c73ffe4c0c1c48600c2f1ef2d05eb50
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628779"
---
# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>Migrovat databázi SQL Serveru na SQL Server na virtuálním počítači Azure

Existuje několik metod migrovat databázi uživatele v místním SQL serveru na SQL Server na Virtuálním počítači Azure. Tento článek stručně popisují různé metody a doporučuje nejlepší metody pro různé scénáře.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="what-are-the-primary-migration-methods"></a>Co jsou metody migrace?
Primární migrací metody jsou následující:

* Provést zálohování v místním pomocí komprese a ručně zkopírujte soubor zálohy do virtuálních počítačů Azure
* Provedení zálohování na adresu URL a obnovení virtuálních počítačů Azure z adresy URL
* Odpojení a zkopírujte soubory protokolu a data do Azure blob storage a potom připojit k serveru SQL Server na virtuálním počítači Azure z adresy URL
* Převést fyzický počítač v místním virtuálního pevného disku Hyper-V, nahrajte do Azure Blob storage a pak nasaďte nový virtuální počítač pomocí nahrávaných virtuálního pevného disku
* Dodávejte pevný disk pomocí služby Import/Export pro Windows
* Pokud máte nasazení služby AlwaysOn v místním, použijte [Průvodce přidáním repliky Azure](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) vytvoření repliky v Azure a pak převzetí služeb při selhání, odkazující uživatele na instanci databáze Azure
* Použít systém SQL Server [transakční replikace](https://msdn.microsoft.com/library/ms151176.aspx) konfigurovat instanci serveru SQL Azure jako odběratele a potom zakázat replikaci, odkazující uživatele na instanci Azure database

> [!TIP]
> Stejné postupy můžete použít také pro přesun databází mezi virtuální počítače s SQL serverem v Azure. Například se nedají podporované pro upgrade virtuálního počítače s SQL serverem image z Galerie z jedné verzi nebo edici na jiný. V takovém případě by měl vytvořit nový virtuální počítač SQL Server s novou verzí/edicí a použijte jeden z postupů migrace v tomto článku přesunout databáze z. 

## <a name="choosing-your-migration-method"></a>Volba metody migrace
Pro úroveň výkonu přenosu dat optimální migrate do virtuálního počítače Azure pomocí komprimovaný soubor záložní soubory databáze.

Chcete-li minimalizovat prostoje během procesu migrace databáze, použijte možnost AlwaysOn nebo transakční replikace.

Pokud není možné použít výše uvedené metody, ručně provést migraci vaší databáze. Pomocí této metody, bude obvykle je dobré začít s zálohu databáze, za nímž následuje kopii zálohy databáze do Azure a pak proveďte obnovení databáze. Můžete také zkopírovat soubory databáze, samotné do Azure a připojte je. Existuje několik metod, které lze provádět tento ruční proces migrace databáze do virtuálního počítače Azure.

> [!NOTE]
> Při upgradu na SQL Server 2014 nebo SQL Server 2016 ze starších verzí SQL serveru byste měli zvážit, zda je třeba provést změny. Doporučujeme vám odstranit všechny závislosti na funkcích nepodporuje novou verzi systému SQL Server jako součást váš projekt migrace. Další informace o podporovaných edicích a scénáře, naleznete v tématu [Upgrade na SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

Následující tabulka obsahuje seznam všech metod migrace a popisuje při použití každé metody je nejvhodnější.

| Metoda | Verze zdrojové databáze | Verze cílové databáze | Omezení velikosti zálohy zdrojové databáze | Poznámky |
| --- | --- | --- | --- | --- |
| [Provést zálohování v místním pomocí komprese a ručně zkopírujte soubor zálohy do virtuálních počítačů Azure](#backup-and-restore) |SQL Server 2005 nebo vyšší |SQL Server 2005 nebo vyšší |[Azure storage limitu virtuálních počítačů](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Toto je velmi jednoduché a dobře otestovaný postup pro přesun databází mezi počítači. |
| [Provedení zálohování na adresu URL a obnovení virtuálních počítačů Azure z adresy URL](#backup-to-url-and-restore) |SQL Server 2012 SP1 kumulativní aktualizaci 2 nebo vyšší |SQL Server 2012 SP1 kumulativní aktualizaci 2 nebo vyšší |< 12,8 TB pro SQL Server 2016, jinak < 1 TB | Tato metoda je další způsob, jak přesunout záložní soubor do virtuálního počítače pomocí služby Azure storage. |
| [Odpojení a zkopírujte soubory protokolu a data do Azure blob storage a potom připojit k serveru SQL Server ve virtuálním počítači Azure z adresy URL](#detach-and-attach-from-url) |SQL Server 2005 nebo vyšší |SQL Server 2014 nebo vyšší |[Azure storage limitu virtuálních počítačů](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Tuto metodu použijte, pokud máte v úmyslu [ukládání těchto souborů pomocí služby Azure Blob storage](https://msdn.microsoft.com/library/dn385720.aspx) a připojení k SQL serveru běžícího na Virtuálním počítači Azure, zvláště u velkých databází |
| [Převést na místním počítači na virtuální pevné disky Hyper-V, nahrání do úložiště objektů Blob v Azure a pak nasaďte nový virtuální počítač pomocí nahraný virtuální pevný disk](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) |SQL Server 2005 nebo vyšší |SQL Server 2005 nebo vyšší |[Azure storage limitu virtuálních počítačů](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Použijte v této situaci [používáním vlastní licence SQL serveru](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md), při migraci databáze, která se spustí na starší verzi systému SQL Server, nebo při migraci systémových a uživatelských databázích společně jako součást migrace databáze, které jsou závislé na jiné uživatelské databáze a/nebo systémové databáze. |
| [Dodávejte pevný disk pomocí služby Import/Export pro Windows](#ship-hard-drive) |SQL Server 2005 nebo vyšší |SQL Server 2005 nebo vyšší |[Azure storage limitu virtuálních počítačů](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Použití [služby Import/Export Windows](../../../storage/common/storage-import-export-service.md) po příliš pomalý, například s velmi velkými databázemi ruční export – metoda |
| [Použití Průvodce přidáním repliky Azure](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) |SQL Server 2012 nebo vyšší |SQL Server 2012 nebo vyšší |[Azure storage limitu virtuálních počítačů](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Minimalizuje prostoje, použijte v případě místních nasazení Always On |
| [Použití transakční replikace systému SQL Server](https://msdn.microsoft.com/library/ms151176.aspx) |SQL Server 2005 nebo vyšší |SQL Server 2005 nebo vyšší |[Azure storage limitu virtuálních počítačů](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Pokud potřebujete, aby se minimalizovaly výpadky a nemají v místním nasazení Always On |

## <a name="backup-and-restore"></a>Zálohování a obnovení
Proveďte zálohu vaší databáze bez komprese zkopírujte zálohu k virtuálnímu počítači a potom obnovit databázi. Pokud záložní soubor je větší než 1 TB, musí ho prokládanou, protože maximální velikost disku virtuálního počítače je 1 TB. Použijte následující obecné kroky pro migraci uživatele databáze pomocí této ruční metody:

1. Proveďte úplnou zálohu databáze do místního umístění.
2. Vytvořit nebo nahrát virtuální počítač s verzí SQL serveru pro potřeby.
3. Nastavte připojení na základě vašich požadavků. Zobrazit [připojit k virtuálnímu počítači SQL serveru v Azure (Resource Manager)](virtual-machines-windows-sql-connect.md).
4. Zkopírujte záložní soubory k vašemu virtuálnímu počítači pomocí vzdálené plochy, Průzkumník Windows nebo příkazu kopírování z příkazového řádku.

## <a name="backup-to-url-and-restore"></a>Zálohování na adresu URL a obnovení
Namísto zálohování do místního souboru, můžete použít [zálohování na adresu URL](https://msdn.microsoft.com/library/dn435916.aspx) a potom obnovte virtuální počítač z adresy URL. S SQL serverem 2016 prokládané sady záloh jsou podporovány, se doporučuje pro výkon a musí překračovat omezení velikosti na objekt blob. Pro velmi velké databáze, použití [služby Import/Export Windows](../../../storage/common/storage-import-export-service.md) se doporučuje.

## <a name="detach-and-attach-from-url"></a>Odpojit a připojit z adresy URL
Odpojení vaší databáze a soubory protokolů a přenést je do [úložiště objektů Blob v Azure](https://msdn.microsoft.com/library/dn385720.aspx). Pak připojili databázi z adresy URL na vašem virtuálním počítači Azure. Použijte, pokud chcete, aby fyzické databázové soubory jsou umístěny v úložišti objektů Blob. To může být užitečné u velmi velkých databází. Použijte následující obecné kroky pro migraci uživatele databáze pomocí této ruční metody:

1. Odpojení databází z místní instanci databáze.
2. Zkopírujte soubory odpojenou databázi do úložiště objektů blob v Azure pomocí [příkazového řádku azcopy](../../../storage/common/storage-use-azcopy.md).
3. Soubory databáze z adresy URL Azure připojte k instanci systému SQL Server na virtuálním počítači Azure.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>Převést na virtuální počítač a nahrát na URL a nasadit jako nový virtuální počítač
Tuto metodu použijte k migraci všech systémových a uživatelských databází v instanci systému SQL Server na místním virtuálním počítači Azure. Použijte následující obecné kroky pro migraci celé instance systému SQL Server tímto způsobem ruční:

1. Převeďte fyzický nebo virtuální počítače na virtuální pevné disky Hyper-V.
2. Nahrát soubory virtuálního pevného disku do služby Azure Storage s použitím [rutiny Add-AzureVHD](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx).
3. Nasazení nového virtuálního počítače pomocí nahraný virtuální pevný disk.

> [!NOTE]
> Pokud chcete migrovat celou aplikaci, zvažte použití [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)].

## <a name="ship-hard-drive"></a>Dodávejte pevného disku
Použití [metodu služby Import/Export Windows](../../../storage/common/storage-import-export-service.md) přenášet velké objemy souborových dat do úložiště objektů Blob v Azure v situacích, kdy nahrávání přes síť nepřekonatelně drahé nebo není vhodná. K této službě odesílat jeden nebo více pevné disky obsahující data do datového centra Azure, ve kterém se nahraje vaše data do účtu úložiště.

## <a name="next-steps"></a>Další kroky
Další informace o spuštění SQL serveru na virtuálních počítačích Azure najdete v tématu [systému SQL Server na Azure Virtual Machines – přehled](virtual-machines-windows-sql-server-iaas-overview.md).

> [!TIP]
> Pokud máte dotazy k virtuálním počítačům s SQL Serverem, přečtěte si [Nejčastější dotazy](virtual-machines-windows-sql-server-iaas-faq.md).

Pokyny k vytvoření virtuálního počítače s Azure SQL Server ze zaznamenané bitové kopie, naleznete v tématu [tipy a triky na klonování virtuálních počítačích Azure SQL z zaznamenané image](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) na blogu inženýrů šablon stylů CSS SQL Server.

