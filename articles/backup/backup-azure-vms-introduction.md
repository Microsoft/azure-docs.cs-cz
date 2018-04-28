---
title: Plánování infrastruktury zálohování virtuálních počítačů v Azure | Microsoft Docs
description: Je důležité zvážit při plánování zálohování virtuálních počítačů v Azure
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: zálohování virtuálních počítačů, zálohování virtuálních počítačů
ms.assetid: 19d2cf82-1f60-43e1-b089-9238042887a9
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/23/2018
ms.author: markgal;trinadhk;sogup
ms.openlocfilehash: 299794b100ed438de2995d70419025dd686d2278
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="plan-your-vm-backup-infrastructure-in-azure"></a>Plánování infrastruktury zálohování virtuálních počítačů v Azure
Tento článek obsahuje výkonu a prostředků návrhy vám pomohou naplánovat zálohování infrastrukturu virtuálních počítačů. Definuje také klíčových aspektů služby zálohování; Tyto aspekty může být velmi důležité při určování vaší architektury plánování kapacity a plánování. Pokud jste [připravit vaše prostředí](backup-azure-arm-vms-prepare.md), plánování je dalším krokem, než začnete [k zálohování virtuálních počítačů](backup-azure-arm-vms.md). Pokud potřebujete další informace o virtuálních počítačích Azure, najdete v článku [virtuální počítače dokumentaci](https://azure.microsoft.com/documentation/services/virtual-machines/).

## <a name="how-does-azure-back-up-virtual-machines"></a>Jak funguje Azure zálohuje virtuální počítače?
Když služba Azure Backup spustí úlohu zálohování v naplánovaném čase, aktivační události služby rozšíření zálohování k pořízení snímku v daném okamžiku. Použití služby Azure Backup _VMSnapshot_ rozšíření v systému Windows a _VMSnapshotLinux_ rozšíření v systému Linux. Rozšíření je nainstalován během první zálohování virtuálních počítačů. Jak rozšíření nainstalovat, musí být virtuální počítač spuštěný. Pokud virtuální počítač není spuštěný, služba Backup pořídí snímek základního úložiště (protože aplikace neprovádí žádné zápisy, když je virtuální počítač zastavený).

Při pořizování snímku virtuálních počítačů Windows, služba zálohování koordinuje s Stínová kopie svazku Service (VSS) získat konzistentního snímku disky virtuálního počítače. Pokud zálohujete virtuální počítače s Linuxem, může zapisovat svých vlastních skriptů na zajištění konzistence při pořizování snímku virtuálního počítače. Podrobnosti o vyvolání tyto skripty jsou uvedeny dále v tomto článku.

Jakmile služba Azure Backup pořídí snímek, data se přenesou do trezoru. Pro maximalizaci efektivity služba identifikuje a přenese pouze bloky dat, které se změnily od posledního zálohování.

![Architektura zálohování virtuálního počítače Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

Po dokončení přenosu dat se snímek odstraní a vytvoří se bod obnovení.

> [!NOTE]
> 1. Během procesu zálohování Azure Backup neobsahuje dočasné disk připojený k virtuálnímu počítači. Další informace naleznete v příspěvku na [dočasné úložiště](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).
> 2. Azure Backup trvá úroveň úložiště snímků a přenese tento snímek do trezoru, neměňte klíče účtu úložiště, až do dokončení úlohy zálohování.
> 3. Pro virtuální počítače premium Azure Backup zkopíruje snímku k účtu úložiště. Toto je Ujistěte se, že služba zálohování používá dostatečná IOPS pro přenos dat k trezoru. Tato další kopie úložiště je účtovat podle přidělené velikost virtuálního počítače. 
>

### <a name="data-consistency"></a>Konzistence dat
Zálohování a obnovení obchodní důležitá data ztěžuje skutečnost, že důležitá obchodní data je třeba zálohovat při aplikace, které produkují data běží. Chcete-li to vyřešit, Azure Backup podporuje zálohování konzistentní s aplikací pro Windows a virtuální počítače s Linuxem
#### <a name="windows-vm"></a>Virtuální počítač s Windows
Zálohování Azure trvá úplné zálohování VSS na virtuálních počítačích systému Windows (Další informace o [úplné zálohování VSS](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx)). Pokud chcete povolit zálohování kopírováním služby Stínová kopie svazku, je třeba nastavit ve virtuálním počítači následující klíč registru.

```
[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
"USEVSSCOPYBACKUP"="TRUE"
```

#### <a name="linux-vms"></a>Virtuální počítače s Linuxem
Azure Backup poskytuje skriptovací rozhraní. K zajištění konzistence aplikace při zálohování virtuálních počítačů Linux, vytvořte vlastní skripty před a po skripty, které řídí zálohování pracovního postupu a prostředí. Zálohování Azure vyvolá předběžné skript před přepnutím snímek virtuálního počítače a vyvolá po skript po dokončení úlohy snímek virtuálního počítače. Další podrobnosti najdete v tématu [konzistentní zálohování virtuálních počítačů aplikace pomocí skriptů před a po skript](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).
> [!NOTE]
> Zálohování Azure pouze vyvolá zapsána zákazníka před a po skripty. Pokud skript před a po skripty spustí úspěšně, Azure Backup označí bod obnovení jako aplikace konzistentní. Zákazník je však nakonec odpovědné za konzistence aplikace při použití vlastních skriptů.
>


Tato tabulka popisuje typy konzistence a podmínky, které se objeví pod během virtuálního počítače Azure zálohování a obnovení postupy.

| Konzistence | Na základě služby Stínová kopie svazku | Vysvětlení a podrobnosti |
| --- | --- | --- |
| Konzistentnost aplikací |Ano pro Windows|Aplikace konzistence je ideální pro úlohy, protože zajišťuje, že:<ol><li> Virtuální počítač *spustí*. <li>Je *žádné poškození*. <li>Je *nedošlo ke ztrátě dat*.<li> Data jsou konzistentní vzhledem k aplikaci, která používá data, podle zahrnující aplikace v době zálohování – pomocí služby Stínová kopie svazku nebo předzálohovacího nebo pozálohovacího skriptu.</ol> <li>*Virtuální počítače Windows*-zapisovače služby Stínová kopie svazku, které provádějí konkrétní úlohy akcí souvisejících s konzistenci dat měly nejvíce Microsoft úlohy. Například Microsoft SQL Server obsahuje zapisovač VSS, které zajišťuje, že zápisy do souboru protokolu transakcí a databáze jsou provedeny správně. Pro zálohování virtuálního počítače Windows Azure vytvořit bod obnovení konzistentních s aplikací rozšíření zálohování musí volat pracovní postup služby Stínová kopie svazku a dokončete ji před přepnutím snímek virtuálního počítače. Pro vytvoření snímku virtuálního počítače Azure být přesná musíte dokončit také zapisovače VSS všech aplikací virtuálního počítače Azure. (Další informace [Základy služby Stínová kopie svazku](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx) a podrobné informace o podrobnosti o [jak to funguje](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx)). </li> <li> *Virtuální počítače s Linuxem*– zákazníci mohou spouštět [vlastních skriptů před a po skript pro zajištění konzistence aplikace](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent). </li> |
| Konzistence systému souborů |Ano – pro počítače se systémem Windows |Existují dva scénáře, kde může být bod obnovení *systému souborů konzistentní*:<ul><li>Zálohování virtuálních počítačů Linux v Azure, bez vedlejší script nebo po script nebo pokud vedlejší script nebo po script se nezdařilo. <li>Chyba služby Stínová kopie svazku během zálohování pro virtuální počítače Windows v Azure.</li></ul> V obou těchto případech je nejvhodnější, který se dá zajistit, aby: <ol><li> Virtuální počítač *spustí*. <li>Je *žádné poškození*.<li>Je *nedošlo ke ztrátě dat*.</ol> Aplikace je nutné implementovat vlastní mechanismus "oprava up" na obnovená data. |
| Konzistence havárií |Ne |Tato situace je ekvivalentní k virtuálnímu počítači má "selhání" (prostřednictvím buď logicky nebo pevné resetování). Konzistence havárií obvykle dojde, když je virtuální počítač Azure vypnout v době zálohování. Bod obnovení konzistentní při selhání poskytuje žádné záruky kolem konzistence dat na střední úložiště – buď z perspektivy operačního systému nebo aplikace. Pouze data, která již existuje na disku v době zálohování je zaznamenat a zálohovat. <br/> <br/> Když dochází obvykle žádné záruky, jednotlivými spuštěními operačního systému, a potom kontrolou disku postupu, jako je nástroj chkdsk a opravte případné chyby poškození. Dojde ke ztrátě všech dat v paměti nebo zápisů, které nebyly převedeny na disk. Aplikace se obvykle následuje s vlastní mechanismus ověřování v případě, že vrácení dat je potřeba udělat. <br><br>Jako příklad Pokud transakčního protokolu má položky nejsou k dispozici v databázi, databázový software zobrazí zpět, dokud data je konzistentní. Pokud data je rozdělena mezi více virtuálních disků (např. rozložené svazky), poskytuje bod konzistentní při selhání obnovení žádné záruky pro správnost data. |

## <a name="performance-and-resource-utilization"></a>Výkon a využití prostředků
Jako zálohovací software, který je nasazený na místě měli byste naplánovat kapacitu a využití prostředků musí při zálohování virtuálních počítačů v Azure. [Azure Storage omezuje](../azure-subscription-service-limits.md#storage-limits) definovat, jak struktury nasazení virtuálních počítačů pro získání maximálního výkonu s minimální vliv na spuštěné úlohy.

Při plánování výkonu zálohování věnujte pozornost následující omezení úložiště Azure:

* Maximální počet odchozí na účet úložiště
* Rychlost celkový počet požadavků na účet úložiště

### <a name="storage-account-limits"></a>Limity účtu úložiště
Zálohovaná data zkopírovat z účtu úložiště, přidá do vstupně výstupních operací za sekundu (IOPS) a odchozí (nebo propustnost) metriky účtu úložiště. Ve stejnou dobu jsou virtuální počítače využívají také IOPS a propustnosti. Cílem je zajistit, že zálohování a přenosy dat virtuálních počítačů nepřekračují omezení účtu úložiště.

### <a name="number-of-disks"></a>Počet disků
Proces zálohování se pokusí provést co nejrychleji dokončení úlohy zálohování. Při tom spotřebuje množství prostředků, jak je to možné. Však omezeno všechny vstupně-výstupních operací *propustnost cíl pro jeden objekt Blob*, což může mít 60 MB za sekundu. Při pokusu o jeho rychlost maximalizovat pokusí zálohovat všechny disky Virtuálního počítače proces zálohování *paralelně*. Pokud virtuální počítač má čtyři disky, služba se pokusí zálohovat všechny čtyři disky paralelně. **Počet disků** zálohovaných, je nejdůležitějším faktorem při určování provoz zálohování účet úložiště.

### <a name="backup-schedule"></a>Plán zálohování
Další faktor, který má dopad na výkon je **plán zálohování**. Pokud nakonfigurujete zásady tak, že všechny virtuální počítače jsou zálohovány ve stejnou dobu, jste naplánovali papír provoz. Proces zálohování se pokusí zálohovat všechny disky paralelně. Ke snížení provoz zálohování z účtu úložiště, zálohujte různé virtuální počítače v jinou dobu den, se bez překrytí.

## <a name="capacity-planning"></a>Plánování kapacity
Připravuje umístění předchozí faktorů, třeba naplánovat pro potřeby využití účtu úložiště. Stažení [Excelová tabulka pro plánování kapacity služby zálohování virtuálních počítačů](https://gallery.technet.microsoft.com/Azure-Backup-Storage-a46d7e33) zobrazíte dopad disku a plán zálohování volby.

### <a name="backup-throughput"></a>Zálohování propustnost
Pro každý disk zálohovaných Azure Backup čte bloky na disku a ukládá jenom změněné data (přírůstkové zálohování). V následující tabulce jsou uvedeny průměrné hodnoty propustnost služby zálohování. Pomocí následující data, můžete odhadnout množství času potřebné pro zálohování na disk na danou velikost.

| Operace zálohování | Nejlepší možný propustnost |
| --- | --- |
| Prvotní zálohování |160 MB/s |
| Přírůstkové zálohování (DR) |640 MB/s <br><br> Propustnost zahodí výrazně Pokud změněná data (ke které je možné zálohovat) jsou rozptýlená v rámci disku.|

## <a name="total-vm-backup-time"></a>Celkový čas zálohování virtuálních počítačů
Při čtení a kopírování dat je využita pro práci ve většině případů zálohování, jiné operace přispívat do celkový čas potřebný k zálohování virtuálního počítače:

* Doba potřebná pro [instalovat nebo aktualizovat rozšíření zálohování](backup-azure-arm-vms.md).
* Čas snímek, což je čas potřebný k aktivaci snímku. Snímky jsou aktivované blízko naplánovaný čas zálohování.
* Doba čekání ve frontě. Vzhledem k tomu, že služba zálohování je zpracování záloh z více zákazníků, kopírování zálohování dat ze snímku do zálohování nebo trezor služeb zotavení nemusí spustit okamžitě. V dobu ve špičce zatížení, čekání lze roztáhnout až osm hodin z důvodu počet záloh zpracovává. Celkový čas zálohování virtuálních počítačů je však méně než 24 hodin pro denní zásady zálohování. <br>
**To obsahuje platné pouze pro přírůstkové zálohy a ne pro první zálohy. První čas zálohování je úměrná a může být větší než 24 hodin v závislosti na množství dat a dochází k zálohování čas.**
* Doba přenosu dat, čas potřebný k trezoru úložiště služby zálohování výpočetní přírůstkové změny z předchozí zálohy a přenést tyto změny.

### <a name="why-am-i-observing-longer12-hours-backup-time"></a>Proč se sledování longer(>12 hours) zálohování čas?
Zálohování se skládá ze dvou fází: pořizování snímků a přenosu snímky do trezoru. Služba zálohování optimalizuje úložiště. Při přenosu dat snímků do trezoru, přenáší službu pouze přírůstkové změny z předchozí snímek.  Pokud chcete zjistit přírůstkové změny, vypočítá službu kontrolního součtu bloků. Pokud dojde ke změně bloku, blok bude označen jako blok k odeslání do trezoru. Potom služba projde další do každé identifikované bloků, hledá příležitosti minimalizovat objem dat pro přenos. Po vyhodnocení všechny změněné bloky, služba spojí změny a odesílá je do trezoru. V některých starších verzí aplikací nejsou malé, fragmentovaných zápisy optimální pro úložiště. Pokud snímek obsahuje mnoho malých, fragmentovaných zápisy, službu stráví další čas zpracování dat zapsaných pomocí aplikací. Blok zápisu doporučené aplikace ze služby Azure, pro aplikace spuštěné ve virtuálním počítači, je minimálně 8 KB. Pokud vaše aplikace používá blok menší než 8 KB, se provádí výkon zálohování. Pomoc s ladění aplikace ke zlepšení výkonu zálohování najdete v tématu [ladění aplikací pro optimální výkon s Azure storage](../virtual-machines/windows/premium-storage-performance.md). I když v článku na výkon zálohování používá příklady úložiště Premium, pokyny platí pro standardní úložiště disky.

## <a name="total-restore-time"></a>Celkový počet obnovení čas
Operace obnovení se skládá z dvě hlavní dílčí úlohy: kopírování dat z trezoru zpět k účtu úložiště zvolené zákazníka a vytvoření virtuálního počítače. Kopírování dat zpět z trezoru závisí na kam jsou zálohy uložené interně v Azure a kde je uložen účet úložiště zákazníka. Čas potřebný ke zkopírování dat závisí na:
* Doba čekání ve frontě - vzhledem k tomu, že zpracuje služba obnovení úlohy z více zákazníků ve stejnou dobu obnovení požadavky jsou umístit do fronty.
* Kopírování dat time – Data se zkopírují z trezoru na účet úložiště zákazníka. Obnovení doba závisí na IOPS a získá propustnost služby Azure Backup v účtu úložiště vybraného zákazníka. Pokud chcete zkrátit čas kopírování během procesu obnovení, vyberte účet úložiště není načíst s jinými aplikace zápisů a čtení.

## <a name="best-practices"></a>Osvědčené postupy
Doporučujeme následující tyto postupy při konfiguraci zálohování pro virtuální počítače:

* Nemáte naplánovat víc než 10 klasické virtuální počítače ze stejné cloudové služby za účelem zálohování ve stejnou dobu. Pokud chcete zálohovat víc virtuálních počítačů ze stejné cloudové služby, odstupňování časů zálohování zahájení o hodinu.
* Neplánujte víc než 40 virtuálních počítačů pro zálohování ve stejnou dobu.
* Plánování zálohování virtuálních počítačů v době mimo špičku. Tímto způsobem služba zálohování používá IOPS pro přenos dat z účtu úložiště uživatele do trezoru.
* Ujistěte se, že je zásada na virtuálních počítačích rozloženy jiným účtům úložiště. Doporučujeme více než 20 celkový disky z jednoho úložiště účtu chráněn stejným plán zálohování. Pokud máte větší než 20 disky v účtu úložiště, rozloženy víc zásad získat požadované IOPS během přenosu fáze procesu zálohování těchto virtuálních počítačů.
* Neobnovujte virtuálních počítačů spuštěné na storage úrovně Premium stejný účet úložiště. Pokud proces operace obnovení se shoduje se operace zálohování, snižuje dostupné IOPS pro zálohování.
* Pro zálohování virtuálních počítačů Premium Ujistěte se, tento účet úložiště, hostitelé prémiové disky má alespoň 50 % volného místa pro přípravu snímek pro úspěšnou zálohu. 
* Ujistěte se, že danou verzi jazyka python na virtuální počítače s Linuxem povolené pro zálohování je 2.7

## <a name="data-encryption"></a>Šifrování dat
Zálohování Azure není šifrování dat v rámci procesu zálohování. Můžete však šifrování dat v rámci virtuálního počítače a bezproblémově zálohování chráněných dat (Další informace o [zálohování šifrovaná data](backup-azure-vms-encryption.md)).

## <a name="calculating-the-cost-of-protected-instances"></a>Výpočet nákladů na chráněné instance
Azure virtuální počítače, které jsou zálohovány pomocí Azure Backup, odpovídají rolím [cenách zálohování Azure](https://azure.microsoft.com/pricing/details/backup/). Výpočet chráněné instance je založen na *skutečné* velikost virtuálního počítače, který je součtem všechna data ve virtuálním počítači – s výjimkou dočasné úložiště.

Ceny pro zálohování virtuálních počítačů není založena na maximální podporovaná velikost pro každý datový disk připojen k virtuálnímu počítači. Ceny podle dat uložených v datový disk. Podobně faktury úložiště zálohy je založen na množství dat, který je uložený ve službě Azure Backup, tedy součet skutečná data v každém bodu obnovení.

Například využít A2 standardní velikosti virtuálního počítače, který má dva dalších datových disků s maximální velikost 1 TB. V následující tabulce jsou dat uložených na každém z těchto disků:

| Typ disku | Maximální velikost | Skutečná data obsažená |
| --------- | -------- | ----------- |
| Disk operačním systému |1023 GB |17 GB |
| Místního disku / dočasné disku |135 GB |5 GB (pro zálohování není součástí) |
| Datový disk 1 |1023 GB |30 GB |
| Datový disk 2 |1023 GB |0 GB |

Skutečná velikost virtuálního počítače v tomto případě je 17 GB + 30 GB + 0 GB = 47 GB. Tato velikost chráněné Instance (47 GB) se změní na základ pro měsíčních nákladů. Jak roste množství dat ve virtuálním počítači, velikost chráněné Instance odpovídajícím způsobem použitý pro změny fakturace.

Fakturace se nespustí, dokud se nedokončí první úspěšné zálohy. V tomto okamžiku začne fakturace pro úložiště a chráněné instance. Fakturace pokračuje, dokud není žádné zálohování dat uložených v trezoru pro virtuální počítač. Pokud zastavíte ochranu na virtuálním počítači, ale v trezoru zálohování dat virtuálního počítače existuje, bude pokračovat fakturace.

Fakturace pro zadaný virtuální počítač zastaví pouze v případě, že ochrana je zastavena a veškerá zálohovaná data se odstraní. Při zastavení ochrany a neexistují žádné aktivní úlohy zálohování, velikost poslední úspěšné zálohy virtuálního počítače se změní na velikost chráněné Instance používaných pro měsíčních nákladů.

## <a name="questions"></a>Máte dotazy?
Máte-li nějaké dotazy nebo pokud víte o funkci, kterou byste uvítali, [odešlete nám svůj názor](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Další postup
* [Zálohování virtuálních počítačů](backup-azure-arm-vms.md)
* [Správa zálohování virtuálních počítačů](backup-azure-manage-vms.md)
* [Obnovení virtuálních počítačů](backup-azure-arm-restore-vms.md)
* [Řešení problémů zálohování virtuálních počítačů](backup-azure-vms-troubleshoot.md)
