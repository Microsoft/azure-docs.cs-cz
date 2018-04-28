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
ms.date: 04/20/2018
ms.author: adigan,markgal
ms.openlocfilehash: 905f6b13928d11243202059af0ad255971102da8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Zálohování souborů a aplikací v Azure zásobníku
Azure Backup můžete použít k ochraně (nebo zálohování) souborům a aplikacím v Azure zásobníku. K zálohování souborů a aplikací, nainstalujte Microsoft Azure Backup Server jako virtuální počítač spuštěný v Azure zásobníku. Po instalaci Azure Backup Server, přidejte disky Azure zvýšit místní úložiště, které jsou k dispozici pro krátkodobé zálohování. Azure Backup Server používá úložiště Azure pro dlouhodobé uchovávání.

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
| Stav systému | Stav systému |
| Úplné obnovení systému (BMR) | BMR, stav systému, soubory a složky | 

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
Každý virtuální počítač Azure zásobníku se dodává s dočasným diskovým úložištěm, která je k dispozici uživateli jako svazek D:`\`. Místní pracovní oblasti vyžadované nástrojem Azure Backup lze nakonfigurovat, aby se nacházejí v D:`\`, a umístění mezipaměti můžete umístit na jednotce C:`\`. Tímto způsobem žádné úložiště musí být carved směrem od datových disků připojených k virtuálnímu počítači serveru Azure Backup.

### <a name="scaling-deployment"></a>Škálování nasazení
Pokud chcete změnit měřítko nasazení, máte následující možnosti:
  - Vertikální navýšení kapacity – zvýšíte velikost virtuálního počítače serveru Azure Backup z řady pro řadu D a místní úložiště [podle pokynů virtuálního počítače Azure zásobníku](../azure-stack/user/azure-stack-manage-vm-disks.md).
  - Datové – odešlete starší data do serveru Azure Backup a zachovat pouze nejnovější data v úložišti připojeném k serveru Azure Backup.
  - Horizontální navýšení kapacity – přidat další servery zálohování Azure k ochraně úloh.


## <a name="bare-metal-recovery-for-azure-stack-vm"></a>Úplné obnovení systému pro virtuální počítač Azure zásobníku

Zálohy úplného obnovení (BMR) chrání soubory operačního systému a všechna data nepostradatelný svazek, kromě dat uživatele. Zálohování BMR zahrnuje zálohu stavu systému. Následující postupy popisují, jak k obnovení dat o úplné obnovení systému. 

### <a name="run-recovery-on-the-azure-backup-server"></a>Spustit obnovení na Azure Backup Server 

Otevřete konzolu serveru Azure Backup.

1. V konzole, klikněte na **obnovení**, najít počítač, který chcete obnovit a klikněte na tlačítko **úplné obnovení systému**.
2. Dostupné body obnovení se zobrazí v kalendáři tučně. Vyberte datum a čas bodu obnovení, který chcete použít.
3. V **vybrat typ obnovení**, vyberte **kopírovat do síťové složky**.
4. V **zadejte cíl**, vyberte, pokud chcete zkopírovat data. Mějte na paměti, že vybraný cíl musí obsahovat dostatek místa pro vytvoření bodu obnovení. Doporučuje se, že vytvoříte novou složku.
5. V **zadat možnosti obnovení**, vyberte nastavení zabezpečení, které chcete použít a vyberte, zda chcete použít snímky hardwaru založené na síti SAN pro rychlejší obnovení.     Snímky hardwaru založené na síti SAN jsou možnost jenom v případě, že máte síť SAN s Tato funkce povolena a možnost vytvářet a rozdělovat je tak aby zapisovatelné. Také pro snímky hardwaru založené na síti SAN pracovat, chráněného počítače a serveru Azure Backup musí být připojen ke stejné síti.
6. Nastavte možnosti oznámení a klikněte na tlačítko **obnovit** na **Souhrn** stránky.

### <a name="set-up-the-share-location"></a>Nastavení umístění sdílené složky
V konzole serveru Azure Backup:
1. V umístění pro obnovení přejděte do složky obsahující zálohování.
2. Sdílejte složku nad souborem WindowsImageBackup tak, aby kořenovém adresáři sdílené složky nacházela složka WindowsImageBackup. Pokud není sdílená složka WindowsImageBackup, operace obnovení zálohu nenalezne. Chcete-li připojit pomocí prostředí WinRE, musíte WinRE dostupné sdílené složky a správnou IP adresu a přihlašovací údaje.

### <a name="restore-the-machine"></a>Tento počítač obnovit

1. Na virtuálním počítači, ve které chcete obnovit BMR otevřete řádek se zvýšenými oprávněními a zadejte následující příkazy. **/bootore** Určuje, že prostředí Windows RE spustí automaticky při příštím spuštění systému.
```
Reagent /boottore
shutdown /r /t 0
```

2. V dialogovém okně otevírání vyberte nastavení jazyka a národního prostředí. Na **nainstalovat** obrazovku, vyberte **opravit tento počítač**.
3. Na **možnosti obnovení systému** vyberte **obnovení počítače pomocí bitové kopie systému jste vytvořili dříve**.
4. Na **výběr zálohy bitové kopie systému** vyberte **Vybrat bitovou kopii systému** > **Upřesnit** > **hledat bitovou kopii systému v síti**. Pokud se zobrazí upozornění, vyberte **Ano**. Vybrat bitovou kopii, přejděte do sdílené síťové složce, zadejte přihlašovací údaje a vyberte bod obnovení. Tato funkce proskenuje pro konkrétní dostupné zálohy v daném bodě obnovení. Vyberte bod obnovení.
5. V **Vvybrat způsob obnovení zálohy**, vyberte **formátovat a znovu rozdělit disky**. Na další obrazovce ověřte nastavení a klikněte na tlačítko **Dokončit** spustíte úlohu obnovení. Restartujte podle potřeby.

## <a name="see-also"></a>Další informace najdete v tématech
Informace o používání serveru Azure Backup k ochraně jiné úlohy najdete v následujících článcích:
- [Zálohování farmy služby SharePoint](backup-azure-backup-sharepoint-mabs.md)
- [Zálohování serveru SQL](backup-azure-sql-mabs.md)
