---
title: Konfigurace úložiště pro virtuální počítače SQL Server | Dokumenty společnosti Microsoft
description: Toto téma popisuje, jak Azure konfiguruje úložiště pro virtuální počítače SQL Server během zřizování (model nasazení Správce prostředků). Také vysvětluje, jak můžete nakonfigurovat úložiště pro stávající virtuální počítače SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/26/2019
ms.author: mathoma
ms.openlocfilehash: 93f01b3c23e08e7f432841d8a77cbe3602bff1c5
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81482138"
---
# <a name="storage-configuration-for-sql-server-vms"></a>Konfigurace úložiště pro virtuální počítače SQL Serveru

Když nakonfigurujete image virtuálního počítače SQL Server v Azure, portál pomáhá automatizovat konfiguraci úložiště. To zahrnuje připojení úložiště k virtuálnímu počítači, zpřístupnění tohoto úložiště serveru SQL Server a jeho konfigurace pro optimalizaci pro vaše konkrétní požadavky na výkon.

Toto téma vysvětluje, jak Azure konfiguruje úložiště pro vaše virtuální počítače SQL Server během zřizování i pro stávající virtuální počítače. Tato konfigurace je založená na [doporučených postupech výkonu](virtual-machines-windows-sql-performance.md) pro virtuální počítače Azure se systémem SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Požadavky

Chcete-li použít nastavení konfigurace automatického úložiště, váš virtuální počítač vyžaduje následující charakteristiky:

* Zřízena s [SQL Server galerie image](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).
* Používá [model nasazení Správce prostředků](../../../azure-resource-manager/management/deployment-models.md).
* Používá [prémiové SSD](../disks-types.md).

## <a name="new-vms"></a>Nové virtuální společnosti

Následující části popisují, jak nakonfigurovat úložiště pro nové virtuální počítače SQL Server.

### <a name="azure-portal"></a>portál Azure

Při zřizování virtuálního počítače Azure pomocí bitové kopie galerie serveru SQL Server vyberte **změnit konfiguraci** na kartě **Nastavení serveru SQL Server** a otevřete stránku Konfigurace úložiště optimalizované ho za výkon. Můžete buď ponechat hodnoty ve výchozím nastavení, nebo upravit typ konfigurace disku, který nejlépe vyhovuje vašim potřebám na základě vašeho pracovního vytížení. 

![Konfigurace úložiště virtuálních počítačů serveru SQL Server během zřizování](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

V části **Optimalizace úložiště**vyberte typ úlohy, pro kterou nasazujete SQL Server . S **možností Obecné** optimalizace, ve výchozím nastavení budete mít jeden datový disk s 5000 max IOPS a budete používat stejný disk pro vaše data, transakční protokol a úložiště TempDB. Výběrem **transakčního zpracování** (OLTP) nebo **datového skladu** vytvoříte samostatný disk pro data, samostatný disk pro transakční protokol a použijete místní disk SSD pro databázi TempDB. Neexistují žádné rozdíly v úložišti mezi **transakční zpracování** a **ukládání dat**, ale změnit [konfiguraci prokládaných a trasovací příznaky](#workload-optimization-settings). Výběr úložiště premium nastaví ukládání do mezipaměti na *ReadOnly* pro datovou jednotku a *Žádný* pro jednotku protokolu podle [osvědčených postupů výkonu virtuálního zařízení SQL Server](virtual-machines-windows-sql-performance.md). 

![Konfigurace úložiště virtuálních počítačů serveru SQL Server během zřizování](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration.png)

Konfigurace disku je zcela přizpůsobitelná, takže můžete nakonfigurovat topologii úložiště, typ disku a iOP, které potřebujete pro úlohy virtuálního počítače SQL Server. Máte také možnost použít UltraSSD (náhled) jako možnost pro **typ disku,** pokud váš sql server virtuální počítač je v jedné z podporovaných oblastí (východní US 2, jihovýchodní Asie a severní Evropa) a jste povolili [ultra disky pro vaše předplatné](/azure/virtual-machines/windows/disks-enable-ultra-ssd).  

Kromě toho máte možnost nastavit ukládání do mezipaměti pro disky. Virtuální počítače Azure mají vícevrstvou technologii ukládání do mezipaměti s názvem [Blob Cache](/azure/virtual-machines/windows/premium-storage-performance#disk-caching) při použití s [disky Premium](/azure/virtual-machines/windows/disks-types#premium-ssd). Mezipaměť objektů blob používá kombinaci paměti VIRTUAL Machine RAM a místního ssd serveru pro ukládání do mezipaměti. 

Ukládání disků do mezipaměti pro premium SSD může být *pouze pro čtení*, *ReadWrite* nebo *Žádný*. 

- Ukládání do mezipaměti *pouze pro čtení* je velmi výhodné pro datové soubory serveru SQL Server, které jsou uloženy v úložišti Premium. Ukládání do mezipaměti *pouze pro čtení* přináší nízkou latenci čtení, vysoké čtení VOPS a propustnost jako čtení se provádí z mezipaměti, která je v paměti virtuálního zařízení a místní SSD. Tato čtení jsou mnohem rychlejší než čtení z datového disku, který je z úložiště objektů blob Azure. Úložiště Premium nepočítá čtení podávané z mezipaměti do vstupně-diskových vstupně-sad a propustnost. Proto vaše použitelné je schopen dosáhnout vyšší celkové vipony a propustnost. 
- *Žádná* konfigurace mezipaměti by měla být použita pro disky hostující soubor protokolu serveru SQL Server, protože soubor protokolu je zapsán postupně a nemá prospěch z ukládání do mezipaměti *Pouze pro čtení.* 
- Ukládání do mezipaměti *readwrite* by nemělo být používáno k hostování souborů serveru SQL Server, protože SQL Server nepodporuje konzistenci dat s mezipamětí *ReadWrite.* Zapíše nehospodárné kapacity mezipaměti objektů *blob ReadOnly* a latence mírně zvýšit, pokud zápisy projít *jen pro čtení* vrstvy mezipaměti objektů blob. 


   > [!TIP]
   > Ujistěte se, že konfigurace úložiště odpovídá omezením vynuceným vybranou velikostí virtuálního počítače. Volba parametrů úložiště, které překračují omezení výkonu velikosti virtuálního počítače, bude mít za následek chybu: `The desired performance might not be reached due to the maximum virtual machine disk performance cap.`. Buď snížit iops změnou typu disku nebo zvýšit omezení omezení omezení výkonu zvýšením velikosti virtuálního počítače. 


Na základě vašich možností Azure po vytvoření virtuálního počítače provede následující úlohy konfigurace úložiště:

* Vytvoří a připojí prémiové ssd disy k virtuálnímu počítači.
* Nakonfiguruje datové disky tak, aby byly přístupné serveru SQL Server.
* Konfiguruje datové disky do fondu úložiště na základě zadaných požadavků na velikost a výkon (IOPS a propustnost).
* Přidruží fond úložiště k nové jednotce ve virtuálním počítači.
* Optimalizuje tuto novou jednotku na základě zadaného typu pracovního vytížení (datové sklady, transakční zpracování nebo obecné).

Další podrobnosti o konfiguraci nastavení úložiště Azure najdete v [části Konfigurace úložiště](#storage-configuration). Úplný návod, jak vytvořit virtuální počítač SQL Server na webu Azure Portal, najdete v [kurzu zřizování](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Spravovat šablony zdrojů

Pokud použijete následující šablony Správce prostředků, jsou ve výchozím nastavení připojeny dva datové disky premium bez konfigurace fondu úložiště. Tyto šablony však můžete přizpůsobit a změnit počet prémiových datových disků, které jsou připojeny k virtuálnímu počítači.

* [Vytvoření virtuálního počítače pomocí automatického zálohování](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Vytvoření virtuálního virtuálního montovana s automatickým opravováním](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Vytvoření virtuálního virtuálního mísa s integrací AKV](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

### <a name="quickstart-template"></a>Šablona pro rychlý start

Pomocí následující šablony rychlého startu můžete nasadit virtuální ho disponující virtuálním počítačem SQL Serveru pomocí optimalizace úložiště. 

* [Vytvoření virtuálního virtuálního zařízení s optimalizací úložiště](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage/)
* [Vytvoření virtuálního počítače pomocí UltraSSD](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>Existující virtuální chod

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

U stávajících virtuálních počítačů SQL Server můžete upravit některá nastavení úložiště na webu Azure Portal. Otevřete [prostředek virtuálních počítačů SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)a vyberte **Přehled**. Stránka Přehled serveru SQL Server zobrazuje aktuální využití úložiště virtuálního počítače. V tomto grafu se zobrazí všechny jednotky, které existují ve vašem virtuálním počítači. Pro každou jednotku se úložný prostor zobrazuje ve čtyřech částech:

* Data SQL
* Protokol SQL
* Jiné (úložiště mimo SQL)
* K dispozici.

Chcete-li změnit nastavení úložiště, vyberte **Konfigurovat v** části **Nastavení**. 

![Konfigurace úložiště pro existující virtuální modul SQL Server](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

Můžete upravit nastavení disku pro jednotky, které byly nakonfigurovány během procesu vytváření virtuálních počítačů serveru SQL Server. Výběrem **možnosti Prodloužit jednotku** se otevře stránka změny jednotky, která umožňuje změnit typ disku a přidat další disky. 

![Konfigurace úložiště pro existující virtuální modul SQL Server](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="storage-configuration"></a>Konfigurace úložiště

Tato část obsahuje odkaz na změny konfigurace úložiště, které Azure automaticky provádí během zřizování nebo konfigurace virtuálních počítačů SQL na webu Azure Portal.

* Azure konfiguruje fond úložiště z úložiště vybraného z vašeho virtuálního počítače. Další část tohoto tématu obsahuje podrobnosti o konfiguraci fondu úložiště.
* Automatická konfigurace úložiště vždy používá prémiové datové [disky SSD](../disks-types.md) P30. V důsledku toho je mapování 1:1 mezi vybraný počet terabajtů a počet datových disků připojených k virtuálnímu počítači.

Informace o cenách najdete na stránce [Ceny úložiště](https://azure.microsoft.com/pricing/details/storage) na kartě **Diskové úložiště.**

### <a name="creation-of-the-storage-pool"></a>Vytvoření fondu úložiště

Azure používá následující nastavení k vytvoření fondu úložiště na virtuálních počítačích SQL Server.

| Nastavení | Hodnota |
| --- | --- |
| Velikost pruhu |256 KB (datové sklady); 64 KB (Transakční) |
| Velikosti disků |1 TB každý |
| Mezipaměť |Čtení |
| Velikost přidělení |64 Velikost alokační jednotky NTFS KB |
| Obnovení | Jednoduché obnovení (bez odolnosti proti chybám) |
| Počet sloupců |Počet datových disků až 8<sup>1</sup> |


<sup>1</sup> Po vytvoření fondu úložiště nelze změnit počet sloupců ve fondu úložiště.


## <a name="workload-optimization-settings"></a>Nastavení optimalizace pracovního vytížení

Následující tabulka popisuje tři dostupné možnosti typu pracovního vytížení a jejich odpovídající optimalizace:

| Typ pracovního vytížení | Popis | Optimalizace |
| --- | --- | --- |
| **Obecné** |Výchozí nastavení, které podporuje většinu úloh |Žádná |
| **Transakční zpracování** |Optimalizuje úložiště pro tradiční databázové úlohy OLTP |Trasovací vlajka 1117<br/>Trasovací vlajka 1118 |
| **Datové sklady** |Optimalizuje úložiště pro analytické úlohy a úlohy vytváření sestav |Trasová vlajka 610<br/>Trasovací vlajka 1117 |

> [!NOTE]
> Typ pracovního vytížení můžete zadat jenom při zřizování virtuálního počítače SQL výběrem v kroku konfigurace úložiště.

## <a name="next-steps"></a>Další kroky

Další témata související se spuštěním SQL Serveru ve virtuálních počítačích Azure najdete v článku [SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-server-iaas-overview.md).
