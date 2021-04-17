---
title: 'Storage: Doporučené postupy pro výkon & pokyny'
description: Poskytuje osvědčené postupy a pokyny pro úložiště pro optimalizaci výkonu SQL Server na virtuálním počítači Azure (VM).
services: virtual-machines-windows
documentationcenter: na
author: dplessMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: dpless
ms.reviewer: jroth
ms.openlocfilehash: 23e006c637285ad484e98b23b2a9f506156f519c
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389719"
---
# <a name="storage-performance-best-practices-for-sql-server-on-azure-vms"></a>Storage: osvědčené postupy výkonu pro SQL Server na virtuálních počítačích Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Tento článek poskytuje osvědčené postupy a pokyny pro úložiště pro optimalizaci výkonu SQL Server v Azure Virtual Machines (virtuálních počítačích).

Mezi optimalizací a optimalizací výkonu je typicky kompromis. Tato řada osvědčených postupů pro výkon se zaměřuje na získání *nejlepšího* výkonu pro SQL Server v Azure Virtual Machines. Pokud je vaše úloha méně náročná, možná nebudete potřebovat při každé doporučené optimalizaci. Při hodnocení těchto doporučení Vezměte v úvahu požadavky na výkon, náklady a vzory úloh.

Další informace najdete v dalších článcích v této sérii: [Kontrolní seznam výkonu](performance-guidelines-best-practices-checklist.md), [Velikost virtuálního počítače](performance-guidelines-best-practices-vm-size.md)a [shromáždění směrného plánu](performance-guidelines-best-practices-collect-baseline.md). 

## <a name="checklist"></a>Kontrolní seznam

V následujícím kontrolním seznamu najdete stručný přehled osvědčených postupů úložiště, které zbytek článku pokrývá podrobněji: 

- Před volbou typu disku Sledujte aplikaci a [Určete požadavky na šířku pásma a latenci úložiště](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements) pro SQL Server dat, protokolů a souborů tempdb. 
- Pokud chcete optimalizovat výkon úložiště, naplánujte si nejvyšší dostupný počet vstupně-výstupních operací a používejte ukládání dat do mezipaměti jako funkci výkonu pro čtení dat, zatímco se vyhnete [virtuálnímu počítači a diskům capping](../../../virtual-machines/premium-storage-performance.md#throttling).
- Data, protokoly a soubory tempdb umístěte na samostatné jednotky.
    - Pro datovou jednotku používejte jenom [disky Premium P30 a P40](../../../virtual-machines/disks-types.md#premium-ssd) , abyste zajistili dostupnost podpory mezipaměti.
    - Pro plán jednotky protokolu pro kapacitu a výkon testu oproti nákladům při vyhodnocování [P30 disků úrovně Premium P80](../../../virtual-machines/disks-types.md#premium-ssd)
      - Pokud se vyžaduje latence úložiště v milisekundách, použijte pro protokol transakcí [Azure Ultra disks](../../../virtual-machines/disks-types.md#ultra-disk) . 
      - Pro nasazení virtuálních počítačů řady M-Series zvažte možnost [napsat akcelerátor](../../../virtual-machines/how-to-enable-write-accelerator.md) pro použití disků Azure Ultra.
    - Po výběru optimální velikosti virtuálního počítače umístěte [databázi tempdb](/sql/relational-databases/databases/tempdb-database) do místní dočasné `D:\` jednotky SSD pro většinu SQL Server úloh. 
      - Pokud kapacita místního disku není pro databázi tempdb dostatečná, zvažte možnost navýšení velikosti virtuálního počítače. Další informace najdete v tématu [zásady ukládání do mezipaměti datových souborů](#data-file-caching-policies) .
- Proložením několika datových disků Azure pomocí [prostorů úložiště](/windows-server/storage/storage-spaces/overview) zvyšte šířku pásma vstupně-výstupních operací cílového virtuálního počítače a omezení propustnosti.
- Nastavte [ukládání hostitelů do mezipaměti](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) pro disky datových souborů jen pro čtení.
- Nastavte [mezipaměť hostitele](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) na hodnotu žádné pro soubory protokolu.
    - Nepovolujte ukládání do mezipaměti pro čtení a zápis na discích, které obsahují soubory SQL Server. 
    - Před změnou nastavení mezipaměti disku vždycky zastavte službu SQL Server.
- Pro úlohy vývoje a testování a dlouhodobé archivace záloh zvažte použití služby Storage úrovně Standard. Pro produkční úlohy se nedoporučuje používat HDD úrovně Standard/SDD.
- [Diskové shlukování na základě kreditu](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1-P20) by se mělo považovat jenom za úlohy pro vývoj a testování a systémy oddělení.
- Zřídí účet úložiště ve stejné oblasti jako virtuální počítač SQL Server. 
- Zakažte geograficky redundantní úložiště Azure (geografickou replikaci) a použijte LRS (místní redundantní úložiště) v účtu úložiště.
- Naformátujte datový disk tak, aby používal velikost bloku (alokační jednotka) 64 KB pro všechny datové soubory umístěné na jiné jednotce než na dočasném `D:\` disku (ve výchozím nastavení 4 KB). SQL Server virtuální počítače nasazené prostřednictvím Azure Marketplace se dodávají s datovými disky formátovanými velikostí bloku a proložením pro fond úložiště nastavený na 64 KB. 

Pokud chcete porovnat kontrolní seznam úložiště s ostatními, přečtěte si podrobný [Kontrolní seznam pro osvědčené postupy výkonu](performance-guidelines-best-practices-checklist.md). 

## <a name="overview"></a>Přehled

Chcete-li najít nejúčinnější konfiguraci pro SQL Server úloh na virtuálním počítači Azure, začněte [měřením výkonu úložiště vaší obchodní aplikace](performance-guidelines-best-practices-collect-baseline.md#storage). Jakmile jsou známy požadavky na úložiště, vyberte virtuální počítač, který podporuje nezbytné IOPS a propustnost s příslušným poměrem paměti až vCore. 

Vyberte velikost virtuálního počítače s dostatečnou škálovatelností úložiště pro vaše zatížení a kombinaci disků (obvykle ve fondu úložiště), které splňují požadavky vaší firmy na kapacitu a výkon. 

Typ disku závisí na typu souboru hostovaném na disku a na požadavcích na výkon ve špičce.

> [!TIP]
> Zřizování virtuálního počítače s SQL Server pomocí Azure Portal vám pomůže s procesem konfigurace úložiště a implementuje většinu osvědčených postupů pro úložiště, jako je vytváření samostatných fondů úložiště pro vaše data a soubory protokolů, cílení na `D:\` jednotku tempdb a povolení optimálních zásad ukládání do mezipaměti. Další informace o zřizování a konfiguraci úložiště najdete v tématu [Konfigurace úložiště virtuálních počítačů s SQL](storage-configuration.md). 

## <a name="vm-disk-types"></a>Typy disků virtuálního počítače

Máte možnost výběru na úrovni výkonu pro vaše disky. Typy spravovaných disků, které jsou k dispozici jako základní úložiště (uvedené zvýšením výkonu), jsou standardní jednotky pevného disku (HDD), Standard SSD, Premium Solid-State Drives (SSD) a Ultra disks. 

Výkon disku se zvyšuje s kapacitou, která se seskupuje podle [popisků disku úrovně Premium](../../../virtual-machines/disks-types.md#premium-ssd) , například P1 se 4 GiB a 120 IOPS do P80 s 32 TIB úložištěm a 20 000 IOPS. Premium Storage podporuje mezipaměť úložiště, která pomáhá zlepšit výkon čtení a zápisu pro některé úlohy. Další informace najdete v tématu [Přehled služby Managed disks](../../../virtual-machines/managed-disks-overview.md). 

K dispozici jsou také tři hlavní [typy disků](../../../virtual-machines/managed-disks-overview.md#disk-roles) , které je třeba zvážit pro SQL Server na virtuálním počítači Azure – disk s operačním systémem, dočasný disk a datové disky. Pečlivě si vyberte, co je uložené na jednotce operačního systému `(C:\)` a dočasný dočasný disk `(D:\)` . 

### <a name="operating-system-disk"></a>Disk operačním systému

Disk s operačním systémem je virtuální pevný disk, který se dá spustit a připojit jako běžící verze operačního systému a je označený jako `C:\` jednotka. Když vytvoříte virtuální počítač Azure, bude platforma k VIRTUÁLNÍmu počítači pro disk s operačním systémem připojovat aspoň jeden disk. `C:\`Jednotka je výchozím umístěním pro instalace aplikací a konfiguraci souborů. 

Pro produkční SQL Server prostředí nepoužívejte disk s operačním systémem pro datové soubory, soubory protokolů a protokoly chyb. 

### <a name="temporary-disk"></a>Dočasný disk

Řada virtuálních počítačů Azure obsahuje jiný typ disku nazvaný dočasný disk (označený jako `D:\` jednotka). V závislosti na řadě virtuálních počítačů a velikosti se kapacita tohoto disku bude lišit. Dočasný disk je dočasný, což znamená, že se úložiště disku znovu vytvoří (jako v případě, že se znovu přidělí a přidělí), když se virtuální počítač restartuje nebo se přesune na jiného hostitele (například pro [službu](/troubleshoot/azure/virtual-machines/understand-vm-reboot)). 

Dočasná Úložná jednotka není trvale uložená na vzdáleném úložišti, proto by neměla ukládat soubory databáze uživatele, soubory protokolů transakcí nebo cokoli, co musí být zachováno. 

Pro SQL Server úlohy umístěte databázi tempdb na místní dočasnou jednotku SSD, `D:\` Pokud nezáleží na využití místní mezipaměti. Pokud používáte virtuální počítač, který nemá [dočasný disk](../../../virtual-machines/azure-vms-no-temp-disk.md) , doporučuje se databáze tempdb umístit na vlastní izolovaný disk nebo fond úložiště s nastavením ukládání do mezipaměti jen pro čtení. Další informace najdete v tématu [zásady ukládání dat do mezipaměti databáze tempdb](performance-guidelines-best-practices-storage.md#data-file-caching-policies).

### <a name="data-disks"></a>Datové disky

Datové disky jsou disky vzdáleného úložiště, které se často vytvářejí ve [fondech úložiště](/windows-server/storage/storage-spaces/overview) , aby bylo možné překročit kapacitu a výkon, které může každý disk nabídnout virtuálnímu počítači.

Připojte minimální počet disků, které splňují požadavky na IOPS, propustnost a kapacitu vašich úloh. Nepřekračuje maximální počet datových disků nejmenšího virtuálního počítače, na který plánujete změnit velikost.

Zajistěte, aby se data a soubory protokolů na datových discích zřídily tak, aby vyhovovaly vašim požadavkům. 

Naformátujte datový disk tak, aby používal velikost alokační jednotky 64 KB pro všechny datové soubory umístěné na jiné jednotce než na dočasném `D:\` disku (ve výchozím nastavení je 4 KB). SQL Server virtuální počítače nasazené prostřednictvím Azure Marketplace se dodávají s datovými disky formátovanými velikostí alokační jednotky a prokládání pro fond úložiště s nastavením 64 KB. 

## <a name="premium-disks"></a>Prémiové disky

Použijte disky SSD úrovně Premium pro data a soubory protokolů pro produkční SQL Server úlohy. SSD úrovně Premium IOPS a šířky pásma se liší v závislosti na [velikosti a typu disku](../../../virtual-machines/disks-types.md). 

Pro produkční úlohy použijte disky P30 a/nebo P40 pro SQL Server datových souborů k zajištění podpory ukládání do mezipaměti a použití P30 až P80 pro soubory protokolu transakcí SQL Server.  V případě optimálních celkových nákladů na vlastnictví začněte s P30s (5000 IOPS/200 MB/s) pro soubory dat a protokolů a vyberte vyšší kapacitu, pokud potřebujete řídit počet disků virtuálního počítače.

U úloh OLTP se podle vašich požadavků na disk (nebo fond úložiště) shodují s požadavky na výkon, a to s využitím úloh v časech špičky a `Disk Reads/sec`  +  `Disk Writes/sec` čítačů výkonu. Pro úlohy datového skladu a vytváření sestav se porovnává s cílovou propustností pomocí úloh v časech špičky a `Disk Read Bytes/sec`  +  `Disk Write Bytes/sec` . 

Pomocí prostorů úložiště můžete dosáhnout optimálního výkonu, nakonfigurovat dva fondy, jednu pro soubory protokolu a druhou pro datové soubory. Pokud nepoužíváte diskový svazek, použijte dva disky SSD úrovně Premium mapované na samostatné jednotky, kde jedna jednotka obsahuje soubor protokolu a druhý obsahuje data.

[ZŘÍZENÉ IOPS a propustnost](../../../virtual-machines/disks-types.md#premium-ssd) na disk, které se používají jako součást fondu úložiště. Mezi funkce vstupně-výstupních operací a propustnosti disků je maximální schopnost až do limitů propustnosti virtuálního počítače.

Osvědčeným postupem je použít k dispozici nejmenší možný počet disků a současně splnit minimální požadavky na IOPS (a propustnost) a kapacitu. Vyvážení ceny a výkonu je ale vhodnější díky velkému počtu malých disků místo malého počtu velkých disků.

### <a name="scaling-premium-disks"></a>Škálování prémiových disků

Při prvním nasazení spravovaného disku Azure je úroveň výkonu pro tento disk založena na velikosti zřízeného disku. Určete úroveň výkonu při nasazení nebo ji změňte poté, beze změny velikosti disku. Pokud se poptávka zvýší, můžete zvýšit úroveň výkonu tak, aby vyhovovala vašim obchodním potřebám. 

Změna úrovně výkonu umožňuje správcům připravovat a plnit vyšší požadavky, aniž by se museli spoléhat na [shlukování disků](../../../virtual-machines/disk-bursting.md#credit-based-bursting). 

Využijte co nejrychlejšího výkonu, pokud je to potřeba, kde fakturace je navržená tak, aby splňovala úroveň výkonu úložiště. Upgradujte vrstvu tak, aby odpovídala požadavkům na výkon bez zvýšení kapacity. Vraťte se do původní úrovně, pokud již není vyžadován dodatečný výkon.

Toto cenově výhodné a dočasné rozšiřování výkonu představuje silný případ použití pro cílené události, jako je nákup, testování výkonu, školení a další krátká okna, ve kterých je potřeba vyšší výkon jenom pro krátkodobou dobu. 

Další informace najdete v tématu [úrovně výkonu pro spravované disky](../../../virtual-machines/disks-change-performance.md). 

## <a name="azure-ultra-disk"></a>Azure Ultra disk

Pokud je potřeba doba odezvy kratších milisekund s omezenou latencí, zvažte použití [Azure Ultra disk](../../../virtual-machines/disks-types.md#ultra-disk) pro jednotku protokolu SQL Server, nebo dokonce datovou jednotku pro aplikace, které jsou extrémně citlivé na latenci I/O. 

Je možné nakonfigurovat Ultra disk, kde se kapacita a IOPS můžou nezávisle škálovat. Správci Ultra disk můžou zřídit disk s požadavky na kapacitu, IOPS a propustnost, a to na základě potřeb aplikací. 

Ultra disk není podporován na všech řadách virtuálních počítačů a má jiná omezení, jako je dostupnost oblasti, redundance a podpora Azure Backup. Další informace najdete v tématu [použití disků Azure Ultra](../../../virtual-machines/disks-enable-ultra-ssd.md) pro úplný seznam omezení. 

## <a name="standard-hdds-and-ssds"></a>HDD a SSD úrovně Standard

[Standardní HDD](../../../virtual-machines/disks-types.md#standard-hdd) a SSD mají různou latenci a šířku pásma a jsou doporučovány jenom pro úlohy pro vývoj a testování. Produkční úlohy by měly používat Premium SSD. Pokud používáte SSD úrovně Standard (scénáře pro vývoj a testování), doporučujeme přidat maximální počet datových disků, které [Velikost virtuálního počítače](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json) podporuje, a použít k dosažení nejlepšího výkonu diskové svazky s prostory úložiště.

## <a name="caching"></a>Ukládání do mezipaměti

Virtuální počítače, které podporují mezipaměť služby Premium Storage, můžou využít další funkci nazvanou Azure BlobCache nebo ukládání do mezipaměti hostitele za účelem rozšiřování možností IOPS a propustnosti virtuálního počítače. Virtuální počítače povolené pro úložiště úrovně Premium a Storage úrovně Premium mají tyto dvě odlišná omezení šířky pásma úložiště, která se dají použít společně ke zvýšení výkonu úložiště.

Propustnost vstupně-výstupních operací za sekundu bez mezipaměti se počítá oproti limitům propustnosti disku, který není v mezipaměti. Maximální limity v mezipaměti poskytují další vyrovnávací paměť pro čtení, která pomáhají růst adres a neočekávaných vrcholů.

Pokud je tato možnost podporovaná pro výrazné zlepšení výkonu pro čtení na datové jednotce bez dalších nákladů, povolte ukládání do mezipaměti Premium. 

Čtení a zápisy do Azure BlobCache (v mezipaměti IOPS a propustnost) se nepočítají do neuložených vstupně-výstupních operací za sekundu a omezení propustnosti virtuálního počítače.

> [!NOTE]
> Disková mezipaměť není podporovaná pro disky 4 TiB a větší (P50 a větší). Pokud je k VIRTUÁLNÍmu počítači připojeno více disků, bude každý disk, který je menší než 4 TiB, podporovat ukládání do mezipaměti. Další informace najdete v tématu [ukládání disku do mezipaměti](../../../virtual-machines/premium-storage-performance.md#disk-caching). 

### <a name="uncached-throughput"></a>Propustnost neuložené v mezipaměti

Maximální počet nezpracovaných vstupně-výstupních operací disku a propustnost je maximální limit vzdáleného úložiště, který může virtuální počítač zpracovat. Toto omezení je definováno na virtuálním počítači a nejedná se o omezení základního diskového úložiště. Toto omezení platí jenom pro vstupně-výstupní operace s datovými jednotkami, které se vzdáleně připojují k virtuálnímu počítači, a ne místní vstupně-výstupní operace s dočasnou jednotkou ( `D:\` jednotka) nebo jednotkou operačního systému.

Velikost necache IOPS a propustnosti, které jsou k dispozici pro virtuální počítač, můžete ověřit v dokumentaci k vašemu virtuálnímu počítači.

Například v dokumentaci k [řadě M-Series](../../../virtual-machines/m-series.md) vidíte, že maximální nemezipaměť propustnosti pro Standard_M8ms virtuální počítač je 5000 IOPS a 125 MB/s propustnosti disku bez mezipaměti. 

![Snímek obrazovky zobrazující dokumentaci k propustnosti disku v řadě M-Series](./media/performance-guidelines-best-practices/m-series-table.png)

Podobně vidíte, že Standard_M32ts podporuje 20 000 neuložené diskové vstupně-výstupní operace a 500 MB/s s nevyrovnávací propustností disku. Tento limit se řídí na úrovni virtuálního počítače bez ohledu na základní diskové úložiště Premium.

Další informace najdete v tématu [omezení neuložených do mezipaměti a v mezipaměti](../../../virtual-machines/linux/disk-performance-linux.md#virtual-machine-uncached-vs-cached-limits).


### <a name="cached-and-temp-storage-throughput"></a>Propustnost mezipaměti a dočasné úložiště

Maximální propustnost úložiště v mezipaměti a dočasné úložiště je samostatný limit od neuloženého limitu propustnosti virtuálního počítače. Azure BlobCache se skládá z kombinace paměti s náhodným přístupem hostitele virtuálního počítače a místně připojeného disku SSD. Dočasná jednotka ( `D:\` jednotka) v rámci virtuálního počítače je také hostována v této místní jednotce SSD.

Maximální propustnost úložiště v mezipaměti a dočasné úložiště řídí vstupně-výstupní operace s místní dočasnou jednotkou ( `D:\` jednotkou) a Azure BlobCache **pouze v případě** , že je povoleno ukládání do mezipaměti hostitele. 

Pokud je ukládání do mezipaměti povolené v Premium Storage, můžou se virtuální počítače škálovat nad rámec omezení vzdáleného úložiště, které neukládá do mezipaměti vstupně-výstupních operací a omezení propustnosti.  

Pouze některé virtuální počítače podporují úložiště Premium Storage i Storage úrovně Premium (což je nutné ověřit v dokumentaci k virtuálnímu počítači). Například dokumentace k [řadě M-Series](../../../virtual-machines/m-series.md) indikuje, že podporuje úložiště Premium Storage i Storage úrovně Premium: 

![Snímek obrazovky, který ukazuje podporu Premium Storage řady M-Series](./media/performance-guidelines-best-practices/m-series-table-premium-support.png)

Omezení mezipaměti se budou lišit v závislosti na velikosti virtuálního počítače. Například virtuální počítač Standard_M8ms podporuje 10000 z paměti IOPS disku a propustnost disku 1000 MB/s v mezipaměti s celkovou velikostí mezipaměti 793 GiB. Podobně Standard_M32ts virtuální počítač podporuje 40000 z paměti IOPS disku a propustnost disku 400 MB/s v mezipaměti s celkovou velikostí mezipaměti 3174 GiB. 

![Snímek obrazovky znázorňující dokumentaci k propustnosti disku v mezipaměti řady M-Series.](./media/performance-guidelines-best-practices/m-series-table-cached-temp.png)

V existujícím virtuálním počítači můžete ručně povolit ukládání do mezipaměti hostitele. Před provedením jakýchkoli změn v zásadách ukládání do mezipaměti virtuálního počítače zastavte všechny úlohy aplikací a SQL Server služby. Změna nastavení mezipaměti virtuálního počítače způsobí, že se cílový disk odpojí a znovu připojí po použití nastavení.

### <a name="data-file-caching-policies"></a>Zásady ukládání datových souborů do mezipaměti

Zásady ukládání do mezipaměti úložiště se liší v závislosti na typu SQL Server datových souborů, které jsou hostovány na disku. 

Následující tabulka poskytuje souhrn doporučených zásad ukládání do mezipaměti na základě typu SQL Server dat: 

|SQL Server disk |Doporučení |
|---------|---------|
| **Datový disk** | Povolte `Read-only` ukládání do mezipaměti pro disky hostující SQL Server datových souborů. <br/> Čtení z mezipaměti bude rychlejší než neuložené čtení z datového disku. <br/> Neuložený počet vstupně-výstupních operací a propustnosti a propustnosti a propustnosti zanášejí celkový možný výkon dostupný z virtuálního počítače v rámci omezení virtuálních počítačů, ale skutečný výkon se bude lišit v závislosti na schopnosti úlohy používat mezipaměť (poměr přístupů do mezipaměti). <br/>|
|**Disk protokolu transakcí**|Nastavte zásady ukládání do mezipaměti `None` pro disky hostující transakční protokol.  Není k dispozici žádný přínos pro povolení ukládání do mezipaměti pro disk protokolu transakcí a ve skutečnosti, že buď `Read-only` nebo `Read/Write` ukládání do mezipaměti je povoleno na jednotce protokolu může snížit výkon zápisů na jednotku a snížit množství mezipaměti dostupné pro čtení na datové jednotce.  |
|**Disk s operačním systémem** | Výchozí zásada ukládání do mezipaměti by mohla být `Read-only` nebo `Read/write` pro jednotku operačního systému. <br/> Nedoporučuje se měnit úroveň ukládání do mezipaměti jednotky operačního systému.  |
| **tempdb**| Pokud se databáze tempdb nedá umístit na dočasný disk `D:\` z důvodu kapacity, změňte velikost virtuálního počítače tak, aby získal větší dočasný disk, nebo umístěte databázi tempdb na samostatnou datovou jednotku s `Read-only` nakonfigurovaným ukládáním do mezipaměti. <br/> Mezipaměť virtuálního počítače a dopředná jednotka používá místní jednotku SSD, takže mějte na paměti, že při změně velikosti jako databáze tempdb se počítá I/O, a to při hostování na dočasné jednotce.| 
| | | 


Další informace najdete v tématu [ukládání disku do mezipaměti](../../../virtual-machines/premium-storage-performance.md#disk-caching). 


## <a name="disk-striping"></a>Diskové svazky

Analyzujte propustnost a šířku pásma požadovanou pro datové soubory SQL, abyste zjistili počet datových disků, včetně souboru protokolu a databáze tempdb. Omezení propustnosti a šířky pásma se liší podle velikosti virtuálního počítače. Další informace najdete v tématu o [velikosti virtuálních počítačů](../../../virtual-machines/sizes.md) .

Přidejte další datové disky a použijte diskové svazky pro další propustnost. Například aplikace, která potřebuje 12 000 IOPS a 180 MB/s, může použít tři prokládané disky P30 k zajištění propustnosti 15 000 a 600 MB/s. 

Pokud chcete konfigurovat diskové svazky, přečtěte si téma [disking](storage-configuration.md#disk-striping). 

## <a name="disk-capping"></a>Disk capping 

Na úrovni disku i virtuálního počítače jsou omezení propustnosti. Omezení maximálního počtu IOPS na virtuální počítač a na disk se liší a jsou vzájemně nezávislá.

Aplikace, které spotřebovávají prostředky nad rámec těchto limitů, budou omezené (označované také jako omezené). Vyberte virtuální počítač a velikost disku v diskovém proužku, který splňuje požadavky aplikace a nebude capping omezení. Pro řešení capping použijte ukládání do mezipaměti nebo ladění aplikace, aby se vyžadovala menší propustnost.

Například aplikace, která potřebuje 12 000 IOPS a 180 MB/s, může: 
- Použijte [Standard_M32ms](../../../virtual-machines/m-series.md) s maximální propustností disku, která není v mezipaměti, 20 000 IOPS a 500 MB/s.
- Prodávejte tři P30 disky pro zajištění propustnosti 15 000 IOPS a 600 MB/s.
- Použijte [Standard_M16ms](../../../virtual-machines/m-series.md) virtuální počítač a používejte ukládání do mezipaměti hostitele k využití místní mezipaměti při využívání propustnosti. 

Virtuální počítače nakonfigurované pro horizontální navýšení kapacity v době vysokého využití by měly zřídit úložiště s dostatečným IOPS a propustností pro podporu maximální velikosti virtuálního počítače a přitom udržet celkový počet disků menší nebo roven maximálnímu počtu, který je určený pro použití v nejmenší.

Další informace o omezeních capping disku a používání ukládání do mezipaměti pro předcházení capping najdete v tématu [disk v/v capping](../../../virtual-machines/disks-performance.md).

> [!NOTE] 
> U některých capping disků může stále docházet k dostatečnému výkonu pro uživatele. Optimalizujte a udržujte úlohy místo změny velikosti na větší virtuální počítač a vyrovnávat náklady a výkon pro firmu. 


## <a name="write-acceleration"></a>Akcelerace zápisu

Akcelerace zápisu je funkce disku, která je dostupná jenom pro Virtual Machines [řady M-Series](https://docs.microsoft.com/azure/virtual-machines/m-series) . Účelem akcelerace zápisu je vylepšit latenci vstupu a výstupu zápisu na Azure Premium Storage, pokud potřebujete s vysokým počtem důležitých OLTP úloh nebo prostředí datového skladu. 

Pomocí akcelerace zápisu Vylepšete latenci zápisu na jednotku hostující soubory protokolů. Nepoužívejte akceleraci zápisu pro SQL Server datových souborů. 

Akcelerátor zápisu disky sdílí stejný limit IOPS jako virtuální počítač. Připojené disky nemůžou překročit limit Akcelerátor zápisu IOPS pro virtuální počítač.  

Následující tabulka popisuje počet datových disků a IOPS podporovaných na virtuální počítač: 

| Skladová položka virtuálního počítače  | Počet disků Akcelerátor zápisu  | Akcelerátor zápisu vstupně-výstupních operací na virtuální počítač  |
|---|---|---|
| M416ms_v2 M416s_v2  | 16  | 20000  |
| M128ms, M128s  | 16  | 20000  |
| M208ms_v2 M208s_v2  | 8  | 10000  |
| M64ms, M64ls, M64s  |  8 | 10000 |
| M32ms, M32ls, M32ts, M32s  | 4  | 5000  |
| M16ms, M16s  | 2 | 2500 |
| M8ms, M8s  | 1 | 1250 |

K dispozici je několik omezení pro použití akcelerace zápisu. Další informace najdete v tématu [omezení při použití akcelerátor zápisu](../../../virtual-machines/how-to-enable-write-accelerator.md#restrictions-when-using-write-accelerator).


### <a name="comparing-to-azure-ultra-disk"></a>Porovnání s Azure Ultra diskem

Největší rozdíl mezi akcelerací zápisu a disky Azure Ultra je, že zrychlení zápisu je funkce virtuálního počítače, která je dostupná jenom pro řady M-Series a Azure Ultra disks je možnost úložiště. Akcelerace zápisu je mezipaměť optimalizovaná pro zápis s vlastními omezeními na základě velikosti virtuálního počítače. Azure Ultra disks je možnost úložného prostoru na disku s nízkou latencí pro Azure Virtual Machines. 

Pokud je to možné, používejte pro disk transakčního protokolu akceleraci zápisu na disky Ultra. Pro virtuální počítače, které nepodporují akceleraci zápisu, ale vyžadují nízkou latenci v transakčním protokolu, použijte Azure Ultra disks. 

## <a name="monitor-storage-performance"></a>Monitorování výkonu úložiště

K vyhodnocení potřeb úložiště a určení toho, jak dobře je úložiště prováděno, je nutné pochopit, co je třeba změřit a jaké mají tyto indikátory význam. 

[IOPS (vstup/výstup za sekundu)](../../../virtual-machines/premium-storage-performance.md#iops) je počet požadavků, které aplikace vytváří do úložiště za sekundu. Měření IOPS pomocí čítačů sledování výkonu `Disk Reads/sec` a `Disk Writes/sec` . [OLTP (online zpracování transakcí)](/azure/architecture/data-guide/relational-data/online-transaction-processing) potřebuje k dosažení optimálního výkonu zvýšit počet vstupně-výstupních operací. Mezi příklady aplikací OLTP jsou všechny aplikace, jako jsou systémy zpracování plateb, Online nákupy a maloobchodní prodejní systémy.

[Propustnost](../../../virtual-machines/premium-storage-performance.md#throughput) je objem dat odesílaných do základního úložiště, který se často měří podle megabajtů za sekundu. Měření propustnosti pomocí čítačů sledování výkonu `Disk Read Bytes/sec` a `Disk Write Bytes/sec` . [Datové sklady](/azure/architecture/data-guide/relational-data/data-warehousing) jsou optimalizované k maximalizaci propustnosti během IOPS. Mezi příklady aplikací datového skladu patří aplikace jako úložiště dat pro účely analýzy, generování sestav, ETL workstreams a další cíle business intelligence.

Velikosti vstupně-výstupních jednotek ovlivňují možnosti IOPS a propustnosti jako menší vstupně-výstupní velikosti a větší počet vstupně-výstupních velikostí je vyšší propustnost. SQL Server automaticky zvolí optimální velikost vstupně-výstupních operací. Další informace o najdete v tématu [optimalizace IOPS, propustnosti a latence pro vaše aplikace](../../../virtual-machines/premium-storage-performance.md#optimize-iops-throughput-and-latency-at-a-glance). 

K dispozici jsou konkrétní Azure Monitor metriky, které jsou nevýznamné pro zjišťování capping na úrovni virtuálního počítače a disku a také pro spotřebu a stav mezipaměti Azureblobu. Pokud chcete identifikovat klíčové čítače, které se mají přidat do řešení pro monitorování a Azure Portal řídicím panelu, přečtěte si téma [metriky využití úložiště](../../../virtual-machines/disks-metrics.md#storage-io-utilization-metrics). 

> [!NOTE]
> Azure Monitor aktuálně nenabízí metriky na úrovni disku pro dočasný dočasný disk `(D:\)` . Procento vstupně-výstupních operací IOPS pro virtuální počítače spotřebované procento a procento spotřebované šířky pásma v mezipaměti budou odrážet IOPS a propustnost z dočasné dočasné jednotky `(D:\)` i do ukládání do mezipaměti hostitele společně.


## <a name="next-steps"></a>Další kroky

Další informace o osvědčených postupech pro výkon najdete v dalších článcích v této sérii:
- [Rychlý kontrolní seznam](performance-guidelines-best-practices-checklist.md)
- [Velikost virtuálního počítače](performance-guidelines-best-practices-vm-size.md)
- [Shromáždit směrný plán](performance-guidelines-best-practices-collect-baseline.md)

Osvědčené postupy zabezpečení najdete v tématu [požadavky na zabezpečení pro SQL Server v Azure Virtual Machines](security-considerations-best-practices.md).

Podrobné testování výkonu SQL Server na virtuálních počítačích Azure pomocí srovnávacích testů TPC-E a TPC_C najdete v blogu věnovaném [optimalizaci výkonu OLTP](https://techcommunity.microsoft.com/t5/sql-server/optimize-oltp-performance-with-sql-server-on-azure-vm/ba-p/916794).

Další informace o SQL Server článcích o virtuálních počítačích najdete v článku [SQL Server na Azure Virtual Machines přehled](sql-server-on-azure-vm-iaas-what-is-overview.md). Pokud máte dotazy k virtuálním počítačům s SQL Serverem, přečtěte si [Nejčastější dotazy](frequently-asked-questions-faq.md).
