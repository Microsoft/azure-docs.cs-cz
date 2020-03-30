---
title: Přehled architektury
description: Poskytuje přehled architektury, komponent a procesů používaných službou Azure Backup.
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: b093c6702bb26fe537622727fe1b623141bf4160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273615"
---
# <a name="azure-backup-architecture-and-components"></a>Architektura a komponenty Azure Backup

[Službu Azure Backup](backup-overview.md) můžete použít k zálohování dat na cloudovou platformu Microsoft Azure. Tento článek shrnuje architekturu, součásti a procesy azure backupu.

## <a name="what-does-azure-backup-do"></a>K čemu funguje Azure Backup?

Azure Backup zálohuje data, stav počítače a úlohy spuštěné na místních počítačích a instancích virtuálních počítačů Azure.Azure Backup backup s a Existuje celá řada scénářů zálohování Azure.

## <a name="how-does-azure-backup-work"></a>Jak azure zálohování funguje?

Počítače a data můžete zálohovat pomocí několika metod:

- **Zálohování místních strojů**:
  - Místní počítače s Windows můžete zálohovat přímo do Azure pomocí agenta Azure Backup Microsoft Azure Recovery Services (MARS). Linuxové počítače nejsou podporovány.
  - Místní počítače můžete zálohovat na záložní server – buď Správce ochrany dat system center (DPM), nebo Microsoft Azure Backup Server (MABS). Záložní server pak můžete zálohovat do trezoru služby Recovery Services v Azure.

- **Zálohování virtuálních počítačů Azure:**
  - Virtuální počítače Azure můžete zálohovat přímo. Azure Backup nainstaluje rozšíření zálohy pro agenta virtuálního počítače Azure, který běží na virtuálním počítači. Toto rozšíření zálohuje celý virtuální ms.
  - Můžete zálohovat konkrétní soubory a složky na virtuálním počítači Azure spuštěním agenta MARS.
  - Virtuální počítače Azure můžete zálohovat na mabs, který běží v Azure, a pak můžete zálohovat MABS do trezoru služby Recovery Services.

Přečtěte si další informace o [tom, co můžete zálohovat,](backup-overview.md) a o [podporovaných scénářích zálohování](backup-support-matrix.md).

## <a name="where-is-data-backed-up"></a>Kde jsou zálohována data?

Azure Backup ukládá zálohovaná data do trezoru služby Recovery Services. Trezor je entita online úložiště v Azure, která se používá k ukládání dat, jako jsou záložní kopie, body obnovení a zásady zálohování.

Trezory služby Recovery Services mají následující funkce:

- Trezory usnadňují uspořádání záložních dat a minimalizují režii správy.
- V každém předplatném Azure můžete vytvořit až 500 trezorů.
- Zálohované položky můžete sledovat v trezoru, včetně virtuálních počítačů Azure a místních počítačů.
- Přístup k trezoru můžete spravovat pomocí řízení přístupu na základě rolí Azure [(RBAC).](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
- Můžete určit způsob replikace dat v úložišti pro redundanci:
  - **Místně redundantní úložiště (LRS):** Chcete-li chránit před selháním v datovém centru, můžete použít LRS. LRS replikuje data do jednotky škálování úložiště. [Další informace](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs).
  - **Geograficky redundantní úložiště (GRS): Chcete-li**chránit před výpadky v celé oblasti, můžete použít GRS. GRS replikuje data do sekundární oblasti. [Další informace](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).
  - Ve výchozím nastavení používají trezory služby Recovery Services služby GRS.

## <a name="backup-agents"></a>Agenti zálohování

Azure Backup poskytuje různé agenty zálohování v závislosti na tom, jaký typ počítače se zálohuje:

**Agent** | **Podrobnosti**
--- | ---
**Agent MARS** | <ul><li>Běží na jednotlivých místních počítačích se systémem Windows Server pro zálohování souborů, složek a stavu systému.</li> <li>Běží na virtuálních počítačích Azure pro zálohování souborů, složek a stavu systému.</li> <li>Běží na serverech DPM/MABS pro zálohování disku místního úložiště DPM/MABS do Azure.</li></ul>
**Rozšíření virtuálního počítače Azure** | Běží na virtuálních počítačích Azure a zálohovat je do trezoru.

## <a name="backup-types"></a>Typy zálohování

Následující tabulka vysvětluje různé typy záloh a při jejich použití:

**Typ zálohy** | **Podrobnosti** | **Použití**
--- | --- | ---
**Do bloku** | Úplná záloha obsahuje celý zdroj dat. Trvá větší šířku pásma sítě než rozdílové nebo přírůstkové zálohy. | Používá se pro počáteční zálohování.
**Diferenciální** |  Rozdílová záloha ukládá bloky, které se změnily od počáteční úplné zálohy. Používá menší množství sítě a úložiště a neuchovává redundantní kopie nezměněných dat.<br/><br/> Neefektivní, protože datové bloky, které jsou nezměněny mezi pozdější zálohy jsou přeneseny a uloženy. | Služba Azure Backup se nepoužívá.
**Přírůstkový** | Přírůstkové zálohování ukládá pouze bloky dat, které se změnily od předchozí zálohy. Vysoká efektivita úložiště a sítě. <br/><br/> S přírůstkovým zálohováním není třeba doplňovat úplné zálohy. | Používá DPM/MABS pro zálohování disků a používá se ve všech zálohách do Azure. Nepoužívá se pro zálohování serveru SQL Server.

## <a name="sql-server-backup-types"></a>Typy zálohování serveru SQL Server

Následující tabulka vysvětluje různé typy záloh používaných pro databáze serveru SQL Server a jak často se používají:

**Typ zálohy** | **Podrobnosti** | **Použití**
--- | --- | ---
**Úplné zálohování** | Úplná záloha databáze zálohuje celou databázi. Obsahuje všechna data v určité databázi nebo v sadě filegroups nebo souborů. Úplná záloha také obsahuje dostatek protokolů k obnovení dat. | Maximálně můžete spustit jednu úplnou zálohu denně.<br/><br/> Můžete si vybrat, zda chcete vytvořit úplnou zálohu v denním nebo týdenním intervalu.
**Rozdílové zálohování** | Rozdílová záloha je založena na nejnovější, předchozí zálohování plných dat.<br/><br/> Zachycuje pouze data, která se změnila od úplné zálohy. |  Maximálně můžete spustit jednu rozdílovou zálohu za den.<br/><br/> Nelze nakonfigurovat úplnou zálohu a rozdílovou zálohu ve stejný den.
**Zálohování transakčního protokolu** | Záloha protokolu umožňuje obnovení v čase až do určité sekundy. | Maximálně můžete nakonfigurovat transakční zálohy protokolu každých 15 minut.

### <a name="comparison-of-backup-types"></a>Porovnání typů záloh

Spotřeba úložiště, cíl doby obnovení (RTO) a spotřeba sítě se liší pro každý typ zálohy. Následující obrázek znázorňuje porovnání typů záloh:

- Zdroj dat A se skládá z 10 bloků úložiště A1-A10, které jsou zálohovány měsíčně.
- Bloky A2, A3, A4 a A9 se mění první měsíc a blok A5 se mění následující měsíc.
- Pro rozdílové zálohy, ve druhém měsíci jsou zálohovány změněné bloky A2, A3, A4 a A9. Třetí měsíc se znovu zálohují tyto stejné bloky, společně se změněným blokem A5. Změněné bloky se budou zálohovat až do doby, kdy dojde k dalšímu úplnému zálohování.
- Pro přírůstkové zálohy jsou ve druhém měsíci bloky A2, A3, A4 a A9 označeny jako změněné a přenesené. Třetí měsíc se označí a přenese pouze změněný blok A5.

![Obrázek znázorňující porovnání metod zálohování](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Funkce zálohování

Následující tabulka shrnuje podporované funkce pro různé typy zálohování:

**Funkce** | **Přímé zálohování souborů a složek (pomocí agenta MARS)** | **Zálohování virtuálních počítačových společností Azure** | **Stroje nebo aplikace s DPM/MABS**
--- | --- | --- | ---
Zálohování do trezoru | ![Ano][green] | ![Ano][green] | ![Ano][green]
Zálohování na disk DPM/MABS a potom na Azure | | | ![Ano][green]
Komprese dat odeslaných pro zálohování | ![Ano][green] | Při přenosu dat se nepoužívá žádná komprese. Skladování je mírně nafouknuto, ale obnova je rychlejší.  | ![Ano][green]
Spustit přírůstkové zálohování |![Ano][green] |![Ano][green] |![Ano][green]
Zálohování deduplikovaných disků | | | ![Částečně][yellow]<br/><br/> Pouze pro servery DPM/MABS nasazené místně.

![Klíč tabulky](./media/backup-architecture/table-key.png)

## <a name="backup-policy-essentials"></a>Základy zásad zálohování

- Pro každý trezor je vytvořena zásada zálohování.
- Zásady zálohování lze vytvořit pro zálohování následujících úloh
  - Virtuální počítač Azure
  - SQL ve virtuálním počítači Azure
  - Sdílená složka Azure
- Zásadu lze přiřadit k mnoha zdrojům. Zásady zálohování virtuálních počítačů Azure se můžou použít k ochraně mnoha virtuálních počítačích Azure.
- Zásada se skládá ze dvou složek
  - Plán: Kdy provést zálohu
  - Uchovávání: Jak dlouho by měla být zachována každá záloha.
- Plán lze definovat jako "denní" nebo "týdenní" s určitým časovým bodem.
- Uchovávání lze definovat pro "denní", "týdenní", "měsíční", "roční" záložní body.
- "týdně" označuje zálohu v určitý den v týdnu, "měsíčně" se rozumí záloha v určitý den v měsíci a "roční" se vztahuje k zálohování v určitý den v roce.
- Uchovávání pro "měsíční", "roční" záložní body se označuje jako "LongTermRetention".
- Když je vytvořen trezor, vytvoří se taky zásada pro zálohy virtuálních počítačích Azure s názvem DefaultPolicy a dá se použít k zálohování virtuálních počítačů Azure.

## <a name="architecture-built-in-azure-vm-backup"></a>Architektura: Integrovaná záloha virtuálních počítačích Azure

1. Když povolíte zálohování pro virtuální počítač Azure, záloha se spustí podle zadaného plánu.
1. Během první zálohy rozšíření zálohování je nainstalován na virtuálním počítači, pokud je spuštěnvirtuální počítač.
    - Pro virtuální počítače se systémem Windows je nainstalována rozšíření VMSnapshot.
    - Pro virtuální počítače s Linuxem je nainstalováno rozšíření VMSnapshot Linux.
1. Rozšíření trvá snímek na úrovni úložiště.
    - Pro spuštěné virtuální počítače se systémem Windows, které jsou spuštěny zálohování souřadnice se službou Windows Volume Shadow Copy Service (VSS) pořídit snímek konzistentní s aplikací virtuálního počítače. Ve výchozím nastavení záloha přebírá úplné zálohy VSS. Pokud backup nemůže pořizovat snímek konzistentní s aplikací, pořídí snímek konzistentní se souborem.
    - U virtuálních počítačů s Linuxem pořídí aplikace Backup snímek konzistentní se soubory. U snímků konzistentních s aplikací je třeba ručně přizpůsobit skripty před a po.
    - Zálohování je optimalizováno paralelním zálohováním jednotlivých disků virtuálního počítače. Pro každý disk zálohovaný Azure Backup přečte bloky na disku a ukládá pouze změněná data.
1. Po pořízení snímku jsou data přenesena do úložiště.
    - Jsou zkopírovány pouze bloky dat, které se změnily od poslední zálohy.
    - Data nejsou zašifrovaná. Azure Backup můžou zálohovat virtuální počítače Azure, které byly zašifrované pomocí Azure Disk Encryption.
    - Data snímku nemusí být okamžitě zkopírována do úložiště. Ve špičce může zálohování trvat několik hodin. Celkový čas zálohování pro virtuální počítače bude kratší než 24 hodin pro denní zásady zálohování.
1. Po odeslání dat do úložiště je vytvořen bod obnovení. Ve výchozím nastavení jsou snímky zachovány po dobu dvou dnů před jejich odstraněním. Tato funkce umožňuje obnovení operace z těchto snímků, a tím snížit časy obnovení. Zkracuje čas potřebný k transformaci a kopírování dat zpět z úložiště. Viz [Funkce okamžitého obnovení azure.](https://docs.microsoft.com/azure/backup/backup-instant-restore-capability)

K zálohování virtuálních počítačů Azure nemusíte explicitně povolit připojení k internetu.

![Zálohování virtuálních počítačů Azure](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Architektura: Přímé zálohování místních počítačů se systémem Windows Server nebo souborů nebo složek virtuálních počítačů Azure

1. Chcete-li nastavit scénář, stáhněte a nainstalujte agenta MARS do počítače. Pak vyberete, co chcete zálohovat, kdy budou zálohy spuštěny a jak dlouho budou v Azure uchovávány.
1. Počáteční záloha se spustí podle nastavení zálohování.
1. Agent MARS používá VSS pořizovat snímek bodu v čase svazků vybraných pro zálohování.
    - Agent MARS používá k zachycení snímku pouze operaci zápisu systému Windows.
    - Vzhledem k tomu, že agent nepoužívá žádné aplikace VSS zapisovače, nezachycuje snímky konzistentní s aplikací.
1. Po pořízení snímku pomocí služby VSS vytvoří agent MARS virtuální pevný disk (VHD) ve složce mezipaměti, kterou jste zadali při konfiguraci zálohy. Agent také ukládá kontrolní součty pro každý datový blok.
1. Přírůstkové zálohy spustit podle plánu, který zadáte, pokud spustíte zálohování na vyžádání.
1. V přírůstkové zálohy změněné soubory jsou identifikovány a nový virtuální pevný disk je vytvořen. Virtuální pevný disk je komprimovaný a šifrovaný a pak je odeslán do trezoru.
1. Po dokončení přírůstkové zálohy se nový virtuální pevný disk sloučí s virtuálním pevným diskem vytvořeným po počáteční replikaci. Tento sloučený virtuální pevný disk poskytuje nejnovější stav, který se má použít pro porovnání pro průběžné zálohování.

![Zálohování místních počítačů se systémem Windows Server s agentem MARS](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Architektura: Zálohování na DPM/MABS

1. Agent ochrany DPM nebo MABS nainstalujete do počítačů, které chcete chránit. Potom přidáte počítače do skupiny ochrany DPM.
    - Chcete-li chránit místní počítače, musí být server DPM nebo MABS umístěn místně.
    - K ochraně virtuálních počítačů Azure musí být server MABS umístěn v Azure a běží jako virtuální počítač Azure.
    - Pomocí dpm/MABS můžete chránit záložní svazky, sdílené složky, soubory a složky. Můžete také chránit stav systému (holý kov) a můžete chránit konkrétní aplikace pomocí nastavení zálohování podle aplikace.
1. Když nastavíte ochranu pro počítač nebo aplikaci v DPM/MABS, vyberete zálohování na místní disk MABS/DPM pro krátkodobé úložiště a do Azure pro ochranu online. Můžete také určit, kdy by měla být spuštěna záloha do místního úložiště DPM/MABS a kdy by měla být spuštěna online záloha do Azure.
1. Disk chráněnéúlohy je zálohován na místní disky MABS/DPM podle zadaného plánu.
1. Disky DPM/MABS jsou zálohovány do trezoru agentem MARS, který je spuštěn na serveru DPM/MABS.

![Zálohování počítačů a úloh chráněných dpm nebo MABS](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Úložiště virtuálních zařízení Azure

Virtuální počítače Azure používají disky k ukládání operačního systému, aplikací a dat. Každý virtuální počítač Azure má alespoň dva disky: disk pro operační systém a dočasný disk. Virtuální počítače Azure můžou mít taky datové disky pro data aplikací. Disky jsou uloženy jako Virtuální pevné disky.

- Virtuální počítače se ukládají jako objekty BLOB stránky ve standardních nebo prémiových účtech úložiště v Azure:
  - **Standardní skladování:** Spolehlivá a nízkonákladová podpora disků pro virtuální počítače s spuštěnými úlohami, které nejsou citlivé na latenci. Standardní úložiště může používat standardní disky ssd disků nebo standardní pevné disky (HDD).
  - **Prémiové úložiště:** Podpora vysoce výkonného disku. Používá prémiové disky SSD.
- Existují různé úrovně výkonu pro disky:
  - **Standardní disk hdd:** S pomocí pevných disků a používá se pro nákladově efektivní úložiště.
  - **Standardní Disk SSD:** Kombinuje prvky prémiových Disků SSD a standardních disků HDD. Nabízí konzistentnější výkon a spolehlivost než hdd, ale stále nákladově efektivní.
  - **Disk Premium SSD:** Zálohované ssd disy a poskytuje vysoký výkon a nízkou latenci pro virtuální servery, které jsou spuštěny úlohy náročné na vstupně-výstupní chod.
- Disky lze spravovat nebo nespravovat:
  - **Nespravované disky:** Tradiční typ disků používaných virtuálními počítačemi. Pro tyto disky vytvoříte vlastní účet úložiště a zadáte jej při vytváření disku. Pak musíte zjistit, jak maximalizovat prostředky úložiště pro vaše virtuální počítače.
  - **Spravované disky:** Azure vytváří a spravuje účty úložiště za vás. Zadáte velikost disku a úroveň výkonu a Azure vytvoří spravované disky pro vás. Při přidávání disků a škálování virtuálních počítačů azure zpracovává účty úložiště.

Další informace o úložišti disku a dostupných typech disků pro virtuální počítače najdete v těchto článcích:

- [Spravované disky Azure pro virtuální počítače s Windows](../virtual-machines/windows/managed-disks-overview.md)
- [Spravované disky Azure pro virtuální počítače s Linuxem](../virtual-machines/linux/managed-disks-overview.md)
- [Dostupné typy disků pro virtuální počítače](../virtual-machines/windows/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Zálohování a obnovení virtuálních počítačů Azure pomocí prémiového úložiště

Virtuální počítače Azure můžete zálohovat pomocí úložiště úrovně Premium s Azure Backup:

- Během procesu zálohování virtuálních počítačů s úložištěm premium vytvoří služba Backup dočasné pracovní umístění s názvem *AzureBackup-*, v účtu úložiště. Velikost pracovní umístění se rovná velikosti snímku bodu obnovení.
- Ujistěte se, že účet úložiště premium má dostatek volného místa pro dočasné pracovní umístění. Další informace najdete v tématu [Škálovatelnost cíle pro účty úložiště objektů blob stránky premium](../storage/blobs/scalability-targets-premium-page-blobs.md). Neupravujte pracovní umístění.
- Po dokončení úlohy zálohování je pracovní umístění odstraněno.
- Cena úložiště používaná pro pracovní umístění je konzistentní s [cenami úložiště premium](../virtual-machines/windows/disks-types.md#billing).

Když obnovíte virtuální počítače Azure pomocí úložiště premium, můžete je obnovit do prémiového nebo standardního úložiště. Obvykle byste je obnovili do úložiště premium. Ale pokud potřebujete jenom podmnožinu souborů z virtuálního zařízení, může být nákladově efektivní obnovit je do standardního úložiště.

### <a name="back-up-and-restore-managed-disks"></a>Zálohování a obnovení spravovaných disků

Virtuální počítače Azure můžete zálohovat pomocí spravovaných disků:

- Virtuální počítače zálohovat se spravovanými disky stejným způsobem, jako to udělat všechny ostatní virtuální počítače Azure. Virtuální počítač můžete zálohovat přímo z nastavení virtuálního počítače nebo můžete povolit zálohování virtuálních počítačů v trezoru služby Recovery Services.
- Virtuální počítače na spravovaných discích můžete zálohovat prostřednictvím kolekcí RestorePoint postavených na spravovaných discích.
- Azure Backup také podporuje zálohování virtuálních počítačů se spravovanými disky, které byly zašifrované pomocí Azure Disk Encryption.

Když obnovíte virtuální počítače se spravovanými disky, můžete obnovit na úplný virtuální účet se spravovanými disky nebo na účet úložiště:

- Během procesu obnovení Azure zpracovává spravované disky. Pokud používáte možnost účtu úložiště, spravujete účet úložiště, který se vytvořil během procesu obnovení.
- Pokud obnovíte zašifrovaný spravovaný virtuální virtuální ms, ujistěte se, že klíče a tajné klíče virtuálního aplikace existují v trezoru klíčů před zahájením procesu obnovení.

## <a name="next-steps"></a>Další kroky

- Projděte si matici podpory, kde [najdete informace o podporovaných funkcích a omezeních pro scénáře zálohování](backup-support-matrix.md).
- Nastavte zálohu pro jeden z těchto scénářů:
  - [Záloha virtuálních počítačích Azure](backup-azure-arm-vms-prepare.md).
  - [Zálohujte počítače se systémem Windows přímo](tutorial-backup-windows-server-to-azure.md)bez záložního serveru.
  - [Nastavte MABS](backup-azure-microsoft-azure-backup.md) pro zálohování do Azure a pak zálohovat úlohy mabs.
  - [Nastavte dpm](backup-azure-dpm-introduction.md) pro zálohování do Azure a pak zálohujte úlohy na DPM.

[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png
