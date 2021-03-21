---
title: Informace o zálohování virtuálních počítačů Azure
description: V tomto článku se dozvíte, jak služba Azure Backup zálohuje virtuální počítače Azure a jak postupovat podle osvědčených postupů.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 691fe991ad141696c0c68e915d7225001a1befd0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98733566"
---
# <a name="an-overview-of-azure-vm-backup"></a>Přehled zálohování virtuálních počítačů Azure

Tento článek popisuje, jak [služba Azure Backup](./backup-overview.md) zálohuje virtuální počítače Azure (VM).

Azure Backup poskytuje nezávislé a izolované zálohy, které chrání před nezamýšleným zničením dat na virtuálních počítačích. Zálohy jsou uloženy v trezoru služby Recovery Services s integrovanou správou bodů obnovení. Konfigurace a škálování jsou jednoduché, zálohy jsou optimalizované a obnovení je možné provádět snadno a podle potřeby.

V rámci procesu zálohování [se bere snímek](#snapshot-creation)a data se přenesou do trezoru Recovery Services bez dopadu na produkční úlohy. Snímek poskytuje různé úrovně konzistence, jak je popsáno [zde](#snapshot-consistency).

Azure Backup také obsahuje specializované nabídky pro databázové úlohy, jako jsou [SQL Server](backup-azure-sql-database.md) a [SAP HANA](sap-hana-db-about.md) , které pracují s úlohou, nabízí 15 minut RPO (cíl bodu obnovení) a povoluje zálohování a obnovení jednotlivých databází.

## <a name="backup-process"></a>Proces zálohování

Toto je postup, kterým Azure Backup provádí zálohování virtuálních počítačů Azure:

1. Pro virtuální počítače Azure, které jsou vybrané pro zálohování, Azure Backup spustí úlohu zálohování podle zadaného plánu zálohování.
1. Při prvním zálohování se na virtuálním počítači nainstaluje rozšíření zálohování, pokud je virtuální počítač spuštěný.
    - Pro virtuální počítače s Windows se nainstaluje [rozšíření VMSnapshot](../virtual-machines/extensions/vmsnapshot-windows.md) .
    - Pro virtuální počítače se systémem Linux se nainstaluje [rozšíření VMSnapshotLinux](../virtual-machines/extensions/vmsnapshot-linux.md) .
1. Pro virtuální počítače s Windows, na kterých běží, se zaregistrují služby Windows služba Stínová kopie svazku (VSS), aby se snímek virtuálního počítače konzistentní vzhledem k aplikacím vybral.
    - Ve výchozím nastavení provádí zálohování úplné zálohy VSS.
    - Pokud zálohování nemůže pořídit snímek konzistentní vzhledem k aplikacím, pak bude mít snímek konzistentní se souborem základního úložiště (protože při zastavení virtuálního počítače nedochází k žádným zápisům aplikací).
1. Pro virtuální počítače se systémem Linux aplikace Backup provede zálohu konzistentní se souborem. U snímků konzistentních vzhledem k aplikacím je nutné ručně přizpůsobit skripty před/po.
1. Po zálohování získá snímek data do trezoru.
    - Zálohování se optimalizuje tak, že se všechny disky virtuálních počítačů zálohují paralelně.
    - Služba Azure Backup přečte u každého zálohovaného disku bloky na disku a identifikuje a přenese pouze bloky dat, které se od předchozího zálohování změnily (rozdíl).
    - Data snímku se nemusí do trezoru zkopírovat okamžitě. Může to trvat několik hodin v době špičky. Celková doba zálohování virtuálního počítače bude u zásad denního zálohování menší než 24 hodin.
1. Změny provedené na virtuálním počítači s Windows po Azure Backup jsou zapnuté:
    - Microsoft Visual C++ 2013 Redistributable (x64) – na virtuálním počítači je nainstalovaná 12.0.40660.
    - Typ spuštění služby Stínová kopie svazku (VSS) se změnil na automatické z ručního.
    - Přidaná služba systému Windows IaaSVmProvider

1. Po dokončení přenosu dat se snímek odebere a vytvoří se bod obnovení.

![Architektura zálohování virtuálních počítačů Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encryption-of-azure-vm-backups"></a>Šifrování záloh virtuálních počítačů Azure

Když zálohujete virtuální počítače Azure pomocí Azure Backup, jsou virtuální počítače v klidovém stavu šifrované pomocí Šifrování služby Storage (SSE). Azure Backup taky můžou zálohovat virtuální počítače Azure, které jsou zašifrované pomocí Azure Disk Encryption.

**Šifrování** | **Podrobnosti** | **Podpora**
--- | --- | ---
**SSE** | Pomocí SSE Azure Storage poskytuje šifrování v klidovém prostředí tím, že před uložením automaticky šifruje data. Azure Storage také dešifruje data před jejich načtením. Azure Backup podporuje zálohování virtuálních počítačů se dvěma typy Šifrování služby Storage:<li> **SSE s klíči spravovanými platformou**: Toto šifrování je ve výchozím nastavení pro všechny disky ve vašich virtuálních počítačích. Další informace najdete [tady](../virtual-machines/disk-encryption.md#platform-managed-keys).<li> **SSE s použitím klíčů spravovaných zákazníkem**. Pomocí CMK můžete spravovat klíče používané k šifrování disků. Další informace najdete [tady](../virtual-machines/disk-encryption.md#customer-managed-keys). | Azure Backup používá SSE pro šifrování virtuálních počítačů Azure v klidovém prostředí.
**Azure Disk Encryption** | Azure Disk Encryption šifruje operační systém a datové disky pro virtuální počítače Azure.<br/><br/> Azure Disk Encryption se integruje s šifrovacími klíči BitLockeru (BEKs), které jsou v trezoru klíčů zabezpečené jako tajné klíče. Azure Disk Encryption se taky integruje s klíči šifrovacího klíče Azure Key Vault (KEK). | Azure Backup podporuje zálohování spravovaných a nespravovaných virtuálních počítačů Azure šifrovaných jenom pomocí BEKs nebo s BEKs společně s KEK.<br/><br/> BEKs i KEK se zálohují a šifrují.<br/><br/> Vzhledem k tomu, že se zálohují KEK a BEKs, můžou uživatelé s potřebnými oprávněními v případě potřeby obnovit klíče a tajné kódy zpátky do trezoru klíčů. Tito uživatelé můžou také obnovit zašifrovaný virtuální počítač.<br/><br/> Šifrované klíče a tajné kódy nejde číst neoprávněnými uživateli nebo Azure.

U spravovaných a nespravovaných virtuálních počítačů Azure podporuje zálohování jak virtuální počítače zašifrované jenom s BEKs, nebo virtuální počítače zašifrované pomocí BEKs společně s KEK.

Zálohované BEKs (tajné klíče) a KEK (klíče) jsou šifrované. Dají se číst a používat jenom v případě, že se obnoví zpátky do trezoru klíčů autorizovanými uživateli. Neoprávnění uživatelé nebo Azure můžou číst nebo používat zálohované klíče nebo tajné klíče.

BEKs se také zálohují. Takže pokud dojde ke ztrátě BEKs, autorizovaní uživatelé můžou obnovit BEKs do trezoru klíčů a obnovit šifrované virtuální počítače. Pouze uživatelé s potřebnou úrovní oprávnění mohou zálohovat a obnovovat šifrované virtuální počítače nebo klíče a tajné kódy.

## <a name="snapshot-creation"></a>Vytvoření snímku

Azure Backup převezme snímky podle plánu zálohování.

- **Virtuální počítače s Windows:** V případě virtuálních počítačů s Windows se služba Backup koordinuje se stínovou kopií svazku, aby vybrala snímek konzistentní vzhledem k aplikacím na discích virtuálních počítačů.  Ve výchozím nastavení Azure Backup provede úplnou zálohu služby VSS (při zálohování se zkrátí protokoly aplikací, jako je SQL Server v době zálohování, aby se získala záloha s konzistentním nastavením úrovně aplikace).  Pokud používáte databázi SQL Server v zálohování virtuálních počítačů Azure, můžete změnit nastavení tak, aby se zabralo zálohování kopírováním STÍNových kopií (pro zachování protokolů). Další informace najdete v [tomto článku](./backup-azure-vms-troubleshoot.md#troubleshoot-vm-snapshot-issues).

- **Virtuální počítače se systémem Linux:** Aby bylo možné využívat snímky virtuálních počítačů se systémem Linux konzistentní vzhledem k aplikacím, použijte k zápisu vlastních skriptů k zajištění konzistence architekturu předzálohovacího a pozálohovacího skriptu pro Linux.

  - Azure Backup Vyvolá pouze přednastavené skripty, které jste napsali.
  - Pokud se předzálohovací a pozálohovací skripty úspěšně spustí, Azure Backup označí bod obnovení jako konzistentní s aplikací. Pokud však používáte vlastní skripty, jste nakonec odpovědni za konzistenci aplikací.
  - [Přečtěte si další informace](backup-azure-linux-app-consistent.md) o tom, jak nakonfigurovat skripty.

## <a name="snapshot-consistency"></a>Konzistence snímků

Následující tabulka vysvětluje různé typy konzistence snímků:

**Snímek** | **Podrobnosti** | **Obnovovací** | **Aspekty**
--- | --- | --- | ---
**Konzistentní vzhledem k aplikacím** | Zálohování konzistentní s aplikací zaznamenává obsah paměti a nedokončené vstupně-výstupní operace. Snímky konzistentní vzhledem k aplikacím používají zapisovač VSS (nebo předzálohovací skripty pro Linux), aby se zajistila konzistence dat aplikace před tím, než dojde k zálohování. | Při obnovování virtuálního počítače pomocí snímku konzistentního vzhledem k aplikacím se spustí virtuální počítač. Nedošlo k žádnému poškození nebo ztrátě dat. Aplikace se spustí v konzistentním stavu. | Windows: Všechny zapisovače VSS byly úspěšné.<br/><br/> Linux: jsou nakonfigurovány a úspěšně spouštěny skripty před prodejem.
**Konzistentní se systémem souborů** | Zálohy konzistentní se systémem souborů poskytují konzistenci tím, že pořizuje všechny soubory ve stejnou dobu.<br/><br/> | Při obnovování virtuálního počítače pomocí snímku konzistentního se systémem souborů se spustí virtuální počítač. Nedošlo k žádnému poškození nebo ztrátě dat. Aplikace potřebují implementovat vlastní mechanizmus "opravování", aby se zajistila konzistence obnovených dat. | Windows: některé zapisovače VSS selhaly. <br/><br/> Linux: výchozí (pokud nejsou nakonfigurovány nebo se nezdařily skripty před prodejem)
**Konzistentní vzhledem k selháním** | K snímkům konzistentním se selháním obvykle dochází v případě, že se virtuální počítač Azure vypíná v době zálohování. Budou zachycena a zálohována pouze data, která na disku již existují v době zálohování. | Spustí spouštěcí proces virtuálního počítače následovaný kontrolou disku, aby opravil chyby poškození. Veškerá data v paměti nebo operace zápisu, které nebyly přeneseny na disk před ztrátou chyby. Aplikace implementují svá vlastní ověření dat. Databázová aplikace může například použít svůj transakční protokol k ověření. Pokud transakční protokol obsahuje položky, které nejsou v databázi, software databáze zahrne transakce zpět, dokud nebudou data konzistentní. | Virtuální počítač je ve stavu vypnutí (zastaveno/zrušeno přidělení).

>[!NOTE]
> Pokud je stav zřizování **úspěšný**, Azure Backup provede zálohování konzistentní vzhledem k systému souborů. Pokud stav zřizování není **k dispozici** nebo **se nezdařil**, jsou pořízeny zálohy konzistentní vzhledem k havárii. Pokud stav zřizování **vytváříte** nebo **odstraňujete**, znamená to, že Azure Backup opakuje operace.

## <a name="backup-and-restore-considerations"></a>Důležité informace o zálohování a obnovení

**Aspekty** | **Podrobnosti**
--- | ---
**Disk** | Zálohování disků virtuálních počítačů je paralelní. Pokud například virtuální počítač obsahuje čtyři disky, Služba Backup se pokusí zálohovat všechny čtyři disky paralelně. Zálohování je přírůstkové (pouze změněná data).
**Plánování** |  Pro snížení zátěže zálohování zálohujte různé virtuální počítače v různou dobu a ujistěte se, že se časy nepřekrývají. Zálohování virtuálních počítačů ve stejnou dobu způsobuje blokování provozu.
**Příprava záloh** | Mějte na paměti čas potřebný k přípravě zálohy. Doba přípravy zahrnuje instalaci nebo aktualizaci rozšíření zálohování a aktivaci vytvoření snímku podle plánu zálohování.
**Přenos dat** | Zvažte dobu potřebnou pro Azure Backup k identifikaci přírůstkových změn z předchozí zálohy.<br/><br/> Při přírůstkovém zálohování služba Azure Backup určuje změny tím, že počítá kontrolní součet bloku. Pokud se blok změní, označí se pro přesun do trezoru. Služba analyzuje identifikované bloky a pokouší se dále minimalizovat objem přenášených dat. Po vyhodnocení všech změněných bloků služba Azure Backup přenese změny do trezoru.<br/><br/> Mezi pořízením snímku a jeho zkopírováním do trezoru může dojít k prodlevě. V časech špičky může trvat až osm hodin, než se snímky přenesou do trezoru. V případě denního zálohování bude čas zálohování virtuálních počítačů kratší než 24 hodin.
**Prvotní zálohování** | Přestože je celková doba zálohování v případě přírůstkového zálohování kratší než 24 hodin, u prvního zálohování to platit nemusí. Doba potřebná k prvotnímu zálohování bude záviset na velikosti dat a době zpracování zálohování.
**Obnovit frontu** | Azure Backup procesy obnovují úlohy z více účtů úložiště ve stejnou dobu a zařadí požadavky na obnovení do fronty.
**Obnovit kopii** | Během procesu obnovení se data zkopírují z trezoru do účtu úložiště.<br/><br/> Celková doba obnovení závisí na vstupně-výstupních operacích za sekundu (IOPS) a propustnosti účtu úložiště.<br/><br/> Pokud chcete zkrátit dobu kopírování, vyberte účet úložiště, který není načtený s jinými zápisy a čteními aplikace.

### <a name="backup-performance"></a>Výkon zálohování

Tyto běžné scénáře mohou ovlivnit celkovou dobu zálohování:

- **Přidává se nový disk do chráněného virtuálního počítače Azure:** Pokud dojde k přírůstkové zálohování virtuálního počítače a přidání nového disku, čas zálohování se zvýší. Celková doba zálohování může být poslední více než 24 hodin kvůli počáteční replikaci nového disku a rozdílové replikaci stávajících disků.
- **Fragmentované disky:** Operace zálohování jsou rychlejší, když jsou změny disků souvislé. Pokud jsou změny rozloženy a rozděleny na disk, bude zálohování pomalejší.
- Změny **disku:** Pokud chráněné disky s přírůstkovým zálohováním docházejí z provozu s více než 200 GB, zálohování může trvat dlouhou dobu (více než osm hodin), než se dokončí.
- **Verze zálohy:** Nejnovější verze služby Backup (známá jako verze okamžitého obnovení) využívá více optimalizovaného procesu než porovnání kontrolního součtu k identifikaci změn. Pokud ale používáte rychlé obnovení a odstranili jste snímek zálohy, zálohování se přepne na porovnání kontrolního součtu. V tomto případě bude operace zálohování delší než 24 hodin (nebo selhání).

### <a name="restore-performance"></a>Obnovit výkon

Tyto běžné scénáře mohou ovlivnit celkovou dobu obnovení:

- Celková doba obnovení závisí na vstupně-výstupních operacích za sekundu (IOPS) a propustnosti účtu úložiště.
- Celková doba obnovení může být ovlivněna v případě, že je cílový účet úložiště načten s jinými operacemi čtení a zápisu aplikace. Pokud chcete zlepšit operaci obnovení, vyberte účet úložiště, který není načtený s ostatními aplikačními daty.

## <a name="best-practices"></a>Osvědčené postupy

Při konfiguraci zálohování virtuálních počítačů doporučujeme dodržovat tyto postupy:

- Upravte výchozí naplánované časy nastavené v zásadách. Pokud je například výchozí čas v zásadách nastavený na 00:00, zvyšte tento čas o několik minut, aby se zajistilo optimální využití prostředků.
- Pokud obnovujete virtuální počítače z jednoho trezoru, důrazně doporučujeme, abyste používali jiné [účty úložiště pro obecné účely v2](../storage/common/storage-account-upgrade.md) , abyste zajistili, že cílový účet úložiště nebude omezený. Každý virtuální počítač například musí mít jiný účet úložiště. Pokud se například obnoví 10 virtuálních počítačů, použijte 10 různých účtů úložiště.
- Pro zálohování virtuálních počítačů, které používají Prémiové úložiště s okamžitým obnovením, doporučujeme přidělit *50%* volného místa celkového přiděleného prostoru úložiště, který se vyžaduje **jenom** pro první zálohování. 50% volného místa není požadavkem na zálohování po dokončení prvního zálohování.
- Omezení počtu disků na účet úložiště je relativní vzhledem k tomu, jak často k diskům přistupují aplikace, které jsou spuštěné na virtuálním počítači IaaS (infrastruktura jako služba). Obecně platí, že pokud je v jednom účtu úložiště 5 až 10 nebo více disků, měli byste vyrovnat zatížení přesunem některých disků do samostatných účtů úložiště.
- Pokud chcete obnovit virtuální počítače se spravovanými disky pomocí prostředí PowerShell, zadejte další parametr ***TargetResourceGroupName*** a určete skupinu prostředků, do které se budou spravované disky obnovovat. další [informace najdete tady](./backup-azure-vms-automation.md#restore-managed-disks).

## <a name="backup-costs"></a>Náklady na zálohování

U virtuálních počítačů Azure zálohovaných v Azure Backup se vztahují [ceny Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

Fakturace se nespustí, dokud se nedokončí první úspěšná záloha. V tomto okamžiku začíná fakturace úložiště i chráněných virtuálních počítačů. Fakturace bude pokračovat, dokud se všechna zálohovaná data pro virtuální počítač uloží do trezoru. Pokud zastavíte ochranu virtuálního počítače, ale záložní data pro virtuální počítač existují v trezoru, bude fakturace pokračovat.

Fakturace za zadaný virtuální počítač se zastaví jenom v případě, že se ochrana zastaví a všechna zálohovaná data se odstraní. Když se ochrana zastaví a nejsou k dispozici žádné aktivní úlohy zálohování, stane se velikost poslední úspěšné zálohy virtuálního počítače v rámci velikosti chráněné instance použité pro měsíční vyúčtování.

Výpočet velikosti chráněné instance je založen na *skutečné* velikosti virtuálního počítače. Velikost virtuálního počítače je součtem všech dat ve virtuálním počítači, s výjimkou dočasného úložiště. Ceny jsou založené na skutečných datech uložených na datových discích, nikoli na maximální podporované velikosti pro každý datový disk, který je připojený k virtuálnímu počítači.

Podobně platí, že faktura za úložiště záloh je založena na množství dat uložených v Azure Backup, což je součet skutečných dat v každém bodu obnovení.

Například Vezměte virtuální počítač a2-Standard, který má dva další datové disky s maximální velikostí 32 TB. V následující tabulce jsou uvedena skutečná data uložená na jednotlivých těchto discích:

**Disk** | **Maximální velikost** | **Skutečná data k dispozici**
--- | --- | ---
Disk OS | 32 TB | 17 GB
Místní/dočasný disk | 135 GB | 5 GB (Nezahrnuto do zálohování)
Datový disk 1 | 32 TB| 30 GB
Datový disk 2 | 32 TB | 0 GB

Skutečná velikost virtuálního počítače v tomto případě je 17 GB + 30 GB + 0 GB = 47 GB. Tato chráněná velikost instance (47 GB) se bude základem pro měsíční faktura. Vzhledem k nárůstu množství dat ve virtuálním počítači se velikost chráněné instance, která se používá pro fakturaci, změní na odpovídající.

## <a name="next-steps"></a>Další kroky

- [Připravte se na zálohování virtuálních počítačů Azure](backup-azure-arm-vms-prepare.md).