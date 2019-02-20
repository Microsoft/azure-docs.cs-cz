---
title: Matice podpory pro Microsoft Azure Backup server a System Center DPM
description: Tento článek shrnuje podporu Azure Backup při použití Microsoft Azure Backup Server nebo System Center DPM k zálohování v místním a prostředky virtuálních počítačů Azure.
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 74b6d953939a569a3240c9d64134d143dc4f179e
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430847"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-serversystem-center-dpm"></a>Matice podpory pro zálohování pomocí Microsoft Azure Backup Server nebo System Center DPM

Můžete použít [služby Azure Backup](backup-overview.md) pro zálohování místních počítačů a úlohy a virtuální počítače Azure. Tento článek shrnuje podporu nastavení a omezení pro zálohování počítačů pomocí Microsoft Azure Backup Server (MABS) nebo System Center Data Protection Manager (DPM) a Azure Backup.

## <a name="about-mabsdpm"></a>O MABS/DPM

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807) je podnikové řešení, která lze konfigurovat, usnadňuje a spravuje zálohování a obnovení počítačů enterprise a data. To je součástí [System Center](https://www.microsoft.com/cloud-platform/system-center-pricing) sadu produktů.


Microsoft Azure Backup Server (MABS) je server produktu, který je možné zálohovat na místní fyzické servery, virtuální počítače (VM) a aplikace běžící na a virtuální počítače Azure.

- MABS je založená na System Center Data Protection Manager (DPM) a poskytuje podobné funkce s několika rozdíly:
    - Žádné licence pro System Center je potřeba ke spouštění MABS.
    - Pro MABS a DPM Azure poskytují dlouhodobé úložiště pro zálohování. Kromě toho aplikace DPM můžete zálohovat data pro dlouhodobé ukládání na pásku. MABS neposkytuje tuto funkci.
- Stažení serveru MABS z [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=57520). Můžete spustit místně, nebo na Virtuálním počítači Azure v Azure.

Aplikace DPM a MABS podporu zálohování širokou škálu aplikací a serverem a klientskými operačními systémy. Poskytují více scénářů zálohování:
    - Můžete zálohovat na úrovni počítače pomocí zálohování stavu systému nebo úplné obnovení systému.
    - Můžete zálohovat určitých svazků, sdílených složek, složek a souborů.
    - Můžete zálohovat pomocí optimalizovaná s ohledem na aplikace nastavení konkrétní aplikace.

## <a name="mabsdpm-backup"></a>MABS/zálohování

Zálohování používající MABS/DPM a Azure Backup pracuje následujícím způsobem:

1. Agent ochrany aplikace DPM nebo MABS nainstalováni na každém počítači, které se bude zálohovat. 
2. Počítače a aplikace jsou zálohovány do místního úložiště na aplikaci DPM nebo MABS.
3. Agent Microsoft Azure Recovery Services (MARS) je nainstalována na serveru aplikace DPM nebo MABS.
4. Agenta MARS zálohuje aplikace DPM nebo MABS disky zálohování trezoru služby Recovery Services v Azure pomocí Azure Backup.

Další informace najdete tady:
- [Další informace](backup-architecture.md#architecture-back-up-to-dpmmabs) o architektuře MABS.
- [Kontrola](backup-support-matrix-mars-agent.md) co je podporováno pro agenta MARS.

## <a name="supported-scenarios"></a>Podporované scénáře 

**Scénář** | **Agent** | **Umístění**
--- | --- | ---
**Zálohování místních počítačů a úloh** | Agent ochrany aplikace DPM nebo MABS běží na počítačích, které chcete zálohovat<br/><br/> Agenta MARS na serveru MABS/DPM | MABS/DPM musí být spuštěné místně
**Zálohování virtuálních počítačů a úloh v Azure** | Agent ochrany aplikace DPM nebo MABS na chráněný počítač<br/><br/> Agenta MARS na MABS/DPM | Aplikace DPM nebo MABS musí být spuštěná na Virtuálním počítači Azure.

## <a name="supported-deployments"></a>Podporované nasazení

Aplikace DPM nebo MABS se dá nasadit jako uvedené v tabulce.

**Nasazení** | **Podpora** | **Podrobnosti**
--- | --- | ---
**Místně nasadit** | Fyzický server<br/><br/>Virtuální počítač Hyper-V<br/><br/> Virtuální počítač VMware | Pokud jako virtuální počítač VMware pouze zálohuje virtuální počítače VMware a úloh spuštěných na těchto virtuálních počítačích je nainstalovaná aplikace DPM nebo MABS.
**Nasazený jako Azure Stack virtuálního počítače** | Pouze MABS | Aplikace DPM nelze použít k zálohování virtuálních počítačů Azure Stack.
**Nasazená jako virtuální počítač Azure** | Chrání virtuální počítače Azure a úloh běžících na těchto virtuálních počítačů. | MABS/aplikace DPM běžící v Azure, nemůžete zálohovat na místních počítačích.


## <a name="supported-mabs-and-dpm-operating-systems"></a>Podporované operační systémy MABS a aplikace DPM

Azure Backup můžete zálohovat MABS/aplikace DPM s některým z následujících operačních systémů. Operační systémy by měly běžet nejnovější aktualizace service Pack a aktualizace.

**Scénář** | **MABS/DPM** 
--- | --- 
**MABS na Virtuálním počítači Azure** | Windows Server 2012 R2<br/><br/> Windows 2016 Datacenter<br/><br/> Windows 2019 Datacenter<br/><br/> Doporučujeme že začít s imagí z marketplace.<br/><br/> Minimální A2 Standard s využitím dvou jader a 3,5 GB paměti RAM. 
**Aplikace DPM na Virtuálním počítači Azure** | System Center 2012 R2 s aktualizací Update 3 nebo novější.<br/><br/> Operační systém Windows jako [vyžadované System Center](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).<br/><br/> Doporučujeme že začít s imagí z marketplace.<br/><br/> Minimální A2 Standard s využitím dvou jader a 3,5 GB paměti RAM. 
**MABS místní** | Podporované 64bitové operační systémy:<br/><br/> -MABS verze 3 a vyšší: Windows Server 2019 (Standard, Datacenter, Essentials) <br/><br/> MABS verze 2 a vyšší: Windows Server 2016 (Standard, Datacenter, Essentials)<br/><br/> Všechny verze MABS: Windows Server 2012 R2/2012 (Standard, Datacenter, Foundation)<br/><br/>Všechny verze MABS: Windows Storage Server 2012 R2/2012 (Standard nebo pracovní skupiny)
**Aplikace DPM na místě** | Fyzický server nebo Hyper-V virtuálního počítače: System Center 2012 SP1 nebo novější.<br/><br/> Virtuální počítač VMware: System Center 2012 R2 s aktualizací Update 5 nebo novější. 



## <a name="management-support"></a>Podpora správy
**Problém** | **Podrobnosti**
--- | ---
**Instalace** | Aplikace DPM nebo MABS musí být nainstalován na počítači jedinému účelu.<br/><br/> Na řadiči domény, na počítači s instalací role aplikačního serveru, na počítači se systémem Exchange Server nebo System Center Operations Manager nebo na uzlu clusteru, neinstalujte DPM nebo MABS.<br/><br/> [Kontrola](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server) všechny požadavky na systém aplikace DPM.
**Domény** | Server DPM nebo MABS by měl být připojený k doméně. Nejprve nainstalovat a pak připojit se k aplikaci DPM nebo MABS do domény. Přesun DPM nebo MABS do nové domény po nasazení se nepodporuje.
**Storage** | Moderní úložiště záloh (MB) se podporuje od verze DPM 2016 nebo MABS 2 a vyšší. Není k dispozici pro MABS v1.
**MABS upgrade** | Přímo instalací MABS v3, nebo upgradovat z MABS v2 na MABS v3. [Další informace](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**Přesunutí MABS** | Přesunutí MABS na nový server při zachování úložiště je podporováno, pokud používáte MB.<br/><br/> Server musí mít stejný název jako původní. Pokud chcete zachovat stejného fondu úložiště a používat stejnou databázi MABS pro uložení bodů obnovení dat nelze změnit název.<br/><br/> Vzhledem k tomu, že budete potřebovat k obnovení budete potřebovat zálohu databáze MABS.




## <a name="mabs-support-on-azure-stack"></a>Podpora MABS ve službě Azure Stack

MABS na virtuálním počítači Azure Stack můžete nasadit pro zálohování virtuálních počítačů Azure Stack a úlohy můžete spravovat z jednoho místa.

**Komponenta** | **Podrobnosti**
--- | --- 
**MABS na virtuálním počítači Azure Stack** | Minimální velikost A2. Doporučujeme že začít s bitovou kopii systému Windows Server 2012 R2 nebo Windows Server 2016 na webu Azure Marketplace.<br/><br/> Cokoli, byste neměli instalovat na virtuálním počítači MABS.
**MABS úložiště** | Použijte účet samostatného úložiště pro virtuální počítač MAB. MARS agenta spuštěného na MABS potřebuje dočasné úložiště pro umístění mezipaměti a k uložení obnovení dat z cloudu.
**Fond úložiště MABS** | Velikost fondu úložiště MABS se určuje podle počtu a velikosti disků připojených k virtuálnímu počítači MABS. Všechny velikosti virtuálních počítačů Azure Stack je maximální počet disků. A2 je třeba čtyři disky.
**Uchování MABS** | Více než pět dní by neměl zachovat zálohovaná data na místní disky MABS.
**MABS vertikálně navýšit kapacitu** | Vertikálně navýšit kapacitu vašeho nasazení, můžete zvýšit velikost virtuálního počítače MABS. Například od A do řady D series.<br/><br/> Můžete také zajistit dat pomocí služby backup do Azure už zátěže pravidelně a v případě potřeby můžete nasadit další servery MABS.
Rozhraní .NET framework na MABS | Virtuální počítač MABS vyžaduje rozhraní .NET Framework 3.3 SP1 nebo vyšší na něm nainstalován.
**MABS domain** | Virtuální počítač MABS musí být připojené k doméně. Uživatel domény s oprávněními správce, musíte nainstalovat MABS na virtuálním počítači.
**Zálohování dat Azure Stack virtuálního počítače** | Můžete zálohovat soubory, složky a aplikace.
**Podporované zálohování** | Tyto operační systémy se podporují pro virtuální počítače, které chcete zálohovat:<br/><br/> – Windows Server prostřednictvím půlročního kanálu (Datacenter nebo Enterprise nebo Standard)<br/><br/> Windows Server 2016/2012 R2/2012/2008 R2.
**Podpora SQL pro virtuální počítače Azure Stack** | Zálohování SQL serveru 2016/2014/2012 s aktualizací SP1.<br/><br/> Zálohování a obnovení databáze.
**Podpora služby SharePoint pro virtuální počítače Azure Stack** | SharePoint 2016/2013/2010.<br/><br/> Zálohování a obnovení farmy, databáze, front-endu, webový server.
**Požadavky na síť pro zálohování virtuálních počítačů** | Všechny virtuální počítače v Azure stacku úlohy musí být ve stejné virtuální síti a patří do stejného předplatného.


## <a name="dpmmabs-networking-support"></a>Aplikace DPM nebo MABS síťové podpory

### <a name="url-access"></a>Přístup k adrese URL

Server DPM nebo MABS potřebuje přístup k těmto adresám URL:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="dpmmabs-connectivity-to-azure-backup"></a>Aplikace DPM nebo MABS připojení ke službě Azure Backup

Připojení ke službě Azure Backup se vyžaduje pro zálohy fungovat správně a předplatné Azure, které by měl být aktivní. Následující tabulka uvádí chování, pokud tyto dvě věci nedojde.

**MABS do Azure** | **Předplatné** | **Zálohování a obnovení** 
--- | --- | --- 
Připojeno | Aktivní | Zálohování na disk aplikace DPM nebo MABS<br/><br/> Zálohování do systému Azure<br/><br/> Obnovení z disku<br/><br/> Obnovení z Azure
Připojeno | Platnost vypršela nebo zrušení zřízení | Žádné zálohování na disk nebo Azure.<br/><br/> Pokud platnost předplatného můžete obnovit z disku nebo v Azure.<br/><br/> Pokud předplatné je Vyřazená z provozu nelze obnovit z disku nebo v Azure. Odstraní se body obnovení Azure.
Bez připojení k více než 15 dnů | Aktivní | Žádné zálohování na disk nebo Azure.<br/><br/> Můžete obnovit z disku nebo v Azure.
Bez připojení k více než 15 dnů | Platnost vypršela nebo zrušení zřízení | Žádné zálohování na disk nebo Azure.<br/><br/> Pokud platnost předplatného můžete obnovit z disku nebo v Azure.<br/><br/> Pokud předplatné je Vyřazená z provozu nelze obnovit z disku nebo v Azure. Odstraní se body obnovení Azure.



## <a name="dpmmabs-storage-support"></a>Podpora úložiště DPM nebo MABS

Data zálohována do DPM nebo MABS se ukládají na úložiště na místním disku. 

**Storage** | **Podrobnosti**
--- | ---
**MBS** | Moderní úložiště záloh (MB) se podporuje od verze DPM 2016 nebo MABS 2 a vyšší. Není k dispozici pro MABS v1.
**MABS úložiště na virtuálním počítači Azure** | Data se ukládají na disky Azure připojené k virtuálnímu počítači DPM nebo MABS a spravovat v aplikaci DPM nebo MABS. Počet disků, které lze použít pro fond úložiště DPM nebo MABS je omezen velikostí virtuálního počítače.<br/><br/> A2 VM: 4 disky; A3 VM: 8 disky; A4 VM: 16 disků s maximální velikostí 1 TB pro každý disk. Určuje celkový úložiště záloh fondu k dispozici.<br/><br/> Množství dat, která můžete zálohovat, závisí na počtu a velikosti připojených disků.
**MABS uchovávání dat na virtuálním počítači Azure** | Doporučujeme ukládat data za jeden den na disku aplikace DPM nebo MABS Azure a z aplikace DPM nebo MABS zálohovat do trezoru pro delší dobu uchování. Můžete tedy chránit větší množství dat přesměrováním do služby Azure Backup.


### <a name="modern-backup-storage-mbs"></a>Moderní úložiště záloh (MB)
Z aplikace DPM 2016/MABS v2 (se systémem Windows Server 2016) a vyšší, můžete využít výhod moderního úložiště záloh (MB).

- MB zálohy jsou uložené na disku pro odolný systém souborů (ReFS).
- Použití MB odolný systém souborů blokovat klonování a pro rychlejší zálohování a efektivnější využívání prostoru úložiště.
- Když přidáte svazky do místního fondu úložiště DPM nebo MABS, je nakonfigurovaný pomocí písmen jednotek. Potom můžete nakonfigurovat úlohy úložiště v různých svazcích.
- Při vytváření skupiny ochrany a zálohování dat do DPM nebo MABS, vyberte jednotku, kterou chcete použít. Například může ukládání záloh SQL nebo jiná vysoké zatížení vstupně-výstupních operací disku vysoký výkon a úlohy, které se zálohují méně často na nižší výkon disku.


## <a name="supported-backups-to-mabs"></a>Podporované záloh MABS

Následující tabulka shrnuje, co můžete zálohovat na platformu MABS z místních počítačů a virtuálních počítačů Azure.


**Backup** | **Verze** | **MABS** | **Podrobnosti**
--- | --- | --- | ---
**Windows 10, 8.1, 8, 7**<br/><br/>(32 nebo 64 bitů) | MABS v3, v2, V1 | Lokálně | Svazek nebo sdílenou složku/složka či soubor<br/><br/> Svazky s odstraněnými podporována<br/><br/> Svazky musí mít aspoň 1 GB a systému souborů NTFS.
**Windows Server 2016 (Datacenter, Standard, ne Nano)**<br/><br/> 64/32-bit | MABS v3, v2 | Virtuální počítač na místní nebo Azure.| Svazek nebo sdílenou složku/složka či soubor; System – stav/úplné<br/><br/> Svazky s odstraněnými podporována.
**Windows Server 2012 R2 (Datacenter a Standard)**<br/><br/> 64/32-bit | MABS v3, v2, v1 | Virtuální počítač na místní nebo Azure. | **Místní ochrana**: Svazek nebo sdílenou složku/složka či soubor; System – stav/úplné<br/><br/> **Azure VMprotection**: Svazek nebo sdílenou složku/složka či soubor<br/><br/> Svazky s odstraněnými podporována.
**Windows Server 2012 s aktualizací SP1 (Datacenter a Standard)**<br/><br/> 64/32-bit | MABS v3, v2, v1<br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) musí být nainstalována. | Virtuální počítač na místní nebo Azure. | **Místní ochrana**: Svazek nebo sdílenou složku/složka či soubor; System – stav/úplné<br/><br/> **Azure ochranu virtuálního počítače**: Svazek nebo sdílenou složku/složka či soubor<br/><br/> Svazky s odstraněnými podporována.
**Windows Server 2012 (Datacenter a Standard)**<br/><br/> 64/32-bit | MABS v1 | Virtuální počítač na místní nebo Azure. | **Místní ochrana**: Svazek nebo sdílenou složku/složka či soubor; System – stav/úplné<br/><br/> **Azure ochranu virtuálního počítače**: Svazek nebo sdílenou složku/složka či soubor<br/><br/> Svazky s odstraněnými podporována.
**Windows 2008 R2 s aktualizací SP1 (Standard a Enterprise)**<br/><br/> 64/32-bit | Podporuje MABS v3, v1 a v2.<br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) musí být nainstalována. | Virtuální počítač na místní nebo Azure. |   **Místní ochrana**: Svazek nebo sdílenou složku/složka či soubor; System – stav/úplné<br/><br/> **Azure ochranu virtuálního počítače**: Svazek nebo sdílenou složku/složka či soubor<br/><br/> Svazky s odstraněnými podporována.
**Windows 2008 R2 (Standard a Enterprise)**<br/><br/> 64/32-bit | MABS v1. MABS v2 nebo v3 operační systém musí běžet s aktualizací SP1. | Virtuální počítač na místní nebo Azure. | **Místní ochrana**: Svazek nebo sdílenou složku/složka či soubor; System – stav/úplné<br/><br/> **Azure ochranu virtuálního počítače**: Svazek nebo sdílenou složku/složka či soubor<br/><br/> Svazky s odstraněnými podporována.
**Windows Server 2008 s aktualizací SP2**<br/><br/> 64/32-bit | MABS v1, v2, v3 | Když MABS je nasazená jako fyzický počítač na místní nebo virtuální počítač Hyper-V se podporuje jen v1.<br/><br/> MABS verze 1, 2, 3 je podporováno MABS je nasazená jako virtuální počítač VMware.<br/><br/> Není podporována pro MABS spuštěnou na virtuálním počítači Azure | Svazek nebo sdílenou složku/složka či soubor; System – stav/úplné.
**Windows Storage Server 2008** | MABS v1, v2, v3 | MABS jako fyzický server nebo Hyper-V virtuálního počítače v místních | Není podporována pro MABS spuštěnou na virtuálním počítači Azure. | Svazek nebo sdílenou složku/složka či soubor; System – stav/úplné.
**SQL Server 2017** | MABS v3 | Virtuální počítač na místní nebo Azure.| Proveďte zálohu databáze serveru SQL Server.<br/><br/> Zálohování clusteru serveru SQL Server podporováno.<br/><br/>Databáze uložené ve sdílených svazcích clusteru není podporována.
**SQL Server 2016/2016 s aktualizací SP1** | MABS v3, v2 | Virtuální počítač na místní nebo Azure.| Proveďte zálohu databáze serveru SQL Server.<br/><br/> Zálohování clusteru serveru SQL Server podporováno.<br/><br/>Databáze uložené ve sdílených svazcích clusteru není podporována.
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | MABS v3, v2, v1 | Virtuální počítač na místní nebo Azure.| Proveďte zálohu databáze serveru SQL Server.<br/><br/> Zálohování clusteru serveru SQL Server podporováno.<br/><br/>Databáze uložené ve sdílených svazcích clusteru není podporována.
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | MABS v3, v2, V1 | Místní. | Zálohování samostatný server Exchange, databáze ve skupině DAG<br/><br/> Obnovte poštovní schránka, databáze poštovní schránky ve skupině DAG.<br/><br/> ReFS nepodporuje.<br/><br/> Proveďte zálohu nesdílené diskové clustery.<br/><br/> Zálohování serveru Exchange Server nakonfigurovaný pro průběžnou replikaci.
**SharePoint 2016**<br/><br/> **Sharepoint 2013**<br/><br/> **SharePoint 2010** | MABS v3, v2, v1 | Virtuální počítač na místní nebo Azure | Zálohování farmy, frontendový webový server.<br/><br/> Obnovení farmy, databáze, webová aplikace, soubor nebo položka seznamu, vyhledávání služby SharePoint, frontendový webový server.<br/><br/> Nelze zálohovat farmu pomocí AlwaysOn pro databázi obsahu.
**Hyper-V ve Windows serveru 2016**<br/><br/> **Windows Server 2012 R2/2012** (Datacenter/Standard)<br/><br/> **Windows Server 2008 R2 (s aktualizací SP1)** | MABS v3, v2, v1 | Lokálně | **MABS agenta na hostiteli Hyper-V**: Zálohujte celé virtuální počítače a hostitele datové soubory. Zálohování virtuálních počítačů s místním úložištěm, virtuální počítače v clusteru s úložištěm CSV, virtuální počítače s úložištěm souborového serveru SMB.<br/><br/> **Na virtuálním počítači hosta agenta MABS**: Zálohujte úlohy běžící na virtuálním počítači. Sdílené svazky clusteru.<br/><br/> **Obnovení**: Virtuální počítač, obnovení na úrovni položky z virtuálního pevného disku/svazek/složky a soubory.<br/><br/> **Virtuální počítače s Linuxem**: Zálohujte, když je spuštěna technologie Hyper-V v systému Windows Server 2012 R2 a vyšší. Obnovení pro virtuální počítače s Linuxem je pro celý počítač.
**Virtuální počítače VMware: vCenter nebo vSphere ESXi 5.5/6.0/6.5** | MABS v3, v2, v1.<br/><br/> MABS v1 musí kumulativní aktualizace 1) | Lokálně | Zálohování virtuálních počítačů VMware na úložiště sdílených svazků clusteru, systém souborů NFS a po síti SAN.<br/><br/> Obnovte celý virtuální počítač.<br/><br/> Zálohování Windows/Linux.<br/><br/> Obnovení na úrovni položek složky nebo soubory pro virtuální počítače s Windows jenom.<br/><br/> Aplikace Vapp VMware nejsou podporovány.<br/><br/> Obnovení pro virtuální počítače s Linuxem je pro celý počítač.



## <a name="supported-backups-to-dpm"></a>Podporované zálohy DPM

Následující tabulka shrnuje, co je možné zálohovat do aplikace DPM z místních počítačů a virtuálních počítačů Azure.



**Backup** | **DPM** | **Podrobnosti**
--- | --- | --- 
**Windows 10, 8.1, 8, 7**<br/><br/>(32 nebo 64 bitů) | Jen místně.<br/><br/> Pro zálohování systému Windows 10 pomocí DPM 2012 R2 doporučujeme nejprve nainstalovat [aktualizace 11](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager). | Svazek nebo sdílenou složku/složka či soubor<br/><br/> Svazky s odstraněnými podporována<br/><br/> Svazky musí mít aspoň 1 GB a systému souborů NTFS.
**Windows Server 2016 (Datacenter, Standard, ne Nano)**<br/><br/> 64/32-bit | Virtuální počítač na místní nebo Azure.<br/><br/> Pouze v aplikaci DPM 2016.| Svazek nebo sdílenou složku/složka či soubor; System – stav/úplné<br/><br/> Svazky s odstraněnými podporována.
**Windows Server 2012 R2 (Datacenter a Standard)**<br/><br/> 64/32-bit | Virtuální počítač na místní nebo Azure | **Místní ochrana**: Svazek nebo sdílenou složku/složka či soubor; System – stav/úplné<br/><br/> **Azure ochranu virtuálního počítače**: Svazek nebo sdílenou složku/složka či soubor<br/><br/> Svazky s odstraněnými podporován pomocí DPM 2012 R2 a vyšší.
**Windows Server 2012 s aktualizací SP1 (Datacenter a Standard)**<br/><br/> 64/32-bit | Virtuální počítač na místní nebo Azure. | **Místní ochrana**: Svazek nebo sdílenou složku/složka či soubor; System – stav/úplné<br/><br/> **Azure ochranu virtuálního počítače**: Svazek nebo sdílenou složku/složka či soubor<br/><br/> Svazky s odstraněnými podporován pomocí DPM 2012 R2 a vyšší.
**Windows Server 2012 (Datacenter a Standard)**<br/><br/> 64/32-bit |  Virtuální počítač na místní nebo Azure. | **Místní ochrana**: Svazek nebo sdílenou složku/složka či soubor; System – stav/úplné<br/><br/> **Azure ochranu virtuálního počítače**: Svazek nebo sdílenou složku/složka či soubor<br/><br/> Svazky s odstraněnými podporován pomocí DPM 2012 R2 a vyšší.
**Windows 2008 R2 s aktualizací SP1 (Standard a Enterprise)**<br/><br/> 64/32-bit | Virtuální počítač na místní nebo Azure <br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) musí být nainstalována. |   **Místní ochrana**: Svazek nebo sdílenou složku/složka či soubor; System – stav/úplné<br/><br/> **Azure ochranu virtuálního počítače**: Svazek nebo sdílenou složku/složka či soubor.
**Windows 2008 R2 (Standard a Enterprise)**<br/><br/> 64/32-bit | Místní.<br/><br/> Aplikace DPM nelze nainstalovat jako virtuální počítač VMware.<br/><br/> Aplikace DPM běžící na Virtuálním počítači Azure se nepodporuje. | **Místní ochrana**: Svazek nebo sdílenou složku/složka či soubor; System – stav/úplné
**Windows Server 2008 s aktualizací SP2**<br/><br/> 64/32-bit | Jen místně.<br/><br/> Aplikace DPM je podporována při spuštění jako virtuální počítač VMware. Spuštění jako fyzický server nebo virtuální počítač Hyper-V se nepodporuje. | Svazek nebo sdílenou složku/složka či soubor; System – stav/úplné.
**Windows Storage Server 2008** | Aplikace DPM v místním spuštěná jako fyzický server nebo virtuální počítač Hyper-V. | Svazek nebo sdílenou složku/složka či soubor; System – stav/úplné.
**SQL Server 2017** | DPM SAC; Aplikace DPM 2016 používá kumulativní aktualizaci 5 nebo novější<br/><br/> Virtuální počítač na místní nebo Azure.| Proveďte zálohu databáze serveru SQL Server.<br/><br/> Zálohování clusteru serveru SQL Server podporováno.<br/><br/>Databáze uložené ve sdílených svazcích clusteru není podporována.
**SQL Server 2016 SP1** | Není podporováno pro DPM 2012 R2 Podporováno pro DPM SAC, aplikace DPM 2016 s kumulativní aktualizací 4 nebo novějším.<br/><br/> Virtuální počítač na místní nebo Azure.| Proveďte zálohu databáze serveru SQL Server.<br/><br/> Zálohování clusteru serveru SQL Server podporováno.<br/><br/>Databáze uložené ve sdílených svazcích clusteru není podporována.
**SQL Server 2016** | Není podporováno pro DPM 2012 R2. Podporováno pro DPM SAC, aplikace DPM 2016 z kumulativní aktualizaci 2 a vyšší.<br/><br/> Virtuální počítač na místní nebo Azure.| Proveďte zálohu databáze serveru SQL Server.<br/><br/> Zálohování clusteru serveru SQL Server podporováno.<br/><br/>Databáze uložené ve sdílených svazcích clusteru není podporována.
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | SQL Server 2014 s DPM 2012 R2 s kumulativní aktualizací 4 a vyšší.<br/><br/> Virtuální počítač na místní nebo Azure.| Proveďte zálohu databáze serveru SQL Server.<br/><br/> Zálohování clusteru serveru SQL Server podporováno.<br/><br/>Databáze uložené ve sdílených svazcích clusteru není podporována.
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | Pro Exchange 2016 musí aplikace DPM 2012 R2 s kumulativní aktualizací 9 nebo novější.<br/><br/> Lokálně | Zálohování samostatný server Exchange, databáze ve skupině DAG<br/><br/> Obnovte poštovní schránka, databáze poštovní schránky ve skupině DAG.<br/><br/> ReFS nepodporuje.<br/><br/> Proveďte zálohu nesdílené diskové clustery.<br/><br/> Zálohování serveru Exchange Server nakonfigurovaný pro průběžnou replikaci.
**SharePoint 2016**<br/><br/> **Sharepoint 2013**<br/><br/> **SharePoint 2010** | SharePoint 2016 na DPM 2016 a vyšší.<br/><br/>Virtuální počítač na místní nebo Azure | Zálohování farmy, frontendový webový server.<br/><br/> Obnovení farmy, databáze, webová aplikace, soubor nebo položka seznamu, vyhledávání služby SharePoint, frontendový webový server.<br/><br/> Nelze zálohovat farmu pomocí AlwaysOn pro databázi obsahu.
**Hyper-V ve Windows serveru 2016**<br/><br/> **Windows Server 2012 R2/2012** (Datacenter/Standard)<br/><br/> **Windows Server 2008 R2 (s aktualizací SP1)** | Technologie Hyper-V na 2016 pro DPM 2016 a vyšší podporovány.<br/><br/> Lokálně | **MABS agenta na hostiteli Hyper-V**: Zálohujte celé virtuální počítače a hostitele datové soubory. Zálohování virtuálních počítačů s místním úložištěm, virtuální počítače v clusteru s úložištěm CSV, virtuální počítače s úložištěm souborového serveru SMB.<br/><br/> **Na virtuálním počítači hosta agenta MABS**: Zálohujte úlohy běžící na virtuálním počítači. Sdílené svazky clusteru.<br/><br/> **Obnovení**: Virtuální počítač, obnovení na úrovni položky z virtuálního pevného disku/svazek/složky a soubory.<br/><br/> **Virtuální počítače s Linuxem**: Zálohujte, když je spuštěna technologie Hyper-V v systému Windows Server 2012 R2 a vyšší. Obnovení pro virtuální počítače s Linuxem je pro celý počítač.
**Virtuální počítače VMware: vCenter nebo vSphere ESXi 5.5/6.0/6.5** | MABS v3, v2, v1.<br/><br/> Aplikace DPM 2012 R2 vyžaduje kumulativní aktualizaci 1 System Center) | Lokálně | Zálohování virtuálních počítačů VMware na úložiště sdílených svazků clusteru, systém souborů NFS a po síti SAN.<br/><br/> Obnovte celý virtuální počítač.<br/><br/> Zálohování Windows/Linux.<br/><br/> Obnovení na úrovni položek složky nebo soubory pro virtuální počítače s Windows jenom.<br/><br/> Aplikace Vapp VMware nejsou podporovány.<br/><br/> Obnovení pro virtuální počítače s Linuxem je pro celý počítač.


- Všimněte si, že by měly zálohovat pomocí DPM nebo MABS clusterových úloh ve stejné doméně jako aplikace DPM nebo MABS, nebo v podřízené nebo důvěryhodné doméně. 
- Ověřování NTLM/certifikát můžete použít k zálohování dat v nedůvěryhodných doménách nebo pracovních skupinách.



## <a name="next-steps"></a>Další postup

- [Další informace](backup-architecture.md#architecture-back-up-to-dpmmabs) o architektuře MABS.
- [Kontrola](backup-support-matrix-mars-agent.md) co je podporováno pro agenta MARS.
- [Nastavit](backup-azure-microsoft-azure-backup.md) serveru MABS.
- [Nastavení] (https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180
