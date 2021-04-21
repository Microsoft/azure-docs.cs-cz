---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 03/04/2021
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 4162fe12ff54f16cd5f982f6a576905227c9a107
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107820978"
---
## <a name="disk-level-bursting"></a>Roztržení na úrovni disku

### <a name="on-demand-bursting-preview"></a>Shlukování na vyžádání (Preview)

Disky, které používají model shlukování disku na vyžádání, můžou rozdělovat za původní zřízené cíle, jak je to často vyžaduje jejich zatížení až do maximálního počtu shluků. Například na TiB P30 disku je zřízené IOPS 5000 vstupně-výstupních operací. Když je na tomto disku povolené rozšíření disku, můžou vaše úlohy vystavit IOs na tento disk až do maximálního výkonu při nárůstu počtu 30 000 IOPS a 1 000 MB/s.

Pokud očekáváte, že úlohy budou často běžet nad rámec zřízeného cíle výkonu, bude shlukování disku nenákladově efektivní. V takovém případě doporučujeme místo toho změnit úroveň výkonu disku na [vyšší úroveň](../articles/virtual-machines/disks-performance-tiers.md) . získáte tak lepší základní výkon. Projděte si podrobnosti o fakturaci a vyhodnoťte je se vzorem přenosů vašich úloh.

Než povolíte shlukování na vyžádání, můžete pochopit následující:

[!INCLUDE [managed-disk-bursting-regions-limitations](managed-disk-bursting-regions-limitations.md)]

#### <a name="regional-availability"></a>Regionální dostupnost

[!INCLUDE [managed-disk-bursting-availability](managed-disk-bursting-availability.md)]

#### <a name="billing"></a>Fakturace

Disky používající model shlukování na vyžádání se účtují hodinovou zátěžovou zátěžovou sazbou a náklady na transakce se vztahují na všechny shlukové transakce nad rámec zřízeného cíle. Náklady na transakce se účtují s využitím modelu průběžných plateb na základě neuloženého disku IOs, včetně čtení a zápisu, které překračují zřízené cíle. Následuje příklad vzorů provozu na disku za fakturační hodinu:

Konfigurace disku: SSD úrovně Premium – 1 TiB (P30), povolený shlukování disku.

- 00:00:00 – 00:10:00 disk IOPS pod zřízeným cílem 5 000 IOPS 
- 00:10:01 – aplikace 00:10:10 vystavila úlohu Batch, která způsobila, že se disk IOPS rozpíná při 6 000 IOPS po dobu 10 sekund. 
- 00:10:11 – 00:59:00 disk IOPS pod zřízeným cílem 5 000 IOPS 
- 00:59:01 – 01:00:00 aplikace vystavila jinou úlohu služby Batch, která způsobila selhání disku za sekundu při 7 000 IOPS po dobu 60 sekund. 

V této fakturované hodině se náklady na roztržení skládají ze dvou poplatků:

První poplatek je paušální poplatek za povolení shluku $X (určený podle vaší oblasti). Tento paušální poplatek se vždycky účtuje na disk bez ohledu na stav připojení, dokud není zakázaný. 

Druhý je cena za transakci za sekundu. Při roztržení disku došlo ve dvou časových slotech. Z 00:10:01 – 00:10:10 je kumulovaná transakce shluku (6 000 – 5 000) X 10 = 10 000. Z 00:59:01 – 01:00:00 je kumulovaná transakce shluku (7 000 – 5 000) X 60 = 120 000. Celkový počet transakcí shluku je 10 000 + 120 000 = 130 000. Náklady na shlukové transakce se budou účtovat podle $Y na základě 13 jednotek 10 000 transakcí (na základě regionálních cen).

V takovém případě se celkové náklady na diskové shlukování této hodiny fakturace rovnají hodnotě $X + $Y. Stejný výpočet by se měl použít pro vytváření shluků přes zřízené cíle v MB/s. Převádíme nadlimitní využití MB na transakce s velikostí vstupně-výstupních operací 256KB. Pokud přenosy na disk překročí současně zřízené vstupně-výstupní operace za sekundu/s, můžete pro výpočet shlukových transakcí použít následující příklad. 

Konfigurace disku: SSD úrovně Premium – 1 TB (P30), povolený shlukování disku.

- 00:00:01 – 00:00:05 aplikace vystavila úlohu Batch, která způsobuje, že se vstupně-výstupní operace disku za sekundu po dobu 5 sekund zapříčiní 300 10 000 nárůstu počtu disků
- 00:00:06 – 00:00:10 aplikace vystavila úlohu obnovení, která způsobila, že se vstupně-výstupní operace disku rozpíná při 6 000 600 IOPS po dobu 5 sekund.

Transakce shlukového přenosu je účtována jako maximální počet transakcí ze zátěže IOPS nebo MB/s. Z 00:00:01 – 00:00:05 jsou kumulovaná transakce shluku Max ((10 000 – 5 000), (300-200) * 1024/256)) * 5 = 25 000 transakcí. Z 00:00:06 – 00:00:10 jsou kumulovaná transakce shluku Max ((6 000 – 5 000), (600-200) * 1024/256)) * 5 = 8 000 transakcí. Na to se zahrnou paušální poplatek za povolení shluku, který vám umožní získat celkové náklady na povolení shlukování disků na vyžádání. 

Podrobnosti o cenách a použití [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator/?service=storage) k vyhodnocení vašich úloh najdete na [stránce s cenami Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/) . 


Pokud chcete povolit shlukování na vyžádání, přečtěte si téma [Povolení shlukování na](../articles/virtual-machines/disks-enable-bursting.md)vyžádání.

### <a name="credit-based-bursting"></a>Shlukování na základě kreditu

Pro velikosti disků P20 a menší je k dispozici škálování na základě kreditu ve všech oblastech v cloudech veřejných, vládních a čínských Azure. Ve výchozím nastavení je pro všechna nová i existující nasazení podporovaných velikostí disků povolená možnost shlukování disku. Shlukování na úrovni virtuálních počítačů používá jenom kredity na základě kreditu.

## <a name="virtual-machine-level-bursting"></a>Virtuální počítač na úrovni virtuálních počítačů

Pro shlukování na úrovni virtuálních počítačů se používá jenom model založený na kreditech pro roztržení, který je ve výchozím nastavení povolený pro všechny virtuální počítače, které ho podporují.

Rozpínání na úrovni virtuálních počítačů je povolené ve všech oblastech ve veřejném cloudu Azure na těchto podporovaných velikostech: 
- [Řada Lsv2](../articles/virtual-machines/lsv2-series.md)
- [Řada Dv3 a DSv3](../articles/virtual-machines/dv3-dsv3-series.md)
- [Řada Ev3 a Esv3](../articles/virtual-machines/ev3-esv3-series.md)

## <a name="bursting-flow"></a>Tok shlukování

Systém navýšení zátěžového kreditu se vztahuje stejným způsobem na úrovni virtuálního počítače i na úrovni disku. Váš prostředek, ať už virtuální počítač nebo disk, začne s plně vyčerpanými kredity ve svém vlastním shlukovém intervalu. Tyto kredity vám umožňují navýšit na až 30 minut při maximálním počtu shlukových přenosů. Nashromáždíte kredity vždy, když se prostředek IOPS nebo MB/s prostředku využije pod cílem výkonu prostředku. Pokud váš prostředek narostl kredity a vaše zatížení potřebuje dodatečný výkon, váš prostředek může využít tyto kredity k tomu, aby dosáhl nad rámec jeho limitu a zvýšil jeho výkon, aby splnil požadavky na zatížení.

![Diagram shlukování.](media/managed-disks-bursting/bucket-diagram.jpg)

Způsob, jakým trávete dostupné kredity, je až vám. Po sobě jdoucí kredity nebo občas v průběhu celého dne můžete využít 30 minut roztržení. Při nasazení prostředků se dodávají s úplným přidělením kreditů. Pokud dojde k jejich vyčerpání, trvá to méně než jeden den, než se skladuje. Kredity si můžete vyřídit podle svého uvážení, ale pokud chcete prostředky rozlišit, nemusí být interval shlukování plný. Akumulace shluků se liší v závislosti na jednotlivých prostředcích, protože vychází z nevyužitých vstupně-výstupních operací a MB/s pod jejich cílovým výkonem. Vyšší základní prostředky výkonu mohou snižovat kredity na více zdrojů, a to rychleji než nižší směrné nároky na výkon. Například volnoběh disku P1 bude mít za sekundu 120 vstupně-výstupních operací za sekundu, zatímco disk volnoběhu P20 by znamenal 2 300 vstupně-výstupních operací za sekundu.

## <a name="bursting-states"></a>Stavy shlukování
Existují tři stavy, ve kterých může být prostředek zapnutý s povoleným roztržením:
- **Nabíhají** – provoz v/v prostředku používá méně než cíl výkonu. Nahromadění kreditů při roztržení za IOPS a MB/s se od sebe liší. Vaším prostředkem může být časově rozlišené kredity IOPS a útraty v MB/s a naopak.
- **Shluking** – provoz prostředku používá více než cíl výkonu. Shlukový přenos nezávisle spotřebovává kredity z IOPS nebo šířky pásma.
- **Konstanta** – provoz prostředku je přesně v cíli výkonu.

## <a name="bursting-examples"></a>Příklady shlukování

Následující příklady znázorňují způsob, jakým funguje shlukování s různými kombinacemi virtuálních počítačů a disků. Aby bylo možné tyto příklady snadno sledovat, zaměřte se na MB/s, ale stejná logika se použije nezávisle na IOPS.

### <a name="non-burstable-virtual-machine-with-burstable-disks"></a>Virtuální počítač bez zátěže s použitím diskových disků s více shluky
**Kombinace virtuálních počítačů a disků:** 
- Standard_D8as_v4 
    - Neuložené MB/s v mezipaměti: 192
- Disk s operačním systémem P4
    - Zřízené MB/s: 25
    - Maximální počet shluků MB/s: 170 
- 2 datové disky P10 
    - Zřízené MB/s: 100
    - Maximální počet shluků MB/s: 170

 Když se virtuální počítač spustí, načte data z disku s operačním systémem. Vzhledem k tomu, že disk s operačním systémem je součástí virtuálního počítače, který se spouští, bude disk s operačním systémem plný kredity. Tyto kredity umožní, aby se při spuštění disku s operačním systémem za sekundu 170 MB/s.

![Virtuální počítač odešle požadavek na propustnost 192 MB/s na disk s operačním systémem a disk s operačním systémem odpoví 170 MB/s dat.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-startup.jpg)

Po dokončení spuštění se aplikace na virtuálním počítači spustí a má nekritickou úlohu. Tato úloha vyžaduje 15 MB/S, která se rovnoměrně rozloží napříč všemi disky.

![Aplikace pošle požadavek na 15 MB/s propustnosti virtuálnímu počítači, virtuální počítač provede požadavek a pošle každému z nich požadavek na 5 MB/s. Každý disk vrátí 5 MB/s, virtuální počítač vrátí 15 MB/s do aplikace.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-idling.jpg)

Aplikace pak potřebuje zpracovat dávkovou úlohu, která vyžaduje 192 MB/s. disk s operačním systémem používá 2 MB/s a zbývající jsou rovnoměrně rozdělená mezi datovými disky.

![Aplikace odesílá požadavek na virtuální počítač z propustnosti 192 MB/s a pošle žádost do datových disků (95 MB/s každého) a 2 MB na disk s operačním systémem, datové disky, které splňují požadavky, a všechny disky vrátí požadovanou propustnost do virtuálního počítače, který je vrátí do aplikace.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-bursting.jpg)

### <a name="burstable-virtual-machine-with-non-burstable-disks"></a>Virtualizovaný virtuální počítač s disky, které nelze rozshlukovat
**Kombinace virtuálních počítačů a disků:** 
- Standard_L8s_v2 
    - Neuložené MB/s v mezipaměti: 160
    - Maximální počet shluků MB/s: 1 280
- Disk s operačním systémem P50
    - Zřízené MB/s: 250 
- 2 datové disky P10 
    - Zřízené MB/s: 250

 Po počátečním spuštění se aplikace na virtuálním počítači spustí a má nekritickou úlohu. Tato úloha vyžaduje 30 MB/s, která se rovnoměrně rozloží napříč všemi disky.
![Aplikace pošle požadavek na virtuální počítač o 30 MB/s a pošle požadavek na virtuální počítač a pošle každý z nich požadavek na 10 MB/s. Každý disk vrátí 10 MB/s, virtuální počítač vrátí 30 MB/s na aplikaci.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-normal.jpg)

Aplikace pak potřebuje zpracovat dávkovou úlohu, která vyžaduje 600 MB/s. Standard_L8s_v2 rozšíří, aby splňovala tuto poptávku, a požadavky na disky se rovnoměrně rozloží na P50 disky.

![Aplikace odesílá požadavek na virtuální počítač z propustnosti 600 MB/s, virtuální počítač bere v úvahu nárůst požadavků a každý z jeho disků pošle požadavek na 200 MB/s. Každý disk vrátí 200 MB/s, protože shluky virtuálních počítačů vrátí do aplikace 600 MB/s.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-bursting.jpg)
### <a name="burstable-virtual-machine-with-burstable-disks"></a>Virtuální počítač s více podsítěmi s roztržením disků
**Kombinace virtuálních počítačů a disků:** 
- Standard_L8s_v2 
    - Neuložené MB/s v mezipaměti: 160
    - Maximální počet shluků MB/s: 1 280
- Disk s operačním systémem P4
    - Zřízené MB/s: 25
    - Maximální počet shluků MB/s: 170 
- 2. datové disky P4 
    - Zřízené MB/s: 25
    - Maximální počet shluků MB/s: 170 

Když se virtuální počítač spustí, bude nárůst požadavků na jeho limit počtu 1 280 MB/s z disku s operačním systémem a disk s operačním systémem bude reagovat na výkon při jeho nárůstu na 170 MB/s.

![Při spuštění se virtuální počítač rozpíná za účelem odeslání žádosti o 1 280 MB/s na disk s operačním systémem, nárůsty disku s operačním systémem za účelem vrácení 1 280 MB/s.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-startup.jpg)

Po spuštění spustíte aplikaci, která má nekritickou úlohu. Tato aplikace vyžaduje 15 MB/s, která se rovnoměrně rozšíří napříč všemi disky.

![Aplikace pošle požadavek na 15 MB/s propustnosti virtuálnímu počítači, virtuální počítač provede požadavek a pošle každému z nich požadavek na 5 MB/s. Každý disk vrátí 5 MB/s odpovědí, virtuální počítač vrátí 15 MB/s na aplikaci.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-idling.jpg)

Aplikace pak potřebuje zpracovat dávkovou úlohu, která vyžaduje 360 MB/s. Standard_L8s_v2 se rozpíná, aby splňovala tuto poptávku, a pak požadavky. Disk s operačním systémem vyžaduje jenom 20 MB/s. Zbývajících 340 MB/s se zpracovává na základě shlukování datových disků P4.

![Aplikace odesílá požadavek na virtuální počítač z propustnosti 360 MB/s, virtuální počítač bere v úvahu nárůst požadavků a každý z nich odešle požadavek na 170 MB/s a 20 MB/s z disku s operačním systémem. Každý disk vrátí požadované MB/s, což počet nárůstů virtuálních počítačů vrátí do aplikace v počtu 360 MB/s.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-bursting.jpg)
