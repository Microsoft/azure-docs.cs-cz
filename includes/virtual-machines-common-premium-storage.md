---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: ramankumarlive
ms.service: storage
ms.topic: include
ms.date: 06/05/2018
ms.author: ramankum
ms.custom: include file
ms.openlocfilehash: 5cbe6f1f8f15e9da8e1fe6961d3da9b9e2a31e4b
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34806379"
---
# <a name="high-performance-premium-storage-and-managed-disks-for-vms"></a>Vysoce výkonné úložiště Premium a spravované disky pro virtuální počítače
Azure Premium Storage nabízí podporu vysoce výkonné, nízkou latencí disku pro virtuální počítače (VM) s vstupní a výstupní (I/O)-zatížení s intenzivním. Disky virtuálních počítačů, které používají úložiště Premium ukládat data do disků SSD (Solid-State Drive). Pokud chcete využít výhod rychlosti a výkonu prémiové disky úložiště, můžete migrovat existující disky virtuálních počítačů do úložiště úrovně Premium.

V Azure můžete připojit k virtuálnímu počítači několik disků úložiště premium. Aplikace s více disky, poskytuje až 256 TB úložiště na virtuální počítač. Storage úrovně Premium můžete dosáhnout aplikace 80 000 vstupně-výstupních operací za sekundu (IOPS) na virtuální počítač a propustnost disku až 2 000 MB za sekundu (MB/s) na virtuální počítač. Operace čtení poskytují velmi nízkou latenci.

Storage úrovně Premium Azure nabízí možnost skutečně navýšení a shift náročné podnikové aplikace jako farmy Dynamics AX, Dynamics CRM, Exchange Server, SAP Business Suite a služby SharePoint do cloudu. Úlohy náročné na výkon databáze můžete spustit v aplikacích, jako je SQL Server, Oracle, MongoDB, MySQL a Redis, které vyžadují konzistentní vysoký výkon a nízkou latencí.

> [!NOTE]
> Nejlepšího výkonu pro vaši aplikaci doporučujeme migraci všech disků virtuálního počítače, které vyžaduje vysokou IOPS pro Storage úrovně Premium. Pokud disk nevyžadují vysokou IOPS, můžete pomoct limit náklady udržováním v standardní úložiště Azure. V standardního úložiště data disku virtuálního počítače ukládána na pevných disků (HDD) místo na jednotkách SSD.
> 

Azure nabízí dva způsoby vytvoření prémiové disky úložiště pro virtuální počítače:

* **Nespravované disky**

    Původní metodou je použití nespravované disky. Nespravované disku můžete spravovat účty úložiště, které použijete k uložení souborů virtuálního pevného disku (VHD), které odpovídají vaše disky virtuálních počítačů. Soubory VHD jsou uloženy jako objekty BLOB stránky v účtech úložiště Azure. 

* **Spravované disky**

    Pokud vyberete [Azure spravované disky](../articles/virtual-machines/windows/managed-disks-overview.md), spravuje účty úložiště, které používáte pro disky virtuálních počítačů Azure. Určete typ disku (Standard nebo Premium) a velikost disku, které potřebujete. Azure vytváří a spravuje disku za vás. Nemusíte si dělat starosti o uvedení disky více účtů úložiště zajistit zůstat v limitech škálovatelnosti pro účty úložiště. Který zpracovává Azure.

Doporučujeme, abyste zvolili spravované disky využívat výhod jejich řadu funkcí.

Začít s Storage úrovně Premium, [vytvořit účet Azure zdarma](https://azure.microsoft.com/pricing/free-trial/). 

Informace o migraci stávající virtuální počítače do úložiště úrovně Premium najdete v tématu [převést virtuální počítač s Windows z disků nespravované na spravované disky](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md) nebo [převeďte virtuální počítač s Linuxem z disků nespravované na spravované disky](../articles/virtual-machines/linux/convert-unmanaged-to-managed-disks.md).

> [!NOTE]
> Storage úrovně Premium je k dispozici ve většině oblastí. Seznam dostupných oblastí najdete v části řádek pro **diskového úložiště** v [Azure produkty podle oblasti](https://azure.microsoft.com/regions/#services).
> 

## <a name="features"></a>Funkce

Zde jsou některé funkce úložiště Premium Storage:

* **Disky úložiště Premium**

    Premium Storage podporuje disky virtuálních počítačů, které je možné připojit k virtuálním počítačům konkrétní velikost řady. Premium Storage podporuje DS-series, DSv2-series, GS-series, Ls-series, Fs-series a Esv3-series virtuálních počítačů. Máte možnost volby velikostí disku sedm: P4 (32 GB), P6 (64 GB), P10 (128 GB), P20 (512 GB), P30 (1024 GB), P40 (2 048 GB), P50 (4095 GB). P4 a velikosti disků P6 ještě podporují jenom pro spravované disky. Velikost každého disku má svou vlastní specifikace výkonu. V závislosti na požadavcích vaší aplikace můžete jeden nebo více disků připojit k virtuálnímu počítači. Jsme specifikace v podrobněji popisují [Storage úrovně Premium škálovatelnosti a cílech výkonnosti](#scalability-and-performance-targets).

* **Objekty BLOB stránky Premium**

    Premium Storage podporuje objekty BLOB stránky. Objekty BLOB stránky použijte k ukládání trvalé, nespravované disky pro virtuální počítače v Storage úrovně Premium. Na rozdíl od standardní Azure Storage Storage úrovně Premium není podporují objekty BLOB bloku, doplňovací objekty BLOB, soubory, tabulky a fronty. Objekty BLOB stránky Premium podporují šesti velikost se pohybuje od P10 P50 a P60 (8191GiB). Objekt blob stránky P60 Premium nepodporuje být připojené jako disky virtuálních počítačů. 

    Jakýkoli objekt umístit do prémiový účet úložiště bude objekt blob stránky. Objekt blob stránky Připnutí na jednu z podporovaných velikostí zřízené. Z tohoto důvodu prémiový účet úložiště není určena pro použití k ukládání objektů BLOB velmi malé.

* **Prémiový účet úložiště**

    Pokud chcete začít používat úložiště úrovně Premium, vytvořte účet úložiště premium pro nespravovaná disky. V [portál Azure](https://portal.azure.com), abyste mohli vytvořit účet úložiště premium, vyberte **Premium** úroveň výkonu. Vyberte **místně redundantní úložiště (LRS)** možnost replikace. Můžete také můžete vytvořit účet úložiště premium nastavením úroveň výkonu na **Premium_LRS**. Pokud chcete změnit úroveň výkonu, použijte jednu z následujících postupů:
     
    - [Prostředí PowerShell pro úložiště Azure](../articles/storage/common/storage-powershell-guide-full.md#manage-the-storage-account)
    - [Azure CLI pro úložiště Azure](../articles/storage/common/storage-azure-cli.md#manage-storage-accounts)
    - [Azure Storage prostředků zprostředkovatele REST API](https://docs.microsoft.com/rest/api/storagerp) (pro nasazení Azure Resource Manager) nebo jedna z knihovny klienta Azure Storage resource provider

    Informace o omezeních účtu úložiště premium najdete v tématu [Storage úrovně Premium škálovatelnosti a cílech výkonnosti](#premium-storage-scalability-and-performance-targets).

* **Místně redundantní úložiště úrovně Premium**

    Prémiový účet úložiště podporuje jenom místně redundantního úložiště jako možnost replikace. Místně redundantní úložiště udržuje tři kopie dat v jedné oblasti. Místní zotavení po havárii, je třeba zálohovat vaše disky virtuálních počítačů v jiné oblasti s použitím [Azure Backup](../articles/backup/backup-introduction-to-azure-backup.md). Také musíte použít účet geograficky redundantní úložiště (GRS) jako úložiště záloh. 

    Azure pomocí svého účtu úložiště jako kontejner pro nespravovaná disky. Když vytvoříte virtuální počítač Azure, který podporuje službu Premium Storage s nespravované disky, a vyberte účet úložiště premium, operačního systému a datové disky jsou uložené v daném účtu úložiště.

## <a name="supported-vms"></a>Podporované virtuální počítače

Premium Storage podporuje B-series, DS-series, DSv2-series, DSv3-series, Esv3-series, GS-series, Ls-series, M-series a virtuálních počítačů služby Fs-series. Můžete použít úložiště standard a premium disků s těmito typy virtuálních počítačů. Disky úložiště premium nelze použít s řadou virtuálních počítačů, které nejsou Premium úložiště kompatibilní.


Informace o typech a velikostech virtuálních počítačů v Azure pro Windows najdete v tématu věnovaném [velikostem virtuálních počítačů s Windows](../articles/virtual-machines/windows/sizes.md). Informace o typech a velikostech virtuálních počítačů v Azure pro Linux najdete v tématu věnovaném [velikostem virtuálních počítačů s Linuxem](../articles/virtual-machines/linux/sizes.md).

Toto jsou některé funkce služby DS-series, DSv2-series, GS-series, Ls-series a Fs-series virtuální počítače:

* **Cloudové služby**

    DS-series virtuálních počítačů můžete přidat do cloudové služby, který má jenom virtuální počítače DS-series. Nepřidávejte do cloudové služby, který má jiného typu než virtuální počítače DS-series DS-series virtuálních počítačů. Existující virtuální pevné disky můžete migrovat do nového Cloudová služba, která spouští jenom virtuální počítače DS-series. Pokud chcete použít stejné virtuální IP adresy pro novou cloudovou službu, který je hostitelem služby DS-series virtuální počítače, použijte [rezervovaných IP adres](../articles/virtual-network/virtual-networks-instance-level-public-ip.md). Virtuální počítače GS-series mohou být přidány do cloudové služby, který má jenom virtuální počítače GS-series.

* **Disk operačního systému**

    Můžete nastavit vašeho virtuálního počítače služby Storage úrovně Premium k použití prémiový nebo disk standardní operačního systému. Pro dosažení co nejlepších výsledků doporučujeme používat disk operačního systému úložiště Premium.

* **Datové disky**

    Premium a standard disků můžete použít ve stejném Virtuálním úložiště Premium. Storage úrovně Premium můžete zřídit virtuální počítač a přiřadit několik trvalých datových disků virtuálního počítače. V případě potřeby, pokud chcete zvýšit kapacitu a výkon svazku, můžete rozkládají napříč vaše disky.

    > [!NOTE]
    > Pokud rozkládají premium storage datových disků pomocí [prostory úložiště](http://technet.microsoft.com/library/hh831739.aspx), nastavit prostory úložiště s 1 sloupec pro každý disk, který používáte. Celkový výkon prokládané svazku, jinak hodnota může být nižší, než se očekávalo kvůli nevyrovnaná distribuce přenosů mezi disky. Ve výchozím nastavení, ve Správci serveru můžete nastavit sloupce pro až 8 disky. Pokud máte více než 8 disky pomocí prostředí PowerShell k vytvoření svazku. Ručně zadejte počet sloupců. Jinak hodnota uživatelského rozhraní správce serveru pořád používá 8 sloupce, i když máte více disků. Pokud máte v sadě jeden stripe 32 disků, můžete například zadejte 32 sloupců. Chcete-li určit počet sloupců, které využívá virtuální disk, [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) rutiny prostředí PowerShell, použijte *NumberOfColumns* parametr. Další informace najdete v tématu [prostory úložiště – přehled](http://technet.microsoft.com/library/hh831739.aspx) a [nejčastější dotazy prostory úložiště](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx).
    >
    > 

* **Mezipaměti**

    Virtuální počítače v velikost řady, která podporují službu Premium Storage mít jedinečné funkce ukládání do mezipaměti pro vysoké úrovně propustnosti a latence. Ukládání do mezipaměti schopností překračuje základní výkon disku úložiště premium. Můžete nastavit zásady na prémiové disky úložiště pro ukládání do mezipaměti disku **jen pro čtení**, **ReadWrite**, nebo **žádné**. Disk výchozí zásady ukládání do mezipaměti je **jen pro čtení** pro všechny datové disky premium a **ReadWrite** pro disky operačního systému. Pro optimální výkon pro vaši aplikaci použijte nastavení správné mezipaměti. Například pro náročné pro čtení nebo jen pro čtení datových disků, jako je například datové soubory SQL serveru, nastavte disku zásady ukládání do mezipaměti **jen pro čtení**. Pro náročné zápisu nebo pouze pro zápis datových disků, jako jsou soubory protokolu serveru SQL Server, nastavte disku ukládání do mezipaměti zásady **žádné**. Další informace o optimalizaci návrhu s Premium Storage najdete v tématu [návrh z hlediska výkonu Storage úrovně Premium](../articles/virtual-machines/windows/premium-storage-performance.md).

* **Analýzy**

    Analýza výkonu virtuálních počítačů pomocí disků v Storage úrovně Premium, zapněte diagnostiku virtuálního počítače v [portál Azure](https://portal.azure.com). Další informace najdete v tématu [monitorování virtuálního počítače Azure pomocí rozšíření diagnostiky Azure](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/). 

    Zobrazit výkon disku, pomocí nástrojů na základě operačního systému jako [sledování výkonu systému Windows](https://technet.microsoft.com/library/cc749249.aspx) pro virtuální počítače Windows a [iostat](http://linux.die.net/man/1/iostat) příkazu pro virtuální počítače s Linuxem.

* **Limity škálování virtuálních počítačů a výkon**

    Velikost pro všechny podporované Storage úrovně Premium virtuálních počítačů má limity škálování a výkon specifikace IOPS, šířky pásma a počet disků, které je možné připojit na virtuální počítač. Pokud používáte prémiové disky úložiště s virtuálními počítači, zkontrolujte, zda je dostatečná IOPS a šířky pásma na vašem virtuálním počítači pro provoz diskové jednotky.

    Virtuální počítač STANDARD_DS1 má například vyhrazené šířky pásma souboru 32 MB/s pro provoz disk úložiště premium. Disk úložiště premium P10 můžete zadat šířku pásma 100 MB/s. Pokud disk úložiště premium P10 je připojen k tomuto virtuálnímu počítači, můžete přejít jenom až 32 MB/s. Nemůže používat maximální 100 MB/s, můžete zadat P10 disku.

    V současné době je největší virtuálního počítače v DS-series Standard_DS15_v2. Standard_DS15_v2 můžete zadat až 960 MB/s na všechny disky. Největší virtuálního počítače v GS-series je Standard_GS5. Standard_GS5 můžete zadat až 2 000 MB/s na všechny disky.

    Tato omezení jsou disku pouze pro provoz. Tyto limity neobsahují přístupů k mezipaměti a síťový provoz. Samostatné šířky pásma je k dispozici pro provoz sítě virtuálních počítačů. Šířka pásma pro síťový provoz se liší od vyhrazené šířky pásma používané disky úložiště premium.

    Nejnovější informace o maximální IOPS a propustnost (šířka pásma) pro virtuální počítače podporované Premium Storage najdete v části [velikosti virtuálních počítačů Windows](../articles/virtual-machines/windows/sizes.md) nebo [velikosti virtuálního počítače s Linuxem](../articles/virtual-machines/linux/sizes.md).

    Další informace o discích úložiště premium a jejich omezení IOPS a propustnost najdete v tabulce v další části.

## <a name="scalability-and-performance-targets"></a>Škálovatelnost a cíle výkonnosti
V této části popisují jsme škálovatelnost a výkon cíle vzít v úvahu při použití úložiště Premium.

Prémiové účty úložiště mají následující cíle škálovatelnosti:

| Celkový počet účet kapacity | Celková šířka pásma pro účet místně redundantní úložiště |
| --- | --- | 
| Kapacita disku: 35 TB <br>Pořízení snímku kapacity: 10 TB | Až 50 gigabity za sekundu pro příchozí<sup>1</sup> + odchozí<sup>2</sup> |

<sup>1</sup> všechna data (počet požadavků), které se odesílají na účet úložiště

<sup>2</sup> všechna data (odpovědí), které byly přijaty z účtu úložiště

Další informace najdete v tématu [Azure Storage škálovatelnosti a cílech výkonnosti](../articles/storage/common/storage-scalability-targets.md).

Pokud používáte prémiové účty úložiště pro nespravovaná disky a aplikace překračuje cíle škálovatelnosti účtu jedno úložiště, můžete migrovat do spravovaných disky. Pokud nechcete, aby k migraci na spravované disky, sestavení aplikace pomocí více účtů úložiště. Potom oddílu data mezi různými tyto účty úložiště. Například pokud se chcete připojit 51 TB disky napříč více virtuálními počítači, rozloženy je dva účty úložiště. 35 TB je tento limit pro jeden prémiový účet úložiště. Zajistěte, aby jeden prémiový účet úložiště nikdy má více než 35 TB zřizované disky.

### <a name="premium-storage-disk-limits"></a>Limity disk úložiště Premium
Pokud zřizujete disk úložiště premium, velikost disku určuje maximální IOPS a propustnost (šířka pásma). Azure nabízí sedm typy disků úložiště premium: P4 (spravované jen disky), P6 (spravované jen disky), P10, P20, P30, P40 a P50. Každý typ disku úložiště premium má zvláštní omezení pro IOPS a propustnosti. Limity pro typy disků jsou popsané v následující tabulce:

| Disky typu Premium  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Velikost disku           | 32 GB| 64 GB| 128 GB| 256 GB| 512 GB            | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| Vstupně-výstupní operace za sekundu / disk       | 120   | 240   | 500   | 1100   | 2300              | 5000              | 7500              | 7500              | 
| Propustnost / disk | 25 MB za sekundu  | 50 MB za sekundu  | 100 MB za sekundu | 125 MB za sekundu | 150 MB za sekundu | 200 MB za sekundu | 250 MB za sekundu | 250 MB za sekundu | 

> [!NOTE]
> Zkontrolujte, zda je k dispozici na vašem virtuálním počítači pro provoz diskové jednotky, dostatečnou šířku pásma, jak je popsáno v [Storage úrovně Premium podporované virtuální počítače](#premium-storage-supported-vms). Jinak propustnost disku a IOPS je omezen na nižší hodnoty. Maximální propustnost a IOPS jsou založené na omezení virtuálního počítače, není na disku omezení popsané v předchozí tabulce.  
> 
> 

Zde jsou některé důležité věci vědět o cíle škálovatelnost a výkon úložiště Premium:

* **Zřízené kapacitu a výkon**

    Při zřizování disk úložiště premium, na rozdíl od standardní úložiště je zaručena kapacitu, IOPS a propustnost tohoto disku. Například pokud vytvoříte P50 disk, Azure zřídí kapacitu 4095 GB úložiště, 7500 IOPS a propustnost 250 MB/s pro tento disk. Aplikace můžete použít, nebo jeho část kapacitu a výkon.

* **Velikost disku**

    Azure mapuje velikost disku (zaokrouhlený nahoru) na nejbližší premium úložiště disku možnost, jak je uvedeno v tabulce v předchozí části. Například velikost 100 GB na disku je klasifikován tak možnost P10. Může provádět až 500 IOPS, s až 100 MB/s propustnost. Podobně disk 400 GB je klasifikován tak P20 velikost. Může provádět až 2,300 IOPS, s propustností 150 MB/s.
    
    > [!NOTE]
    > Snadno můžete zvětšit velikost existujícího disky. Například můžete chtít zvětšit velikost 30 GB disk 128 GB, nebo i 1 TB. Nebo můžete chtít převést P20 disk na disk P30, protože budete potřebovat větší kapacitu nebo další IOPS a propustnosti. 
    > 
 
* **Velikost vstupně-výstupních operací**

    Velikost vstupně-výstupních operací je 256 KB. Pokud přenášených dat je menší než 256 KB, považuje 1 jednotka vstupně-výstupní operace. Větší velikosti vstupně-výstupních operací, se počítají jako více vstupně-výstupních operací velikost 256 KB. Například 1100 KB vstupně-výstupních operací se počítá jako jednotky 5 vstupně-výstupní operace.

* **Propustnost**

    Omezení propustnosti zahrnuje zápisy na disk, a obsahuje operací čtení disku, které nejsou obsluhovat z mezipaměti. Například P10 disk má propustnost 100 MB/s na disk. V následující tabulce jsou uvedeny některé příklady platný propustnost pro P10 disk:

    | Maximální propustnost za P10 disku | Bez mezipaměti čtení z disku | Bez mezipaměti zápisy na disk |
    | --- | --- | --- |
    | 100 MB/s | 100 MB/s | 0 |
    | 100 MB/s | 0 | 100 MB/s |
    | 100 MB/s | 60 MB/s | 40 MB/s |

* **Přístupů k mezipaměti**

    Počet přístupů do mezipaměti nejsou omezeny přidělené IOPS nebo propustnost disku. Například při použití datový disk se **jen pro čtení** nastavení mezipaměti na virtuální počítač, který podporuje úložiště úrovně Premium, čtení, které jsou obsluhovány z mezipaměti se nevztahují IOPS a propustnost CAP k vzdálené ploše disku. Pokud je zatížení disku převážně operace čtení a může získat velmi vysoké propustnosti. Mezipaměti podléhá samostatné IOPS a omezení propustnosti na virtuální počítač úrovně, na základě velikosti virtuálního počítače. Virtuální počítače DS-series mají přibližně 4 000 IOPS a propustnost 33 MB/s za jádra pro mezipaměť a místní SSD vstupně-výstupních operací. Virtuální počítače GS-series mít maximálně 5 000 IOPS a propustnost 50 MB/s na jádro pro mezipaměť a místní SSD vstupně-výstupních operací. 

## <a name="throttling"></a>Throttling
Omezování může dojít, pokud vaše aplikace IOPS nebo propustnost překročí přidělenou limity pro disk úložiště premium. Omezování může vyskytnout, pokud celkový počet disku provozu na všechny disky na virtuální počítač překračuje disku limit šířky pásma serveru k dispozici pro virtuální počítač. Abyste se vyhnuli, omezení šířky pásma, doporučujeme omezit počet nevyřízených žádostí v/v disku. Použijte omezení na základě škálovatelnosti a cílech výkonnosti pro disk, který máte zřízen a na šířku pásma disku, která je k dispozici pro virtuální počítač.  

Aplikace můžete dosáhnout nejnižší latenci, když je navržená tak, aby se zabránilo omezení. Ale pokud počet nevyřízených žádostí v/v disku je příliš malá, vaše aplikace nelze využít výhod maximální IOPS a úrovní propustnosti, které jsou k dispozici na disk.

Následující příklady ukazují, jak k výpočtu omezení úrovně. Všechny výpočty vycházejí z jednotky velikost 256 KB vstupně-výstupních operací.

### <a name="example-1"></a>Příklad 1
Vaše aplikace zpracovala za jednu sekundu na P10 disk 495 vstupně-výstupních operací jednotky o velikosti 16 KB. Jednotky vstupně-výstupních operací se počítají jako 495 IOPS. Pokud se pokusíte 2 MB vstupně-výstupních operací ve stejném druhý, celkový počet jednotek vstupně-výstupních operací se rovná 495 + 8 IOPS. Důvodem je, že vstupně-výstupních operací 2 MB = 2 048 KB / 256 KB = 8 vstupně-výstupních operací jednotek, když velikost jednotky vstupně-výstupních operací je 256 KB. Vzhledem k tomu, že součet 495 + 8 přesahuje limit 500 IOPS pro disk, omezování nastane.

### <a name="example-2"></a>Příklad 2
Vaše aplikace zpracovala 400 vstupně-výstupních operací jednotky velikost 256 KB na P10 disku. Je celková šířka pásma využívat (400 &#215; 256) / 1 024 KB = 100 MB/s. P10 disk může mít propustnost 100 MB/s. Pokud se aplikace pokusí provést další vstupně-výstupních operací v této druhé, je omezena, protože překračuje limit přidělená.

### <a name="example-3"></a>Příklad 3
Máte virtuální počítač, DS4 s dvěma P30 disky připojené. Každý disk P30 je propustnost 200 MB/s. Virtuální počítač DS4 má však kapacitou disku celkové šířky pásma 256 MB/s. Nelze drive i připojené disky a maximální propustnost na tomto virtuálním počítači DS4 ve stejnou dobu. Chcete-li tento problém vyřešili, dokáže odolat provoz 200 MB/s na jednom disku a 56 MB/s na jiný disk. Pokud součet disku provozu, prochází přes 256 MB/s, je omezen provoz disku.

> [!NOTE]
> Pokud disk provozu většinou obsahuje malé vstupně-výstupních operací velikosti, bude pravděpodobně aplikace nedosáhli limitu IOPS před omezení propustnosti. Ale pokud přenosů disku většinou obsahuje velké vstupně-výstupních operací velikosti, pravděpodobně aplikace se setkají omezení propustnosti nejprve místo IOPS limit. Pomocí optimální velikost vstupně-výstupních operací můžete zvýšit IOPS vaší aplikace a kapacity propustnosti. Navíc můžete omezit počet čekajících vstupně-výstupní požadavky na disk.
> 

Další informace o návrhu pro vysoký výkon pomocí Storage úrovně Premium, najdete v části [návrh z hlediska výkonu Storage úrovně Premium](../articles/virtual-machines/windows/premium-storage-performance.md).

## <a name="snapshots-and-copy-blob"></a>Snímků a kopírování objektů Blob

Soubor virtuálního pevného disku ke službě úložiště je objekt blob stránky. Můžete pořizovat snímky objekty BLOB stránky a zkopírujte je do jiného umístění, například na jiný účet úložiště.

### <a name="unmanaged-disks"></a>Nespravované disky

Vytvoření [přírůstkové snímky](../articles/virtual-machines/linux/incremental-snapshots.md) pro nespravovaná premium disky stejným způsobem použít snímky s standardní úložiště. Storage úrovně Premium podporuje pouze místně redundantního úložiště jako možnost replikace. Doporučujeme vytvořit snímky a poté zkopírujte snímky do geograficky redundantní standardní účet úložiště. Další informace najdete v tématu [možnosti redundance úložiště Azure](../articles/storage/common/storage-redundancy.md).

Pokud je disk připojen k virtuálnímu počítači, nejsou povolené některé operace rozhraní API na disku. Například nelze provést [kopírovat objekt Blob](/rest/api/storageservices/Copy-Blob) operaci u tohoto objektu blob, pokud je disk připojen k virtuálnímu počítači. Místo toho nejprve vytvořte snímek tomuto objektu blob pomocí [snímku Blob](/rest/api/storageservices/Snapshot-Blob) REST API. Potom klikněte [kopírovat objekt Blob](/rest/api/storageservices/Copy-Blob) snímku zkopírovat připojený disk. Alternativně můžete odpojit disk a potom proveďte všechny potřebné operace.

Na snímky objektu blob úložiště premium se vztahují následující omezení:

| Limit úložiště Premium | Hodnota |
| --- | --- |
| Maximální počet snímků za objektů blob | 100 |
| Kapacitě účtu úložiště pro snímky<br>(Obsahuje data pouze snímky. Nezahrnuje data v základní objekt blob.) | 10 TB |
| Minimální doba mezi po sobě jdoucí snímky | 10 minut |

Udržovat geograficky redundantní kopie vašeho snímků, můžete zkopírovat snímky z prémiový účet úložiště do geograficky redundantní standardní účet úložiště pomocí nástroje AzCopy nebo kopírovat objekt Blob. Další informace najdete v tématu [přenos dat pomocí nástroje příkazového řádku azcopy](../articles/storage/common/storage-use-azcopy.md) a [kopírovat objekt Blob](/rest/api/storageservices/Copy-Blob).

Podrobné informace o provádění operací REST pro objekty BLOB stránky v prémiový účet úložiště najdete v tématu [Blob operace služby s Azure Premium Storage](http://go.microsoft.com/fwlink/?LinkId=521969).

### <a name="managed-disks"></a>Managed Disks

Snímek se spravovaným diskem je jen pro čtení kopie spravovaného disku. Snímek je uložený jako standardní se spravovaným diskem. V současné době [přírůstkové snímky](../articles/virtual-machines/windows/incremental-snapshots.md) nejsou podporovány pro spravované disky. Naučte se vytvořit snímek pro se spravovaným diskem, najdete v tématu [vytvoření kopie virtuální pevný disk uložený jako spravovaný Azure disku pomocí spravovaného snímky ve Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md) nebo [vytvoření kopie virtuální pevný disk uložený jako spravovaný Azure disku pomocí spravované snímky v systému Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md).

Pokud se spravovaným diskem je připojen k virtuálnímu počítači, nejsou povolené některé operace rozhraní API na disku. Například nelze vygenerovat sdílený přístupový podpis (SAS) k provedení operace kopírování, když je disk připojen k virtuálnímu počítači. Místo toho nejprve vytvořte snímek disku a pak proveďte kopírování snímku. Alternativně můžete odpojit disk a pak generovat SAS k provedení operace kopírování.


## <a name="premium-storage-for-linux-vms"></a>Storage úrovně Premium pro virtuální počítače s Linuxem
Můžete nastavit vaše virtuální počítače s Linuxem v Premium Storage můžete použít následující informace:

K dosažení cíle škálovatelnosti v Storage úrovně Premium, pro všechny disky úložiště premium s mezipamětí, nastavte **jen pro čtení** nebo **žádné**, je nutné zakázat "překážek", když připojení systému souborů. Překážek v tomto scénáři není nutné, protože zápisy na disky úložiště premium jsou trvalé, aby se tato nastavení mezipaměti. Pokud je požadavek zápisu úspěšně dokončen, data byla zapsána do trvalého úložiště. Zakázat "překážek", použijte jednu z následujících metod. Zvolte pro systém souborů:
  
* Pro **reiserFS**, abyste zakázat překážek, použijte `barrier=none` možnost připojit. (Chcete-li povolit překážek, použijte `barrier=flush`.)
* Pro **ext3/ext4**, abyste zakázat překážek, použijte `barrier=0` možnost připojit. (Chcete-li povolit překážek, použijte `barrier=1`.)
* Pro **XFS**, abyste zakázat překážek, použijte `nobarrier` možnost připojit. (Chcete-li povolit překážek, použijte `barrier`.)
* Storage úrovně premium disky s mezipamětí nastaven na hodnotu **ReadWrite**, povolit překážek pro odolnost zápisu.
* Pro svazek popisky a zachová tak po restartování virtuálního počítače je nutné aktualizovat /etc/fstab s odkazy na identifikátor UUID (UUID) na disky. Další informace najdete v tématu [přidejte spravované disk do virtuálního počítače s Linuxem](../articles/virtual-machines/linux/add-disk.md).

Následující Linuxových distribucích ověření pro Storage úrovně Premium. Pro lepší výkon a stabilitu Storage úrovně Premium doporučujeme upgradu virtuálních počítačů na jednu z těchto verzí minimálně (nebo novější verzi). Některá z verzí nejnovější Linux integrační služby LIS (), v4.0, vyžadují pro Azure. Chcete-li stáhnout a nainstalovat distribuční, pomocí následujícího odkazu uvedené v následující tabulce. Jsme dokončení ověření jsme Image přidat do seznamu. Všimněte si, že naše ověření ukazují, že výkon se liší u každé bitové kopie. Výkon závisí na charakteristiky zatížení a nastavení bitové kopie. Jiné bitové kopie jsou optimalizovaných pro různé druhy úloh.

| Distribuce | Verze | Podporované jádra | Podrobnosti |
| --- | --- | --- | --- |
| Ubuntu | 12.04 | 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-server-20150119-en-us-30GB |
| Ubuntu | 14.04 | 3.13.0-44.73+ | Ubuntu-14_04_1-LTS-amd64-server-20150123-en-us-30GB |
| Debian | 7.x, 8.x | 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12| 3.12.36-38.1+| suse-sles-12-priority-v20150213 <br> suse-sles-12-v20150213 |
| SUSE | SLES 11 SP4 | 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+| 3.18.4+ | CoreOS 584.0.0 |
| CentOS | 6.5, 6.6, 6.7, 7.0 | &nbsp; | [LIS4 vyžaduje](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Další informace v poznámce v další části* |
| CentOS | 7.1+ | 3.10.0-229.1.2.el7+ | [Doporučená LIS4](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Další informace v poznámce v další části* |
| Red Hat Enterprise Linux (RHEL) | 6.8+, 7.2+ | &nbsp; | &nbsp; |
| Oracle | 6.0+, 7.2+ | &nbsp; | UEK4 nebo RHCK |
| Oracle | 7.0-7.1 | &nbsp; | UEK4 nebo RHCK s[LIS 4.1 +](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |
| Oracle | 6.4-6.7 | &nbsp; | UEK4 nebo RHCK s[LIS 4.1 +](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |


### <a name="lis-drivers-for-openlogic-centos"></a>Ovladače LIS pro OpenLogic CentOS

Pokud používáte OpenLogic CentOS virtuální počítače, spusťte následující příkaz k instalaci nejnovější ovladače:

```
sudo rpm -e hypervkvpd  ## (Might return an error if not installed. That's OK.)
sudo yum install microsoft-hyper-v
```

Pokud chcete aktivovat nové ovladače, restartujte počítač.

## <a name="pricing-and-billing"></a>Ceny a fakturace

Pokud používáte Storage úrovně Premium, platí následující fakturace aspekty:

* **Velikost disku a objektů blob úložiště Premium**

    Fakturace disku úložiště premium nebo objekt blob, závisí na velikost zřízeného disku nebo objektů blob. Azure mapuje velikost zřízeného (zaokrouhlený nahoru) na nejbližší možnost disk úložiště premium. Podrobnosti najdete v tématu v tabulce v [Storage úrovně Premium škálovatelnosti a cílech výkonnosti](#premium-storage-scalability-and-performance-targets). Každý disk se mapuje na velikost podporované zřízeného disku a se fakturuje odpovídajícím způsobem. Fakturace pro všechny zajišťovaný disk je průběžně každou hodinu pomocí měsíční poplatek nabídky Storage úrovně Premium. Například pokud zřízený P10 disku a odstranit po 20 hodin, můžete se účtují pro nabídku P10 účtovány poměrnou částí 20 hodin. To je bez ohledu na množství skutečné dat zapsaných na disk nebo IOPS a propustnost použít.

* **Snímky Premium nespravované disky**

    Snímky na nespravované prémiové disky se účtují dodatečnou kapacitu používané snímky. Další informace o snímcích najdete v tématu [vytvoření snímku objektu blob](/rest/api/storageservices/Snapshot-Blob).

* **Premium spravované snímky disky**

    Snímek se spravovaným diskem je jen pro čtení kopie disku. Disk se ukládají jako standardní se spravovaným diskem. Snímek stojí stejné jako standardní spravovaná disku. Například pokud pořídíte snímek 128 GB premium se spravovaným diskem, náklady na snímku je ekvivalentem na 128 GB disk standardní spravované.  

* **Odchozí přenosy dat**

    [Odchozí přenosy dat](https://azure.microsoft.com/pricing/details/data-transfers/) (data přejdete mimo datových centrech Azure) jsou zpoplatněné podle využití šířky pásma.

Podrobné informace o cenách pro Storage úrovně Premium Storage úrovně Premium podporované virtuální počítače a spravované disků, naleznete v článcích:

* [Ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/)
* [Ceny virtuálních počítačů](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Spravované disky ceny](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="azure-backup-support"></a>Podporu zálohování Azure 

Místní zotavení po havárii, je třeba zálohovat vaše disky virtuálních počítačů v jiné oblasti s použitím [Azure Backup](../articles/backup/backup-introduction-to-azure-backup.md) a účet úložiště GRS jako trezor záloh.

Postup vytvoření úlohy zálohování se zálohy založené na čase, snadno obnovení virtuálních počítačů a zásady uchovávání záloh, používají Azure Backup. Můžete použít zálohování i s disky a nespravované. Další informace najdete v tématu [zálohování Azure pro virtuální počítače s nespravované disky](../articles/backup/backup-azure-vms-first-look-arm.md) a [zálohování Azure pro virtuální počítače s spravované disky](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup). 

## <a name="next-steps"></a>Další postup
Další informace o Premium Storage naleznete v následujících článcích.
