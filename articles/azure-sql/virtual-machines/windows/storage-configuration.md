---
title: Konfigurace úložiště pro virtuální počítače s SQL Server | Microsoft Docs
description: Toto téma popisuje, jak Azure nakonfiguruje úložiště pro SQL Server virtuálních počítačů během zřizování (Azure Resource Manager modelu nasazení). Vysvětluje taky, jak můžete nakonfigurovat úložiště pro stávající virtuální počítače s SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/26/2019
ms.author: mathoma
ms.openlocfilehash: 982bd9239c5e95c9b7af09b5f54c5a09067ca7c6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565406"
---
# <a name="configure-storage-for-sql-server-vms"></a>Konfigurace úložiště pro virtuální počítače s SQL Server
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

V tomto článku se dozvíte, jak nakonfigurovat úložiště pro SQL Server na Azure Virtual Machines (virtuálních počítačích).

SQL Server virtuální počítače nasazené prostřednictvím imagí z Marketplace automaticky dodržují výchozí [Doporučené postupy úložiště](performance-guidelines-best-practices-storage.md) , které je možné upravit během nasazení. Některá z těchto nastavení konfigurace můžete po nasazení změnit. 


## <a name="prerequisites"></a>Požadavky

Pokud chcete použít nastavení konfigurace automatizovaného úložiště, váš virtuální počítač musí mít následující vlastnosti:

* Je zřízené s [imagí galerie SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo) nebo se zaregistroval s [rozšířením SQL IaaS]().
* Používá [model nasazení Správce prostředků](../../../azure-resource-manager/management/deployment-models.md).
* Používá [prémiové SSD](../../../virtual-machines/disks-types.md).

## <a name="new-vms"></a>Nové virtuální počítače

Následující části popisují, jak nakonfigurovat úložiště pro nové virtuální počítače SQL Server.

### <a name="azure-portal"></a>portál Azure

Při zřizování virtuálního počítače Azure pomocí Image Galerie SQL Server vyberte **změnit konfiguraci** na kartě **nastavení SQL Server** a otevřete stránku konfigurace optimalizovaného úložiště pro výkon. Můžete buď ponechat hodnoty ve výchozím nastavení, nebo upravit typ konfigurace disku, který nejlépe vyhovuje vašim potřebám na základě vašich úloh. 

![Snímek obrazovky, který zvýrazní kartu nastavení SQL Server a možnost změny konfigurace.](./media/storage-configuration/sql-vm-storage-configuration-provisioning.png)

V části **optimalizace úložiště** vyberte typ úlohy, kterou nasazujete SQL Server. Když použijete možnost **Obecná** optimalizace, budete mít ve výchozím nastavení jeden datový disk s 5000 maximálním IOPS a tuto jednotku budete používat pro vaše data, protokol transakcí a úložiště tempdb. 

Když vyberete možnost **transakční zpracování** (OLTP) nebo **datové sklady** , vytvoří se samostatný disk pro data, samostatný disk pro transakční protokol a použije se místní SSD pro databázi tempdb. Neexistují žádné rozdíly v úložištích mezi **transakčním zpracováním** a **datovým skladem**, ale změní [konfiguraci Stripe a příznaky trasování](#workload-optimization-settings). Když zvolíte Storage úrovně Premium, nastavíte ukládání do mezipaměti pro *čtení* datové jednotky do mezipaměti a *žádné* pro tuto jednotku protokolu neplatí jako [osvědčené postupy pro výkon virtuálního počítače SQL Server](performance-guidelines-best-practices.md). 

![SQL Server konfigurace úložiště virtuálních počítačů během zřizování](./media/storage-configuration/sql-vm-storage-configuration.png)

Konfigurace disku je zcela přizpůsobitelná, takže můžete nakonfigurovat topologii úložiště, typ disku a IOPs, které budete potřebovat pro úlohu SQL Server virtuálních počítačů. Máte také možnost používat UltraSSD (Preview) jako možnost pro **typ disku** , pokud je váš SQL Server virtuální počítač v některé z podporovaných oblastí (východní USA 2, jihovýchodní asie a Severní Evropa) a máte povolené [Ultra disks pro vaše předplatné](../../../virtual-machines/disks-enable-ultra-ssd.md).  

Kromě toho máte možnost nastavit ukládání do mezipaměti pro disky. Virtuální počítače Azure mají vícevrstvou technologii ukládání do mezipaměti s názvem [BLOB cache](../../../virtual-machines/premium-storage-performance.md#disk-caching) , pokud se používá na [prémiových discích](../../../virtual-machines/disks-types.md#premium-ssd). Mezipaměť objektů BLOB používá kombinaci paměti RAM virtuálního počítače a místní jednotky SSD pro ukládání do mezipaměti. 

Ukládání do *mezipaměti na disku* pro SSD úrovně Premium může být *jen pro čtení, pro čtení* nebo *žádné*. 

- Ukládání do mezipaměti *jen pro čtení* je velmi užitečné pro SQL Server datových souborů uložených v Premium Storage. Ukládání *jen pro* čtení přináší nízkou latenci čtení, vysoký počet vstupně-výstupních operací za sekundu a propustnost, jako je čtení prováděné z mezipaměti, která je v paměti virtuálního počítače a místní SSD. Tyto čtení jsou mnohem rychlejší než čtení z datového disku, který se nachází v úložišti objektů BLOB v Azure. Storage úrovně Premium nepočítá s tím, že se čtení poskytované z mezipaměti týká vstupně-výstupních operací disku a propustnosti. Díky tomu je možné dosáhnout vyššího celkového počtu vstupně-výstupních operací za sekundu a propustnosti. 
- Pro disky hostující SQL Server log by se měla použít konfigurace *žádné* mezipaměti, protože soubor protokolu se zapisuje postupně a nemá výhodu pro ukládání do mezipaměti *jen pro čtení* . 
- *Mezipaměť pro* čtení z mezipaměti by se neměla používat k hostování SQL serverch souborů, protože SQL Server nepodporuje konzistenci dat s *mezipamětí pro čtení* . V případě, že zápisy přecházejí do vrstev mezipaměti objektů BLOB *jen pro čtení* , zapisuje se kapacita odpadu mezipaměti objektů BLOB *jen pro čtení* . 


   > [!TIP]
   > Ujistěte se, že vaše konfigurace úložiště odpovídá omezením podle zvolené velikosti virtuálního počítače. Pokud zvolíte parametry úložiště, které překračují maximální výkon velikosti virtuálního počítače, bude to mít za následek upozornění: `The desired performance might not be reached due to the maximum virtual machine disk performance cap` . Buď snižte počet vstupně-výstupních operací změnou typu disku, nebo zvyšte omezení výkonu zakončení zvýšením velikosti virtuálního počítače. Tím se nezastaví zřizování. 


Na základě vašich voleb provede Azure po vytvoření virtuálního počítače tyto úlohy konfigurace úložiště:

* Vytvoří a připojí SSD úrovně Premium k virtuálnímu počítači.
* Nakonfiguruje datové disky k dispozici pro SQL Server.
* Nakonfiguruje datové disky do fondu úložiště na základě zadaného počtu požadavků na velikost a výkon (IOPS a propustnost).
* Přidružuje fond úložiště k nové jednotce na virtuálním počítači.
* Optimalizuje tuto novou jednotku na základě zadaného typu úlohy (datové sklady, zpracování transakcí nebo obecné).

Úplný návod, jak vytvořit SQL Server virtuální počítač v Azure Portal, najdete v [kurzu zřizování](../../../azure-sql/virtual-machines/windows/create-sql-vm-portal.md).

### <a name="resource-manager-templates"></a>Šablony Resource Manageru

Pokud použijete následující šablony Správce prostředků, standardně se připojí dva datové disky Premium bez konfigurace fondu úložiště. Tyto šablony však můžete přizpůsobit a změnit počet disků prémiových dat, které jsou připojeny k virtuálnímu počítači.

* [Vytvoření virtuálního počítače pomocí automatizovaného zálohování](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Vytvoření virtuálního počítače pomocí automatizovaných oprav](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Vytvoření virtuálního počítače s integrací integrace](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

### <a name="quickstart-template"></a>Šablona pro rychlý start

K nasazení virtuálního počítače s SQL Server pomocí optimalizace úložiště můžete použít následující šablonu pro rychlý Start. 

* [Vytvoření virtuálního počítače s optimalizací úložiště](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage/)
* [Vytvoření virtuálního počítače pomocí UltraSSD](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>Existující virtuální počítače

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

U stávajících SQL Server virtuálních počítačů můžete upravit některá nastavení úložiště v Azure Portal. Otevřete [prostředek virtuálních počítačů SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource)a vyberte **Přehled**. Na stránce Přehled SQL Server se zobrazuje aktuální využití úložiště virtuálního počítače. V tomto grafu se zobrazí všechny jednotky, které se nacházejí na vašem VIRTUÁLNÍm počítači. Pro každou jednotku se prostor úložiště zobrazuje ve čtyřech částech:

* Data SQL
* Protokol SQL
* Jiné (jiné úložiště než SQL)
* K dispozici

Pokud chcete upravit nastavení úložiště, vyberte **Konfigurovat** v části **Nastavení**. 

![Snímek obrazovky, který zvýrazní možnost konfigurace a část využití úložiště.](./media/storage-configuration/sql-vm-storage-configuration-existing.png)

Můžete upravit nastavení disku pro jednotky, které byly nakonfigurované během procesu vytváření virtuálního počítače SQL Server. Když vyberete možnost **Zvětšit jednotku** , otevře se stránka pro úpravy jednotky, která umožňuje změnit typ disku a přidat další disky. 

![Konfigurace úložiště pro existující virtuální počítač SQL Server](./media/storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="automated-changes"></a>Automatizované změny

V této části najdete referenční informace o změnách konfigurace úložiště, které Azure automaticky provede během SQL Server zřizování a konfigurace virtuálních počítačů v Azure Portal.

* Azure nakonfiguruje fond úložiště z úložiště vybraného z virtuálního počítače. V další části tohoto tématu najdete podrobné informace o konfiguraci fondu úložiště.
* Automatická konfigurace úložiště vždycky používá datové disky [Premium SSD](../../../virtual-machines/disks-types.md) P30. V důsledku toho je k dispozici mapování 1:1 mezi vybraným počtem terabajtů a počtem datových disků připojených k VIRTUÁLNÍmu počítači.

Informace o cenách najdete na stránce s [cenami za úložiště](https://azure.microsoft.com/pricing/details/storage) na kartě **Disk Storage** .

### <a name="creation-of-the-storage-pool"></a>Vytvoření fondu úložiště

Azure na SQL Server virtuálních počítačích vytvoří fond úložiště pomocí následujících nastavení.

| Nastavení | Hodnota |
| --- | --- |
| Velikost pruhu |256 KB (datové sklady); 64 KB (transakční) |
| Velikosti disků |1 TB |
| Mezipaměť |Číst |
| Velikost přidělení |velikost alokační jednotky systému souborů NTFS v 64 KB |
| Obnovovací | Jednoduché obnovení (bez odolnosti) |
| Počet sloupců |Počet datových disků až do 8<sup>1</sup> |


<sup>1</sup> po vytvoření fondu úložiště nemůžete změnit počet sloupců ve fondu úložiště.


### <a name="workload-optimization-settings"></a>Nastavení optimalizace úloh

Následující tabulka popisuje tři dostupné možnosti typu úlohy a jejich odpovídající optimalizace:

| Typ úlohy | Description | Optimalizace |
| --- | --- | --- |
| **Obecné** |Výchozí nastavení, které podporuje většinu úloh |Žádné |
| **Zpracování transakcí** |Optimalizuje úložiště pro tradiční databázové OLTP úlohy. |Příznak trasování 1117<br/>Příznak trasování 1118 |
| **Datové sklady** |Optimalizuje úložiště pro analytické a generování sestav úloh. |Příznak trasování 610<br/>Příznak trasování 1117 |

> [!NOTE]
> Typ úlohy můžete zadat, jenom když zřídíte virtuální počítač SQL Server tím, že ho vyberete v kroku konfigurace úložiště.

## <a name="enable-caching"></a>Enable caching 

Změňte zásady ukládání do mezipaměti na úrovni disku. Můžete to udělat pomocí Azure Portal, [PowerShellu](/powershell/module/az.compute/set-azvmdatadisk)nebo rozhraní příkazového [řádku Azure CLI](/cli/azure/vm/disk). 

Pokud chcete změnit zásady ukládání do mezipaměti v Azure Portal, postupujte takto:

1. Zastavte službu SQL Server. 
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 
1. Přejděte na virtuální počítač, v části **Nastavení** vyberte **disky** . 
   
   ![Snímek obrazovky zobrazující okno Konfigurace disku virtuálního počítače v Azure Portal.](./media/storage-configuration/disk-in-portal.png)

1. Z rozevíracího seznamu vyberte vhodné zásady pro ukládání do mezipaměti pro váš disk. 

   ![Snímek obrazovky zobrazující konfiguraci zásad ukládání do mezipaměti disku v Azure Portal.](./media/storage-configuration/azure-disk-config.png)

1. Jakmile se změna projeví, restartujte virtuální počítač SQL Server a spusťte službu SQL Server. 


## <a name="enable-write-accelerator"></a>Povolit Akcelerátor zápisu

Akcelerace zápisu je funkce disku, která je dostupná jenom pro Virtual Machines řady M-Series. Účelem akcelerace zápisu je vylepšit latenci vstupu a výstupu zápisu na Azure Premium Storage, pokud potřebujete s vysokým počtem důležitých OLTP úloh nebo prostředí datového skladu. 

Před provedením změn v zásadách akcelerace zápisu zastavte všechny aktivity SQL Server a vypněte službu SQL Server. 

Pokud jsou disky prokládané, povolte pro každý disk akceleraci zápisu a virtuální počítač Azure by měl být před provedením změn vypnut. 

Pokud chcete povolit akceleraci zápisu pomocí Azure Portal, postupujte podle následujících kroků:

1. Zastavte službu SQL Server. Pokud jsou disky prokládané, vypněte virtuální počítač. 
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 
1. Přejděte na virtuální počítač, v části **Nastavení** vyberte **disky** . 
   
   ![Snímek obrazovky zobrazující okno Konfigurace disku virtuálního počítače v Azure Portal.](./media/storage-configuration/disk-in-portal.png)

1. Z rozevíracího seznamu vyberte možnost mezipaměť s **akcelerátor zápisu** pro disk. 

   ![Snímek obrazovky s informacemi o zásadách mezipaměti pro akcelerátory zápisu](./media/storage-configuration/write-accelerator.png)

1. Po uplatnění změny spusťte virtuální počítač a SQL Server službu. 

## <a name="disk-striping"></a>Diskové svazky

Pro zvýšení propustnosti můžete přidat další datové disky a používat diskové svazky. Pokud chcete zjistit počet datových disků, analyzujte propustnost a šířku pásma požadovanou pro vaše SQL Server datové soubory, včetně protokolů a databází tempdb. Omezení propustnosti a šířky pásma se liší podle velikosti virtuálního počítače. Další informace najdete v tématu o [velikosti virtuálních počítačů](../../../virtual-machines/sizes.md) .


* U systémů Windows 8/Windows Server 2012 a novějších používejte [prostory úložiště](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11)) s následujícími pokyny:

  1. Nastavte prokládaný (velikost Stripe) na 64 KB (65 536 bajtů), aby se zabránilo dopadu na chybné zarovnání oddílu. Tato nastavení se musí nastavit pomocí PowerShellu.

  2. Nastavte počet sloupců = počet fyzických disků. Použijte PowerShell při konfiguraci více než 8 disků (ne Správce serveru uživatelského rozhraní).

Například následující PowerShell vytvoří nový fond úložiště s velikostí prokládání na 64 KB a počet sloupců rovný velikosti fyzického disku ve fondu úložiště:

  ```powershell
  $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
  
  New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" `
      -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
      -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
      –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
      -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
      -AllocationUnitSize 65536 -Confirm:$false 
  ```

  * Pro Windows 2008 R2 nebo starší můžete použít dynamické disky (svazky Stripe OS) a velikost Stripe je vždycky 64 KB. Tato možnost je zastaralá od Windows 8/Windows Serveru 2012. Informace najdete v tématu věnovaném podpoře na [virtuální diskové službě přechod na rozhraní API pro správu úložiště systému Windows](https://docs.microsoft.com/windows/win32/w8cookbook/vds-is-transitioning-to-wmiv2-based-windows-storage-management-api).
 
  * Pokud pro [SQL Server instancí clusteru s podporou převzetí služeb při selhání](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure)používáte [prostory úložiště s přímým přístupem (S2D)](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) , musíte nakonfigurovat jeden fond. I když se na tomto jediném fondu dají vytvořit různé svazky, budou všechny sdílet stejné charakteristiky, například stejné zásady pro ukládání do mezipaměti.
 
  * Určete počet disků přidružených k vašemu fondu úložiště na základě očekávání zatížení. Mějte na paměti, že různé velikosti virtuálních počítačů umožňují různé počty připojených datových disků. Další informace najdete v tématu [velikosti pro virtuální počítače](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json).


## <a name="next-steps"></a>Další kroky

Další témata související se spouštěním SQL Server ve virtuálních počítačích Azure najdete v tématu [SQL Server v Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md).
