---
title: Informace o zálohování virtuálních počítačů Azure
description: Další informace o zálohování virtuálních počítačů Azure a poznamenejte si některé osvědčené postupy.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: c331c3617f421c913abbc3554aa5ba17e86cb978
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429172"
---
# <a name="about-azure-vm-backup"></a>Informace o zálohování virtuálních počítačů Azure

Tento článek popisuje, jak [služby Azure Backup](backup-introduction-to-azure-backup.md) zálohuje virtuální počítače Azure.

## <a name="backup-process"></a>Proces zálohování

Zde je, jak Azure Backup dokončení zálohování pro virtuální počítače Azure.

1. Služba Azure Backup pro virtuální počítače Azure, které jsou vybrány pro zálohování, iniciuje úlohu zálohování podle plánu zálohování, které zadáte.
2. Při prvním zálohování nainstalovaný rozšíření zálohování na virtuálním počítači, pokud běží.
    - Pro virtuální počítače s Windows _VMSnapshot_ je rozšíření nainstalované.
    - Pro virtuální počítače s Linuxem _VMSnapshotLinux_ je rozšíření nainstalované.
3. Pro Windows virtuálních počítačů, které jsou spuštěné, souřadnice zálohování pomocí VSS pořízení konzistentního snímku virtuálního počítače.
    - Ve výchozím nastavení zálohování trvá úplné zálohy stínové kopie svazku.
    - Pokud záloha není schopen pořízení konzistentního snímku, pak trvá konzistentními snímek základního úložiště (protože aplikace neprovádí žádné zápisy dojít při zastavení virtuálního počítače).
4. Zálohování virtuálních počítačů Linux trvá v zájmu konzistentní zálohování. Pro snímky konzistentní vzhledem k budete muset ručně upravit předzálohovacího nebo pozálohovacího skripty.
5. Po pořízení snímku data se přenesou do trezoru. 
    - Zálohování je optimalizované zálohování každého disku virtuálního počítače paralelně.
    - Pro každý disk zálohovaný Azure Backup přečte bloky na disku a identifikuje a přenese pouze bloky dat, které se změnily od posledního zálohování (rozdílového).
    - Poté, co se pořídí snímek, data se přenesou do trezoru.
    - Data snímku nemusí být hned zkopírují do trezoru. Může trvat několik hodin, během špičky. Celkový čas zálohování pro virtuální počítač menší bude tento po dobu 24 hodin denně zásady zálohování.

6. Po dokončení přenosu dat se snímek odstraní a vytvoří se bod obnovení.

![Architektura zálohování virtuálních počítačů Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encrypting-azure-vm-backups"></a>Šifrování záloh virtuálních počítačů Azure

Při zálohování virtuálních počítačů Azure pomocí Azure Backup, virtuální počítače se šifrují při nečinnosti pomocí šifrování služby Storage (SSE). Kromě toho Azure Backup můžete zálohovat virtuální počítače Azure, které jsou šifrované pomocí služby Azure Disk Encryption (ADE).


**Šifrování** | **Podrobnosti** | **Podpora**
--- | --- | ---
**ADE** | Šifruje ADE šifruje operačního systému a datové disky pro virtuální počítače Azure.<br/><br/> ADE se integruje s Bitlockerem šifrovacích klíčů (klíče BEK), ochrany ve službě key vault jako tajné kódy, nebo s Azure Key Vault key šifrovacích klíčů (KEK). | Azure Backup podporuje zálohování spravovaných a nespravovaných Azure virtuální počítače jsou šifrované pomocí klíče BEK pouze nebo pomocí klíče BEK spolu s KEK.<br/><br/> Oba klíče BEK a zálohovat a šifrovaná.<br/><br/> Protože KEK a klíče BEK jsou zálohovány, v případě potřeby uživatelé s oprávněními obnovit klíče a tajné kódy zpět do služby key vault a obnovit šifrovaný virtuální počítač.<br/><br/> Šifrované klíče a tajné klíče nelze číst neoprávnění uživatelé, nebo v Azure.
**SSE** | SSE Azure storage poskytuje šifrování v klidovém stavu tím, že automaticky šifruje data před uložením a dešifruje před načítání. | Azure Backup používá SSE pro šifrování neaktivních dat virtuálních počítačů Azure.

- Zálohování virtuálních počítačů, které jsou šifrované pomocí Bitlockeru šifrovací klíč (klíče BEK) pouze a klíče BEK spolu s Key(KEK) šifrovací klíč je podporováno pro spravované a nespravované virtuální počítače Azure.
- Zálohy (tajné údaje) klíče BEK a KEK (klíče) jsou šifrována. Může být číst a použít jenom při obnovení zpět do služby key vault Autorizovaní uživatelé. Azure ani neautorizovaným uživatelům může číst nebo použití zálohovaných klíčů nebo tajných klíčů.
- Protože klíč BEK je také zálohovat, pokud dojde ke ztrátě klíče BEK, Autorizovaní uživatelé obnovit klíče BEK pro trezor klíčů a obnovení šifrovaných virtuálních počítačů. 
- Pouze uživatelé s správnou úroveň oprávnění může zálohování a obnovení šifrovaných virtuálních počítačů, jakož i klíče a tajné kódy.



## <a name="taking-snapshots"></a>Pořizování snímků

Azure Backup snímky podle plánu zálohování. 

- **Virtuální počítače s Windows**: Pro virtuální počítače s Windows služba Backup koordinuje s Stínová kopie svazku Service (VSS) na snímek konzistentní vzhledem k diskům virtuálních počítačů.
    - Ve výchozím nastavení Azure Backup pořídí úplné zálohy stínové kopie svazku. [Další informace](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx).
    - Pokud chcete změnit nastavení tak, aby zálohování Azure trvá záložní kopie VSS, nastavte následující klíč registru z příkazového řádku: **REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgent" /v USEVSSCOPYBACKUP /t REG_SZ /d TRUE /f**.
- **Virtuální počítače s Linuxem**: Pokud budete chtít využít konzistentní snímky virtuálního počítače s Linuxem pomocí Linuxu předzálohovací skript a framework k zápisu vlastních skriptů k zajištění konzistence pro provedení pozálohovacího skriptu.
    -  Azure Backup vyvolá pouze předzálohovacího nebo pozálohovacího skriptech psaných sami.
    - Pokud se předzálohovací skript a pozálohovacích skriptů proběhl úspěšně, Azure Backup označí bod obnovení jako konzistentní s aplikací. Však si nakonec odpovědné za konzistentnost aplikací při použití vlastních skriptů.
    - [Další informace](backup-azure-linux-app-consistent.md) týkající se konfigurace skriptů.


### <a name="snapshot-consistency"></a>Konzistence snímku

Následující tabulka vysvětluje různé typy konzistence.

**snímek** | **Podrobnosti** | **Obnovení** | **Posouzení**
--- | --- | --- | ---
**Konzistentní s aplikací** | Zálohování konzistentní vzhledem k zachycení paměti obsahu a čeká se na vstupně-výstupních operací. Snímky konzistentní s pomocí zapisovače VSS (nebo předzálohovacího nebo pozálohovacího skriptu pro Linux), který zajištění konzistence dat aplikací, než dojde k zálohování. | Při obnovení s konzistentní vzhledem k snímku virtuálního počítače se spustí. Neexistuje žádný poškození nebo ztrátu. Spuštění aplikace v konzistentním stavu. | Windows: Všechny zapisovače VSS úspěšné<br/><br/> Linux: Předzálohovacího nebo pozálohovacího skripty jsou konfigurovány a byla úspěšná
**Konzistentní se systémem souborů** | Konzistentní zálohování souborů poskytuje konzistentní zálohování souborů na disku díky pořizování snímku všechny soubory ve stejnou dobu.<br/><br/> | Při obnovování s konzistentními snímku virtuálního počítače se spustí. Neexistuje žádný poškození nebo ztrátu. Aplikace je třeba implementovat vlastní mechanismus "Opravit" Ujistěte se, že je obnovená data konzistentní vzhledem k aplikacím. | Windows: Některé zapisovače služby VSS se nezdařila <br/><br/> Linux: Výchozí (Pokud předzálohovacího nebo pozálohovacího skriptů nejsou nakonfigurovaná nebo neúspěšných)
**Konzistentní při selhání** | Konzistenci při chybě často dochází při vypnutí virtuálního počítače Azure v době zálohování.  Pouze data, která již existuje na disku v době zálohování je zaznamenat a zálohovat.<br/><br/> Vytvoření bodu obnovení konzistentního nezaručuje konzistenci dat pro operační systém nebo aplikace. | Nejsou žádné záruky, ale obvykle spustí virtuální počítač a způsobem s diskem zaškrtněte, pokud chcete opravit chyby poškozující. Žádná data v paměti nebo zápisu, které nebyly převedeny na disku se ztratí. Aplikace implementovat vlastní ověřovací data. Například pro aplikaci na databázi, pokud transakčního protokolu obsahuje položky, které nejsou v databázi, databázový software postupně až do data jsou konzistentní vzhledem k aplikacím. | Virtuální počítač je ve stavu vypnutí


## <a name="restore-considerations"></a>Důležité informace o obnovení 



**Posouzení** | **Podrobnosti**
--- | ---
**Disk** | Zálohování z disku virtuálního počítače je paralelní. Například pokud má čtyři disky virtuální počítač, se služba pokusí zálohovat všechny čtyři disky paralelně. Zálohování je přírůstkové (jen změněných dat).
**Plánování** |  Pro omezení provozu v zálohování, zálohování různých virtuálních počítačů v různých časech dne bez překrývá. Zálohování virtuálních počítačů ve stejnou dobu způsobí, že provoz zablokovaný.
**Příprava zálohování** | Zvažte zálohování Přípravný čas, který zahrnuje instalaci nebo aktualizaci rozšíření zálohování a aktivuje snímku podle plánu zálohování.
**Přenos dat** | Čas potřebný pro služby backup pro výpočet přírůstkové změny z předchozí zálohy.<br/><br/> V přírůstkové záloze zjištění změn služby počítače kontrolního součtu bloku. Pokud blok se změní jeho identifikované pro odeslání do trezoru. Služba přejde do bloků identifikovaných pokus o další minimalizovat objem dat pro přenos. Po vyhodnocení všechny blokované změny přenesou do trezoru.<br/><br/> Může existovat zpoždění mezi pořizování snímku a zkopírováním do trezoru.<br/><br/> Ve špičkách může trvat až 8 hodin pro zálohy proces. Čas zálohování pro virtuální počítač bude méně než 24 hodin pro denní zálohování.
**Prvotní zálohování** | I když je platný pro přírůstkové zálohování celkový čas zálohování za méně než 24 hodin, nemusí být pro první zálohy. Čas potřeby bude záviset na velikosti dat a kdy dochází k zálohování.
**Obnovit frontu** | Azure Backup procesy obnovení úlohy z více účtů úložiště ve stejnou dobu a obnovení žádostí jsou vloženy do fronty.
**Obnovit kopii** | Během procesu obnovení data zkopírována z úložiště do účtu úložiště.<br/><br/> Obnovit doba závisí na vstupně-výstupních operací a propustnosti účtu úložiště.<br/><br/> Pokud chcete zkrátit dobu kopírování, vyberte účet úložiště, které není načteno s jinými aplikace zápisy a čtení.


### <a name="backup-performance"></a>Výkon zálohování

Tyto běžné scénáře mohou mít vliv na čas zálohování:

- Přidejte nový disk na chráněném virtuálním počítači Azure: -Li přidat nový disk virtuálního počítače probíhá přírůstkové zálohování, zálohování trvat déle než 24 hodin z důvodu počáteční replikaci nového disku spolu s rozdílovou replikaci stávající disky.
- Fragmentované disky: Operace zálohování jsou rychlejší, když jsou společně umístěná změny na disku. Pokud změny rozprostřete a fragmentované napříč disk, bude pomalejší zálohování. 
- Četnost změn disku: Pokud denní četnosti změn z více než 200 GB chráněné disky procházející přírůstkové zálohování, zálohování může trvat dlouhou dobu (víc než 8 hodin) na dokončení. 
- Záložní verze: Pokud používáte nejnovější verzi služby Backup (označovanou jako rychlé obnovení), využívá více optimalizované proces než porovnání kontrolního součtu pro srovnání změn. Pokud používáte nejnovější verzi a odstranili snímek zálohy, zálohování přepne na použití kontrolního součtu porovnání a operace zálohování bude trvat déle než 24 hodin (nebo neúspěch).

## <a name="best-practices"></a>Osvědčené postupy 
Doporučujeme následující tyto postupy při konfiguraci záloh virtuálních počítačů:

- Zvažte úpravu čas plánu výchozí nastavení v zásadách. Pokud výchozí doba je, že je zásada 12:00 AM, představte si třeba narůstajících o minut tak, aby se optimálně použít prostředky.
- Pro virtuální počítač Premium přiděluje zálohy na funkci bez – rychlé RP přibližně 50 % celkový účet prostor úložiště. Služba Backup vyžaduje tento prostor ke zkopírování snímku do stejného účtu úložiště a přenosu do trezoru.
- Pro obnovení virtuálních počítačů z jediného trezoru, důrazně doporučujeme použít jiný [účty storage v2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) zajistit cílový účet úložiště není získat omezené. Například každý virtuální počítač musí mít jiný účet úložiště (Pokud 10 virtuálních počítačů se obnoví a zvažte použití 10 jiný účet úložiště).
- Obnovení z úložné vrstvy 1. úrovně (snímek) dokončí se během několika minut (protože jde o stejný účet úložiště) proti úložné vrstvy 2. úrovně (trezor), to může trvat hodiny. Doporučujeme, abyste použili [rychlé obnovení](backup-instant-restore-capability.md) funkce pro rychlejší obnovení pro případ, ve kterém jsou data dostupná ve vrstvě 1 (Pokud data obsahují pro obnovení z trezoru pak bude trvat dobu).
- Vzhledem k jak velké disky přistupuje aplikace běžící na virtuálním počítači IaaS je limit na počet disků v účtu úložiště. Ověření, pokud více disků jsou hostované na jeden účet úložiště. Obecně platí Pokud je 5 až 10 disků nebo více na jeden účet úložiště, Vyrovnávání zatížení přesunutím některé disky do samostatné účty úložiště.

## <a name="backup-costs"></a>Náklady na zálohování

Virtuální počítače Azure zálohovat pomocí služby Azure Backup platí pro ně [ceny služby Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

- Fakturace se nespustí, dokud se nedokončí prvního úspěšného zálohování. V tomto okamžiku začne fakturace za úložiště a chráněné virtuální počítače.
- Fakturace pokračuje, dokud je všechny dat záloh v trezoru pro virtuální počítač. Pokud zastavíte ochranu pro virtuální počítač, ale data záloh pro virtuální počítač existuje v trezoru, pokračuje fakturace.
- Fakturace pro zadanou virtuální počítač zastaví pouze v případě, že ochrana je zastavena a veškerá zálohovaná data se odstraní.
- Při zastavení ochrany a neexistují žádné aktivní úlohy zálohování, velikost z posledního úspěšného zálohování virtuálního počítače se změní velikost chráněné instance, používá pro měsíční náklady.
- Výpočet chráněné instance je založen na *skutečné* velikost virtuálního počítače, který je součet všech dat na virtuálním počítači, s výjimkou dočasné úložiště.
- Cena je založená na skutečná data uložená v datový disk.
- Ceny pro zálohování virtuálních počítačů není založena na maximální podporovaná velikost pro každý datový disk připojený k virtuálnímu počítači.
- Obdobně faktura za úložiště zálohování je založená na množství dat, která je uložená ve službě Azure Backup, který je součtem skutečná data v každém bodu obnovení.

Jako příklad může posloužit standardní A2 velikosti virtuálního počítače s, který má dva další datové disky o maximální velikosti 4 TB. V následující tabulce jsou uvedené dat uložených v každém z těchto disků:

**Disk** | **Maximální velikost** | **Skutečná data k dispozici**
--- | --- | ---
Disk OS | 4095 GB | 17 GB 
Místní/dočasného disku | 135 GB | 5 GB (není součástí pro zálohování) 
Datový disk 1 | 4095 GB | 30 GB 
Datový disk 2 | 4095 GB | 0 GB 

- Skutečná velikost virtuálního počítače v tomto případě je 17 GB + 30 GB + 0 GB = 47 GB.
- Tato velikost chráněné instance (47 GB) se změní základ pro měsíční náklady.
- Jak roste množství dat ve virtuálním počítači, velikost chráněné instance odpovídajícím způsobem použitý pro změny fakturace.


## <a name="next-steps"></a>Další postup

Nyní [Příprava](backup-azure-arm-vms-prepare.md) pro zálohování virtuálních počítačů Azure.
