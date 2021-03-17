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
ms.openlocfilehash: 82b4c127f983f3133326bf7fb538e40713ef9655
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100580373"
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

