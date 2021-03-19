---
title: Migrovat disk protokolu na Ultra disk
description: Naučte se migrovat SQL Server na disk s protokolem virtuálního počítače Azure do Azure Ultradisk, abyste mohli využívat vysoký výkon a nízkou latenci.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/09/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: e8410b4e0997798eba5ee91f361c3a5f1ce47ef1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586297"
---
# <a name="migrate-log-disk-to-ultra-disk"></a>Migrovat disk protokolu na Ultra disk
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Disky Azure Ultra poskytují vysokou propustnost, vysoký počet vstupně-výstupních operací za sekundu a trvale nízkou latenci disku pro SQL Server na virtuálním počítači Azure (VM). 

V tomto článku se dozvíte, jak migrovat disk protokolu do Ultra SSD, abyste mohli využít výhody výkonu nabízené Ultra disky. 

## <a name="back-up-database"></a>Zálohování databáze

Dokončete [úplnou zálohu](backup-restore.md) databáze. 

## <a name="attach-disk"></a>Připojit disk

Jakmile na virtuálním počítači povolíte ultradisk kompatibilitu, připojte SSD úrovně Ultra k virtuálnímu počítači. 

Ultra disk se podporuje u podmnožiny velikostí a oblastí virtuálních počítačů. Než budete pokračovat, ověřte, že je váš virtuální počítač v oblasti, zóně a velikosti, která podporuje Ultra disk. [Velikost virtuálního počítače a oblast můžete určit a ověřit](../../../virtual-machines/disks-enable-ultra-ssd.md#determine-vm-size-and-region-availability) pomocí Azure CLI nebo PowerShellu. 

### <a name="enable-compatibility"></a>Povolit kompatibilitu

Pokud chcete povolit kompatibilitu, postupujte podle těchto kroků:

1. Přejít na virtuální počítač v [Azure Portal](https://portal.azure.com/). 
1. Zastavte nebo zrušte přidělení virtuálního počítače. 
1. V části **Nastavení** vyberte **disky** a pak vyberte **Další nastavení**. 

   :::image type="content" source="media/storage-migrate-to-ultradisk/additional-disks-settings-azure-portal.png" alt-text="V části nastavení v Azure Portal vyberte další nastavení pro disky.":::

1. Vyberte **Ano** , pokud chcete **Povolit kompatibilitu s Ultra diskem**. 

   :::image type="content" source="../../../virtual-machines/media/virtual-machines-disks-getting-started-ultra-ssd/enable-ultra-disks-existing-vm.png" alt-text="Snímek obrazovky zobrazující možnost Ano":::

1. Vyberte **Uložit**. 



### <a name="attach-disk"></a>Připojit disk

Pomocí Azure Portal připojte k virtuálnímu počítači Ultra disk. Podrobnosti najdete v tématu [připojení Ultra disk](../../../virtual-machines/disks-enable-ultra-ssd.md#attach-an-ultra-disk).

Po připojení disku spusťte virtuální počítač ještě jednou pomocí Azure Portal. 



## <a name="format-disk"></a>Formátovat disk

Připojte se k virtuálnímu počítači a naformátujte Ultra disk.  

K naformátování Ultra disk použijte následující postup:

1. Připojte se k VIRTUÁLNÍmu počítači pomocí protokol RDP (Remote Desktop Protocol) (RDP).
1. K formátování a vytvoření oddílů nově připojeného Ultra disku použijte [správu disků](/windows-server/storage/disk-management/overview-of-disk-management) . 


## <a name="use-disk-for-log"></a>Použít disk pro protokol

Nakonfigurujte SQL Server pro použití nové jednotky protokolu. Můžete tak učinit pomocí jazyka Transact-SQL (T-SQL) nebo SQL Server Management Studio (SSMS). Účet, který se používá pro účet služby SQL Server, musí mít úplnou kontrolu nad novým umístěním souboru protokolu. 

### <a name="configure-permissions"></a>Konfigurace oprávnění

1. Ověřte účet služby, který používá služba SQL Server. Můžete to provést pomocí SQL Server Configuration Manager nebo Services. msc.
1. Přejděte na nový disk. 
1. Vytvořte složku (nebo více složek), která se má použít pro soubor protokolu. 
1. Klikněte pravým tlačítkem na složku a vyberte **vlastnosti**.
1. Na kartě **zabezpečení** udělte oprávnění Úplné řízení k účtu služby SQL Server. 
1. Kliknutím na **OK**  uložte nastavení. 
1. Tento postup opakujte pro každou složku na úrovni root, kde plánujete mít data SQL. 

### <a name="use-new-log-drive"></a>Použít novou jednotku protokolu 

Po udělení oprávnění použijte příkaz Transact-SQL (T-SQL) nebo SQL Server Management Studio (SSMS) k odpojení databáze a přesunutí stávajících souborů protokolu do nového umístění.

   > [!CAUTION]
   > Odpojením databáze přejdete do režimu offline, ukončíte připojení a vrátíte zpátky všechny transakce, které jsou v provozu. Pokračujte s opatrností a během časového intervalu pro správu a údržbu. 



# <a name="transact-sql-t-sql"></a>[Transact-SQL (T-SQL)](#tab/tsql)

Pomocí T-SQL přesuňte stávající soubory do nového umístění:

1. Připojte se k databázi v SQL Server Management Studio a otevřete **nové okno dotazu** . 
1. Získat existující soubory a umístění:

   ```sql
   USE AdventureWorks
   GO

   sp_helpfile
   GO
   ```

1. Odpojit databázi: 

   ```sql
   USE master
   GO

   sp_detach_db 'AdventureWorks'
   GO
   ```

1. Pomocí Průzkumníka souborů přesuňte soubor protokolu do nového umístění na disku Ultra. 

1. Připojte databázi a určete nová umístění souborů: 

   ```sql
    sp_attach_db 'AdventureWorks'
   'E:\Fixed_FG\AdventureWorks.mdf',
   'E:\Fixed_FG\AdventureWorks_2.ndf',
   'F:\New_Log\AdventureWorks_log.ldf'
   GO
   ```

V tomto okamžiku se databáze nachází v online režimu s protokolem v novém umístění. 



# <a name="sql-server-management-studio-ssms"></a>[SQL Server Management Studio (SSMS)](#tab/ssms)

Pomocí SSMS přesuňte stávající soubory do nového umístění:

1. Připojte se k databázi v SQL Server Management Studio (SSMS). 
1. Klikněte pravým tlačítkem na databázi, vyberte **vlastnosti** a pak vyberte **soubory**. 
1. Poznamenejte si cestu k existujícím souborům. 
1. Kliknutím na **tlačítko OK** zavřete dialogové okno. 
1. Klikněte pravým tlačítkem na databázi a vyberte **úlohy**  >  **Odpojit**. 
1. Postupujte podle pokynů průvodce a odpojte databázi. 
1. Použijte Průzkumníka souborů k ručnímu přesunutí souboru protokolu do nového umístění.
1. Připojit databázi v SQL Server Management Studio
   1. V **Průzkumník objektů** klikněte pravým tlačítkem na **databáze** a vyberte **připojit databázi**. 
   1. Pomocí dialogového okna přidejte jednotlivé soubory včetně souboru protokolu do nového umístění. 
   1. Vyberte **OK** k připojení databáze. 

V tomto okamžiku se databáze nachází v online režimu s protokolem v novém umístění.

---


## <a name="next-steps"></a>Další kroky

Další nastavení pro zlepšení výkonu najdete v článku [osvědčené postupy pro výkon](performance-guidelines-best-practices.md) . 

Přehled SQL Server v Azure Virtual Machines najdete v následujících článcích:

- [Přehled SQL Server na virtuálních počítačích s Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Přehled SQL Server on Linux virtuálních počítačů](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
