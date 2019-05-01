---
title: Informace o zálohování virtuálních počítačů Azure
description: Další informace o zálohování virtuálních počítačů Azure a poznamenejte si některé osvědčené postupy.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: raynew
ms.openlocfilehash: 93be913182db56941c346ef0cad47f70c0d614c9
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64706829"
---
# <a name="about-azure-vm-backup"></a>Informace o zálohování virtuálních počítačů Azure

Tento článek popisuje, jak [služby Azure Backup](backup-introduction-to-azure-backup.md) zálohuje virtuální počítače Azure (VM).

## <a name="backup-process"></a>Proces zálohování

Zde je, jak Azure Backup dokončení zálohování pro virtuální počítače Azure:

1. Pro virtuální počítače Azure, které jsou vybrány pro zálohování zálohování Azure spustí úlohu zálohování podle plánu zálohování, které zadáte.
1. Při prvním zálohování zálohování rozšíření je nainstalováno na virtuálním počítači Pokud virtuální počítač je spuštěný.
    - U virtuálních počítačů s Windows _VMSnapshot_ je rozšíření nainstalované.
    - Pro virtuální počítače s Linuxem _VMSnapshotLinux_ je rozšíření nainstalované.
1. Pro Windows virtuálních počítačů, které jsou spuštěné, souřadnice zálohování s Windows Stínová kopie svazku Service (VSS) k pořízení konzistentního snímku virtuálního počítače.
    - Ve výchozím nastavení zálohování trvá úplné zálohy stínové kopie svazku.
    - Pokud zálohování nelze pořízení konzistentního snímku, pak trvá konzistentními snímek základního úložiště (protože aplikace neprovádí žádné zápisy dojít při zastavení virtuálního počítače).
1. Pro virtuální počítače s Linuxem Backup Pořídí zálohu konzistentní. Pro snímky konzistentní budete muset ručně upravit předzálohovacího nebo pozálohovacího skripty.
1. Jakmile Backup pořídí snímek, přenese data do trezoru.
    - Zálohování je optimalizované zálohování každého disku virtuálního počítače paralelně.
    - Pro každý disk, který se zálohuje Azure Backup přečte bloky na disku a identifikuje a přenese pouze bloky dat, které se změnily od posledního zálohování (rozdílového).
    - Data snímku nemusí být hned zkopírují do trezoru. Může trvat několik hodin, během špičky. Celkový čas zálohování pro virtuální počítač bude méně než 24 hodin denně zásady zálohování.
 1. Změny virtuálního počítače s Windows po povolení Azure Backup na ní jsou:
    -   Microsoft Visual C++ 2013 Redistributable(x64) - 12.0.40660 je nainstalovaná ve virtuálním počítači
    -   Typ spouštění služby Stínová kopie svazku (VSS) ručně změnit na hodnotu automaticky.
    -   Přidání služby IaaSVmProvider Windows

1. Po dokončení přenosu dat se snímek odstraní a vytvoří bod obnovení.

![Architektura zálohování virtuálních počítačů Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encryption-of-azure-vm-backups"></a>Šifrování záloh virtuálních počítačů Azure

Při zálohování virtuálních počítačů Azure pomocí Azure Backup, virtuální počítače se šifrují při nečinnosti pomocí šifrování služby Storage (SSE). Azure Backup můžete také zálohovat virtuální počítače Azure, které jsou šifrované pomocí Azure Disk Encryption.

**Šifrování** | **Podrobnosti** | **Podpora**
--- | --- | ---
**Azure Disk Encryption** | Azure Disk Encryption zašifruje operačního systému a datové disky pro virtuální počítače Azure.<br/><br/> Azure Disk Encryption se integruje s Bitlockerem šifrovací klíče (BEKs), které jsou chráněné ve službě key vault jako tajné kódy. Azure Disk Encryption se také integruje s Azure Key Vault key šifrovací klíče (KEKs). | Azure Backup podporuje zálohování spravované a nespravované virtuální počítače Azure zašifrovaná BEKs pouze nebo s BEKs spolu s KEKs.<br/><br/> BEKs a KEKs jsou zálohovány a šifrovaná.<br/><br/> Protože KEKs a BEKs jsou zálohovány, můžou uživatelé s potřebnými oprávněními obnovit klíče a tajné kódy zpět do trezoru klíčů v případě potřeby. Tito uživatelé mohou také obnovit šifrovaný virtuální počítač.<br/><br/> Šifrované klíče a tajné klíče nelze číst neoprávnění uživatelé, nebo v Azure.
**SSE** | Díky SSE Azure Storage poskytuje šifrování v klidovém stavu tím, že automaticky šifruje data před uložením. Azure Storage také dešifruje před načtením. | Azure Backup používá SSE pro šifrování v klidovém stavu virtuálních počítačů Azure.

Pro spravované a nespravované virtuální počítače Azure Backup podporuje virtuální počítače jsou šifrované pomocí BEKs jenom nebo šifrované pomocí BEKs spolu s KEKs virtuálních počítačů.

Zazálohované BEKs (tajné údaje) a KEKs (klíče) jsou šifrována. Může být číst a používat pouze v případě, že obnovit zpět do služby key vault Autorizovaní uživatelé. Azure ani neautorizovaným uživatelům může číst nebo používat zálohovaných klíčů nebo tajných klíčů.

BEKs jsou také zálohovat. Takže pokud BEKs jsou ztraceny, oprávněným uživatelům můžete obnovit BEKs k trezoru klíčů a obnovení šifrovaných virtuálních počítačů. Pouze uživatelé s potřebnou úroveň oprávnění může zálohování a obnovení šifrovaných virtuálních počítačů nebo klíče a tajné kódy.

## <a name="snapshot-creation"></a>Vytvoření snímku

Azure Backup pořizuje snímky podle plánu zálohování.

- **Virtuální počítače s Windows:** Pro virtuální počítače s Windows služba Backup koordinuje pomocí VSS snímek konzistentní vzhledem k diskům virtuálních počítačů.

  - Ve výchozím nastavení Azure Backup pořídí úplné zálohy stínové kopie svazku. [Další informace](https://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx).
  - Chcete-li změnit nastavení tak, aby Azure Backup pořídí záložní kopie VSS, nastavte následující klíč registru z příkazového řádku:

    **REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgent" /v / USEVSSCOPYBACKUP /t REG_SZ /d TRUE f**

- **Virtuální počítače s Linuxem:** Pokud chcete pořizovat snímky konzistentní vzhledem k virtuálních počítačů s Linuxem, použijte Linux předzálohovací skript a framework k zápisu vlastních skriptů k zajištění konzistence pro provedení pozálohovacího skriptu.

  - Azure Backup vyvolá pouze skripty předzálohovacího nebo pozálohovacího autorem je.
  - Pokud předzálohovacích skriptů a pozálohovacích skriptů proběhl úspěšně, Azure Backup označí bod obnovení jako konzistentní s aplikací. Nicméně pokud používáte vlastní skripty, jste platformy, konečnou zodpovědnost za účelem zajištění konzistence aplikace.
  - [Další informace](backup-azure-linux-app-consistent.md) o tom, jak nakonfigurovat skripty.

### <a name="snapshot-consistency"></a>Konzistence snímku

Následující tabulka vysvětluje různé typy konzistence snímku:

**snímek** | **Podrobnosti** | **Obnovení** | **Posouzení**
--- | --- | --- | ---
**Konzistentní s aplikací** | Zálohování konzistentní vzhledem k zachycení paměti obsahu a čeká se na vstupně-výstupních operací. Snímky konzistentní vzhledem k pomocí zapisovače VSS (nebo předzálohovacího nebo pozálohovacího skripty pro Linux) k zajištění konzistence dat aplikací, než dojde k zálohování. | Pokud chcete obnovit virtuální počítač pomocí konzistentního snímku, virtuální počítač se spustí. Neexistuje žádný poškození nebo ztrátu. Spuštění aplikace v konzistentním stavu. | Windows: Všechny zapisovače VSS úspěšné<br/><br/> Linux: Předzálohovacího nebo pozálohovacího skripty jsou konfigurovány a byla úspěšná
**Konzistentní se systémem souborů** | Konzistentní zálohování systému souborů poskytuje konzistenci, protože pořízení snímku všechny soubory ve stejnou dobu.<br/><br/> | Pokud chcete obnovit virtuální počítač s snímek konzistentní vzhledem k systému souborů, virtuální počítač se spustí. Neexistuje žádný poškození nebo ztrátu. Aplikace je třeba implementovat vlastní mechanismus "Opravit" Ujistěte se, že je obnovená data konzistentní vzhledem k aplikacím. | Windows: Některé zapisovače služby VSS se nezdařila <br/><br/> Linux: Výchozí (Pokud předzálohovacího nebo pozálohovacího skriptů nejsou nakonfigurovaná nebo neúspěšných)
**Konzistentní při selhání** | Snímky konzistentních při chybě obvykle dochází, pokud virtuální počítač Azure vypne v době zálohování. Pouze data, která již existuje na disku v době zálohování je zaznamenat a zálohovat.<br/><br/> Vytvoření bodu obnovení konzistentního nezaručuje konzistenci dat pro operační systém nebo aplikace. | I když neexistují žádné záruky, obvykle spustí virtuální počítač a potom spustí kontrolu disku opravit chyby poškozující. Žádná data v paměti nebo operace zápisu, které nebyly převedeny na disk před selhání se ztratí. Aplikace implementovat vlastní ověřovací data. Databáze aplikace například můžete použít jeho transakční protokol pro ověřování. Pokud transakčního protokolu obsahuje položky, které nejsou v databázi, databázový software postupně transakce zpět, dokud se data konzistentní vzhledem k aplikacím. | Virtuální počítač je ve stavu vypnutí

## <a name="backup-and-restore-considerations"></a>Důležité informace o zálohování a obnovení

**Posouzení** | **Podrobnosti**
--- | ---
**Disk** | Zálohování disků virtuálních počítačů je paralelní. Například pokud má čtyři disky virtuální počítač, služba Backup se pokusí zálohovat všechny čtyři disky paralelně. Zálohování je přírůstkové (jen změněných dat).
**Plánování** |  Pro omezení provozu v zálohování, zálohování různých virtuálních počítačů v různých časech, dne a ujistěte se, že časy nepřekrývají. Zálohování virtuálních počítačů ve stejnou dobu způsobí, že provoz zablokovaný.
**Příprava zálohování** | Mějte na paměti čas potřebný k přípravě zálohování. Doba přípravy zahrnuje instalaci nebo aktualizaci rozšíření zálohování a aktivuje se snímek podle plánu zálohování.
**Přenos dat** | Vezměte v úvahu doba potřebná pro službu Azure Backup k identifikaci přírůstkové změny z předchozí zálohy.<br/><br/> V přírůstkové záloze Azure Backup, zda změny tím výpočet kontrolního součtu bloku. Pokud se změní bloku budou označena k přenosu do trezoru. Služba analyzuje identifikované bloky pokus o dále omezit objem přenášených dat. Po vyhodnocení se změněnými bloky, Azure Backup přenáší změny v úložišti.<br/><br/> Může existovat zpoždění mezi pořizování snímku a zkopírováním do trezoru.<br/><br/> Ve špičce může trvat až 8 hodin pro zálohy na zpracování. Čas zálohování pro virtuální počítač bude méně než 24 hodin pro denní zálohování.
**Prvotní zálohování** | I když celkový čas zálohování pro přírůstkové zálohování je kratší než 24 hodin, které nemusí být v případě první zálohy. Doba potřebná pro prvotního zálohování bude záviset na velikosti dat a zálohování se zpracovává.
**Obnovit frontu** | Azure Backup procesy obnovení úlohy z více účtů úložiště ve stejnou dobu a vloží obnovení žádostí ve frontě.
**Obnovit kopii** | Během procesu obnovení data zkopírována z úložiště do účtu úložiště.<br/><br/> Celkový počet obnovení doba závisí na vstupně-výstupních operací za sekundu (IOPS) a propustnost účtu úložiště.<br/><br/> Pokud chcete zkrátit dobu kopírování, vyberte účet úložiště, které není načteno s jinými aplikace zápisy a čtení.

### <a name="backup-performance"></a>Výkon zálohování

Tyto běžné scénáře mohou ovlivnit celkový čas zálohování:

- **Přidání nového disku na chráněném virtuálním počítači Azure:** Pokud virtuální počítač Probíhá přírůstkové zálohování a přidat nový disk, zvýší se čas zálohování. Celkový čas zálohování z důvodu počáteční replikaci nového disku spolu s rozdílovou replikaci stávající disků trvat déle než 24 hodin.
- **Fragmentované disky:** Operace zálohování jsou rychlejší, když změny na disku jsou souvislé. Pokud změny rozprostřete a fragmentované napříč disk, bude pomalejší zálohování.
- **Četnost změn disku:** Pokud chráněný disky, které probíhá Přírůstková záloha mít denní četnosti změn z více než 200 GB, zálohování může trvat dlouhou dobu (víc než 8 hodin) na dokončení.
- **Záložní verze:** Nejnovější verzi služby Backup (označovanou jako rychlé obnovení) používá více optimalizované proces než porovnání kontrolního součtu pro určení změn. Ale v případě, že používáte rychlé obnovení a odstranili snímek zálohy, zálohování přepne na porovnání kontrolního součtu. V takovém případě operace zálohování bude trvat déle než 24 hodin (nebo neúspěch).

## <a name="best-practices"></a>Osvědčené postupy

Když konfigurujete záloh virtuálních počítačů, doporučujeme po těchto postupů:

- Změňte výchozí plán časy, které jsou nastavené v zásadách. Pokud je výchozí doba v zásadách 12:00 AM, zvýšte časování o několik minut, tak, aby se optimálně použít prostředky.
- Pro zálohování virtuálních počítačů, které používají úložiště úrovně premium, doporučujeme spustit nejnovější verzi Azure Backup ([rychlé obnovení](backup-instant-restore-capability.md)). Pokud používáte nejnovější verzi, zálohování přiděluje přibližně 50 procent celkové úložného prostoru. Služba Backup vyžaduje, aby tento prostor ke zkopírování snímku do stejného účtu úložiště a přenosu do trezoru.
- Pokud se obnovení virtuálních počítačů z jediného trezoru, důrazně doporučujeme použít jiný [účty úložiště pro obecné účely v2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) zajistit, že cílový účet úložiště není získat omezené. Například každý virtuální počítač musí mít jiný účet úložiště. Například pokud se obnoví 10 virtuálních počítačů, použijte 10 jiný účet úložiště.
- Obnovení z vrstvy úložiště pro obecné účely v1 (snímek) se během několika minut dokončit, protože snímek je ve stejném účtu úložiště. Obnovení z vrstvy úložiště pro obecné účely v2 (trezor) může trvat hodiny. V případech, kdy je k dispozici ve službě storage pro obecné účely v1 dat, doporučujeme použít [rychlé obnovení](backup-instant-restore-capability.md) funkce rychlejší zálohování. (Pokud data je nutné obnovit z trezoru, pak bude trvat déle.)
- Limit počtu disků v účtu úložiště je relativní vzhledem k jak často disky jsou v přístupu k aplikacím, které běží na infrastrukturu jako službu (IaaS) virtuálního počítače. Obecně platí Pokud je 5 až 10 disků nebo více na jeden účet úložiště, Vyrovnávání zatížení přesunutím některé disky do samostatné účty úložiště.

## <a name="backup-costs"></a>Náklady na zálohování

Virtuální počítače Azure zálohovat pomocí služby Azure Backup platí pro ně [ceny služby Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

Fakturace se nespustí, dokud se nedokončí prvního úspěšného zálohování. V tomto okamžiku začne fakturace za úložiště a chráněné virtuální počítače. Fakturace pokračuje tak dlouho, dokud všechna data zálohování pro virtuální počítač uložený v trezoru. Pokud zastavíte ochranu pro virtuální počítač, ale data záloh pro virtuální počítač existuje v trezoru, pokračuje fakturace.

Fakturace pro zadanou virtuální počítač zastaví pouze v případě, že ochrana je zastavena a veškerá zálohovaná data se odstraní. Při zastavení ochrany a neexistují žádné aktivní úlohy zálohování, velikost z posledního úspěšného zálohování virtuálního počítače se změní velikost chráněné instance, používá pro měsíční náklady.

Výpočet velikosti chráněné instance je založen na *skutečné* velikost virtuálního počítače. Velikost Virtuálního počítače je součet všech dat na virtuálním počítači, s výjimkou dočasné úložiště. Cena je založená na vlastní data, která má uložená v datové disky, ne na maximální podporovaná velikost pro každý datový disk, který je připojen k virtuálnímu počítači.

Obdobně faktura za úložiště zálohování je založená na množství dat, která je uložená ve službě Azure Backup, který je součtem skutečná data v každém bodu obnovení.

Jako příklad může posloužit virtuální počítač s velikostí standardní A2, který má dva další datové disky o maximální velikosti 4 TB. V následující tabulce jsou uvedeny dat uložených v každém z těchto disků:

**Disk** | **Maximální velikost** | **Skutečná data k dispozici**
--- | --- | ---
Disk OS | 4095 GB | 17 GB
Místní/dočasného disku | 135 GB | 5 GB (není součástí pro zálohování)
Datový disk 1 | 4095 GB | 30 GB
Datový disk 2 | 4095 GB | 0 GB

Skutečná velikost virtuálního počítače v tomto případě je 17 GB + 30 GB + 0 GB = 47 GB. Tato velikost chráněné instance (47 GB) se změní základ pro měsíční náklady. Jak roste množství dat ve virtuálním počítači, velikost chráněné instance se používá pro změny tak, aby odpovídaly.

## <a name="next-steps"></a>Další postup

Nyní [Příprava zálohování virtuálních počítačů Azure](backup-azure-arm-vms-prepare.md).
