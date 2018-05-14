---
title: Zálohování Azure zásobníku souborů a aplikací | Microsoft Docs
description: Zálohování a obnovení zásobník Azure souborů a aplikací pro vaše prostředí zásobníku Azure pomocí Azure Backup.
services: backup
documentationcenter: ''
author: adiganmsft
manager: shivamg
editor: ''
keyword: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/11/2018
ms.author: adigan,markgal
ms.openlocfilehash: 99ac43efa5d3211bbe2d790f28532e682058313c
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Zálohování souborů a aplikací v Azure zásobníku
Azure Backup můžete použít k ochraně (nebo zálohování) souborům a aplikacím v Azure zásobníku. K zálohování souborů a aplikací, nainstalujte Microsoft Azure Backup Server jako virtuální počítač spuštěný v Azure zásobníku. Všechny aplikace spuštěné na jakýkoli server zásobník Azure ve stejné virtuální síti můžete chránit. Po instalaci Azure Backup Server, přidejte disky Azure zvýšit místní úložiště, které jsou k dispozici pro krátkodobé zálohování. Azure Backup Server používá úložiště Azure pro dlouhodobé uchovávání.

> [!NOTE]
> I když jsou podobné serveru Azure Backup a System Center Data Protection Manager (DPM), aplikace DPM není podporována pro použití s Azure zásobníku.
>


## <a name="azure-backup-server-protection-matrix"></a>Systém ochrany Azure Backup Serveru
Azure Backup Server chrání následující úlohy virtuálního počítače Azure zásobníku.

| Chráněný zdroj dat. | Ochrana a obnovení |
| --------------------- | ----------------------- |
| Roční kanálu Windows serveru částečně – Datacenter nebo Enterprise nebo Standard | Svazky, soubory a složky |
| Windows Server 2016 - Datacenter nebo Enterprise nebo Standard | Svazky, soubory a složky |
| Windows Server 2012 R2 – Datacenter nebo Enterprise nebo Standard | Svazky, soubory a složky |
| Windows Server 2012 – Datacenter a Entprise nebo standardní | Svazky, soubory a složky |
| Windows Server 2008 R2 – Datacenter nebo Enterprise nebo Standard | Svazky, soubory a složky |
| SQL Server 2016 | Databáze |
| SQL Server 2014 | Databáze |
| SQL Server 2012 SP1 | Databáze |
| SharePoint 2013 | Farma, databáze, front-endu, webový server |
| SharePoint 2010 | Farma, databáze, front-endu, webový server |


## <a name="install-azure-backup-server"></a>Nainstalujte Azure Backup Server
K instalaci serveru Azure Backup na virtuálním počítači Azure zásobníku, najdete v článku [Příprava zálohování úloh pomocí serveru Azure Backup](backup-azure-microsoft-azure-backup.md). Před instalací a konfigurací serveru Azure Backup, mějte na paměti z následujících akcí:

### <a name="determining-size-of-virtual-machine"></a>Určení velikost virtuálního počítače
Azure Backup Server spustit na virtuálním počítači Azure zásobníku, použijte velikosti A2 nebo větší. Pomoc při výběru velikost virtuálního počítače, stáhněte si [kalkulačky velikost virtuálního počítače Azure zásobníku](https://www.microsoft.com/download/details.aspx?id=56832).

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Virtuální sítě na virtuálních počítačích Azure zásobníku
Všechny virtuální počítače, které jsou používány Azure zásobník úlohy musí patřit do stejné virtuální síti Azure a předplatné Azure.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Ukládání zálohovaných dat na místní disk a v Azure
Azure Backup Server ukládá zálohovaná data na disky Azure připojené k virtuálnímu počítači, pro provozní obnovení. Jakmile disky a prostoru úložiště jsou připojené k virtuálnímu počítači, serveru Azure Backup spravuje úložiště za vás. Velikost úložiště zálohování dat závisí na počtu a velikosti disků připojených ke každému [virtuální počítač Azure zásobníku](../azure-stack/user/azure-stack-storage-overview.md). Každý velikost virtuálního počítače Azure zásobník má maximální počet disků, které je možné připojit k virtuálnímu počítači. A2 je například čtyři disky. A3 je osmi disky. A4 je 16 disků. Znovu Určuje velikost a počet disků fondu úložiště celkový zálohování.

> [!IMPORTANT]
> Měli byste **není** zachovat provozní obnovení (zálohování) data na disky připojené k Azure Backup Server pro více než pět dní.
>

Ukládání zálohovaných dat v Azure snižuje infrastruktury zálohování v zásobníku Azure. Pokud jsou data starší než pět dní, by měly být uložené v Azure.

K uložení zálohy dat v Azure, vytvořte nebo použijte trezoru služeb zotavení. Při přípravě k zálohování serveru Azure Backup zatížení, které budete [konfigurace trezor služeb zotavení](backup-azure-microsoft-azure-backup.md#recovery-services-vault). Po nakonfigurování pokaždé, když úloha zálohování se spustí, je bod obnovení vytvořen v trezoru. Každý trezor služeb zotavení obsahuje až 9999 body obnovení. V závislosti na počtu body obnovení vytvořené a jak dlouho jsou uchovávány můžete zachovat zálohovaná data mnoho let. Například může vytvořit měsíční body obnovení a uchovávány pět let.
 
### <a name="using-sql-server"></a>Pomocí SQL serveru
Pokud chcete použít vzdálený SQL Server pro databázi serveru Azure Backup, vyberte pouze zásobník virtuálního počítače Azure systémem SQL Server.

### <a name="azure-backup-server-vm-performance"></a>Azure výkonu zálohování virtuálního počítače serveru
Pokud sdíleny s jinými virtuálními počítači, může ovlivnit výkon virtuálního počítače Azure Backup Server velikost účtu úložiště a omezení IOPS. Z tohoto důvodu by měl použít účet samostatného úložiště pro virtuální počítač serveru Azure Backup. Agent Azure Backup spuštěný na serveru Azure Backup Server potřebuje dočasné úložiště pro:
    - vlastní použití (umístění mezipaměti),
    - data obnovená z cloudu (místní pracovní oblasti)
  
### <a name="configuring-azure-backup-temporary-disk-storage"></a>Konfigurace zálohování Azure dočasným diskovým úložištěm
Každý virtuální počítač Azure zásobníku se dodává s dočasným diskovým úložištěm, která je k dispozici uživateli jako svazek `D:\`. Místní pracovní oblasti vyžadované nástrojem Azure Backup lze nakonfigurovat, aby se nacházejí v `D:\`, a umístění mezipaměti můžete umístit na `C:\`. Tímto způsobem žádné úložiště musí být carved směrem od datových disků připojených k virtuálnímu počítači serveru Azure Backup.

### <a name="scaling-deployment"></a>Škálování nasazení
Pokud chcete změnit měřítko nasazení, máte následující možnosti:
  - Vertikální navýšení kapacity – zvýšíte velikost virtuálního počítače serveru Azure Backup z řady pro řadu D a místní úložiště [podle pokynů virtuálního počítače Azure zásobníku](../azure-stack/user/azure-stack-manage-vm-disks.md).
  - Datové – odešlete starší data do serveru Azure Backup a zachovat pouze nejnovější data v úložišti připojeném k serveru Azure Backup.
  - Horizontální navýšení kapacity – přidat další servery zálohování Azure k ochraně úloh.

## <a name="see-also"></a>Další informace najdete v tématech
Informace o používání serveru Azure Backup k ochraně jiné úlohy najdete v následujících článcích:
- [Zálohování farmy služby SharePoint](backup-azure-backup-sharepoint-mabs.md)
- [Zálohování serveru SQL](backup-azure-sql-mabs.md)
