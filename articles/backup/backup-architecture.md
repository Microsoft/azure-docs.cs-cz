---
title: Architektura služby Azure Backup
description: Obsahuje přehled architektury, komponenty a procesy používané službou Azure Backup.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: 4be483994bd7bc5bd97b1e59df230f66e9b4e24e
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430342"
---
# <a name="azure-backup-architecture"></a>Architektura služby Azure Backup

Můžete použít [služby Azure Backup](backup-overview.md) k zálohování dat do cloudu Microsoft Azure. Tento článek shrnuje architektury Azure Backup, komponenty a procesy. 


## <a name="what-does-azure-backup-do"></a>Co dělá Azure Backup?

Azure Backup zálohuje data, stav počítače a úlohy běžící na místních počítačů a virtuálních počítačů Azure. Existuje mnoho scénářů Azure Backup.

## <a name="how-does-azure-backup-work"></a>Jak funguje Azure Backup?

Můžete zálohovat počítače a data pomocí několika metod.

- **Zálohování místních počítačů**:
    - Můžete zálohovat na místních počítačích Windows přímo do Azure pomocí agenta Azure Backup Microsoft Azure Recovery Services (MARS). Počítače s Linuxem nejsou podporovány.
    - Můžete zálohovat na místních počítačích na záložní server (System Center Data Protection Manager (DPM) nebo Microsoft Azure Backup Server (MABS)) a pak zase zálohování Zálohování serveru do trezoru služby Azure Backup Recovery Services v Azure.
- **Zálohování virtuálních počítačů Azure**:
    - Můžete zálohovat virtuální počítače Azure přímo. Azure Backup nainstaluje rozšíření zálohování na virtuálním počítači Azure agenta spuštěného na virtuálním počítači k tomu. Zálohuje celý virtuální počítač.
    - Můžete zálohovat konkrétní soubory a složky na virtuálním počítači Azure spuštěním agenta MARS.
    - Můžete zálohovat virtuální počítače Azure pro MABS běžící v Azure a pak zase zálohujte MABS do trezoru.

Další informace o [co můžete zálohovat](backup-overview.md), a [Podporované scénáře zálohování](backup-support-matrix.md).


## <a name="where-is-data-backed-up"></a>Kde se data zálohována?

Azure Backup ukládá zálohovaná data v trezoru služby Recovery Services. Trezor je entita online úložiště v Azure, která se používá k ukládání dat, jako jsou záložní kopie, body obnovení a zásady zálohování.

- Trezory služby usnadňují uspořádání dat záloh a současně minimalizují režii na správu.
- V rámci předplatného Azure můžete vytvořit až 500 trezorů.
- Můžete monitorovat zálohovaných položek v trezoru, včetně virtuálních počítačů Azure a místních počítačů.
- Můžete spravovat přístup k trezoru s využitím Azure [řízení přístupu na základě role (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
- Můžete určit, jak data v trezoru se replikují pro zajištění redundance:
    - **LRS**: Místně redundantní úložiště (LRS) můžete použít k chrání před selháním v datovém centru. LRS se replikuje data na jednotce škálování úložiště. [Další informace](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs).
    - **GRS**: Můžete použít geograficky redundantní úložiště (GRS): Chrání před výjimečnými výpadky celé oblasti. Replikuje data do sekundární oblasti. [Další informace](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs). 
    - Ve výchozím nastavení používají trezory služby Recovery Services pro zálohování GRS. 




### <a name="backup-agents"></a>Agenty služby Backup

Azure Backup poskytuje různí agenti, v závislosti na typu zálohování.

**Agent** | **Podrobnosti** 
--- | --- 
**Agent Microsoft Azure Recovery Services (MARS)** | (1) spustí se v jednotlivých místních Windows serverů k zálohování souborů, složek a stavu systému<br/><br/> (2) běží na virtuálních počítačích Azure k zálohování souborů, složek a stavu systému.<br/><br/>  (3) běží na serverech DPM nebo MABS zálohování na disk aplikace DPM nebo MABS místního úložiště do Azure. 
**Rozšíření virtuálního počítače Azure** | Běží na virtuálních počítačích Azure zálohovat do trezoru.


## <a name="backup-types"></a>Typy zálohování

**Typ zálohování** | **Podrobnosti** | **Použití**
--- | --- | ---
**Úplné** | Zálohování obsahuje celý zdroj dat. Úplné zálohování používá větší šířku pásma sítě. | Používá se pro prvotní zálohování.
**Rozdílové** |  Ukládá bloky, které se změnily od prvotní úplné zálohy. Používá menší množství sítě a úložiště a redundantní kopie nezměněných dat. nezachová.<br/><br/> Neefektivní, protože beze změny mezi dalšími zálohami datové bloky budou přeneseny a uložené. | Nepoužívá se službou Azure Backup.
**Přírůstkové** | Vysokou efektivitu úložiště a sítě. Ukládá pouze bloky dat, které se změnily od předchozí zálohy. <br/><br/> Není nutné k není nutné k doplnění s úplnými zálohami s přírůstkovým zálohováním. | Používají DPM nebo MABS zálohování na disk a používat v všechny zálohy do Azure.

## <a name="sql-server-backup-types"></a>Typy zálohování systému SQL Server

**Typ zálohování** | **Podrobnosti** | **Použití**
--- | --- | ---
**Úplné zálohování** | Úplná záloha databáze vytvoří zálohu celé databáze. Obsahuje všechna data v konkrétní databázi nebo sadu skupiny souborů nebo souborů a dostatek protokoly a tato data obnovit. | Maximálně můžete aktivovat jednu úplnou zálohu denně.<br/><br/> Můžete provést úplné zálohování na denní nebo týdenní interval.
**Rozdílová záloha** | Rozdílové zálohy je založená na nejnovější, předchozí úplná záloha.<br/><br/> Zachytí pouze data, která se změnila od úplného zálohování. |  Maximálně můžete aktivovat jeden rozdílové zálohy za den.<br/><br/> Úplné zálohování a rozdílovou zálohu nelze konfigurovat ve stejný den.
**Zálohy transakčního protokolu** | Záloha protokolu umožňuje obnovení bodu v čase až po konkrétní sekundy. | Maximálně můžete nakonfigurovat zálohy transakčního protokolu každých 15 minut.


### <a name="comparison"></a>Porovnání

Spotřeba úložiště (RTO) plánovaná doba obnovení a využití sítě se liší pro každý typ zálohování. Následující obrázek znázorňuje srovnání typy zálohování.
- Zdroj dat A skládá z 10 úložiště blokuje A1 – A10, které se zálohují každý měsíc.
- Bloky A2, A3, A4 a A9 se mění první měsíc a blok A5 se mění následující měsíc.
- Pro rozdílové zálohování druhý měsíc změněné bloky A2, A3, A4 a A9 se zálohují. Třetí měsíc se znovu zálohují tyto stejné bloky, společně se změněným blokem A5. Změněné bloky se budou zálohovat až do doby, kdy dojde k dalšímu úplnému zálohování.
- Přírůstkové zálohování, po vytvoření úplné zálohy v prvním měsíci bloky A2, A3, A4 a A9 jsou označeny jako změnit a přenést do dalšího měsíce. Třetí měsíc se označí a přenese pouze změněný blok A5. 

![obrázek ukazující porovnání způsobů zálohování](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Funkce služby zálohování

Následující tabulka shrnuje funkce pro různé typy zálohování.

**Funkce** | **Počítače s Windows v místním (přímo)** | **Virtuální počítače Azure** | **Počítače a aplikací pomocí aplikace DPM nebo MABS**
--- | --- | --- | ---
Zálohovat do trezoru | ![Ano][green] | ![Ano][green] | ![Ano][green] 
Zálohování na disk aplikace DPM nebo MABS a Azure | | | ![Ano][green] 
Komprese dat odeslaných k zálohování | ![Ano][green] | Při přenosu dat se nepoužívá žádná komprese. Úložiště se mírně zvětšený, ale je rychlejší obnovení.  | ![Ano][green] 
Spustit přírůstkové zálohování |![Ano][green] |![Ano][green] |![Ano][green] 
Zálohování disků s odstraněním duplicit | | | ![Částečně][yellow]<br/><br/> Pro aplikaci DPM nebo MABS servery nasazené jen místně. 

![klíč tabulky](./media/backup-architecture/table-key.png)





## <a name="architecture-direct-backup-of-azure-vms"></a>Architektura: Přímé zálohování virtuálních počítačů Azure

1. Při povolení zálohování pro virtuální počítač Azure, zálohování se spustí podle plánu, který zadáte.
2. Při prvním zálohování nainstalovaný rozšíření zálohování na virtuálním počítači, pokud běží.
    - Pro virtuální počítače s Windows VMSnapshot je rozšíření nainstalované.
    - Pro virtuální počítače s Linuxem je nainstalované rozšíření VMSnapshot Linuxu.
3. Rozšíření pořídí snímek úrovně úložiště. 
    - Pro Windows virtuálních počítačů, které jsou spuštěné, souřadnice zálohování pomocí VSS pořízení konzistentního snímku virtuálního počítače. Ve výchozím nastavení zálohování trvá úplné zálohy stínové kopie svazku. Pokud záloha není schopen pořízení konzistentního snímku, trvá snímek konzistentní vzhledem k souboru.
    - Zálohování virtuálních počítačů Linux trvá v zájmu konzistentní zálohování. Pro snímky konzistentní vzhledem k budete muset ručně upravit předzálohovacího nebo pozálohovacího skripty.
    - Zálohování je optimalizované zálohování každého disku virtuálního počítače paralelně. Azure Backup pro každý disk zálohování, přečte bloky na disku a uloží jenom změněná data. 
4. Poté, co se pořídí snímek, data se přenesou do trezoru. 
    - Pouze bloky dat, které se změnily od posledního zálohování jsou zkopírovány.
    - Data nejsou šifrována. Azure Backup můžete zálohovat virtuální počítače Azure, které jsou šifrované pomocí služby Azure Disk Encryption (ADE).
    - Data snímku nemusí být hned zkopírují do trezoru. Může trvat několik hodin, během špičky. Celkový čas zálohování pro virtuální počítač menší bude tento po dobu 24 hodin denně zásady zálohování.
5. Po odeslání dat do trezoru snímek odstraní a vytvoří bod obnovení.

Všimněte si, že virtuální počítače Azure pro příkazy pro řízení potřebovat přístup k Internetu. Pokud zálohujete na jiné úlohy ve virtuálním počítači (například zálohování serveru SQL Server), musí back data také přístup k Internetu. 

![Zálohování virtuálních počítačů Azure](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders"></a>Architektura: Přímé zálohování místních Windows machines nebo Azure VM souborů a složek

1. Chcete-li nastavit scénář, stáhnout a nainstalovat agenta Microsoft Azure Recovery Services (MARS) na počítači, vyberte co bude zálohováno, když se spustí zálohování a jak dlouho se bude nacházet v Azure.
2. Spuštění prvotního zálohování v souladu s nastavení zálohování.
3. Agenta MARS používá službu Stínová kopie Windows svazku (VSS) k vytvoření snímku bodu v čase svazky vybrané pro zálohování.
    - Agenta MARS využívá jenom zápisu systému Windows k zachycení snímku.
    - Agent nepoužívá žádné aplikace zapisovače VSS a proto nezachytí snímky konzistentní.
3. Po pořízení snímek služby VSS, agenta MARS vytvoří virtuální pevný disk ve složce mezipaměti, který jste zadali při konfiguraci zálohování, a ukládá kontrolních součtů pro každé datové bloky. 
4. Spouštět přírůstkové zálohování podle plánu, který jste zadali, není-li spustit zálohování ad hoc.
5. V přírůstkové zálohy změněné soubory jsou identifikovány a je vytvořen nový virtuální pevný disk. Má komprimovaná a šifrovaná a odeslán do trezoru.
6. Po dokončení přírůstkového zálohování, nový virtuální pevný disk je sloučen s virtuální disk VHD vytvořený po počáteční replikaci, poskytuje nejnovější stav má být použit pro porovnání pro probíhající zálohování. 

![Zálohování Windows serveru v místním pomocí agenta MARS](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Architektura: Zálohování do DPM nebo MABS

1. Nainstalujte agenta ochrany aplikace DPM nebo MABS na počítačích, které chcete chránit a přidejte počítače do skupiny ochrany aplikace DPM.
    - K ochraně místní počítače, server DPM nebo MABS musí být v místním.
    - Pokud chcete chránit virtuální počítače Azure, se musí nacházet MABS server v Azure, spuštěná jako virtuální počítač Azure.
    - Pomocí aplikace DPM nebo MABS lze chránit zálohovat svazky, sdílené složky, soubory a složky. Můžete chránit počítače systému stav/úplné a chránit konkrétní aplikace s podporou aplikace nastavení zálohování.
2. Když nastavíte ochranu pro počítače nebo aplikace v aplikaci DPM nebo MABS, vyberete zálohovat na místní disk MABS/DPM pro krátkodobé uložení a do Azure (online ochrany). Můžete také určit, kdy se má spustit zálohování do místního úložiště DPM nebo MABS a kdy se má spustit online zálohování do Azure.
3. Vytvoření zálohy disku chráněné úlohy na lokální disky MABS/DPM podle plánu, který jste zadali.
4. DPM nebo MABS disků jsou zálohovány do trezoru pomocí agenta MARS běží na serveru aplikace DPM nebo MABS.

![Zálohování počítačů a úloh, které jsou chráněné službou DPM nebo MABS](./media/backup-architecture/architecture-dpm-mabs.png)







## <a name="azure-vm-storage"></a>Úložiště virtuálního počítače Azure

- Virtuální počítače Azure využívají disky k ukládání svůj operační systém, aplikace a data.
- Virtuální počítače Azure obsahovat aspoň dva disky. Jeden pro operační systém a dočasný disk. Můžou také mít datové disky pro data aplikací. Disky se ukládají jako virtuální pevné disky.
- Virtuální pevné disky jsou uložené jako objekty BLOB stránky v účtech úložiště úrovně standard nebo premium v Azure.
    - Storage úrovně Standard: Podporu spolehlivé, úsporné disků pro virtuální počítače spuštěné úlohy, které nejsou citlivá na latenci. Storage úrovně Standard můžete použít standardní disky SSD nebo standardní HDD disky.
    - Storage úrovně Premium: Podporu vysoce výkonných disků. Využívá disky SSD úrovně premium.
- Existují jiné výkonové úrovně pro disky:
    - Standardní disk na pevný disk: Založená na jednotkách HDD a použít pro nákladově efektivní úložiště.
    - Disk SSD na úrovni Standard: Kombinuje element SSD disky úrovně premium a standard HDD disky, nabízí konzistentnější výkon a spolehlivost než pevný disk, ale stále nákladově efektivní.
    - Disk SSD na úrovni Premium: Se opírá o SSD, poskytuje vysoce výkonné a s nízkou latencí pro virtuální počítače spuštěné úlohy náročné na vstupně-výstupních operací.
- Disky můžou být spravovaná nebo nespravovaná:
    - Nespravované disky: Tradiční typy disků, které jsou používány virtuálními počítači. Pro tyto disky vytvořte si vlastní účet úložiště a ho zadat při vytváření disku. Budete muset zjistit, jak maximalizovat prostředky úložiště pro virtuální počítače.
    - Spravované disky: Vytváření a správu účtů úložiště za vás postará Azure. Zadejte úroveň výkonu a velikosti disku a Azure vytvoří, spravovat disky za vás. Jak přidat disky a škálovat virtuální počítače s – se postará Azure storage.

Další informace:

- Další informace o diskové úložiště pro [Windows](../virtual-machines/windows/managed-disks-overview.md) a [Linux](../virtual-machines/linux/managed-disks-overview.md) virtuálních počítačů.
- Další informace o dostupných [typy disků](../virtual-machines/windows/disks-types.md) například standard a premium.


### <a name="backing-up-and-restoring-azure-vms-with-premium-storage"></a>Zálohování a obnovení virtuálních počítačů Azure díky službě premium storage 

Můžete zálohovat virtuální počítače Azure pomocí služby premium storage s Azure Backup:

- Při zálohování virtuálních počítačů se službou premium storage, vytvoří služba Backup dočasné pracovní umístění s názvem "AzureBackup-", v účtu úložiště. Velikost pracovního umístění odpovídá velikosti snímku bodu obnovení.
- Ujistěte se, zda má účet premium storage dostatek volného místa pro toto dočasné pracovní umístění. [Další informace](../storage/common/storage-scalability-targets.md#premium-storage-account-scale-limits). Neupravujte pracovní umístění.
- Po dokončení úlohy zálohování je pracovní umístění odstraněno.
- Cena úložiště použitého pro pracovní umístění je konzistentní s [ceny za službu storage úrovně premium](../virtual-machines/windows/disks-types.md#billing).

Při obnovení virtuálních počítačů Azure pomocí služby premium storage můžete obnovit na premium nebo standard storage. Obvykle by obnovit na úroveň premium, ale může být cenově výhodnější standardu Pokud potřebujete jenom podmnožinu souborů z virtuálního počítače.

### <a name="backing-up-and-restoring-managed-disks"></a>Zálohování a obnovení spravovaných disků

Můžete zálohovat virtuální počítače Azure se spravovanými disky.
- Zálohujete virtuální počítače se spravovanými disky stejným způsobem, který používáte jakýkoli jiný virtuální počítač Azure. Virtuální počítač můžete zálohovat přímo z nastavení virtuálního počítače, nebo můžete povolit zálohování pro virtuální počítače v trezoru služby Recovery Services.
- Virtuální počítače na spravovaných discích můžete zálohovat prostřednictvím kolekcí RestorePoint postavených na spravovaných discích.
- Azure Backup podporuje také zálohování spravovaného disku virtuální počítače šifrované službou Azure Disk encryption (ADE).

Při obnovení virtuálních počítačů se spravovanými disky, můžete obnovit do kompletního virtuálního počítače se spravovanými disky nebo do účtu úložiště.
- – Se postará Azure spravované disky během procesu obnovení, a pomocí možnosti účtu úložiště, spravovat účet úložiště, který je vytvořen při obnovování.
- Pokud obnovit spravovaný virtuální počítač, který je šifrovaný virtuální počítač klíče a tajné klíče má ukončit ve službě key vault před zahájením procesu obnovení.




## <a name="next-steps"></a>Další postup

- [Kontrola](backup-support-matrix.md) matice podpory pro další informace o podporovaných funkcích a omezení pro scénáře zálohování.
- Nastavení zálohování pro jednom ze scénářů:
    - [Zálohování virtuálních počítačů Azure](backup-azure-arm-vms-prepare.md)
    - [Zálohování počítačů Windows přímo](tutorial-backup-windows-server-to-azure.md), bez zálohování serveru.
    - [Nastavit MABS](backup-azure-microsoft-azure-backup.md) pro zálohování do Azure a pak znovu úloh rozšiřování MABS.
    - [Nastavení DPM](backup-azure-dpm-introduction.md) pro zálohování do Azure a pak zálohování úloh DPM.


[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png

