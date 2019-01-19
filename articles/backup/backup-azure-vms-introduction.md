---
title: Informace o zálohování virtuálních počítačů Azure
description: Další informace o zálohování virtuálních počítačů Azure a poznamenejte si některé osvědčené postupy.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: raynew
ms.openlocfilehash: 128e389a8d6928f9f133fe9d649d0fc7e982e4df
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/18/2019
ms.locfileid: "54402350"
---
# <a name="about-azure-vm-backup"></a>Informace o zálohování virtuálních počítačů Azure

Tento článek popisuje, jak [služby Azure Backup](backup-introduction-to-azure-backup.md) zálohuje virtuální počítače Azure.

## <a name="backup-process"></a>Proces zálohování

Zde je, jak Azure Backup dokončení zálohování pro virtuální počítače Azure.

1. Služba Azure Backup pro virtuální počítače Azure, které jsou vybrány pro zálohování, iniciuje úlohu zálohování podle plánu zálohování, které zadáte.
2. Služba se aktivuje rozšíření zálohování.
    - Použijte virtuální počítače s Windows _VMSnapshot_ rozšíření.
    - Použití virtuálních počítačů s Linuxem _VMSnapshotLinux_ rozšíření.
    - Rozšíření se nainstaluje při prvním zálohování virtuálního počítače.
    - K instalaci rozšíření, musí být virtuální počítač spuštěný.
    - Pokud virtuální počítač není spuštěný, služba Backup pořídí snímek základního úložiště (protože aplikace neprovádí žádné zápisy, když je virtuální počítač zastavený).
4. Rozšíření zálohování pořídí snímek konzistentní vzhledem k aplikacím/konzistentní při selhání úroveň úložiště.
5. Po pořízení snímku data se přenesou do trezoru. Pro maximalizaci efektivity služba identifikuje a přenese pouze bloky dat, které se změnily od posledního zálohování (rozdílového).
5. Po dokončení přenosu dat se snímek odstraní a vytvoří se bod obnovení.

![Architektura zálohování virtuálních počítačů Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="data-encryption"></a>Šifrování dat

Azure Backup nepodporuje šifrování dat jako součást procesu zálohování. Azure Backup podporuje zálohování virtuálních počítačů Azure, které jsou šifrované pomocí Azure Disk Encryption.

- Zálohování virtuálních počítačů šifrované pomocí nástroje Bitlocker šifrování Key(BEK) pouze a klíče BEK spolu s Key(KEK) šifrovací klíč je podporována pro spravované a nespravované virtuální počítače Azure.
- BEK(secrets) a KEK(keys) zálohovat se šifrují, aby mohl být číst a použít jenom při obnovení zpět do služby key vault Autorizovaní uživatelé.
- Protože klíč BEK je také zálohovat, v situacích, kdy dojde ke ztrátě nebo klíče BEK oprávněným uživatelům můžete obnovit klíče BEK pro trezor klíčů a obnovení šifrovaných virtuálních počítačů. Klíče a tajné kódy šifrované virtuální počítače jsou zálohovány v zašifrované podobě, aby neoprávnění uživatelé ani Azure může číst nebo použití zálohovaných klíčů a tajných kódů. Pouze uživatelé s správnou úroveň oprávnění může zálohování a obnovení šifrovaných virtuálních počítačů, jakož i klíče a tajné kódy.

## <a name="snapshot-consistency"></a>Konzistence snímku

Azure Backup pořídí pořizovat snímky jsou spuštěné aplikace, snímky konzistentní.

- **Virtuální počítače s Windows**: Pro virtuální počítače s Windows služba Backup koordinuje s Stínová kopie svazku Service (VSS) Chcete-li získat snímek konzistentní vzhledem k aplikacím disků virtuálního počítače.
    - Ve výchozím nastavení Azure Backup pořídí úplné zálohy stínové kopie svazku. [Další informace](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx).
    - Pokud chcete změnit nastavení tak, aby zálohování Azure trvá záložní kopie VSS, nastavte následující klíč registru:
        ```
        [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
        ""USEVSSCOPYBACKUP"="TRUE"
        ```
        - Spustit následující příkaz z příkazového řádku se zvýšenými oprávněními (jako správce), chcete-li nastavit výše uvedené klíče registru:
          ```
          REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgent" /v USEVSSCOPYBACKUP /t REG_SZ /d TRUE /f
          ```
- **Virtuální počítače s Linuxem**: Aby se zajistilo, že jsou vaše virtuální počítače s Linuxem konzistentní při Azure Backup pořídí snímek, můžete použít rozhraní Linux předzálohovací a pozálohovací skript. Můžete napsat vlastní skripty zajistit konzistenci při pořízení snímku virtuálního počítače.
    -  Azure Backup vyvolá pouze před a po skriptech psaných vámi.
    - Pokud se předzálohovací skript a pozálohovacích skriptů proběhl úspěšně, Azure Backup označí bod obnovení jako konzistentní s aplikací. Však si nakonec odpovědné za konzistentnost aplikací při použití vlastních skriptů.
    - [Další informace](backup-azure-linux-app-consistent.md) týkající se konfigurace skriptů.


#### <a name="consistency-types"></a>Typy konzistence

Následující tabulka vysvětluje různé typy konzistence.

**snímek** | **Podrobnosti** | **Obnovení** | **Posouzení**
--- | --- | --- | ---
**Konzistentní s aplikací** | Zálohování konzistentní vzhledem k zachycení paměti obsahu a čeká se na vstupně-výstupních operací. Snímky konzistentní s pomocí zapisovače VSS (nebo předzálohovacího nebo pozálohovacího skriptu pro Linux), který zajištění konzistence dat aplikací, než dojde k zálohování. | Při obnovení s konzistentní vzhledem k snímku virtuálního počítače se spustí. Neexistuje žádný poškození nebo ztrátu. Spuštění aplikace v konzistentním stavu. | Windows: Všechny zapisovače VSS úspěšné<br/><br/> Linux: Předzálohovacího nebo pozálohovacího skripty jsou konfigurovány a byla úspěšná
**Konzistentní se systémem souborů** | Konzistentní zálohování souborů poskytuje konzistentní zálohování souborů na disku díky pořizování snímku všechny soubory ve stejnou dobu.<br/><br/> | Při obnovování s konzistentními snímku virtuálního počítače se spustí. Neexistuje žádný poškození nebo ztrátu. Aplikace je třeba implementovat vlastní mechanismus "Opravit" Ujistěte se, že je obnovená data konzistentní vzhledem k aplikacím. | Windows: Některé zapisovače služby VSS se nezdařila <br/><br/> Linux: Výchozí (Pokud předzálohovacího nebo pozálohovacího skriptů nejsou nakonfigurovaná nebo neúspěšných)
**Konzistentní při selhání** | Konzistenci při chybě často dochází při vypnutí virtuálního počítače Azure v době zálohování.  Pouze data, která již existuje na disku v době zálohování je zaznamenat a zálohovat.<br/><br/> Vytvoření bodu obnovení konzistentního nezaručuje konzistenci dat pro operační systém nebo aplikace. | Nejsou žádné záruky, ale obvykle spustí virtuální počítač a způsobem s diskem zaškrtněte, pokud chcete opravit chyby poškozující. Žádná data v paměti nebo zápisu, které nebyly převedeny na disku se ztratí. Aplikace implementovat vlastní ověřovací data. Například pro aplikaci na databázi, pokud transakčního protokolu obsahuje položky, které nejsou v databázi, databázový software postupně až do data jsou konzistentní vzhledem k aplikacím. | Virtuální počítač je ve stavu vypnutí


## <a name="service-and-subscription-limits"></a>Omezení služby a předplatného

Azure Backup má několik omezení předplatných a trezory.

[!INCLUDE [azure-backup-limits](../../includes/azure-backup-limits.md)]


## <a name="backup-performance"></a>Výkon zálohování

### <a name="disk-considerations"></a>Důležité informace o disku

Zálohováním všech disků Virtuálního počítače paralelně optimalizuje operace zálohování. Například pokud má čtyři disky virtuální počítač, se služba pokusí zálohovat všechny čtyři disky paralelně. Pro každý disk zálohovaný Azure Backup přečte bloky na disku a ukládá jenom změněná data (přírůstkové zálohování).


### <a name="scheduling-considerations"></a>Aspekty plánování

Plánování zálohování má vliv na výkon.

- Pokud konfigurujete zásady, aby všechny virtuální počítače zálohované ve stejnou dobu, jste naplánovali zaseknutý provoz, jak procesu zálohování se pokusí zálohovat všechny disky paralelně.
- Pro omezení provozu v zálohování, zálohování v různé době jeden den platformě bez překrytí různých virtuálních počítačů.


### <a name="time-considerations"></a>Důležité informace o času

Při načítání a kopírování dat se neztrácí nejdéle zálohování, přidejte další operace celkový čas potřebný k zálohování virtuálního počítače:

- **Instalace rozšíření zálohování**: Čas potřebný k instalaci nebo aktualizaci linka záložního telefonu.
- **Aktivační událost snímku**: Čas potřebný k aktivaci snímku. Snímky se aktivují přibližně v době plánované zálohování.
- **Doba čekání ve frontě**: Od zálohování služby procesy úloh z více účtů úložiště zákazníka ve stejnou dobu nemusí data snímku hned zkopírovat do trezoru služby Recovery Services. Ve špičkách zatížení může trvat až 8 hodin předtím, než zálohy se zpracovávají. Celkový čas zálohování virtuálního počítače je však méně než 24 hodin denně zásady zálohování.
- **Prvotní záloha**: I když je platný pro přírůstkové zálohování celkový čas zálohování za méně než 24 hodin, nemusí být pro první zálohy. Čas potřeby bude záviset na velikosti dat a kdy dochází k zálohování.
- **Doba přenosu dat**: Čas potřebný pro zálohovací služby compute přírůstkové změny z předchozí zálohy a přenést tyto změny do trezoru úložiště.

### <a name="factors-affecting-backup-time"></a>Faktorů, které ovlivňují dobu zálohování

Zálohování se skládá z dvou fází pořizování snímků a snímky přenosu do trezoru. Služba Backup optimalizuje úložiště.

- Při přenosu dat snímků do trezoru, přenese službu pouze přírůstkové změny z předchozího snímku.
- Pokud chcete zjistit přírůstkové změny, vypočítá službu kontrolních součtů bloků.
    - Pokud se změní bloku, blok je označena jako blok, který se pošlou do trezoru.
    - Cvičení service dále do všech identifikovaných bloky, hledají příležitosti minimalizovat objem dat pro přenos.
    - Po vyhodnocení všechny změněné bloky, služba spojí změny a odešle je do trezoru.

Situace, které můžou ovlivnit dobu zálohování, patří:

- **Prvotní zálohování pro nově přidaný disk na Virtuálním počítači už chráněných**: Pokud virtuální počítač Probíhá přírůstkové záloze a přidá nový disk k tomuto virtuálnímu počítači, můžete nově přidaný disk má podstoupit počáteční replikace spolu s rozdílovou replikaci stávající disky zálohování doba trvání jít dál 24 hodin.
- **Fragmentace**: Produktu pro zálohování hledá přírůstkové změny mezi operacemi dvě zálohy. Operace zálohování jsou rychlejší při změny na disku jsou společně umístěná ve srovnání s změny jsou rozšíření napříč pak disku. 
- **Změny**: Denní četnosti změn (pro přírůstkové replikace) disku většího než 200 GB může trvat větší než 8 hodin k dokončení operace. Pokud virtuální počítač má více než jeden disk a jeden z těchto disků trvá delší dobu zálohování, pak ho může mít vliv na celkovou zálohování (nebo může vést k selhání). 
- **Kontrolní součet porovnání (kopie) režim**: Režim kopie je poměrně pomalejší než optimalizovaný režim používaný rychlé RP. Pokud už používáte rychlé RP a odstranily snímky vrstvy 1, zálohování přepne do režimu kopie způsobí operace zálohování do 24 hodin (nebo neúspěch).

## <a name="restore-considerations"></a>Důležité informace o obnovení

Operace obnovení se skládá ze dvou hlavních úloh: kopírování dat z trezoru na zvoleném účtu úložiště a vytvoření virtuálního počítače. Čas potřebný ke zkopírování dat z trezoru závisí na kde jsou zálohy uložené v Azure a umístění účtu úložiště. Čas potřebný ke kopírování dat závisí na:

- **Doba čekání ve frontě**: Protože procesy služeb ve stejnou dobu obnovení úlohy z více účtů úložiště, žádosti o obnovení jsou vloženy do fronty.
- **Kopírování dat čas**: Data zkopírována z úložiště do účtu úložiště. Obnovit doba závisí na vstupně-výstupních operací a propustnosti vybraný účet úložiště, která používá službu Azure Backup. Ke snížení doby kopírování během procesu obnovení, vyberte účet úložiště s jinými aplikace zápisy a čtení není načtený.

## <a name="best-practices"></a>Osvědčené postupy

Doporučujeme následující tyto postupy při konfiguraci záloh virtuálních počítačů:

- Zvažte úpravy výchozích zásad čas k dispozici (pro příklad. Pokud je vaše výchozí zásady čas 12:00 dop. zvažte jeho zvýšením v minutách) pořizování snímků dat k zajištění optimální používání prostředků.
- Pro virtuální počítač Premium přiděluje zálohy na funkci bez – rychlé RP přibližně 50 % celkový účet prostor úložiště. Služba Backup vyžaduje tento prostor ke zkopírování snímku do stejného účtu úložiště a přenosu do trezoru.
- Pro obnovení virtuálních počítačů z jediného trezoru, důrazně doporučujeme použít jiný [účty storage v2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) zajistit cílový účet úložiště není získat omezené. Například každý virtuální počítač musí mít jiný účet úložiště (Pokud 10 virtuálních počítačů se obnoví a zvažte použití 10 jiný účet úložiště).
- Obnovení z úložné vrstvy 1. úrovně (snímek) dokončí se během několika minut (protože jde o stejný účet úložiště) proti úložné vrstvy 2. úrovně (trezor), to může trvat hodiny. Doporučujeme, abyste použili [rychlé obnovení](backup-instant-restore-capability.md) funkce pro rychlejší obnovení pro případ, ve kterém jsou data dostupná ve vrstvě 1 (Pokud data obsahují pro obnovení z trezoru pak bude trvat dobu).
- Vzhledem k jak velké disky přistupuje aplikace běžící na virtuálním počítači IaaS je limit na počet disků v účtu úložiště. Ověření, pokud více disků jsou hostované na jeden účet úložiště. Obecně platí Pokud je 5 až 10 disků nebo více na jeden účet úložiště, Vyrovnávání zatížení přesunutím některé disky do samostatné účty úložiště.

## <a name="backup-costs"></a>Náklady na zálohování

Virtuální počítače Azure zálohovat pomocí služby Azure Backup platí pro ně [ceny služby Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

- Fakturace se nespustí, dokud se nedokončí prvního úspěšného zálohování. V tomto okamžiku začne fakturace za úložiště a chráněné instance.
- Fakturace pokračuje, dokud není žádné zálohování dat uložených v trezoru pro virtuální počítač. Pokud zastavíte ochranu na virtuálním počítači, ale zálohování dat virtuálních počítačů existuje v trezoru, pokračuje fakturace.
- Fakturace pro zadanou virtuální počítač zastaví pouze v případě, že ochrana je zastavena a veškerá zálohovaná data se odstraní.
- Při zastavení ochrany a neexistují žádné aktivní úlohy zálohování, velikost z posledního úspěšného zálohování virtuálního počítače se změní velikost chráněné Instance použitá pro měsíční náklady.
- Výpočet chráněné instance je založen na *skutečné* velikost virtuálního počítače, který je součet všech dat na virtuálním počítači, s výjimkou dočasné úložiště.
- Cena je založená na skutečná data uložená v datový disk.
- Ceny pro zálohování virtuálních počítačů není založena na maximální podporovaná velikost pro každý datový disk připojený k virtuálnímu počítači.
- Obdobně faktura za úložiště zálohování je založená na množství dat, která je uložená ve službě Azure Backup, který je součtem skutečná data v každém bodu obnovení.

Jako příklad může posloužit standardní A2 velikosti virtuálního počítače, který má dva další datové disky o maximální velikosti 4 TB. V následující tabulce jsou uvedené dat uložených v každém z těchto disků:

| Typ disku | Maximální velikost | Skutečná data k dispozici |
| --------- | -------- | ----------- |
| Disk operačním systému |4095 GB |17 GB |
| Místní disk nebo dočasného disku |135 GB |5 GB (není součástí pro zálohování) |
| Datový disk 1 |4095 GB |30 GB |
| Datový disk 2 |4095 GB |0 GB |

- Skutečná velikost virtuálního počítače pro virtuální počítač v tomto případě je 17 GB + 30 GB + 0 GB = 47 GB.
- Tato velikost chráněné Instance (47 GB) se změní základ pro měsíční náklady.
- Jak roste množství dat na virtuálním počítači, velikost chráněné Instance odpovídajícím způsobem použitý pro změny fakturace.


## <a name="next-steps"></a>Další postup

Po kontrole procesu zálohování a důležité informace o výkonu, postupujte takto:

- [Další informace o](../virtual-machines/windows/premium-storage-performance.md) ladění aplikací pro zajištění optimálního výkonu pomocí úložiště Azure. Článek fokus pro službu storage úrovně premium, ale je také možné použít disky storage úrovně standard.
- [Začínáme](backup-azure-arm-vms-prepare.md) zálohou kontrolou podpora virtuálních počítačů a omezení, vytvoření trezoru a probíhá příprava pro zálohy virtuálních počítačů.
