---
title: Migrace databáze serveru SQL Server na SQL Server na virtuálním počítači | Dokumenty společnosti Microsoft
description: Přečtěte si, jak migrovat místní databázi uživatelů na SQL Server ve virtuálním počítači Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.topic: article
ms.date: 08/18/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c8314b04c05e2ecba2715b807171b5c1a2fa988a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646859"
---
# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>Migrovat databázi SQL Serveru na SQL Server na virtuálním počítači Azure

Existuje několik metod pro migraci místní databáze uživatelů SQL Serveru na SQL Server ve virtuálním počítači Azure. Tento článek stručně probere různé metody a doporučí nejlepší metodu pro různé scénáře.


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

  > [!NOTE]
  > SQL Server 2008 a SQL Server 2008 R2 se blíží [ke konci svého životního cyklu podpory](https://www.microsoft.com/sql-server/sql-server-2008) pro místní instance. Chcete-li rozšířit podporu, můžete buď migrovat instanci SQL Serveru do virtuálního počítače Azure, nebo zakoupit rozšířené aktualizace zabezpečení, abyste ji udrželi místně. Další informace najdete [v tématu Rozšíření podpory pro SQL Server 2008 a 2008 R2 s Azure](virtual-machines-windows-sql-server-2008-eos-extend-support.md)

## <a name="what-are-the-primary-migration-methods"></a>Jaké jsou primární metody migrace?
Primární metody migrace jsou:

* Provádění místnízálohy pomocí komprese a ruční kopírování záložního souboru do virtuálního počítače Azure
* Provedení zálohování adresy URL a obnovení virtuálního počítače Azure z adresy URL
* Odpojte a zkopírujte data a soubory protokolu do úložiště objektů blob Azure a pak se připojte k SQL Serveru ve virtuálním počítači Azure z adresy URL
* Převeďte místní fyzický počítač na hyper-v.hd, nahrajte se do úložiště objektů Blob Azure a pak se nasaďte jako nový virtuální počítač pomocí nahraného virtuálního pevného disku
* Odeslání pevného disku pomocí služby Import/Export služby Windows
* Pokud máte místní nasazení skupiny dostupnosti AlwaysOn, použijte [Průvodce přidáním repliky Azure](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) k vytvoření repliky v Azure a následném převzetí služeb při selhání a odkazování uživatelů na instanci databáze Azure.
* Použití [transakční replikace](https://msdn.microsoft.com/library/ms151176.aspx) serveru SQL Server ke konfiguraci instance Azure SQL Serveru jako předplatitele a následném zakázání replikace a odkazování uživatelů na instanci databáze Azure

> [!TIP]
> Tyto stejné techniky můžete také použít k přesunu databází mezi virtuálními počítači SQL Server v Azure. Například neexistuje žádný podporovaný způsob, jak upgradovat sql server galerie image virtuálního uživatele z jedné verze nebo edice do jiné. V takovém případě byste měli vytvořit nový virtuální počítač SQL Server s novou verzí nebo edicí a potom k přesunutí databází pomocí jedné z technik migrace v tomto článku. 

## <a name="choosing-your-migration-method"></a>Výběr metody migrace
Pro optimální výkon přenosu dat migrujte databázové soubory do virtuálního počítače Azure pomocí komprimovaného záložního souboru.

Chcete-li minimalizovat prostoje během procesu migrace databáze, použijte možnost AlwaysOn nebo transakční replikace.

Pokud není možné použít výše uvedené metody, ručně migrujte databázi. Pomocí této metody obvykle začnete se záloha databáze následuje kopie zálohy databáze do Azure a potom provést obnovení databáze. Databázové soubory můžete také zkopírovat do Azure a pak je připojit. Existuje několik metod, pomocí kterých můžete provést tento ruční proces migrace databáze do virtuálního počítače Azure.

> [!NOTE]
> Při upgradu na SQL Server 2014 nebo SQL Server 2016 ze starších verzí SQL Serveru byste měli zvážit, zda jsou potřebné změny. Doporučujeme řešit všechny závislosti na funkcích, které nejsou podporovány novou verzí serveru SQL Server jako součást projektu migrace. Další informace o podporovaných edicích a scénářích naleznete v [tématu Upgrade na SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

V následující tabulce jsou uvedeny všechny primární metody migrace a popisuje, kdy je nejvhodnější použití každé metody.

| Metoda | Verze zdrojové databáze | Verze cílové databáze | Omezení velikosti zálohy zdrojové databáze | Poznámky |
| --- | --- | --- | --- | --- |
| [Provádění místnízálohy pomocí komprese a ruční kopírování záložního souboru do virtuálního počítače Azure](#backup-and-restore) |SQL Server 2005 nebo vyšší |SQL Server 2005 nebo vyšší |[Limit úložiště virtuálních zařízení Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) | Jedná se o velmi jednoduchou a dobře otestopraořenou techniku pro přesouvání databází mezi stroji. |
| [Provedení zálohování adresy URL a obnovení virtuálního počítače Azure z adresy URL](#backup-to-url-and-restore) |SQL Server 2012 SP1 CU2 nebo vyšší |SQL Server 2012 SP1 CU2 nebo vyšší |< 12,8 TB pro SQL Server 2016, jinak < 1 TB | Tato metoda je jen další způsob, jak přesunout záložní soubor do virtuálního počítače pomocí úložiště Azure. |
| [Odpojte a zkopírujte data a soubory protokolu do úložiště objektů blob Azure a pak se připojte k SQL Serveru ve virtuálním počítači Azure z adresy URL](#detach-and-attach-from-url) |SQL Server 2005 nebo vyšší |SQL Server 2014 nebo vyšší |[Limit úložiště virtuálních zařízení Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Tuto metodu použijte, pokud plánujete [ukládat tyto soubory pomocí služby úložiště objektů Blob Azure](https://msdn.microsoft.com/library/dn385720.aspx) a připojit je k SERVERU SQL Server spuštěného ve virtuálním počítači Azure, zejména s velmi rozsáhlými databázemi |
| [Převeďte místní počítač na virtuální disky Hyper-V, nahrajte se do úložiště objektů Blob Azure a pak nasaďte nový virtuální počítač pomocí nahraného virtuálního pevného disku](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) |SQL Server 2005 nebo vyšší |SQL Server 2005 nebo vyšší |[Limit úložiště virtuálních zařízení Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Používá se při [přinášení vlastní licence serveru SQL Server](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md), při migraci databáze, kterou spustíte na starší verzi serveru SQL Server, nebo při společné migraci systémových a uživatelských databází v rámci migrace databáze v závislosti na jiných uživatelských databázích nebo systémových databázích. |
| [Odeslání pevného disku pomocí služby Import/Export služby Windows](#ship-hard-drive) |SQL Server 2005 nebo vyšší |SQL Server 2005 nebo vyšší |[Limit úložiště virtuálních zařízení Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Službu [importu a exportu systému Windows](../../../storage/common/storage-import-export-service.md) použijte v případě, že je metoda ručního kopírování příliš pomalá, například u velmi rozsáhlých databází. |
| [Použití Průvodce přidáním repliky Azure](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) |SQL Server 2012 nebo vyšší |SQL Server 2012 nebo vyšší |[Limit úložiště virtuálních zařízení Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Minimalizuje prostoje, používá se, když máte místní nasazení vždy v místním prostředí. |
| [Použití transakční replikace serveru SQL Server](https://msdn.microsoft.com/library/ms151176.aspx) |SQL Server 2005 nebo vyšší |SQL Server 2005 nebo vyšší |[Limit úložiště virtuálních zařízení Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Použijte, když potřebujete minimalizovat prostoje a nemáte vždy místní nasazení |

## <a name="backup-and-restore"></a>Zálohování a obnovení
Zálohujte databázi pomocí komprese, zkopírujte zálohu do virtuálního počítače a pak databázi obnovte. Pokud je záložní soubor větší než 1 TB, musíte ho prokládat, protože maximální velikost disku virtuálního počítače je 1 TB. Pomocí následujících obecných kroků migrujte uživatelskou databázi pomocí této ruční metody:

1. Proveďte úplnou zálohu databáze do místního umístění.
2. Vytvořte nebo nahrajte virtuální počítač s požadovanou verzí SQL Serveru.
3. Nastavte připojení na základě vašich požadavků. Viz [Připojení k virtuálnímu počítači SQL Server v Azure (Správce prostředků).](virtual-machines-windows-sql-connect.md)
4. Zkopírujte záložní soubory do virtuálního počítače pomocí vzdálené plochy, Průzkumníka Windows nebo příkazu kopírování z příkazového řádku.

## <a name="backup-to-url-and-restore"></a>Zálohování na adresu URL a obnovení
Místo zálohování na místní soubor můžete použít [zálohu na adresu URL](https://msdn.microsoft.com/library/dn435916.aspx) a potom obnovit z adresy URL do virtuálního počítače. S SQL Server 2016 jsou podporovány prokládané zálohovací sady, jsou doporučené pro výkon a vyžaduje překročit omezení velikosti na objekt blob. U velmi rozsáhlých databází se doporučuje používat [službu Import/Export systému Windows.](../../../storage/common/storage-import-export-service.md)

## <a name="detach-and-attach-from-url"></a>Odpojit a připojit od adresy URL
Odpojte databázi a soubory protokolu a přeneste je do [úložiště objektů Blob Azure](https://msdn.microsoft.com/library/dn385720.aspx). Pak připojte databázi z adresy URL na vašem virtuálním počítači Azure. Tuto možnost použijte, pokud chcete, aby se fyzické databázové soubory nasytilo v úložišti objektů Blob. To může být užitečné pro velmi velké databáze. Pomocí následujících obecných kroků migrujte uživatelskou databázi pomocí této ruční metody:

1. Odpojte databázové soubory od instance místní databáze.
2. Zkopírujte odpojené databázové soubory do úložiště objektů blob Azure pomocí [nástroje příkazového řádku AZCopy](../../../storage/common/storage-use-azcopy.md).
3. Připojte databázové soubory z adresy URL Azure k instanci SQL Serveru ve virtuálním počítači Azure.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>Převést na virtuální počítač a nahrát na URL a nasadit jako nový virtuální počítač
Pomocí této metody můžete migrovat všechny systémové a uživatelské databáze v místní instanci SERVERU SQL Server do virtuálního počítače Azure. Pomocí následujících obecných kroků migrujete celou instanci serveru SQL Server pomocí této ruční metody:

1. Převod fyzických nebo virtuálních počítačů na virtuální počítače Hyper-V.
2. Nahrajte soubory Virtuálního pevného disku do Služby Azure Storage pomocí [rutiny Add-AzureVHD](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx).
3. Nasazení nového virtuálního počítače pomocí nahraného virtuálního pevného disku.

> [!NOTE]
> Chcete-li migrovat celou aplikaci, zvažte použití [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)].

## <a name="ship-hard-drive"></a>Odeslání pevného disku
Pomocí [metody Windows Import/Export Service](../../../storage/common/storage-import-export-service.md) můžete přenášet velké objemy dat souborů do úložiště objektů Blob Azure v situacích, kdy je nahrávání v síti příliš nákladné nebo neproveditelné. Pomocí této služby odešlete jeden nebo více pevných disků obsahujících tato data do datového centra Azure, kde se vaše data nahrají do vašeho účtu úložiště.

## <a name="next-steps"></a>Další kroky
Další informace o spuštění SQL Serveru ve virtuálních počítačích Azure najdete v tématu [SQL Server ve virtuálních počítačích Azure .](virtual-machines-windows-sql-server-iaas-overview.md)

> [!TIP]
> Pokud máte dotazy k virtuálním počítačům s SQL Serverem, přečtěte si [Nejčastější dotazy](virtual-machines-windows-sql-server-iaas-faq.md).

Pokyny k vytvoření virtuálního počítače Azure SQL Server ze zachycené bitové kopie najdete v [tématu Tipy & triky na "klonování" virtuálních počítačů Azure SQL ze zachycených bitových kopií](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) na blogu CSS SQL Server Engineers.

