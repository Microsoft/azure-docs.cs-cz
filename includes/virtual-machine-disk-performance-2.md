---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 10/12/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 086ebf71e2da19a96433f32cfb1bae133e875400
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518048"
---
![Graf znázorňující specifikace D s v 3.](media/vm-disk-performance/dsv3-documentation.jpg)

- Maximální propustnost disku, která není v *mezipaměti* , je výchozím limitem úložiště, který může virtuální počítač zpracovat.
- Maximální propustnost úložiště *v mezipaměti* je samostatný limit, pokud povolíte ukládání hostitele do mezipaměti.

Ukládání do mezipaměti hostitele funguje tak, že se úložiště přiblíží k virtuálnímu počítači, který je možné zapsat nebo číst, a to rychle. Velikost úložiště, která je k dispozici pro virtuální počítač pro ukládání do mezipaměti hostitele, je v dokumentaci. Můžete například vidět, že Standard_D8s_v3 se dodává s 200 GiB úložiště mezipaměti.

Ukládání hostitelů do mezipaměti můžete povolit, když vytvoříte virtuální počítač a připojíte disky. V existujícím VIRTUÁLNÍm počítači můžete také zapnout a vypnout ukládání do mezipaměti hostitele na discích.

![Snímek obrazovky, který ukazuje ukládání hostitele do mezipaměti.](media/vm-disk-performance/host-caching.jpg)

Mezipaměť hostitele můžete upravit tak, aby odpovídala požadavkům na zatížení jednotlivých disků. Mezipaměť hostitele můžete nastavit tak, aby byla:

- **Jen pro čtení**: pro úlohy, které dělají pouze operace čtení
- **Čtení a zápis**: pro úlohy, které mají zůstatek operací čtení a zápisu

Pokud vaše úloha nedodržuje žádný z těchto vzorů, nedoporučujeme používat ukládání hostitelů do mezipaměti.

Podíváme se na několik příkladů různých nastavení mezipaměti hostitele, abyste viděli, jak ovlivňuje tok dat a výkon. V tomto prvním příkladu se podíváme na to, co se stane s požadavky na vstupně-výstupní operace, když je nastavení ukládání do mezipaměti hostitele nastaveno **jen pro čtení**.

**Obecného**

- Standard_D8s_v3
  - IOPS v mezipaměti: 16 000
  - Neuložené IOPS s mezipamětí: 12 800
- Datový disk P30
  - IOPS: 5 000
  - Ukládání hostitele do mezipaměti: **jen pro čtení**

Když se provede čtení a v mezipaměti jsou k dispozici požadovaná data, vrátí mezipaměť požadovaná data. Z disku není nutné číst. Tato čtení se započítávají do limitů uložených v mezipaměti virtuálního počítače.

![Diagram znázorňující, že se dosáhlo čtení z mezipaměti hostitele čtení.](media/vm-disk-performance/host-caching-read-hit.jpg)

Když je proveden čtení a požadovaná data *nejsou* v mezipaměti k dispozici, je požadavek na čtení přenesen na disk. Disk pak rozsvítí do mezipaměti i do virtuálního počítače. Tato čtení se započítávají do limitu neuloženého do mezipaměti virtuálního počítače a limitu pro ukládání do mezipaměti virtuálního počítače.

![Diagram znázorňující neúspěšný čtení z mezipaměti hostitele pro čtení](media/vm-disk-performance/host-caching-read-miss.jpg)

Po provedení zápisu musí být zápis zapisován do mezipaměti i do disku před tím, než je považován za úplný. Tento zápis se započítává do limitu neuloženého do mezipaměti virtuálního počítače a limitu pro ukládání do mezipaměti virtuálního počítače.

![Diagram znázorňující zápis do mezipaměti pro čtení hostitele.](media/vm-disk-performance/host-caching-write.jpg)

Teď se podívejme na to, co se stane s požadavky na vstupně-výstupní operace, když je nastavení mezipaměti hostitele nastaveno na **čtení/zápis**.

**Obecného**

- Standard_D8s_v3
  - IOPS v mezipaměti: 16 000
  - Neuložené IOPS s mezipamětí: 12 800
- Datový disk P30
  - IOPS: 5 000
  - Mezipaměť hostitele: **čtení i zápis**

Čtení je zpracováváno stejným způsobem jako jen pro čtení. Zápisy jsou pouze ta, která je odlišná s ukládáním do mezipaměti pro čtení a zápis. Při psaní do mezipaměti hosta je nastavená možnost **čtení/zápis**, je nutné zapsat pouze zápis do mezipaměti hostitele, aby bylo možné považovat za kompletní. Zápis pak laxně vytvářená zapsaný na disk jako proces na pozadí. To znamená, že zápis se započítá do mezipaměti v mezipaměti při zápisu do mezipaměti. Když se laxně vytvářená zapisuje na disk, napočítá se do neuloženého vstupu do mezipaměti.

![Diagram znázorňující zápis do mezipaměti hostitele pro čtení a zápis](media/vm-disk-performance/host-caching-read-write.jpg)

Pojďme pokračovat s naším Standard_D8s_v3m virtuálním počítačem. S výjimkou této doby povolíte ukládání hostitelů do mezipaměti na discích. Také limit IOPS virtuálního počítače je teď 16 000 IOPS. Připojení k virtuálnímu počítači mají tři základní disky P30, které mohou každý popisovač 5 000 IOPS.

**Obecného**

- Standard_D8s_v3
  - IOPS v mezipaměti: 16 000
  - Neuložené IOPS s mezipamětí: 12 800
- Disk s operačním systémem P30
  - IOPS: 5 000
  - Mezipaměť hostitele: **čtení i zápis**
- Dva datové disky P30 × 2
  - IOPS: 5 000
  - Mezipaměť hostitele: **čtení i zápis**

![Diagram znázorňující příklad mezipaměti hostitele.](media/vm-disk-performance/host-caching-example-without-remote.jpg)

Aplikace používá virtuální počítač Standard_D8s_v3 s povoleným ukládáním do mezipaměti. Vytvoří žádost o 15 000 IOPS. Požadavky jsou u každého připojeného disku rozdělené jako 5 000 IOPS. Nedochází k capping výkonu.

## <a name="combined-uncached-and-cached-limits"></a>Kombinované omezení neuložených do mezipaměti a mezipaměti

Limity uložené v mezipaměti virtuálního počítače jsou oddělené od neuložených omezení. To znamená, že můžete povolit ukládání do mezipaměti hostitele na discích připojených k virtuálnímu počítači a přitom Nepovolit ukládání do mezipaměti hostitele na jiných discích. Tato konfigurace umožňuje vašim virtuálním počítačům získat celkový počet vstupně-výstupních operací úložiště pro limit uložený v mezipaměti a limit neuloženého v mezipaměti.

Podívejme se na příklad, který vám pomůže pochopit, jak tato omezení pracují dohromady. Budeme pokračovat s konfigurací připojení k virtuálnímu počítači s Standard_D8s_v3 a disky Premium.

**Obecného**

- Standard_D8s_v3
  - IOPS v mezipaměti: 16 000
  - Neuložené IOPS s mezipamětí: 12 800
- Disk s operačním systémem P30
  - IOPS: 5 000
  - Mezipaměť hostitele: **čtení i zápis**
- Dva datové disky P30 × 2
  - IOPS: 5 000
  - Mezipaměť hostitele: **čtení i zápis**
- Dva datové disky P30 × 2
  - IOPS: 5 000
  - Ukládání hostitele do mezipaměti: **zakázáno**

![Diagram znázorňující příklad mezipaměti hostitele se vzdáleným úložištěm.](media/vm-disk-performance/host-caching-example-with-remote.jpg)

V takovém případě aplikace spuštěná ve virtuálním počítači s Standard_D8s_v3 vytvoří požadavek na 25 000 IOPS. Požadavek se u každého připojeného disku rozděluje jako 5 000 IOPS. Tři disky používají ukládání hostitelů do mezipaměti a dva disky nepoužívají ukládání hostitelů do mezipaměti.

- Vzhledem k tomu, že tři disky, které používají ukládání hostitelů do mezipaměti, jsou v rámci limitů v mezipaměti 16 000, tyto požadavky byly úspěšně dokončeny. Nedochází k capping výkonu úložiště.
- Vzhledem k tomu, že dva disky, které nepoužívají ukládání hostitelů do mezipaměti, jsou v rámci limitů neukládaných do mezipaměti 12 800, tyto požadavky jsou také úspěšně dokončeny. Nedošlo k žádnému capping.

## <a name="disk-performance-metrics"></a>Metriky výkonu disku

Máme metriky v Azure, které vám poskytnou přehled o tom, jak vaše virtuální počítače a disky provádějí. Tyto metriky lze zobrazit pomocí Azure Portal. Lze je také načíst prostřednictvím volání rozhraní API. Metriky se počítají v intervalu jedné minuty. K dispozici jsou následující metriky, které vám pomohou získat informace o virtuálním počítači a vstupně-výstupních discích a také propustnosti:

- **Hloubka fronty disku s operačním systémem**: Počet aktuálních nezpracovaných vstupně-výstupních požadavků, které čekají na čtení nebo zapisování na disk s operačním systémem.
- **Bajty přečtené z disku s operačním systémem/s**: počet bajtů čtených za sekundu z disku s operačním systémem.
- **Operace čtení disku s operačním systémem/s**: počet vstupních operací, které se z disku s operačním systémem čtou za sekundu.
- **Bajty zápisu na disk s operačním systémem/s**: počet bajtů zapsaných za sekundu z disku s operačním systémem.
- **Operace zápisu na disk s operačním systémem/s**: Počet výstupních operací zapsaných za sekundu z disku s operačním systémem.
- **Hloubka fronty datových disků**: Počet aktuálních nezpracovaných vstupně-výstupních požadavků, které čekají na čtení nebo zapisování na datové disky.
- **Bajty přečtené z datového disku/s**: počet bajtů čtených za sekundu z datových disků.
- **Operace čtení z datového disku/s**: počet vstupních operací, které jsou z datových disků čteny za sekundu.
- Bajty zapsané na **datový disk/s**: počet bajtů zapsaných za sekundu z datových disků.
- **Operace zápisu na datový disk/s**: Počet výstupních operací zapsaných za sekundu z datových disků.
- **Bajty čtení z disku/s**: celkový počet bajtů čtených za sekundu ze všech disků připojených k virtuálnímu počítači.
- **Operace čtení z disku/s**: počet vstupních operací, které se za sekundu čtou ze všech disků připojených k virtuálnímu počítači.
- **Bajty zápisu na disk/s**: počet bajtů zapsaných za sekundu ze všech disků připojených k virtuálnímu počítači.
- **Operace zápisu na disk/s**: Počet výstupních operací zapsaných za sekundu ze všech disků připojených k virtuálnímu počítači.

## <a name="storage-io-utilization-metrics"></a>Metriky využití v/v úložiště

Metriky, které vám pomůžou diagnostikovat disk v/v capping:

- **Procento využitého počtu vstupně**-výstupních operací pro datový disk: procento vypočítané IOPS datového disku bylo dokončeno v případě IOPS zřízeného datového disku. Pokud je tato hodnota na 100%, je vaše aplikace spuštěná v/v omezené z limitu IOPS datového disku.
- **Procento využité šířky pásma dat**: procentuální hodnota vypočítaná propustností datového disku dokončenou při propustnosti zřízeného datového disku. Pokud je tato hodnota na 100%, je vaše aplikace spuštěná v/v omezené z limitu šířky pásma datového disku.
- **Procento využitého počtu vstupně-výstupních operací pro operační systém**: procento vypočítané IOPS disku operačního systému se dokončilo prostřednictvím zřízené IOPS disku operačního systému. Pokud je tato hodnota na 100%, je vaše aplikace spuštěná v/v omezené z limitu IOPS disku s operačním systémem.
- **Spotřebované procento šířky pásma disku s operačním systémem**: procentuální hodnota vypočítaná propustností disku s operačním systémem, která se dokončila prostřednictvím zřízené propustnosti disku s operačním systémem. Pokud je tato hodnota na 100%, je vaše aplikace spuštěná v/v omezené z limitu šířky pásma disku s operačním systémem.

Metriky, které vám pomůžou diagnostikovat virtuální počítač v/v capping:

- **Procento využitých vstupně**-výstupních operací IOPS virtuálních počítačů: procento vypočítané celkovým počtem IOPS dokončených přes maximální limit počtu IOPS virtuálního počítače v mezipaměti. Pokud je tato hodnota na 100%, je vaše aplikace spuštěná v/v omezené z limitu IOPS v mezipaměti virtuálního počítače.
- **Procento spotřebované šířky pásma v mezipaměti virtuálního počítače**: procento vypočítané celkovou propustností disku dokončenou při maximální propustnosti virtuálního počítače v mezipaměti. Pokud je tato hodnota na 100%, je vaše aplikace spuštěná v/v omezené z limitu šířky pásma v mezipaměti virtuálního počítače.
- **Procento využitých vstupně**-výstupních operací IOPS v mezipaměti: procento počítané celkovými IOPS na virtuálním počítači, které se dokončily v maximálním limitu počtu vstupně-výstupních operací virtuálních počítačů. Pokud je tato hodnota na 100%, je vaše aplikace spuštěná v/v omezené z limitu IOPS bez mezipaměti virtuálního počítače.
- **Procento spotřebované šířky pásma neuložené v mezipaměti**: procento vypočítané celkovou propustností disku na virtuálním počítači dokončené přes maximální propustnost zajištěných virtuálních počítačů. Pokud je tato hodnota na 100%, je vaše aplikace spuštěná v/v omezené z neuloženého limitu šířky pásma virtuálního počítače.

## <a name="storage-io-utilization-metrics-example"></a>Příklad metrik využití v/v úložiště

Podívejme se na příklad použití těchto nových metrik využití v/v úložiště, které nám pomůžou ladit, kde je v našem systému kritický bod. Nastavení systému je stejné jako v předchozím příkladu, s výjimkou toho, že připojený disk s operačním systémem *není* uložen v mezipaměti.

**Obecného**

- Standard_D8s_v3
  - IOPS v mezipaměti: 16 000
  - Neuložené IOPS s mezipamětí: 12 800
- Disk s operačním systémem P30
  - IOPS: 5 000
  - Ukládání hostitele do mezipaměti: **zakázáno**
- Dva datové disky P30 × 2
  - IOPS: 5 000
  - Mezipaměť hostitele: **čtení i zápis**
- Dva datové disky P30 × 2
  - IOPS: 5 000
  - Ukládání hostitele do mezipaměti: **zakázáno**
