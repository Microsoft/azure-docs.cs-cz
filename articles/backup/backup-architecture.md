---
title: Architektura služby Azure Backup
description: Obsahuje přehled architektury, komponenty a procesy používané službou Azure Backup.
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: dacurwin
ms.openlocfilehash: 7c0a1650490a863f5b3a3cf09a5500d72359e7f1
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67271999"
---
# <a name="azure-backup-architecture"></a>Architektura služby Azure Backup

Můžete použít [služby Azure Backup](backup-overview.md) zálohovat data na cloudové platformě Microsoft Azure. Tento článek shrnuje architektury Azure Backup, komponenty a procesy. 

## <a name="what-does-azure-backup-do"></a>Co dělá Azure Backup?

Azure Backup zálohuje data, stav počítače a úlohy běžící na místních počítačů a instancí virtuálních počítačů (VM) Azure. Existuje mnoho scénářů Azure Backup.

## <a name="how-does-azure-backup-work"></a>Jak funguje Azure Backup?

Můžete zálohovat počítače a data s využitím několik metod:

- **Zálohování místních počítačů**:
    - Pomocí agenta Azure Backup Microsoft Azure Recovery Services (MARS) můžete zálohovat na místních počítačích Windows přímo do Azure. Počítače s Linuxem nejsou podporovány.
    - Místních počítačů můžete zálohovat na záložní server (System Center Data Protection Manager (DPM) nebo Microsoft Azure Backup Server (MABS)). Záložní server lze pak zálohovat do trezoru služby Recovery Services v Azure.

- **Zálohování virtuálních počítačů Azure**:
    - Můžete zálohovat virtuální počítače Azure přímo. Azure Backup nainstaluje rozšíření zálohování do agenta virtuálního počítače Azure, na kterém běží na virtuálním počítači. Toto rozšíření zálohuje celý virtuální počítač.
    - Můžete zálohovat konkrétní soubory a složky na virtuálním počítači Azure spuštěním agenta MARS.
    - Virtuální počítače Azure můžete zálohovat na MABS, na kterém běží v Azure a MABS pak můžete zálohovat do trezoru služby Recovery Services.

Další informace o [co můžete zálohovat](backup-overview.md) a asi [Podporované scénáře zálohování](backup-support-matrix.md).

## <a name="where-is-data-backed-up"></a>Kde se data zálohována?

Azure Backup ukládá zálohovaná data v trezoru služby Recovery Services. Trezor je entita online úložiště v Azure, která se používá k ukládání dat, jako jsou záložní kopie, body obnovení a zásady zálohování.

Trezory služby Recovery Services mají tyto funkce:

- Trezory služby usnadňují uspořádání dat záloh a současně minimalizují režii na správu.
- V rámci předplatného Azure můžete vytvořit až 500 trezorů.
- Můžete monitorovat zálohovaných položek v trezoru, včetně virtuálních počítačů Azure a místních počítačů.
- Můžete spravovat přístup k trezoru s využitím Azure [řízení přístupu na základě role (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
- Můžete určit, jak data v trezoru se replikují pro zajištění redundance:
    - **Místně redundantní úložiště (LRS)** : K ochraně před selháním v datacentru, můžete použít LRS. LRS se replikuje data na jednotce škálování úložiště. [Další informace](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs).
    - **Geograficky redundantní úložiště (GRS)** : Pro ochranu před výjimečnými výpadky celé oblasti, můžete použít GRS. GRS replikuje vaše data do sekundární oblasti. [Další informace](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs). 
    - Ve výchozím nastavení používají GRS trezory služby Recovery Services. 

## <a name="backup-agents"></a>Agenty služby Backup

Azure Backup poskytuje různé agenty služby backup, v závislosti na tom, jaký typ počítačů během zálohování:

**Agent** | **Podrobnosti** 
--- | --- 
**Agenta MARS** | <ul><li>Běží na jednotlivých místních počítačů s Windows serverem k zálohování souborů, složek a stav systému.</li> <li>Běží na virtuálních počítačích Azure k zálohování souborů, složek a stav systému.</li> <li>Běží na serverech DPM nebo MABS zálohování na disk aplikace DPM nebo MABS místního úložiště do Azure.</li></ul> 
**Rozšíření virtuálního počítače Azure** | Běží na virtuálních počítačích Azure zálohovat do trezoru.

## <a name="backup-types"></a>Typy zálohování

Následující tabulka vysvětluje různé typy záloh a jejich použití:

**Typ zálohování** | **Podrobnosti** | **Použití**
--- | --- | ---
**Úplné** | Úplné zálohování obsahuje celý zdroj dat. Má větší šířku pásma sítě než rozdílové nebo přírůstkové zálohování. | Používá se pro prvotní zálohování.
**Rozdílové** |  Rozdílová záloha ukládá bloky, které se změnily od prvotní úplné zálohy. Používá menší množství sítě a úložiště a redundantní kopie nezměněných dat. nezachová.<br/><br/> Neefektivní, protože jsou bloky dat, které jsou beze změny mezi novější zálohy přenesená a uložená. | Nepoužívá se službou Azure Backup.
**Přírůstkové** | Přírůstkové zálohování ukládá pouze bloky dat, která se změnila od posledního zálohování. Vysokou efektivitu úložiště a sítě. <br/><br/> S přírůstkovým zálohováním není nutné k doplnění pomocí úplné zálohy. | Používají DPM nebo MABS zálohování na disk a používat v všechny zálohy do Azure.

## <a name="sql-server-backup-types"></a>Typy zálohování systému SQL Server

Následující tabulka vysvětluje různé typy zálohy používají pro databáze systému SQL Server a jak často používají:

**Typ zálohování** | **Podrobnosti** | **Použití**
--- | --- | ---
**Úplné zálohování** | Úplná záloha databáze vytvoří zálohu celé databáze. Obsahuje všechna data v konkrétní databázi nebo sadu skupiny souborů nebo souborů. Úplné zálohování obsahuje také dostatek protokoly a tato data obnovit. | Maximálně můžete aktivovat jednu úplnou zálohu denně.<br/><br/> Můžete provést úplné zálohování na denní nebo týdenní interval.
**Rozdílová záloha** | Rozdílové zálohování vychází největší zálohování všech dat aktuální a předchozí.<br/><br/> Zachytí pouze data, která se změnila od úplného zálohování. |  Maximálně můžete aktivovat jeden rozdílové zálohy za den.<br/><br/> Úplné zálohování a rozdílovou zálohu nelze konfigurovat ve stejný den.
**Zálohy transakčního protokolu** | Záloha protokolu umožňuje obnovení bodu v čase až po konkrétní sekundy. | Maximálně můžete nakonfigurovat zálohy transakčního protokolu každých 15 minut.

### <a name="comparison-of-backup-types"></a>Porovnání typy zálohování

Spotřeba úložiště (RTO) plánovaná doba obnovení a využití sítě se liší pro každý typ zálohování. Následující obrázek znázorňuje srovnání typy zálohování:

- Zdroje dat A se skládá z 10 úložiště bloků, A1 – A10, které se zálohují každý měsíc.
- Bloky A2, A3, A4 a A9 se mění první měsíc a blok A5 se mění následující měsíc.
- Pro rozdílové zálohování druhý měsíc změněné bloky A2, A3, A4 a A9 se zálohují. Třetí měsíc se znovu zálohují tyto stejné bloky, společně se změněným blokem A5. Změněné bloky se budou zálohovat až do doby, kdy dojde k dalšímu úplnému zálohování.
- Přírůstkové zálohování, druhý měsíc bloky A2, A3, A4 a A9 jsou označeny jako změnit a přenést. Třetí měsíc se označí a přenese pouze změněný blok A5. 

![Obrázek ukazující porovnání způsobů zálohování](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Funkce služby zálohování

Následující tabulka shrnuje podporované funkce pro různé typy zálohování:

**Funkce** | **Místní počítače s Windows serverem (přímo)** | **Virtuální počítače Azure** | **Počítače nebo aplikace pomocí aplikace DPM nebo MABS**
--- | --- | --- | ---
Zálohovat do trezoru | ![Ano][green] | ![Ano][green] | ![Ano][green] 
Zálohování na disk aplikace DPM nebo MABS, pak do Azure | | | ![Ano][green] 
Komprese dat odeslaných k zálohování | ![Ano][green] | Při přenosu dat se nepoužívá žádná komprese. Úložiště se mírně zvětšený, ale je rychlejší obnovení.  | ![Ano][green] 
Spustit přírůstkové zálohování |![Ano][green] |![Ano][green] |![Ano][green] 
Zálohování disků s odstraněním duplicit | | | ![Částečně][yellow]<br/><br/> Pro aplikaci DPM nebo MABS servery nasazené jen místně. 

![klíč tabulky](./media/backup-architecture/table-key.png)

## <a name="architecture-direct-backup-of-azure-vms"></a>Architektura: Přímé zálohování virtuálních počítačů Azure

1. Při povolení zálohování pro virtuální počítač Azure, zálohování se spouští podle plánu, který zadáte.
1. Při prvním zálohování zálohování rozšíření je nainstalováno na virtuálním počítači Pokud virtuální počítač je spuštěný.
    - U virtuálních počítačů s Windows je nainstalované rozšíření VMSnapshot.
    - Pro virtuální počítače s Linuxem je nainstalovaná rozšíření VMSnapshot Linuxu.
1. Rozšíření pořídí snímek úroveň úložiště. 
    - Pro Windows virtuálních počítačů, které jsou spuštěné, souřadnice zálohování se Windows Stínová kopie svazku Service (VSS) k pořízení konzistentního snímku virtuálního počítače. Ve výchozím nastavení zálohování trvá úplné zálohy stínové kopie svazku. Pokud záloha není schopen pořízení konzistentního snímku, trvá snímek konzistentní vzhledem k souboru.
    - Zálohování virtuálních počítačů s Linuxem, trvá snímek konzistentní vzhledem k souboru. Pro snímky konzistentní budete muset ručně upravit předzálohovacího nebo pozálohovacího skripty.
    - Zálohování je optimalizované zálohování každého disku virtuálního počítače paralelně. Azure Backup pro každý disk zálohování, přečte bloky na disku a uloží jenom změněná data. 
1. Poté, co se pořídí snímek, data se přenesou do trezoru. 
    - Pouze bloky dat, která se změnila od posledního zálohování jsou zkopírovány.
    - Data nejsou šifrována. Azure Backup můžete zálohovat virtuální počítače Azure, které bylo zašifrováno službou Azure Disk Encryption.
    - Data snímku nemusí být hned zkopírují do trezoru. Ve špičce zálohování může trvat několik hodin. Celkový čas zálohování pro virtuální počítač bude méně než 24 hodin denně zásady zálohování.
1. Po odeslání dat do trezoru se snímek odstraní a vytvoří bod obnovení.

Virtuální počítače Azure pro příkazy pro řízení potřebovat přístup k Internetu. Pokud zálohujete úlohy ve virtuálním počítači (například zálohování databáze serveru SQL Server), musí back endovým datům také přístup k Internetu. 

![Zálohování virtuálních počítačů Azure](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Architektura: Přímé zálohování počítačů s Windows serverem v místním nebo virtuálním počítači Azure souborů nebo složek

1. Chcete-li nastavit scénář, stáhněte a nainstalujte agenta MARS na počítači. Potom vyberete, co bude zálohováno, když se spustí zálohování a jak dlouho se bude nacházet v Azure.
1. Prvotní zálohování spustí podle nastavení zálohování.
1. Pořídit snímek bodu v čase svazky vybrané pro zálohování pomocí agenta MARS stínové kopie svazku.
    - Agenta MARS pouze operace zápisu Windows systému používá k zachycení snímku.
    - Protože agenta nepoužívá žádné aplikace zapisovače VSS, nezachycuje snímky konzistentní.
1. Po pořízení snímek služby VSS, agenta MARS vytvoří virtuální pevný disk (VHD) ve složce mezipaměti, kterou jste zadali při konfiguraci zálohování. Agent také ukládá kontrolních součtů pro každý blok dat.
1. Spouštět přírůstkové zálohování podle plánu, který jste zadali, není-li spustit zálohování ad hoc.
1. V přírůstkové zálohy změněné soubory jsou identifikovány a je vytvořen nový virtuální pevný disk. Virtuální pevný disk je komprimovaná a šifrovaná a bude odeslán do trezoru.
1. Po dokončení přírůstkového zálohování, nový virtuální pevný disk je sloučen s virtuální disk VHD vytvořený po počáteční replikaci. Tento virtuální pevný disk sloučené poskytuje nejnovější stav má být použit pro porovnání pro probíhající zálohování.

![Zálohování místních počítačů s Windows serverem pomocí agenta MARS](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Architektura: Zálohování do DPM nebo MABS

1. Nainstalujte agenta ochrany aplikace DPM nebo MABS na počítačích, které chcete chránit. Pak přidejte počítače do skupiny ochrany aplikace DPM.
    - K ochraně místní počítače, server DPM nebo MABS musí být v místním.
    - Pokud chcete chránit virtuální počítače Azure, se musí nacházet MABS server v Azure, spuštěná jako virtuální počítač Azure.
    - Pomocí aplikace DPM nebo MABS lze chránit záložní svazky, sdílené složky, soubory a složky. Taky může chránit stav systému počítač (holý počítač), a budete moci chránit konkrétní aplikace s podporou aplikace nastavení zálohování.
1. Když nastavíte ochranu pro počítače nebo aplikace v aplikaci DPM nebo MABS, vyberete zálohovat na místní disk MABS/DPM pro krátkodobé uložení a do Azure pro online ochranu. Můžete také určit, kdy se má spustit zálohování do místního úložiště DPM nebo MABS a kdy se má spustit online zálohování do Azure.
1. Vytvoření zálohy disku chráněné úlohy na lokální disky MABS/DPM souladu s plánem, které jste zadali.
4. Aplikace DPM nebo MABS disků jsou zálohovány do trezoru pomocí agenta MARS, na kterém běží na serveru aplikace DPM nebo MABS.

![Zálohování počítačů a zatížení chráněné aplikací DPM nebo MABS](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Úložiště virtuálního počítače Azure

Virtuální počítače Azure využívají disky k ukládání svůj operační systém, aplikace a data. Každý virtuální počítač Azure má aspoň dva disky: disk operačního systému a dočasný disk. Virtuální počítače Azure můžete taky nechat datových disků pro data aplikací. Disky se ukládají jako virtuální pevné disky.

- Virtuální pevné disky jsou uložené jako objekty BLOB stránky v účtech úložiště úrovně standard nebo premium v Azure:
    - **Storage úrovně Standard:** Podporu spolehlivé, úsporné disků pro virtuální počítače spuštěné úlohy, které nejsou citlivá na latenci. Storage úrovně Standard můžete použít standardní SSD (SOLID-State drive) disky nebo disky standardní jednotkou pevného disku (HDD).
    - **Storage úrovně Premium:** Podporu vysoce výkonných disků. Využívá disky SSD úrovně premium.
- Existují jiné výkonové úrovně pro disky:
    - **Standardní disk na pevný disk:** Založená na jednotkách HDD a použít pro nákladově efektivní úložiště.
    - **Disk SSD na úrovni Standard:** Kombinuje prvky SSD disky úrovně premium a standard HDD disky. Nabízí konzistentní výkon a spolehlivost než pevný disk, ale stále nákladově efektivní.
    - **Disk SSD na úrovni Premium:** Založená na jednotkách SSD a poskytuje vysoce výkonné a s nízkou latencí pro virtuální počítače, na kterých běží I intenzivních vstupně-výstupních operací.
- Disky můžou být spravovaná nebo nespravovaná:
    - **Nespravované disky:** Tradičním typem disků, které jsou používány virtuálními počítači. Pro tyto disky vytvořte si vlastní účet úložiště a ho zadat při vytváření disku. Pak budete muset zjistit, jak maximalizovat prostředky úložiště pro virtuální počítače.
    - **Spravované disky:** Azure vytvoří a spravuje účty úložiště za vás. Zadejte úroveň výkonu a velikosti disku a Azure vytvoří spravované disky za vás. Jak přidat disky a škálovat virtuální počítače, účty úložiště se postará Azure.

Další informace o diskové úložiště a typy dostupných disků pro virtuální počítače najdete v těchto článcích:

- [Spravovaných disků pro virtuální počítače s Windows v Azure](../virtual-machines/windows/managed-disks-overview.md)
- [Spravovaných disků pro virtuální počítače s Linuxem v Azure](../virtual-machines/linux/managed-disks-overview.md)
- [Typy dostupných disků pro virtuální počítače](../virtual-machines/windows/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Zálohování a obnovení virtuálních počítačů Azure díky službě premium storage 

Virtuální počítače Azure můžete zálohovat pomocí služby premium storage s Azure Backup:

- Během zálohování virtuálních počítačů se službou premium storage vytvoří služba Backup dočasné pracovní umístění s názvem *AzureBackup -* , v účtu úložiště. Velikost pracovního umístění se rovná velikosti snímku bodu obnovení.
- Ujistěte se, zda má účet premium storage dostatek volného místa pro toto dočasné pracovní umístění. [Další informace](../storage/common/storage-scalability-targets.md#premium-performance-storage-account-scale-limits). Neupravujte pracovní umístění.
- Po dokončení úlohy zálohování je pracovní umístění odstraněno.
- Cena úložiště použitého pro pracovní umístění je konzistentní s [ceny za službu storage úrovně premium](../virtual-machines/windows/disks-types.md#billing).

Při obnovení virtuálních počítačů Azure pomocí služby premium storage, můžete obnovit na premium nebo standard storage. Obvykle by obnovíte je na premium storage. Ale pokud potřebujete jenom podmnožinu souborů z virtuálního počítače, může být cenově výhodnější obnovit je do úložiště úrovně standard.

### <a name="back-up-and-restore-managed-disks"></a>Zálohování a obnovení spravovaných disků

Můžete zálohovat virtuální počítače Azure se spravovanými disky:

- Zálohujete virtuální počítače se spravovanými disky stejným způsobem, který používáte jakýkoli jiný virtuální počítač Azure. Virtuální počítač můžete zálohovat přímo z nastavení virtuálního počítače, nebo můžete povolit zálohování pro virtuální počítače v trezoru služby Recovery Services.
- Virtuální počítače na spravovaných discích můžete zálohovat prostřednictvím kolekcí RestorePoint postavených na spravovaných discích.
- Azure Backup podporuje také zálohování virtuálních počítačů se spravovanými disky, které byly šifrované pomocí Azure Disk Encryption.

Při obnovení virtuálních počítačů se spravovanými disky, můžete obnovit do kompletního virtuálního počítače se spravovanými disky nebo do účtu úložiště:

- Během procesu obnovení – se postará Azure spravované disky. Pokud používáte možnost účtu úložiště, můžete spravovat účet úložiště, který se vytvoří během procesu obnovení.
- Pokud obnovujete spravovaný virtuální počítač, který je šifrovaný, ujistěte se, že klíče Virtuálního počítače a tajné klíče v trezoru klíčů existují před zahájením procesu obnovení.

## <a name="next-steps"></a>Další postup

- Zkontrolovat v matici podpory, aby [Další informace o podporovaných funkcích a omezení pro scénáře zálohování](backup-support-matrix.md).
- Nastavení zálohování pro jeden z těchto scénářů:
    - [Zálohování virtuálních počítačů Azure](backup-azure-arm-vms-prepare.md).
    - [Zálohování počítačů Windows přímo](tutorial-backup-windows-server-to-azure.md), bez zálohování serveru.
    - [Nastavit MABS](backup-azure-microsoft-azure-backup.md) pro zálohování do Azure a pak znovu úloh rozšiřování MABS.
    - [Nastavení DPM](backup-azure-dpm-introduction.md) pro zálohování do Azure a pak zálohování úloh DPM.


[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png

