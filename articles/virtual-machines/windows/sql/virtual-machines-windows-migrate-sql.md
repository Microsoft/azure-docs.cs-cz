---
title: "Migrace databáze SQL serveru do systému SQL Server na virtuálním počítači | Microsoft Docs"
description: "Další informace o tom, jak migrovat databázi místní uživatele k systému SQL Server ve virtuálním počítači Azure."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: craigg
editor: 
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: jroth
ms.openlocfilehash: 64245a968eca94518d2e4238b4bc5c93c952563a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>Migrovat databázi SQL Serveru na SQL Server na virtuálním počítači Azure

Existuje několik metod pro migraci databáze uživatelů technologie místní systém SQL Server do systému SQL Server ve virtuálním počítači Azure. Tento článek se stručně popisují různé metody a doporučuje nejlepší metody pro různé scénáře.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="what-are-the-primary-migration-methods"></a>Jaké jsou metody primární migrace?
Primární migrace metody jsou následující:

* Místní zálohování pomocí komprese a ručně zkopírujte záložní soubor do virtuálního počítače Azure
* Proveďte zálohu adresy URL a obnovení do virtuálního počítače Azure z adresy URL
* Odpojte a poté zkopírujte soubory protokolu a data do Azure blob storage a pak připojte k serveru SQL Server ve virtuálním počítači Azure z adresy URL
* Převod fyzického počítače na místní disk VHD Hyper-V, odešle do úložiště objektů Blob v Azure a pak nasadit jako nový virtuální počítač pomocí nahrát virtuální pevný disk
* Dodávat pevný disk pomocí služby Windows importu a exportu
* Pokud máte nasazení AlwaysOn místně, použijte [Průvodce přidáním Azure repliky](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) vytvoření repliky ve službě Azure a pak převzetí služeb při selhání, přejdete na příkaz uživatelé instance databáze Azure
* Pomocí systému SQL Server [transakční replikace](https://msdn.microsoft.com/library/ms151176.aspx) konfigurovat instanci serveru SQL Azure jako odběratele a potom zakažte replikaci, přejdete na příkaz uživatelé instance databáze Azure

> [!TIP]
> Stejné postupy můžete použít také k přesunutí databází mezi virtuálním počítačům systému SQL Server v Azure. Není třeba žádné podporované způsob, jak upgradovat Galerie image virtuálního počítače systému SQL Server z jedné verzi nebo edici do jiného. V takovém případě by měl vytvořit nový virtuální počítač SQL Server s novou verzi nebo edici a pak použijte jednu z techniky migrace v tomto článku Přesunutí databází. 

## <a name="choosing-your-migration-method"></a>Volba způsob migrace
Pro optimální data výkonu přenosu migrace soubory databáze do virtuálního počítače Azure komprimované záložního souboru.

Chcete-li minimalizovat prostoje během procesu migrace databáze, použijte možnost AlwaysOn nebo transakční replikace.

Pokud není možné použít výše uvedené metody, ruční migraci databáze. Tuto metodu použijete, se obecně začínat zálohu databáze, za nímž následuje kopii zálohy databáze do Azure a poté proveďte obnovení databáze. Můžete také zkopírovat soubory databáze sami do Azure a připojte je. Existuje několik metod, které můžete provést tento ruční proces migrace databáze do virtuálního počítače Azure.

> [!NOTE]
> Když upgradujete SQL Server 2014 nebo SQL Server 2016 ze starších verzí systému SQL Server, měli byste zvážit, zda je nutné provést změny. Doporučujeme, abyste předem vyřešili všechny závislosti na funkcích nepodporuje novou verzi systému SQL Server jako součást migrace projektu. Další informace o podporovaných edicích a scénáře, najdete v části [upgradujte na SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

Následující tabulka obsahuje seznam všech metod primární migrace a popisuje, když je nejvhodnější použití každé metody.

| Metoda | Verze databáze zdroje | Verze cíle pro databáze | Omezení velikosti zálohy zdrojové databáze | Poznámky |
| --- | --- | --- | --- | --- |
| [Místní zálohování pomocí komprese a ručně zkopírujte záložní soubor do virtuálního počítače Azure](#backup-and-restore) |SQL Server 2005 nebo vyšší |SQL Server 2005 nebo vyšší |[Azure limitu úložiště virtuálních počítačů](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | To je velmi jednoduché a dobře otestované technika pro přesunutí databází mezi počítače. |
| [Proveďte zálohu adresy URL a obnovení do virtuálního počítače Azure z adresy URL](#backup-to-url-and-restore) |SQL Server 2012 SP1 CU2 nebo vyšší |SQL Server 2012 SP1 CU2 nebo vyšší |< 12,8 TB pro SQL Server 2016, jinak < 1 TB | Tato metoda je další způsob, jak přesunout záložní soubor k virtuálnímu počítači pomocí úložiště Azure. |
| [Odpojte a poté zkopírujte soubory protokolu a data do Azure blob storage a pak připojte k serveru SQL Server ve virtuálním počítači Azure z adresy URL](#detach-and-attach-from-url) |SQL Server 2005 nebo vyšší |SQL Server 2014 nebo vyšší |[Azure limitu úložiště virtuálních počítačů](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Tuto metodu použijte, když plánujete [ukládání těchto souborů pomocí služby Azure Blob storage](https://msdn.microsoft.com/library/dn385720.aspx) a připojte je k SQL Server běžící ve virtuálním počítači Azure, zejména s velmi velké databáze |
| [Převést na místním počítači na virtuální pevné disky Hyper-V, odešle do úložiště objektů Blob v Azure a pak nasadit nové virtuální počítač pomocí nahraný virtuální pevný disk](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) |SQL Server 2005 nebo vyšší |SQL Server 2005 nebo vyšší |[Azure limitu úložiště virtuálních počítačů](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Použijte, když [přinesou vlastní licenci na SQL Server](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md), při migraci databáze, která se spustí ve starší verzi systému SQL Server nebo při migraci databáze systému a uživatele společně jako součást migrace závisí na jiné databáze uživatel databáze nebo systémové databáze. |
| [Dodávat pevný disk pomocí služby Windows importu a exportu](#ship-hard-drive) |SQL Server 2005 nebo vyšší |SQL Server 2005 nebo vyšší |[Azure limitu úložiště virtuálních počítačů](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Použití [importu/exportu služby systému Windows](../../../storage/common/storage-import-export-service.md) po příliš pomalé, například s velmi velké databáze ruční copy – metoda |
| [Použití Azure repliky Průvodce přidáním](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) |SQL Server 2012 nebo vyšší |SQL Server 2012 nebo vyšší |[Azure limitu úložiště virtuálních počítačů](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Minimalizuje prostoje, použijte v případě, že místní nasazení Always On |
| [Pomocí transakční replikace systému SQL Server](https://msdn.microsoft.com/library/ms151176.aspx) |SQL Server 2005 nebo vyšší |SQL Server 2005 nebo vyšší |[Azure limitu úložiště virtuálních počítačů](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Použijte, pokud budete muset minimalizovat prostoje a nemají místní nasazení Always On |

## <a name="backup-and-restore"></a>Zálohování a obnovení
Zálohovat databázi s kompresí zkopírujte zálohování na virtuální počítač a potom obnovit databázi. Pokud záložní soubor je větší než 1 TB, musí ho rozkládají, protože maximální velikost disku virtuálního počítače je 1 TB. Použijte následující obecné kroky pro migraci uživatele databáze pomocí této metody Ruční:

1. Proveďte úplnou zálohu databáze do místního umístění.
2. Vytvořit nebo nahrát virtuální počítač s verzí systému SQL Server potřeby.
3. Nastavte připojení podle svých požadavků. V tématu [připojit k virtuálnímu počítači serveru SQL v Azure (Resource Manager)](virtual-machines-windows-sql-connect.md).
4. Zkopírujte záložní soubory do virtuálního počítače pomocí vzdálené plochy, Průzkumníka Windows nebo kopírování příkazu z příkazového řádku.

## <a name="backup-to-url-and-restore"></a>Zálohování na adresu URL a obnovení
Namísto zálohování do místního souboru, můžete použít [zálohování na adresu URL](https://msdn.microsoft.com/library/dn435916.aspx) a potom obnovte virtuální počítač z adresy URL. Pomocí SQL serveru 2016 prokládané zálohovací sklady jsou podporovány, jsou doporučené pro výkon a musí překračovat limity velikosti za objektů blob. Pro velké databáze, použití [importu/exportu služby systému Windows](../../../storage/common/storage-import-export-service.md) se doporučuje.

## <a name="detach-and-attach-from-url"></a>Odpojit a připojit z adresy URL
Odpojení vaší databáze a soubory protokolu a jejich přenos [úložiště objektů Azure Blob](https://msdn.microsoft.com/library/dn385720.aspx). Pak připojili databázi z adresy URL na vašem virtuálním počítači Azure. Použijte, pokud chcete soubory fyzické databáze jsou umístěny v úložišti objektů Blob. To může být užitečná pro velké databáze. Použijte následující obecné kroky pro migraci uživatele databáze pomocí této metody Ruční:

1. Odpojte soubory databáze z místní instanci databáze.
2. Zkopírujte soubory odpojit databáze do úložiště objektů blob v Azure pomocí [příkazového řádku azcopy](../../../storage/common/storage-use-azcopy.md).
3. Připojte soubory databáze z adresy URL Azure k instanci systému SQL Server ve virtuálním počítači Azure.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>Převést na virtuální počítač a nahrát na URL a nasadit jako nový virtuální počítač
Tuto metodu použijte k migraci všech systému a uživatele databáze v instanci systému SQL Server místní na virtuální počítač Azure. Použijte následující obecné kroky pro migraci instanci systému SQL Server pomocí této metody Ruční:

1. Převést fyzické nebo virtuální počítače Hyper-V virtuální pevné disky pomocí [Microsoft Virtual Machine Converter](https://technet.microsoft.com/library/dn874008(v=ws.11).aspx).
2. Odesílat soubory virtuálního pevného disku do úložiště Azure pomocí [rutinu Add-AzureVHD](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx).
3. Nasazení nového virtuálního počítače pomocí nahraný virtuální pevný disk.

> [!NOTE]
> Pokud chcete migrovat celou aplikaci, zvažte použití [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)].

## <a name="ship-hard-drive"></a>Dodávat pevný disk
Použití [metoda importu/exportu služby systému Windows](../../../storage/common/storage-import-export-service.md) přenos velkých objemů dat souborů do úložiště objektů Blob v Azure v situacích, kde odesílání přes síť je výtažkovými nebo není vhodný. S touto službou odeslat jeden nebo více pevné disky obsahující data pro datové centrum Azure, kde bude vaše data nahrán do účtu úložiště.

## <a name="next-steps"></a>Další kroky
Další informace o spuštění systému SQL Server na virtuálních počítačích Azure najdete v tématu [SQL Server na virtuálních počítačích Azure přehled](virtual-machines-windows-sql-server-iaas-overview.md).

> [!TIP]
> Pokud máte dotazy týkající se virtuální počítače systému SQL Server, najdete v článku [– nejčastější dotazy](virtual-machines-windows-sql-server-iaas-faq.md).

Pokyny pro vytvoření virtuálního počítače serveru SQL Azure ze zaznamenané bitové kopie, naleznete v části [tipy a triky na klonování virtuálních počítačích Azure SQL ze zaznamenané bitové kopie](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) na blogu technici serveru SQL šablon stylů CSS.

