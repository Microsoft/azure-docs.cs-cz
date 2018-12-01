---
title: Plánování infrastruktury zálohování virtuálních počítačů v Azure
description: Důležité informace týkající se plánování pro zálohování virtuálních počítačů v Azure
services: backup
author: markgalioto
manager: carmonm
keywords: zálohování virtuálních počítačů, zálohování virtuálních počítačů
ms.service: backup
ms.topic: conceptual
ms.date: 8/29/2018
ms.author: markgal
ms.openlocfilehash: 806d68370921a7658066a9bad770b36b4e8e59bf
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52680034"
---
# <a name="plan-your-vm-backup-infrastructure-in-azure"></a>Plánování infrastruktury zálohování virtuálních počítačů v Azure
Tento článek obsahuje výkonu a prostředků návrhy vám pomohou při plánování infrastruktury zálohování virtuálních počítačů. Také definuje klíčové aspekty služby Backup; Tyto aspekty může být důležité při určování architektury, plánování kapacity a plánování. Pokud jste [připravit vaše prostředí](backup-azure-arm-vms-prepare.md), plánování je dalším krokem, než začnete [k zálohování virtuálních počítačů](backup-azure-arm-vms.md). Pokud potřebujete další informace o virtuálních počítačích Azure, přečtěte si [dokumentace k virtuálním počítačům](https://azure.microsoft.com/documentation/services/virtual-machines/).

> [!NOTE]
> Tento článek je pro použití s spravované a nespravované disky. Pokud používáte nespravované disky, existují doporučení pro účet úložiště. Pokud používáte [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md), není nutné se starat o problémy s využitím výkonu nebo prostředek. Nastavení Azure optimalizuje využití úložiště za vás.
>

## <a name="how-does-azure-back-up-virtual-machines"></a>Jak funguje Azure zálohujete virtuální počítače?
Když služba Azure Backup zahájí úlohu zálohování v naplánovaném čase, aktivační události služby rozšíření zálohování k vytvoření snímku bodu v čase. Služba Azure Backup využívá _VMSnapshot_ rozšíření ve Windows a _VMSnapshotLinux_ rozšíření v systému Linux. Rozšíření se nainstaluje při prvním zálohování virtuálního počítače. K instalaci rozšíření, musí být virtuální počítač spuštěný. Pokud virtuální počítač není spuštěný, služba Backup pořídí snímek základního úložiště (protože aplikace neprovádí žádné zápisy, když je virtuální počítač zastavený).

Při pořizování snímku virtuálních počítačů Windows se služba zálohování sladí se službou Stínová kopie svazku k získání konzistentního snímku disků virtuálního počítače. Pokud zálohujete virtuální počítače s Linuxem, můžete napsat vlastní skripty zajistit konzistenci při pořízení snímku virtuálního počítače. Podrobnosti o vyvolání tyto skripty jsou uvedeny dále v tomto článku.

Jakmile služba Azure Backup pořídí snímek, data se přenesou do trezoru. Pro maximalizaci efektivity služba identifikuje a přenese pouze bloky dat, které se změnily od posledního zálohování.

![Architektura zálohování virtuálních počítačů Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

Po dokončení přenosu dat se snímek odstraní a vytvoří se bod obnovení.

> [!NOTE]
> 1. Během procesu zálohování Azure Backup neobsahuje dočasný disk připojen k virtuálnímu počítači. Další informace najdete v blogu na [dočasného úložiště](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).
> 2. Azure Backup pořídí snímek úroveň úložiště a přenese tento snímek do trezoru. Neměnit klíče účtu úložiště, dokud neskončí úloha zálohování.
>

### <a name="data-consistency"></a>Konzistence dat
Zálohování a obnovení důležitých dat je skutečnost, že je nutné zálohovat důležitá obchodní data a aplikace, které vytvářejí data složité obchodní běží. Z toho Azure Backup podporuje zálohy konzistentní s aplikací pro Windows a virtuální počítače s Linuxem
#### <a name="windows-vm"></a>Virtuální počítač s Windows
Azure Backup trvá úplné zálohování VSS na virtuálních počítačích s Windows (Další informace o [úplné zálohování VSS](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx)). Pokud chcete povolit záložní kopie VSS, následující klíč registru se musí nastavit na virtuálním počítači.

```
[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
"USEVSSCOPYBACKUP"="TRUE"
```

#### <a name="linux-vms"></a>Virtuální počítače s Linuxem

Azure Backup poskytuje skriptovací rozhraní pro řízení pracovního postupu zálohování a prostředí. K zajištění konzistentní zálohování virtuálních počítačů s Linuxem použijte skriptovací rozhraní framework vytvářet skripty před a pozálohovacích skriptů. Vyvolání předzálohovací skript před pořizování snímku virtuálního počítače a pak vyvolejte pozálohovací skript po dokončení úlohy snímku virtuálního počítače. Další informace najdete v článku [Aplikačně konzistentní zálohování virtuálních počítačů s Linuxem](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

> [!NOTE]
> Azure Backup vyvolá pouze zákazník napsané předběžné a pozálohovacích skriptů. Pokud se předzálohovací skript a pozálohovacích skriptů proběhl úspěšně, Azure Backup označí bod obnovení jako aplikace konzistentní vzhledem k aplikacím. Zákazník ale nakonec odpovědné za konzistence aplikací při použití vlastních skriptů.
>

Následující tabulka popisuje typy konzistence a podmínky, které se objeví.

| Konzistence | Na základě VSS | Vysvětlení a podrobnosti |
| --- | --- | --- |
| Konzistentnost aplikací |Ano pro Windows|Konzistentnost aplikací je ideální pro úlohy, protože zajišťuje, že:<ol><li> Virtuální počítač *se spustí*. <li>Je *žádné poškození*. <li>Je *bez ztráty*.<li> Data jsou konzistentní s aplikací, která používá data, zahrnující aplikace v době zálohování--pomocí služby VSS nebo předzálohovacího nebo pozálohovacího skriptu.</ol> <li>*Virtuální počítače s Windows*-nejvíce Microsoft úlohy mají zapisovače služby VSS, které jsou spuštěny akce specifické pro úlohy související s konzistence dat. Například SQL Server VSS writer zajišťuje zápisy do souboru protokolu transakcí a databáze, jsou provedeny správně. Pro zálohy virtuálních počítačů IaaS Windows Pokud chcete vytvořit bod obnovení konzistentní s aplikací, rozšíření zálohování musíte vyvolat pracovní postup služby VSS a dokončete ho, než pořizování snímku virtuálního počítače. Pro snímek virtuálního počítače Azure chcete být přesné musíte dokončit také zapisovače VSS všech aplikací virtuálního počítače Azure. (Další informace [Základy služby Stínová kopie svazku](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx) a ponořte se hlouběji do podrobností o [jak to funguje](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx)). </li> <li> *Virtuální počítače s Linuxem*– zákazníci mohou spouštět [vlastních předsnímkových a posnímkových skriptů k zajištění konzistence aplikace](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent). </li> |
| Systém souborů konzistence |Ano – pro počítače se systémem Windows |Existují dva scénáře, kde může být bod obnovení *-konzistentní se systémem souborů*:<ul><li>Zálohování virtuálních počítačů s Linuxem v Azure, bez předprodukční script/po script nebo pokud předprodukční script/po script se nezdařilo. <li>VSS selhání během zálohování pro virtuální počítače s Windows v Azure.</li></ul> V obou těchto případech je nejlepší, které lze provést Ujistěte se, že: <ol><li> Virtuální počítač *se spustí*. <li>Je *žádné poškození*.<li>Je *bez ztráty*.</ol> Je potřeba implementovat vlastní mechanismus "Opravit" na obnovená data aplikace. |
| Konzistenci při selhání. |Ne |Tato situace je ekvivalentní k virtuálnímu počítači dochází k "selhání" (prostřednictvím obnovitelně nebo pevné reset). Konzistenci při chybě obvykle dochází, když virtuální počítač Azure je vypínání v době zálohování. Bod obnovení konzistentní při selhání poskytuje žádné záruky, konzistenci dat na střední úložiště – buď z hlediska operačního systému nebo aplikace. Pouze data, která již existuje na disku v době zálohování je zaznamenat a zálohovat. <br/> <br/> Když neexistují žádné záruky, obvykle, následovaný kontroly disku boty operačního systému, postupu, jako je nástroj chkdsk a opravte případné chyby poškození. Žádná data v paměti nebo zápisů, které nebyly převedeny na disku se ztratí. Aplikace se obvykle řídí s vlastní mechanismus ověřování v případě, že vrácení změn dat je potřeba udělat. <br><br>Jako příklad Jestliže transakčního protokolu obsahuje položky, není k dispozici v databázi, databázový software vrácena zpět, dokud se data konzistentní vzhledem k aplikacím. Když data se pak rozdělí mezi víc virtuálních disků (jako jsou rozložené svazky), poskytuje bod obnovení konzistentní při selhání žádné záruky správnost data. |

## <a name="performance-and-resource-utilization"></a>Výkon a využití prostředků
Stejně jako zálohovací software, který je nasazená místně měli byste naplánovat kapacitu a využití prostředků potřebám při zálohování virtuálních počítačů v Azure. [Omezení služby Azure Storage](../azure-subscription-service-limits.md#storage-limits) definují strukturu nasazení virtuálních počítačů pro maximální výkon s minimální dopad na probíhající úlohy.

### <a name="number-of-disks"></a>Počet disků
Proces zálohování se pokusí co nejrychleji dokončit úlohu zálohování. Přitom spotřebuje množství prostředků, jak je to možné. Však všechny vstupně-výstupní operace se uplatňuje limit vycházející *nastavte propustnost pro jeden objekt Blob*, která je omezena limitem 60 MB za sekundu. Při pokusu o maximální jeho rychlost, pokusí proces zálohování k zálohování všech disků Virtuálního počítače *paralelně*. Pokud virtuální počítač má čtyři disky, se služba pokusí zálohovat všechny čtyři disky paralelně. **Počet disků** zálohovaných, je nejdůležitějším faktorem při určování provoz zálohování účtu úložiště.

### <a name="backup-schedule"></a>Plán zálohování
Dodatečný faktor, který má vliv na výkon je **plán zálohování**. Pokud nakonfigurujete zásady tak, že jsou všechny virtuální počítače zálohované ve stejnou dobu, jste naplánovali zaseknutý provoz. Proces zálohování se pokusí zálohovat všechny disky paralelně. Pro omezení provozu v zálohování, zálohování v různé době jeden den platformě bez překrytí různých virtuálních počítačů.

## <a name="capacity-planning"></a>Plánování kapacity
Stáhněte si [záložní kapacitu virtuálních počítačů, plánování Excelová tabulka](https://gallery.technet.microsoft.com/Azure-Backup-Storage-a46d7e33) zobrazíte dopadu disku a plánu zálohování volby.

### <a name="backup-throughput"></a>Zálohování propustnost
Pro každý disk zálohovaný Azure Backup přečte bloky na disku a ukládá jenom změněná data (přírůstkové zálohování). V následující tabulce jsou uvedeny průměrné hodnoty propustnosti služby Backup. Pomocí následující data, můžete odhadnout množství času potřebného k zálohování na disk danou velikost.

| Operace zálohování | Nejlepší propustnost |
| --- | --- |
| Prvotní zálohování |160 MB/s |
| Přírůstkové zálohování (DR) |640 MB/s <br><br> Propustnost dochází k výraznému snížení Pokud změněná data (který je možné zálohovat) rozptýlena mezi disku.|

## <a name="total-vm-backup-time"></a>Celkový čas zálohování virtuálního počítače
Při načítání a kopírování dat se neztrácí většinu doby zálohování, jiné operace přispívat celkový čas potřebný k zálohování virtuálního počítače:

* Čas potřebný k [nainstalovat nebo aktualizovat rozšíření zálohování](backup-azure-arm-vms.md).
* Čas snímku, což je čas potřebný k aktivaci snímku. Snímky se aktivují přibližně v době plánované zálohování.
* Doba čekání ve frontě. Od zálohování služby procesy úloh z více zákazníků ve stejnou dobu nemusí data snímku hned zkopírovat do trezoru služby Recovery Services. Ve špičkách zatížení může trvat až 8 hodin předtím, než zálohy se zpracovávají. Celkový čas zálohování virtuálního počítače je však méně než 24 hodin denně zásady zálohování.
Celkový čas zálohování za méně než 24 hodin je platný pro přírůstkové zálohování, ale nemusí být pro první zálohy. První čas zálohování je přímo úměrná a může být kratší než 24 hodin v závislosti na velikosti dat a kdy dochází k zálohování.
* Doba přenosu dat, dobu potřebnou pro zálohování služby compute přírůstkové změny z předchozí zálohy a přenést tyto změny do trezoru úložiště.

### <a name="why-are-backup-times-longer-than-12-hours"></a>Proč jsou časy zálohování delší než 12 hodin?

Zálohování se skládá ze dvou fází: pořizování snímků a snímky přenosu do trezoru. Služba Backup optimalizuje úložiště. Při přenosu dat snímků do trezoru, přenese službu pouze přírůstkové změny z předchozího snímku.  Pokud chcete zjistit přírůstkové změny, vypočítá službu kontrolních součtů bloků. Pokud se změní bloku, blok je označena jako blok, který se pošlou do trezoru. Cvičení služby potom dále do všech identifikovaných bloky, hledají příležitosti minimalizovat objem dat pro přenos. Po vyhodnocení všechny změněné bloky, služba spojí změny a odešle je do trezoru. V některých starších aplikací nejsou malé, fragmentované zápisy optimální pro úložiště. Pokud tento záznam obsahuje mnoho malých, fragmentované zápisy, službu stráví další čas zpracování dat zapsaných aplikace. Pro aplikace běžící uvnitř virtuálního počítače je minimální doporučené zápisům aplikace bloku 8 KB. Pokud vaše aplikace používá blok méně než 8 KB, se provádí výkon zálohování. Nápovědu k ladění aplikace ke zlepšení výkonu zálohování, naleznete v tématu [ladění aplikací pro zajištění optimálního výkonu pomocí úložiště Azure](../virtual-machines/windows/premium-storage-performance.md). I když v článku věnovaném výkon zálohování se používají příklady úložiště úrovně Premium, je možné použít disky storage úrovně Standard pokynů.<br>
Může existovat několik důvodů, proč čas dlouho zálohování:
  1. **První zálohování pro nově přidaný disk do už chráněných virtuálních počítačů** <br>
    Pokud virtuální počítač je již přijít o které jsou předmětem přírůstkové zálohování, když je přidán nový disky zálohování SLA 1 den v závislosti na velikosti nový disk.
  2. **Fragmentace** <br>
    Pokud aplikace zákazníka je špatně nakonfigurována, která přebírá fragmentované malých zápisů.<br>
  3. **Účet úložiště zákazníka přetížení** <br>
      a. Pokud je naplánováno zálohování v době zákazníka produkční aplikace.  
      b. Pokud více než 5 až 10 disků jsou hostované v rámci stejného účtu úložiště.<br>
  4. **Režim Check(CC) konzistence** <br>
      > 1 TB disky, pokud probíhá zálohování v režimu kopie z důvodů uvedených níže:<br>
        a. Přesune spravovaného disku jako součást zákazníka restartování virtuálního počítače.<br>
        b. Zákazník podporuje snímku na základní objekt blob.<br>

## <a name="total-restore-time"></a>Obnovení celkový čas

Operace obnovení se skládá ze dvou hlavních úloh: kopírování dat do účtu úložiště vybrané uživatele obnovení z trezoru a vytvoření virtuálního počítače. Čas potřebný ke zkopírování dat z trezoru závisí na kde jsou zálohy uložené v Azure a umístění účtu úložiště zákazníka. Čas potřebný ke kopírování dat závisí na:
* Doba čekání ve frontě – protože procesy služeb obnovení úlohy z více zákazníků ve stejnou dobu obnovení žádosti se do fronty.
* Čas – kopírování dat se kopírují Data z úložiště do účtu úložiště zákazníka. Obnovit doba závisí na vstupně-výstupních operací a propustnosti služby Azure Backup získává v účtu úložiště vybraného zákazníka. Ke snížení doby kopírování během procesu obnovení, vyberte účet úložiště s jinými aplikace zápisy a čtení není načtený.

## <a name="best-practices"></a>Osvědčené postupy

Doporučujeme následující tyto postupy při konfiguraci zálohování pro všechny virtuální počítače:

* Není naplánovat zálohování pro více než 10 klasické virtuální počítače ze stejné cloudové službě, ve stejnou dobu. Pokud chcete zálohovat několik virtuálních počítačů ze stejné cloudové službě, odstupňování časů zahájení zálohování o hodinu.
* Není naplánovat zálohování pro více než 100 virtuálních počítačů z jednoho trezoru ve stejnou dobu.
* Plánování zálohování virtuálního počítače během hodin mimo špičku. Služba Backup díky tomu používá vstupně-výstupních operací pro přenos dat z účtu úložiště uživatele do trezoru.
* Ujistěte se, že virtuální počítače s Linuxem pro zálohování povolené, máte Python verze 2.7 nebo novější.

### <a name="best-practices-for-vms-with-unmanaged-disks"></a>Osvědčené postupy pro virtuální počítače s nespravovanými disky

Následující doporučení platí pouze pro virtuální počítače s využitím nespravovaných disků. Pokud vaše virtuální počítače používají spravované disky, služba Backup zpracovává všechny činnosti správy úložiště.

* Ujistěte se, že chcete použít zásady zálohování pro virtuální počítače, které jsou rozděleny mezi více účtů úložiště. Více než 20 celkový počet disků z jednoho účtu úložiště by měla být chráněna stejný plán zálohování. Pokud máte větší než 20 disků v účtu úložiště, rozložení těchto virtuálních počítačů mezi více zásad, chcete-li získat požadované vstupně-výstupních operací ve fázi převodu z procesu zálohování.
* Neobnovujte virtuálního počítače běžící v Premium storage do stejného účtu úložiště. Pokud proces operace obnovení se shoduje se operace zálohování, snižuje IOPS k dispozici pro zálohování.
* Pro zálohování virtuálních počítačů úrovně Premium na zásobník záloh virtuálních počítačů V1 měli byste přidělit jenom 50 % celkový úložný prostor účet tak, aby služba Backup můžete zkopírování snímku do účtu úložiště a přenosu dat z účtu úložiště do trezoru.


## <a name="data-encryption"></a>Šifrování dat
Azure Backup nešifruje data jako součást procesu zálohování. Můžete ale šifrování dat v rámci virtuálního počítače a bez problémů zálohování chráněných dat (Další informace o [zálohování šifrovaných dat](backup-azure-vms-encryption.md)).

## <a name="calculating-the-cost-of-protected-instances"></a>Výpočet nákladů na chráněné instance
Virtuální počítače s Azure, které jsou zálohovány pomocí Azure Backup platí pro ně [ceny služby Azure Backup](https://azure.microsoft.com/pricing/details/backup/). Výpočet chráněné instance je založen na *skutečné* velikost virtuálního počítače, který je součet všech dat na virtuálním počítači – s výjimkou dočasné úložiště.

Ceny pro zálohování virtuálních počítačů není založena na maximální podporovaná velikost pro každý datový disk připojený k virtuálnímu počítači. Cena je založená na skutečná data uložená v datový disk. Obdobně faktura za úložiště zálohování je založená na množství dat, která je uložená ve službě Azure Backup, který je součtem skutečná data v každém bodu obnovení.

Jako příklad může posloužit standardní A2 velikosti virtuálního počítače, který má dva další datové disky o maximální velikosti 4 TB. V následující tabulce jsou uvedené dat uložených v každém z těchto disků:

| Typ disku | Maximální velikost | Skutečná data k dispozici |
| --------- | -------- | ----------- |
| Disk operačním systému |4095 GB |17 GB |
| Místní disk nebo dočasného disku |135 GB |5 GB (není součástí pro zálohování) |
| Datový disk 1 |4095 GB |30 GB |
| Datový disk 2 |4095 GB |0 GB |

Skutečná velikost virtuálního počítače v tomto případě je 17 GB + 30 GB + 0 GB = 47 GB. Tato velikost chráněné Instance (47 GB) se změní základ pro měsíční náklady. Jak roste množství dat na virtuálním počítači, velikost chráněné Instance odpovídajícím způsobem použitý pro změny fakturace.

Fakturace se nespustí, dokud se nedokončí prvního úspěšného zálohování. V tomto okamžiku začne fakturace za úložiště a chráněné instance. Fakturace pokračuje, dokud není žádné zálohování dat uložených v trezoru pro virtuální počítač. Pokud zastavíte ochranu na virtuálním počítači, ale zálohování dat virtuálních počítačů existuje v trezoru, pokračuje fakturace.

Fakturace za zadaný virtuální počítač zastaví pouze v případě, že ochrana je zastavena a veškerá zálohovaná data se odstraní. Při zastavení ochrany a neexistují žádné aktivní úlohy zálohování, velikost z posledního úspěšného zálohování virtuálního počítače se změní velikost chráněné Instance použitá pro měsíční náklady.

## <a name="questions"></a>Máte dotazy?
Máte-li nějaké dotazy nebo pokud víte o funkci, kterou byste uvítali, [odešlete nám svůj názor](https://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Další postup
* [Zálohování virtuálních počítačů](backup-azure-arm-vms.md)
* [Správa zálohování virtuálních počítačů](backup-azure-manage-vms.md)
* [Obnovení virtuálních počítačů](backup-azure-arm-restore-vms.md)
* [Řešení potíží s problémy se zálohováním virtuálních počítačů](backup-azure-vms-troubleshoot.md)
