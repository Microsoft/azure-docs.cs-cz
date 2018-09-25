---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: ramankumarlive
ms.service: storage
ms.topic: include
ms.date: 09/24/2018
ms.author: ramankum
ms.custom: include file
ms.openlocfilehash: ea312002a9a1a39505cd4748864ca9dfc1da43dd
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47061071"
---
# <a name="high-performance-premium-storage-and-managed-disks-for-vms"></a>Vysoce výkonné úložiště úrovně Premium a spravovaným diskům pro virtuální počítače

Azure Premium Storage poskytuje podporu vysoce výkonných disků s nízkou latencí pro virtuální počítače (VM) s vstupní a výstupní (I/O)-náročné úlohy. Disky virtuálních počítačů, které používají službu Premium Storage ukládat data na jednotky SSD (Solid-State Drive). Pokud chcete využít výhod rychlost a výkon disků premium storage, můžete migrovat existující disky virtuálních počítačů na Premium Storage.

V Azure můžete připojit několik disků premium storage k virtuálnímu počítači. Použití více disků poskytuje vaše aplikace až 256 TB úložiště na každý virtuální počítač, pokud používáte velikosti náhled vaší aplikace mohou být dlouhé až přibližně 2 PiB úložiště na každý virtuální počítač. Díky službě Premium Storage vaše aplikace může dosáhnout 80 000 vstupně-výstupních operací za sekundu (IOPS) na virtuální počítač a propustnost disku až 2 000 MB za sekundu (MB/s) na virtuální počítač. Operace čtení získáte velmi nízkou latencí.

Díky službě Premium Storage Azure nabízí možnost skutečně lift and shift náročné podnikové aplikace jako je Dynamics AX, Dynamics CRM, Exchange Server, SAP Business Suite a SharePoint farmy do cloudu. Můžete spouštět úlohy náročné na výkon databáze v aplikacích, například systému SQL Server, Oracle, MongoDB, MySQL a Redis, které vyžadují konzistentní vysoký výkon a nízkou latencí.

> [!NOTE]
> Pro zajištění nejlepšího výkonu pro vaši aplikaci doporučujeme migraci všech disků virtuálního počítače, které vyžaduje vysoké vstupně-výstupních operací na Premium Storage. Pokud na disku nevyžaduje vysoké IOPS, pomáhají omezit náklady udržováním ve standardním úložišti Azure. Ve standardním úložišti datového disku virtuálního počítače ukládána na pevných disků (HDD) namísto na jednotkách SSD.

Azure nabízí dva způsoby, jak vytvořit disky storage úrovně premium pro virtuální počítače:

* **Nespravované disky**

    Původní metodou je použití nespravovaných disků. V nespravovaný disk spravovat účty úložiště, které lze použít k uložení souborů virtuálního pevného disku (VHD), které odpovídají disky virtuálních počítačů. Soubory virtuálního pevného disku se ukládají jako objekty BLOB stránky v účtech úložiště Azure. 

* **Spravované disky**

    Pokud zvolíte [Azure Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md), spravuje účty úložiště, které používáte pro disky virtuálních počítačů Azure. Zadejte typ disku (Premium nebo Standard) a velikost disku, které potřebujete. Azure vytvoří a spravuje disk za vás. Nemusíte se starat o umisťováním disků do více účtů úložiště k zajištění, že se vejdete do limitech škálovatelnosti pro vaše účty úložiště. Azure, která zpracuje za vás.

Doporučujeme, abyste zvolili spravované disky, abyste mohli využívat jejich řadu funkcí.

Abyste mohli začít se službou Premium Storage [vytvořte si bezplatný účet Azure](https://azure.microsoft.com/pricing/free-trial/). 

Informace o migraci stávajících virtuálních počítačů služby Premium Storage najdete v tématu [převod virtuálního počítače s Windows z nespravovaných disků na managed disks](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md) nebo [převod virtuálního počítače s Linuxem z nespravovaných disků na managed disks](../articles/virtual-machines/linux/convert-unmanaged-to-managed-disks.md).

> [!NOTE]
> Premium Storage je dostupné ve většině oblastí. Seznam dostupných oblastí naleznete v řádku pro **diskové úložiště** v [dostupné produkty Azure podle oblasti](https://azure.microsoft.com/regions/#services).

## <a name="features"></a>Funkce

Tady jsou některé z funkcí úložiště Premium Storage:

* **Disky storage úrovně Premium**

    Premium Storage podporuje disky virtuálních počítačů, které můžou být připojené k virtuálním počítačům konkrétní velikost series. Premium Storage podporuje širokou škálu virtuálních počítačů Azure. Máte možnost volby osm velikostí disku GA: P4 (32 GB), P6 (64 GiB) P10 (128 GB), P15 (256 GB), P20 (512 GB), P30 (1 024 GB), P40 (2 048 GB), P50 (4 095 GB). Stejně jako tři velikosti disků ve verzi preview: P60 8192 GiB (8 TB) P70 16,348 GiB (16 TB), 32 767 GiB P80 (32 TB). Velikosti disků P4 P6, P60, P70 a P80 jsou aktuálně podporuje jenom pro službu Managed Disks. Velikost každého disku má svůj vlastní specifikace výkonu. V závislosti na požadavcích vaší aplikace můžete připojit jeden nebo víc disků k virtuálnímu počítači. Specifikace podrobněji popisujeme [škálovatelnost a výkonnostní cíle Storage úrovně Premium](#scalability-and-performance-targets).

* **Objekty BLOB stránky úrovně Premium**

    Premium Storage podporuje objekty BLOB stránky. Objekty BLOB stránky použijte pro ukládání trvalých, nespravované disky pro virtuální počítače ve službě Premium Storage. Na rozdíl od úložiště Azure úrovně standard Premium Storage není podporují objekty BLOB bloku, doplňovací objekty BLOB, soubory, tabulky nebo fronty. Objekty BLOB stránky úrovně Premium podporují šest pohybuje od P10 P50 a P60 (8191GiB). Objekt blob stránky úrovně Premium P60 není možné připojit jako disky virtuálních počítačů. 

    Libovolný objekt, do účtu služby premium storage bude objekt blob stránky. Objekty blob stránky přichytí k jednomu z podporovaných zřízené velikosti. To je důvod, proč účtu služby premium storage není určena pro použití k ukládání objektů BLOB velmi malé.

* **Účet Premium storage**

    Pokud chcete začít používat Premium Storage, vytvoření účtu služby premium storage pro nespravované disky. V [webu Azure portal](https://portal.azure.com), abyste mohli vytvořit účet úložiště úrovně premium, zvolte **Premium** úroveň výkonu. Vyberte **místně redundantní úložiště (LRS)** možnost replikace. Můžete také vytvořit účet premium storage podle nastavení úrovně výkonu **Premium_LRS**. Chcete-li změnit úroveň výkonu, použijte jednu z následujících postupů:
     
    - [Prostředí PowerShell pro Azure Storage](../articles/storage/common/storage-powershell-guide-full.md#manage-the-storage-account)
    - [Azure CLI pro úložiště Azure](../articles/storage/common/storage-azure-cli.md#manage-storage-accounts)
    - [Azure Storage Resource Provider REST API](https://docs.microsoft.com/rest/api/storagerp) (pro nasazení Azure Resource Manager) nebo jedna z klientských knihoven Azure Storage resource provider

    Další informace o omezeních účtu úložiště úrovně premium, najdete v článku [škálovatelnost a výkonnostní cíle Storage úrovně Premium](#premium-storage-scalability-and-performance-targets).

* **Místně redundantní úložiště úrovně Premium**

    Jako možnost replikace účtu služby premium storage podporuje jen místně redundantní úložiště. Místně redundantní úložiště udržuje tři kopie dat v rámci jedné oblasti. Místní zotavení po havárii, je nutné zálohovat disky virtuálních počítačů v jiné oblasti pomocí [Azure Backup](../articles/backup/backup-introduction-to-azure-backup.md). Také musíte použít účet geograficky redundantní úložiště (GRS) jako úložiště záloh. 

    Azure používá účet úložiště jako kontejner pro nespravované disky. Při vytváření virtuálního počítače Azure, která podporuje Storage úrovně Premium s nespravovanými disky, a vyberte účet úložiště úrovně premium, operační systém a datové disky jsou uložené v tomto účtu úložiště.

## <a name="supported-vms"></a>Podporované virtuální počítače

Premium Storage se podporuje na širokou škálu virtuálních počítačů Azure. S těmito typy virtuálních počítačů můžete použít disky storage úrovně standard a premium. Disky storage úrovně premium nelze použít s řadu virtuálních počítačů, které úrovně Premium nejsou kompatibilní s úložištěm.


Informace o typech a velikostech virtuálních počítačů v Azure pro Windows najdete v tématu věnovaném [velikostem virtuálních počítačů s Windows](../articles/virtual-machines/windows/sizes.md). Informace o typech a velikostech virtuálních počítačů v Azure pro Linux najdete v tématu věnovaném [velikostem virtuálních počítačů s Linuxem](../articles/virtual-machines/linux/sizes.md).

Tady jsou některé z funkcí podporovaných v premium storage virtuálních počítačů s povoleným:

* **Skupina dostupnosti**

    Použijeme příklad virtuální počítače řady DS-series, můžete přidat virtuální počítač řady DS-series s cloudovou službou, která má jenom virtuální počítače řady DS-series. Nepřidávejte do existující cloudovou službu, která má libovolný typ jiný než virtuální počítače řady DS-series virtuálních počítačů řady DS-series. Vaše stávající virtuální pevné disky můžete migrovat na novou cloudovou službu, která se spouští jenom virtuální počítače řady DS-series. Pokud chcete použít stejnou virtuální IP adresu pro novou cloudovou službu, který je hostitelem vašich virtuálních počítačů řady DS-series, použijte [vyhrazené IP adresy](../articles/virtual-network/virtual-networks-instance-level-public-ip.md).

* **Disk s operačním systémem**

    Váš virtuální počítač Premium Storage můžete nastavit pomocí na úrovni premium nebo standardní operační systém disku. Pro dosažení co nejlepších výsledků doporučujeme používat disk operačního systému založeného na Premium Storage.

* **Datové disky**

    Premium a standard můžete použít ve stejné virtuální počítač Premium Storage. Díky službě Premium Storage můžete zřídit virtuální počítač a připojit několik trvalých datových disků k virtuálnímu počítači. V případě potřeby pro zvýšení kapacity a výkonu svazku, můžete prokládanou napříč disky.

    > [!NOTE]
    > Pokud prokládanou datových disků premium storage s použitím [prostory úložiště](http://technet.microsoft.com/library/hh831739.aspx), nastavit prostory úložiště s 1 sloupec pro každý disk, který používáte. Celkový výkon prokládané svazku, v opačném případě může být nižší, než se očekávalo kvůli nerovnoměrné distribuci provozu discích. Ve výchozím nastavení, ve Správci serveru můžete nastavení sloupců pro až 8 disky. Pokud máte víc než 8 disky pomocí prostředí PowerShell vytvořit svazek. Ručně zadejte počet sloupců. V opačném případě uživatelského rozhraní správce serveru dál používat 8 sloupců, i když máte více disků. Například pokud máte 32 disků v jedné prokládané sady, zadejte 32 sloupce. Chcete-li určit počet sloupců, které používá virtuální disk, [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) rutiny Powershellu, použijte *NumberOfColumns* parametru. Další informace najdete v tématu [prostory úložiště – přehled](http://technet.microsoft.com/library/hh831739.aspx) a [nejčastějších dotazech týkajících se prostory úložiště](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx).
    >
    > 

* **mezipaměť**

    Virtuální počítače (VM), které podporují službu Premium Storage mají jedinečné funkce ukládání do mezipaměti pro vyšší propustnost a snížená latence. Jejich ukládání do mezipaměti funkce překračuje základní výkon disku úrovně premium storage. Ne všechny virtuální počítače podporu ukládání do mezipaměti, takže zkontrolujte ji prosím specifikace virtuálních počítačů pro velikosti virtuálních počítačů vás zajímají další informace.  Virtuální počítače, které podporují ukládání do mezipaměti se označit to jejich specifikace s měřením "Maximální propustnost v mezipaměti a dočasného úložiště".  V jakém jsou také uvedeny přímo pod názvem virtuálního počítače.
    
    S ukládáním do mezipaměti, můžete nastavit zásady na disky premium storage k ukládání do mezipaměti disku **jen pro čtení**, **ReadWrite**, nebo **žádný**. Disk výchozí zásady ukládání do mezipaměti je **jen pro čtení** pro všechny datové disky úrovně premium, a **ReadWrite** pro disky operačního systému. Pro optimální výkon pro vaše aplikace prosím nezapomeňte použít nastavení správnou mezipaměť. 
    
    Jako příklad náročná na výkon pro čtení nebo jen pro čtení datových disků, jako jsou datové soubory SQL serveru, nastavit zásady pro ukládání do mezipaměti **jen pro čtení**. Náročné na zápis nebo jen pro zápis datových disků, jako jsou například soubory protokolu serveru SQL Server, nastavit zásady pro ukládání do mezipaměti **žádný**. 
    
    Další informace o optimalizaci vašeho návrhu díky službě Premium Storage najdete v tématu [návrh pro výkon s Premium Storage](../articles/virtual-machines/windows/premium-storage-performance.md).

* **Analýzy**

    Pokud chcete analyzovat výkon virtuálního počítače pomocí disky v Premium Storage, zapnout diagnostiku virtuálních počítačů v [webu Azure portal](https://portal.azure.com). Další informace najdete v tématu [monitorování virtuálních počítačů Azure pomocí rozšíření Azure Diagnostics](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/). 

    Výkon disku najdete pomocí nástrojů podle operačního systému jako [Windows Performance Monitor](https://technet.microsoft.com/library/cc749249.aspx) pro virtuální počítače s Windows a [iostat](http://linux.die.net/man/1/iostat) příkaz pro virtuální počítače s Linuxem.

* **Limity škálování virtuálních počítačů a výkon**

    Všechny velikosti virtuálních počítačů služby Premium Storage podporuje má limity škálování a výkonu specifikace pro vstupně-výstupních operací, šířky pásma a počtu disků, které je možné připojit na virtuální počítač. Pokud použijete disky premium storage s virtuálními počítači, ujistěte se, že je dostatečná IOPS a šířky pásma na virtuálním počítači pro jednotky diskové přenosy.

    Například STANDARD_DS1 virtuální počítač má vyhrazenou šířku pásma od 32 MB/s pro přenosy disků úložiště úrovně premium. Disk úložiště úrovně premium P10, můžete zadat šířku pásma 100 MB/s. Pokud disk úložiště úrovně premium P10 je připojena k tomuto virtuálnímu počítači, můžete přejít jenom až 32 MB/s. Maximální 100 MB/s, která dokáže poskytovat disku P10 nemůže používat.

    V současné době je největší virtuální počítač v řady DS-series standard_ds15_v2 urychlení sítě. Standard_ds15_v2 urychlení sítě můžete zadat až 960 MB/s na všech discích. Standard_GS5 je největší virtuální počítač v řady GS-series. Standard_GS5 může poskytnout až 2 000 MB/s na všech discích.

    Tato omezení jsou disku pouze pro provoz. Tato omezení jsou přístupy do mezipaměti a přenosech v síti. Samostatné šířka pásma je k dispozici pro síťového provozu virtuálního počítače. Šířka pásma pro síťový provoz se liší od vyhrazené šířky pásma používá disky premium storage.

    Nejnovější informace o maximální IOPS a propustnost (šířka pásma) pro virtuální počítače s podporou služby Premium Storage najdete v části [velikosti virtuálních počítačů s Windows](../articles/virtual-machines/windows/sizes.md) nebo [velikosti virtuálního počítače s Linuxem](../articles/virtual-machines/linux/sizes.md).

    Další informace o disky storage úrovně premium a jejich omezení IOPS a propustnost najdete v tabulce v další části.

## <a name="scalability-and-performance-targets"></a>Škálovatelnost a cíle výkonnosti
V této části popisujeme škálovatelnost a výkonnostní cíle, které je třeba zvážit při použití služby Premium Storage.

Účty úložiště úrovně Premium mají následující cíle škálovatelnosti:

| Celkový počet účtů kapacity | Celková šířka pásma pro účet místně redundantního úložiště |
| --- | --- | 
| Kapacita disku: 35 TB <br>Kapacita snímku: 10 TB | Až 50 gigabity za sekundu pro příchozí<sup>1</sup> + odchozí<sup>2</sup> |

<sup>1</sup> všechna data (požadavky) odesílané do účtu úložiště

<sup>2</sup> všechna data (požadavky), které byly přijaty z účtu úložiště

Další informace najdete v tématu [škálovatelnost a výkonnostní cíle Azure Storage](../articles/storage/common/storage-scalability-targets.md).

Pokud používáte účty služby premium storage pro nespravované disky a vaše aplikace překračuje cíle škálovatelnosti z jednoho účtu úložiště, může být vhodné k migraci na spravované disky. Pokud už nechcete migrovat do managed disks, sestavení aplikace pro použití více účtů úložiště. Potom data rozdělte mezi tyto účty úložiště. Například pokud chcete připojení disků 51 TB napříč několika virtuálními počítači, rozloženy je dva účty úložiště. 35 TB je limit pro účet úložiště jedné úrovně premium. Ujistěte se, že účet úložiště úrovně premium jeden nikdy zajišťované disky větší než 35 TB.

### <a name="premium-storage-disk-limits"></a>Limity disk Storage úrovně Premium
Když si zřídíte disk úložiště úrovně premium, velikost disku určuje maximální IOPS a propustnost (šířka pásma). Azure nabízí osm typů disky storage úrovně premium: P4 (spravované jen disky), P6 (spravované jen disky), P10, P15, P20, P30, P40 nebo P50. Každý typ disku služby premium storage má omezení IOPS a propustnost. Limity pro typy disků jsou popsány v následující tabulce:

| Disků typu Premium | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60 | P70 | P80 | |---|---|---|---|---|---|---|---|---|| -------|| -------|| -------| | Velikost disku | 32 giB | 64 giB | 128 GB | 256 GB | 512 GB | 1024 giB (1 TB) | 2048 giB (2 TB) | 4095 giB (4 TB) | 8192 giB (8 TB) | 16384 giB (16 TB) | 32 767 giB (32 TiB) || IOPS na disk | 120 | 240 | 500 | 1100 | 2300 | 5000 | 7500 | 7500 | 12 500 | 15 000 | 20 000 || Propustnost na disk | 25 MB za sekundu | 50 MB za sekundu | 100 MB za sekundu | 125 MB za sekundu | 150 MB za sekundu | 200 MB za sekundu | 250 MB za sekundu | 250 MB za sekundu | 480 MB za sekundu | 750 MB za sekundu | 750 MB za sekundu |

> [!NOTE]
> Zkontrolujte, zda je k dispozici na na jednotce diskové přenosy virtuálního počítače dostatečnou šířku pásma, jak je popsáno v [virtuálních počítačů služby Premium Storage podporuje](#premium-storage-supported-vms). V opačném případě propustnost disku a vstupně-výstupních operací je omezen na nižší hodnoty. Maximální propustnost a vstupně-výstupních operací jsou založeny na omezení virtuálního počítače, ne na disku omezení jsou popsané v předchozí tabulce.  
> Azure je navržená tak, platforma Premium Storage bude masivně paralelní. Návrh aplikace jako vícevláknového vám pomůže zajistit vysoký výkon cíl nabízíme na větší velikosti disku.

Zde jsou některé důležité kroky vědět o škálovatelnost a výkonnostní cíle služby Premium Storage:

* **Zřízená kapacita a výkon**

    Při zřizování disku úložiště úrovně premium, na rozdíl od úložiště úrovně standard, je zaručena kapacita, IOPS a propustnost tohoto disku. Například pokud vytvoříte P50 disku, Azure mu kapacitu 4 095 GB úložiště, 7500 IOPS a propustnost 250 MB/s pro tento disk. Aplikace můžete použít nebo její část kapacitu a výkon.

* **Velikost disku**

    Velikost disku (zaokrouhluje nahoru) Azure mapuje na nejbližší premium storage disku možnost, jak je uvedeno v tabulce v předchozí části. Například velikost 100 GB disku je klasifikován tak možnost P10. Může provádět maximálně 500 IOPS, s maximálně 100 MB za sekundu propustnosti. Obdobně disku velikost 400 GB je klasifikován tak P20. Můžete provádět až 2,300 vstupně-výstupních operací, s propustností 150 MB/s.

    > [!NOTE]
    > Můžete snadno zvýšit velikost stávajících disků. Například můžete chtít zvětšit velikost 30 GB disk, až 128 GB, nebo dokonce 1 TB. Nebo můžete chtít převést P20 disk na P30 disk, protože budete potřebovat větší kapacitu nebo Další vstupně-výstupních operací a propustnosti. 

* **Velikost vstupně-výstupních operací**

    Velikost vstup nebo výstup je 256 KB. Pokud přenosu dat je menší než 256 KB, bude považován za 1 jednotkovou vstupně-výstupních operací. Větší velikosti vstupně-výstupní operace se počítají jako více vstupně-výstupních operací velikost 256 KB. Například 1100 KB vstupně-výstupní operace se počítá jako 5 jednotky vstupně-výstupních operací.

* **Propustnost**

    Limit propustnosti zahrnuje zápisy na disk a zahrnuje operací čtení disku, které nejsou obsluhovány z mezipaměti. Propustnost 100 MB/s na disk má například disku P10. Některé příklady platný propustnost disku P10 jsou uvedeny v následující tabulce:

    | Maximální propustnost na P10 disk | Bez mezipaměti čtení z disku | Bez mezipaměti zápisy na disk |
    | --- | --- | --- |
    | 100 MB/s | 100 MB/s | 0 |
    | 100 MB/s | 0 | 100 MB/s |
    | 100 MB/s | 60 MB/s | 40 MB/s |

* **Přístupy do mezipaměti**

    Přístupy do mezipaměti nejsou omezeny přidělené vstupně-výstupních operací a propustnosti disku. Například při použití datový disk se **jen pro čtení** nastavení mezipaměti na virtuálním počítači, který podporuje Storage úrovně Premium, čtení, které jsou obsluhovány z mezipaměti se nevztahují na vstupně-výstupních operací a propustnosti caps disku. Pokud převážně je zatížení disku načte, můžou získat velmi vysokou propustností. Mezipaměť je v souladu s samostatné IOPS a propustnost limity na virtuální počítač úrovně, na základě velikosti virtuálního počítače. Virtuální počítače řady DS-series mají přibližně 4 000 IOPS a propustnost 33 MB/s podle počtu jader pro mezipaměť a místní SSD vstupně-výstupních operací. Virtuální počítače řady GS-series mají maximálně 5 000 IOPS a propustnost 50 MB/s podle počtu jader pro mezipaměť a místní SSD vstupně-výstupních operací.

## <a name="throttling"></a>Throttling

Omezení využití sítě může dojít, pokud vaše aplikace vstupně-výstupních operací nebo propustnost překročí přidělené limity pro disk úložiště úrovně premium. Omezení také může dojít, pokud vaše celkové diskové přenosy přes všechny disky na virtuálním počítači překračuje limit na disku šířka pásma dostupná pro virtuální počítač. Abyste předešli omezování, doporučujeme omezit počet čekajících požadavků na vstupně-výstupní operace disku. Pomocí omezení na základě disku, kterou jste zřídili škálovatelnost a výkonnostní cíle a týkající se šířky pásma disku k virtuálnímu počítači k dispozici.  

Vaše aplikace může dosáhnout nejnižší latenci při je navržená tak, aby omezení šířky pásma. Ale pokud počet čekajících požadavků na vstupně-výstupních operací disku je příliš malá, vaše aplikace nelze využít výhod maximální IOPS a propustnosti, které jsou k dispozici na disk.

Následující příklady ukazují, jak vypočítat omezení úrovní. Všechny výpočty jsou založeny na jednotku velikost 256 KB vstupně-výstupních operací.

### <a name="example-1"></a>Příklad 1

Vaše aplikace má v jedné sekundy na disku P10 zpracovat 495 vstupně-výstupní jednotky o velikosti 16 KB. Jednotky vstupně-výstupní operace se počítají jako 495 vstupně-výstupních operací. Pokud se pokusíte 2 MB vstupně-výstupních operací ve stejném za druhé, celkový počet vstupně-výstupní jednotky je rovna 495 + 8 vstupně-výstupních operací. Důvodem je, že vstupně-výstupních operací 2 MB = 2 048 KB a 256 KB = 8 vstupně-výstupních jednotek, když velikost jednotky vstupně-výstupních operací je 256 KB. Protože součet 495 + 8 přesahuje limit 500 vstupně-výstupních operací disku, omezování vyvolá.

### <a name="example-2"></a>Příklad 2

Vaše aplikace zpracovala 400 jednotek velikost 256 KB na disku P10 vstupně-výstupních operací. Celkový počet využité šířky pásma je (400 &#215; 256) / 1 024 KB = 100 MB/s. Disku P10 má limit propustnosti 100 MB/s. Pokud se aplikace pokusí provést další vstupně-výstupní operace v této druhé, bude omezený, protože překračuje limit přidělená.

### <a name="example-3"></a>Příklad 3

Máte DS4 virtuální počítač se dvěma disky P30 připojen. Všechny disky P30 je propustnost 200 MB/s. DS4 virtuální počítač má ale celková disková kapacita šířky pásma 256 MB/s. Obě připojené disky s maximální propustností nelze jednotky na tomto virtuálním počítači DS4 ve stejnou dobu. Chcete-li tento problém vyřešit, může tolerovat provoz 200 MB za sekundu na jeden disk a 56 MB/s na jiném disku. Pokud součet diskové přenosy prochází přes 256 MB/s, je omezen provoz disku.

> [!NOTE]
> Pokud diskové přenosy většinou obsahuje malé velikosti vstupně-výstupních operací, bude vaše aplikace pravděpodobně dosáhl limitu vstupně-výstupních operací než limit propustnosti. Ale pokud diskové přenosy se skládá převážně velikostí velkých vstupně-výstupních operací, aplikace pravděpodobně bude čeho limit propustnosti, namísto limit vstupně-výstupních operací. Vstupně-výstupních operací vaší aplikace a kapacitou propustnosti můžete maximalizovat pomocí optimální velikosti vstupně-výstupních operací. Navíc můžete omezit počet čekajících požadavků vstupně-výstupní operace disku.

Další informace o návrh pro vysoký výkon pomocí služby Premium Storage najdete v tématu [návrh pro výkon s Premium Storage](../articles/virtual-machines/windows/premium-storage-performance.md).

## <a name="snapshots-and-copy-blob"></a>Snímků a kopírování objektů Blob

Na službu úložiště souboru virtuálního pevného disku je objekt blob stránky. Můžete pořizovat snímky objektů BLOB stránky a zkopírujte je do jiného umístění, například na jiný účet úložiště.

### <a name="unmanaged-disks"></a>Nespravované disky

Vytvoření [přírůstkových snímků](../articles/virtual-machines/linux/incremental-snapshots.md) pro disky stejným způsobem použít snímky ve standard storage úrovně premium. Premium Storage podporuje jen místně redundantní úložiště jako možnost replikace. Doporučujeme vytvořit snímky a potom zkopírujte snímků na účet geograficky redundantního úložiště úrovně standard. Další informace najdete v tématu [možnosti redundance Azure Storage](../articles/storage/common/storage-redundancy.md).

Pokud disk připojen k virtuálnímu počítači, nejsou povolené. některé operace rozhraní API na disku. Například nelze provést [objekt Blob kopírování](/rest/api/storageservices/Copy-Blob) operace u objektu blob v případě, že disk je připojený k virtuálnímu počítači. Místo toho nejprve vytvořit snímek tohoto objektu blob pomocí [pořízení snímku objektu Blob](/rest/api/storageservices/Snapshot-Blob) rozhraní REST API. Potom proveďte [objekt Blob kopírování](/rest/api/storageservices/Copy-Blob) snímku kopírování připojený disk. Alternativně můžete odpojit disk a proveďte všechny potřebné operace.

Snímky objektů blob storage úrovně premium platí tato omezení:

| Limit úložiště úrovně Premium | Hodnota |
| --- | --- |
| Maximální počet snímků na objekt blob | 100 |
| Kapacitě účtu úložiště pro snímky<br>(Zahrnuje data pouze snímky. Nezahrnuje data v základním objektu blob.) | 10 TB |
| Minimální čas mezi po sobě jdoucích snímků | 10 minut |

Pokud chcete zachovat geograficky redundantní kopie vašich snímky, můžete zkopírovat snímky z účtu služby premium storage na účet geograficky redundantního úložiště úrovně standard s využitím AzCopy nebo objekt Blob kopírování. Další informace najdete v tématu [přenos dat pomocí nástroje příkazového řádku azcopy](../articles/storage/common/storage-use-azcopy.md) a [objekt Blob kopírování](/rest/api/storageservices/Copy-Blob).

Podrobné informace o provádění operací REST k objektům BLOB stránky v účtu služby premium storage najdete v tématu [operace služby se službou Azure Premium Storage s objekty Blob](http://go.microsoft.com/fwlink/?LinkId=521969).

### <a name="managed-disks"></a>Managed Disks

Snímek spravovaného disku je jen pro čtení kopie spravovaného disku. Snímek se ukládá jako standardní spravovaný disk. V současné době [přírůstkových snímků](../articles/virtual-machines/windows/incremental-snapshots.md) nejsou podporovány pro spravované disky. Informace o vytvoření snímku spravovaného disku najdete v tématu [vytvoření kopie VHD uložené jako spravovaný Azure disk pomocí spravované snímky ve Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md) nebo [vytvoření kopie VHD uložené jako spravovaný Azure s použitím spravovaných disků snímky v systému Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md).

Pokud spravovaný disk připojen k virtuálnímu počítači, nejsou povolené. některé operace rozhraní API na disku. Například nelze vygenerovat sdílený přístupový podpis (SAS) k provedení operace kopírování, zatímco disk připojen k virtuálnímu počítači. Místo toho nejprve vytvořte snímek disku a pak proveďte kopírování snímku. Alternativně můžete odpojit disk a potom generovat SAS k provedení této operace kopírování.


## <a name="premium-storage-for-linux-vms"></a>Storage úrovně Premium pro virtuální počítače s Linuxem
Při nastavení virtuálních počítačů s Linuxem ve službě Premium Storage můžete použít následující informace:

K dosažení cíle škálovatelnosti ve službě Storage úrovně Premium pro všechny disky storage úrovně premium s mezipamětí nastaven na hodnotu **jen pro čtení** nebo **žádný**, při připojení systému souborů je nutné zakázat "překážky". Překážky v tomto scénáři není nutné, protože trvalého pro tato nastavení mezipaměti zápisy na disky premium storage. Až se žádost o zápis úspěšně dokončí, data se zapsala do trvalého úložiště. Zakázat "překážky", použijte jednu z následujících metod. Vyberte si metodu pro systém souborů:
  
* Pro **reiserFS**, chcete-li zakázat bariéry, použijte `barrier=none` možnost připojit. (Chcete-li povolit bariéry, použijte `barrier=flush`.)
* Pro **ext3/ext4**, chcete-li zakázat bariéry, použijte `barrier=0` možnost připojit. (Chcete-li povolit bariéry, použijte `barrier=1`.)
* Pro **XFS**, chcete-li zakázat bariéry, použijte `nobarrier` možnost připojit. (Chcete-li povolit bariéry, použijte `barrier`.)
* Storage úrovně premium disky s mezipamětí nastavena na hodnotu **ReadWrite**, povolte bariéry zápisu kvůli vyšší odolnosti.
* Pro jmenovky svazku se zachovat po restartování virtuálního počítače je nutné aktualizovat /etc/fstab s univerzálně jedinečným identifikátorem (UUID) odkazy na disky. Další informace najdete v tématu [přidání spravovaného disku do virtuálního počítače s Linuxem](../articles/virtual-machines/linux/add-disk.md).

Následující Linuxových distribucích ověřily pro službu Azure Premium Storage. Pro lepší výkon a stabilita díky službě Premium Storage doporučujeme upgradovat vaše virtuální počítače, na některou z těchto verzí, minimálně (nebo na novější verzi). Některé verze nejnovější Linux Integration Services (LIS), v4.0, vyžadují pro Azure. Stáhnout a nainstalovat distribuční, odkazu uvedené v následující tabulce. Můžeme přidat Image do seznamu jsme dokončení ověření. Všimněte si, že naše ověření ukazují, že se mění výkon zvýšením každé bitové kopie. Výkon závisí na charakteristikách úloh a nastavení bitové kopie. Jiné Image je vyladěná pro různé druhy úloh.

| Distribuce | Verze | Podporované jádra | Podrobnosti |
| --- | --- | --- | --- |
| Ubuntu | 12.04 | 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-server-20150119-en-us-30GB |
| Ubuntu | 14.04 | 3.13.0-44.73+ | Ubuntu-14_04_1-LTS-amd64-server-20150123-en-us-30GB |
| Debian | 7.x, 8.x | 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12| 3.12.36-38.1+| suse-sles-12-priority-v20150213 <br> suse-sles-12-v20150213 |
| SUSE | SLES 11 SP4 | 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+| 3.18.4+ | CoreOS 584.0.0 |
| CentOS | 6.5, 6.6, 6.7, 7.0 | &nbsp; | [LIS4 vyžaduje](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Další informace v poznámce v další části* |
| CentOS | 7.1+ | 3.10.0-229.1.2.el7+ | [Doporučené LIS4](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Další informace v poznámce v další části* |
| Red Hat Enterprise Linux (RHEL) | 6.8+, 7.2+ | &nbsp; | &nbsp; |
| Oracle | 6.0+, 7.2+ | &nbsp; | UEK4 nebo RHCK |
| Oracle | 7.0-7.1 | &nbsp; | UEK4 nebo RHCK plánovaným bodem obnovení kratším[LIS verze 4.1 a vyšší](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |
| Oracle | 6.4-6.7 | &nbsp; | UEK4 nebo RHCK plánovaným bodem obnovení kratším[LIS verze 4.1 a vyšší](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |


### <a name="lis-drivers-for-openlogic-centos"></a>Ovladače služby LIS OpenLogic CentOS

Pokud používáte OpenLogic CentOS virtuální počítače, spusťte následující příkaz k instalaci nejnovější ovladače:

```
sudo rpm -e hypervkvpd  ## (Might return an error if not installed. That's OK.)
sudo yum install microsoft-hyper-v
```

Pokud chcete aktivovat nové ovladače, restartujte počítač.

## <a name="pricing-and-billing"></a>Ceny a fakturace

Pokud používáte Storage úrovně Premium, platí následující aspekty fakturace:

* **Velikost disku a objektů blob úložiště úrovně Premium**

    Fakturace pro disk storage úrovně premium nebo objekt blob závisí na zřízená velikost disku nebo objektu blob. Zřízená velikost (zaokrouhluje nahoru) Azure mapuje na nejbližší možnost disku úložiště úrovně premium. Podrobnosti najdete v tabulce v [škálovatelnost a výkonnostní cíle Storage úrovně Premium](#premium-storage-scalability-and-performance-targets). Každý disk mapuje na velikosti zřízeného disku podporované a účtuje se odpovídajícím způsobem. Za všechny zajišťovaným diskem se fakturuje po hodinách pomocí cenu za měsíc pro nabídku služby Premium Storage. Například pokud zřízení disku P10 a odstraní ji po 20 hodin, fakturuje se vám pro nabídky P10 nebo jeho poměrnou část 20 hodin. To je bez ohledu na množství skutečné dat zapsaných na disk nebo IOPS a propustnost použít.

* **Nespravované disky Premium snímky**

    Snímky disků premium nespravované se účtují další zvýšení kapacity používané snímky. Další informace o snímcích najdete v tématu [vytvořit snímek objektu blob](/rest/api/storageservices/Snapshot-Blob).

* **Spravované snímky disků úrovně Premium**

    Snímek spravovaného disku je jen pro čtení kopie disku. Disk je uložený jako standardní spravovaný disk. Snímek stojí stejné jako standardní spravovaný disk. Například pokud pořídíte snímek spravovaného disku úrovně premium 128 GB, náklady na snímku je ekvivalentní k 128 GB spravovaných disků úrovně standard.  

* **Přenosy odchozích dat**

    [Přenosy odchozích dat](https://azure.microsoft.com/pricing/details/data-transfers/) (data odcházející z Datacenter Azure) jsou zpoplatněné využití šířky pásma.

Podrobné informace o cenách pro Premium Storage, služby Premium Storage podporuje virtuální počítače a spravované disky najdete v těchto článcích:

* [Ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/)
* [Ceny Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Ceny služby Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="azure-backup-support"></a>Podpora Azure Backup

Místní zotavení po havárii, je nutné zálohovat disky virtuálních počítačů v jiné oblasti pomocí [Azure Backup](../articles/backup/backup-introduction-to-azure-backup.md) a účtu úložiště GRS jako trezor záloh.

Chcete-li vytvořit úlohu zálohování s časovou synchronizací zálohy, snadné obnovení virtuálního počítače a zásady uchovávání záloh, použijte Azure Backup. Můžete použít zálohování i s spravované a nespravované disky. Další informace najdete v tématu [Azure Backup pro virtuální počítače s nespravovanými disky](../articles/backup/backup-azure-vms-first-look-arm.md) a [Azure Backup pro virtuální počítače se spravovanými disky](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup). 

## <a name="next-steps"></a>Další postup

Další informace o službě Premium Storage naleznete v následujících článcích.
