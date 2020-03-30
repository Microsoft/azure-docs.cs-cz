---
title: Informace o zálohování virtuálních počítačů Azure
description: V tomto článku se dozvíte, jak služba Azure Backup zálohuje virtuální počítače Azure a jak postupovat podle osvědčených postupů.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: f4b36f57362607a13c09896cd7109596aba0a852
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415966"
---
# <a name="an-overview-of-azure-vm-backup"></a>Přehled zálohování virtuálních počítačů Azure

Tento článek popisuje, jak [služba Azure Backup](backup-introduction-to-azure-backup.md) zálohuje virtuální počítače Azure (VM).

Azure Backup poskytuje nezávislé a izolované zálohy, které chrání před nechtěnou destrukcí dat na virtuálních počítačích. Zálohy jsou uloženy v trezoru služby Recovery Services s integrovanou správou bodů obnovení. Konfigurace a škálování jsou jednoduché, zálohy jsou optimalizovány a můžete je snadno obnovit podle potřeby.

Jako součást procesu zálohování [snímek je pořízena](#snapshot-creation)a data se přenáší do trezoru služby Recovery Services bez dopadu na produkční úlohy. Snímek poskytuje různé úrovně konzistence, jak je popsáno [zde](#snapshot-consistency).

Azure Backup má také specializované nabídky pro databázové úlohy, jako je [SQL Server](backup-azure-sql-database.md) a [SAP HANA,](sap-hana-db-about.md) které jsou podporující úlohy, nabízejí 15 minut RPO (cíl bodu obnovení) a umožňují zálohování a obnovení jednotlivých databází.

## <a name="backup-process"></a>Proces zálohování

Azure Backup dokončuje zálohování pro virtuální počítače Azure takto:

1. Pro virtuální počítače Azure, které jsou vybrané pro zálohování, Azure Backup spustí úlohu zálohování podle plánu zálohování, který zadáte.
1. Během první zálohy rozšíření zálohování je nainstalován na virtuálním počítači, pokud je spuštěnvirtuální počítač.
    - Pro virtuální počítače se systémem Windows je [nainstalována rozšíření VMSnapshot.](https://docs.microsoft.com/azure/virtual-machines/extensions/vmsnapshot-windows)
    - Pro virtuální počítače s Linuxem je [nainstalována rozšíření VMSnapshotLinux.](https://docs.microsoft.com/azure/virtual-machines/extensions/vmsnapshot-linux)
1. Pro spuštěné virtuální počítače se systémem Windows, které jsou spuštěny, zálohování souřadnice se službou Windows Volume Shadow Copy Service (VSS) pořídit snímek konzistentní s aplikací virtuálního počítače.
    - Ve výchozím nastavení záloha přebírá úplné zálohy VSS.
    - Pokud backup nemůže pořizovat snímek konzistentní s aplikací, pak trvá snímek základního úložiště konzistentní se souborem (protože při zastavení virtuálního počítače nedojde k zápisu žádné aplikace).
1. U virtuálních počítačů s Linuxem přebírá backup konzistentní se soubory. U snímků konzistentních s aplikací je třeba ručně přizpůsobit skripty před a po.
1. Po backup pořídí snímek, přenese data do úložiště.
    - Zálohování je optimalizováno paralelním zálohováním jednotlivých disků virtuálního počítače.
    - Pro každý disk, který je zálohován, Azure Backup přečte bloky na disku a identifikuje a přenáší pouze datové bloky, které se změnily (delta) od předchozí zálohy.
    - Data snímku nemusí být okamžitě zkopírována do úložiště. Ve špičce to může trvat několik hodin. Celkový čas zálohování pro virtuální počítače bude kratší než 24 hodin pro denní zásady zálohování.
1. Změny provedené ve virtuálním počítači s Windows po povolení azure backupu jsou:
    - Microsoft Visual C++ 2013 Redistributable(x64) - 12.0.40660 je nainstalovaný ve virtuálním jazyce
    - Typ spuštění služby Stínová kopie svazku (VSS) byl změněn na automatický z ručního
    - Je přidána služba IaASVmProvider Windows

1. Po dokončení přenosu dat snímek je odebrán a je vytvořen bod obnovení.

![Architektura zálohování virtuálních strojů Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encryption-of-azure-vm-backups"></a>Šifrování záloh virtuálních virtuálních počítačů Azure

Při zálohování virtuálních počítačů Azure pomocí Azure Backup se virtuální počítače zašifrují v klidovém stavu pomocí šifrování služby Storage Service (SSE). Azure Backup můžete také zálohovat virtuální počítače Azure, které jsou šifrované pomocí Azure Disk Encryption.

**Šifrování** | **Podrobnosti** | **Podpora**
--- | --- | ---
**Azure Disk Encryption** | Azure Disk Encryption šifruje operační systém i datové disky pro virtuální počítače Azure.<br/><br/> Azure Disk Encryption se integruje s šifrovacími klíči nástroje BitLocker (BEKs), které jsou zabezpečeny v trezoru klíčů jako tajné klíče. Azure Disk Encryption se také integruje s šifrovacími klíči klíče Azure Key Vault (KEKs). | Azure Backup podporuje zálohování spravovaných a nespravovaných virtuálních počítačích Azure šifrovaných jenom s BEK nebo s BEK s KEKs společně s KEKs.<br/><br/> Oba BEKs a KEKs jsou zálohovány a šifrovány.<br/><br/> Vzhledem k tomu, že keky a beks jsou zálohovány, uživatelé s potřebnými oprávněními můžete obnovit klíče a tajné klíče zpět do trezoru klíčů v případě potřeby. Tito uživatelé mohou také obnovit šifrovaný virtuální virtuální ms.<br/><br/> Šifrované klíče a tajné klíče nemůžou číst neoprávnění uživatelé ani Azure.
**Sse** | S SSE, Azure Storage poskytuje šifrování v klidovém stavu automaticky šifrování dat před uložením. Azure Storage také dešifruje data před jejich načtením. | Azure Backup používá SSE pro šifrování virtuálních počítačích Azure v klidovém stavu.

Pro spravované a nespravované virtuální počítače Azure podporuje zálohování oba virtuální počítače šifrované jenom beks nebo virtuální počítače šifrované s BEK s KEKs společně s KEKs.

Zálohované BEKs (tajné kódy) a KEKs (klíče) jsou šifrovány. Lze je číst a používat pouze v případě, že je autorizovaní uživatelé obnoví zpět do trezoru klíčů. Neoprávnění uživatelé ani Azure neumí číst nebo používat zálohované klíče nebo tajné klíče.

BEKs jsou také zálohovány. Pokud tedy dojde ke ztrátě BEK, oprávnění uživatelé mohou obnovit beks do trezoru klíčů a obnovit šifrované virtuální chody. Zálohovat a obnovovat šifrované virtuální aplikace nebo klíče a tajné klíče můžou zálohovat a obnovovat jenom uživatelé s potřebnou úrovní oprávnění.

## <a name="snapshot-creation"></a>Vytvoření snímku

Azure Backup pořizuje snímky podle plánu zálohování.

- **Virtuální aplikace Windows:** Pro virtuální počítače se systémem Windows služba Zálohování koordinuje s VSS pořizovat snímek konzistentní s aplikací disků virtuálních počítačů.  Ve výchozím nastavení azure backup trvá úplné zálohy VSS (zkrátí protokoly aplikací, jako je SQL Server v době zálohování získat konzistentní zálohování na úrovni aplikace).  Pokud používáte databázi SQL Serveru na zálohování virtuálních zařízení Azure, můžete upravit nastavení tak, aby záloha kopie VSS (zachovat protokoly). Další informace najdete v [tomto článku](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#troubleshoot-vm-snapshot-issues).

- **Virtuální počítače s Linuxem:** Chcete-li pořizovat snímky virtuálních aplikací pro Linux konzistentní aplikace, použijte linuxový rámec před skripty a postskript, abyste napsali vlastní skripty, abyste zajistili konzistenci.

  - Azure Backup vyvolá jenom skripty před/po, které jste napsali.
  - Pokud se předskripty a postskripty úspěšně spustí, Azure Backup označí bod obnovení jako konzistentní s aplikací. Pokud však používáte vlastní skripty, jste nakonec zodpovědní za konzistenci aplikace.
  - [Přečtěte si další informace](backup-azure-linux-app-consistent.md) o konfiguraci skriptů.

## <a name="snapshot-consistency"></a>Konzistence snímku

Následující tabulka vysvětluje různé typy konzistence snímek:

**Snímek** | **Podrobnosti** | **Obnovení** | **Aspekty**
--- | --- | --- | ---
**Konzistentní s aplikací** | Zálohy konzistentní s aplikací zachycují obsah paměti a čekající vstupně-out operace. Snímky konzistentní s aplikací používají zapisovač VSS (nebo skripty před/post pro Linux), aby byla zajištěna konzistence dat aplikace před zálohováním. | Když obnovujete virtuální hosti s konzistentním snímkem aplikace, virtuální ho se spustí. Neexistuje žádné poškození dat nebo ztráta. Aplikace se spouštějí v konzistentním stavu. | Windows: Všichni autoři VSS uspěli<br/><br/> Linux: Skripty před/po jsou konfigurovány a úspěšné
**Konzistentní se systémem souborů** | Konzistentní zálohy systému souborů poskytují konzistenci tím, že pořizovat snímek všech souborů najednou.<br/><br/> | Když obnovujete virtuální hosti s konzistentním snímkem systému souborů, virtuální ho se spustí. Neexistuje žádné poškození dat nebo ztráta. Aplikace musí implementovat svůj vlastní mechanismus "opravy", aby se ujistil, že obnovená data jsou konzistentní. | Windows: Některé zapisovače VSS se nezdařilo <br/><br/> Linux: Výchozí (pokud nejsou skripty před/po nakonfigurovány nebo se nezdařily)
**Konzistentní s havárií** | Snímky konzistentní s havárií se obvykle vyskytují, pokud se virtuální počítač Azure vypne v době zálohování. Pouze data, která již na disku existují v době zálohování, jsou zachycena a zálohována. | Začíná procesem spouštění virtuálního počítače následovaným kontrolou disku, která opravuje chyby poškození. Všechna data v paměti nebo operace zápisu, které nebyly přeneseny na disk před selháním jsou ztraceny. Aplikace implementují vlastní ověření dat. Databázová aplikace může například použít svůj transakční protokol pro ověření. Pokud transakční protokol obsahuje položky, které nejsou v databázi, databázový software vrátí transakce zpět, dokud nebudou data konzistentní. | Virtuální počítače je ve stavu vypnutí (zastaveno/ naváděné) stavu.

## <a name="backup-and-restore-considerations"></a>Důležité informace o zálohování a obnovení

**Aspekty** | **Podrobnosti**
--- | ---
**Disk** | Zálohování disků virtuálních počítačů je paralelní. Například pokud virtuální počítače má čtyři disky, služba Zálohování pokusí zálohovat všechny čtyři disky paralelně. Zálohování je přírůstkové (pouze změněná data).
**Plánování** |  Chcete-li snížit provoz zálohování, zálohujte různé virtuální počítače v různých denních dobách a ujistěte se, že se časy nepřekrývají. Zálohování virtuálních mů současně způsobuje dopravní zácpy.
**Příprava záloh** | Mějte na paměti čas potřebný k přípravě zálohy. Doba přípravy zahrnuje instalaci nebo aktualizaci rozšíření zálohování a aktivaci snímku podle plánu zálohování.
**Přenos dat** | Zvažte čas potřebný pro Azure Backup k identifikaci přírůstkové změny z předchozí zálohy.<br/><br/> V přírůstkové zálohy Azure Backup určuje změny výpočtem kontrolní součet bloku. Pokud dojde ke změně bloku, je označen pro přenos do trezoru. Služba analyzuje identifikované bloky a pokusí se dále minimalizovat množství dat k přenosu. Po vyhodnocení všech změněných bloků azure backup přenese změny do trezoru.<br/><br/> Mezi pořízením snímku a jeho kopírováním do trezoru může být prodleva.<br/><br/> V době špičky může zpracování záloh trvat až osm hodin. Doba zálohování pro virtuální počítače bude kratší než 24 hodin pro denní zálohování.
**Prvotní zálohování** | Přestože celková doba zálohování pro přírůstkové zálohování je kratší než 24 hodin, nemusí tomu tak být u první zálohy. Doba potřebná pro počáteční zálohování bude záviset na velikosti dat a na zpracování zálohy.
**Obnovit frontu** | Procesy zálohování Azure obnovují úlohy z více účtů úložiště současně a umístí požadavky na obnovení do fronty.
**Obnovit kopii** | Během procesu obnovení se data zkopírují z úložiště do účtu úložiště.<br/><br/> Celková doba obnovení závisí na vstupně-no operací za sekundu (VOPS) a propustnost účtu úložiště.<br/><br/> Chcete-li zkrátit dobu kopírování, vyberte účet úložiště, který není načten s jinými zápisy a čtením aplikací.

### <a name="backup-performance"></a>Výkon zálohování

Tyto běžné scénáře mohou ovlivnit celkový čas zálohování:

- **Přidání nového disku do chráněného virtuálního počítače Azure:** Pokud virtuální počítače prochází přírůstkové zálohování a je přidán nový disk, čas zálohování se zvýší. Celková doba zálohování může trvat déle než 24 hodin z důvodu počáteční replikace nového disku spolu s rozdílovou replikací existujících disků.
- **Fragmentované disky:** Operace zálohování jsou rychlejší, pokud jsou změny disku souvislé. Pokud jsou změny rozprostřeny a fragmentovány na disku, zálohování bude pomalejší.
- **Konve disku:** Pokud chráněné disky, které procházejí přírůstkové zálohování mají denní konve více než 200 GB, zálohování může trvat dlouhou dobu (více než osm hodin) k dokončení.
- **Záložní verze:** Nejnovější verze programu Backup (označovaná jako verze okamžitého obnovení) používá pro identifikaci změn optimalizovanější proces než porovnání kontrolního součtu. Ale pokud používáte okamžité obnovení a odstranili jste snímek zálohy, zálohování se přepne na porovnání kontrolního součtu. V takovém případě operace zálohování překročí 24 hodin (nebo nezdaří).

## <a name="best-practices"></a>Osvědčené postupy

Když konfigurujete zálohy virtuálních aplikací, doporučujeme následující postupy:

- Upravte výchozí časy plánu, které jsou nastaveny v zásadě. Například pokud výchozí čas v zásadě je 12:00 AM, převádí časování o několik minut tak, aby prostředky jsou optimálně používány.
- Pokud obnovujete virtuální chod z jednoho trezoru, důrazně doporučujeme použít různé [účty úložiště v2 pro obecné účely,](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) abyste zajistili, že cílový účet úložiště nebude omezen. Například každý virtuální virtuální paměť musí mít jiný účet úložiště. Pokud se například obnoví 10 virtuálních počítačů, použijte 10 různých účtů úložiště.
- Pro zálohování virtuálních zařízení, které používají úložiště premium, s okamžitým obnovením doporučujeme přidělit *50 %* volného místa z celkového přiděleného úložného prostoru, který je vyžadován **pouze** pro první zálohu. 50% volného místa není podmínkou pro zálohování po dokončení první zálohy
- Limit na počet disků na účet úložiště je relativní vzhledem k tom, jak silně disky jsou přístupné aplikace, které jsou spuštěny na infrastruktuře jako služba (IaaS) virtuální počítač. Obecně platí, že pokud je v jednom účtu úložiště k dispozici 5 až 10 disků nebo více, vyvažte zatížení přesunutím některých disků do samostatných účtů úložiště.

## <a name="backup-costs"></a>Náklady na zálohování

Virtuální počítače Azure zálohované pomocí Azure Backup podléhají [cenám Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

Fakturace se nespustí, dokud neskončí první úspěšná záloha. V tomto okamžiku začíná fakturace pro úložiště i chráněné virtuální počítače. Fakturace pokračuje tak dlouho, dokud jsou všechna data zálohování pro virtuální počítače uložena v trezoru. Pokud zastavíte ochranu pro virtuální počítače, ale záložní data pro virtuální počítače existuje v trezoru, fakturace pokračuje.

Fakturace pro zadaný virtuální účet se zastaví pouze v případě, že je ochrana zastavena a všechna data zálohování se odstraní. Když se ochrana zastaví a neexistují žádné aktivní úlohy zálohování, velikost poslední úspěšné zálohy virtuálního počítače se stane chráněnou velikostí instance používanou pro měsíční fakturu.

Výpočet velikosti chráněné instance je založen na *skutečné* velikosti virtuálního počítače. Velikost virtuálního počítače je součtem všech dat ve virtuálním počítače, s výjimkou dočasného úložiště. Ceny jsou založeny na skutečných datech, která jsou uložená na datových discích, nikoli na maximální podporované velikosti pro každý datový disk, který je připojený k virtuálnímu počítači.

Podobně je účet za úložiště záloh založen na množství dat, která jsou uložená v Azure Backup, což je součet skutečných dat v každém bodě obnovení.

Vezměte například virtuální modul velikosti A2-Standard, který má dva další datové disky s maximální velikostí 32 TB. V následující tabulce jsou uvedena skutečná data uložená na každém z těchto disků:

**Disk** | **Maximální velikost** | **Aktuální údaje**
--- | --- | ---
Disk OS | 32 TB | 17 GB
Místní/dočasný disk | 135 GB | 5 GB (není součástí dodávky zálohy)
Datový disk 1 | 32 TB| 30 GB
Datový disk 2 | 32 TB | 0 GB

Skutečná velikost virtuálního počítače v tomto případě je 17 GB + 30 GB + 0 GB = 47 GB. Tato velikost chráněné instance (47 GB) se stane základem pro měsíční účet. Jak se zvětšuje množství dat ve virtuálním počítači, velikost chráněné instance používaná pro změny fakturace tak, aby odpovídaly.

## <a name="next-steps"></a>Další kroky

Teď [se připravte na zálohování virtuálních počítačů Azure](backup-azure-arm-vms-prepare.md).
