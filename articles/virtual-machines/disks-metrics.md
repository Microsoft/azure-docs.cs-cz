---
title: Metriky disku
description: Příklady metrik shlukování disku
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 60486c41ad843cf193ee0648dfcfef66f7668e47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101674985"
---
# <a name="disk-performance-metrics"></a>Metriky výkonu disku
Azure nabízí v Azure Portal metriky, které poskytují přehled o tom, jak vaše virtuální počítače a disky provádějí. Metriky lze také načíst prostřednictvím volání rozhraní API. Tento článek je rozdělen do 3 pododdílů:

- **Metrika v/v disku, propustnost a hloubka fronty** – tyto metriky umožňují zobrazit výkon úložiště z perspektivy disku a virtuálního počítače.
- **Metriky shlukování disku** – jedná se o metriky, které poskytují pozor na [naše](disk-bursting.md) špičkové disky.
- **Metriky využití v/v úložiště** – tyto metriky vám pomůžou diagnostikovat kritické body výkonu úložiště s využitím disků. 

Všechny metriky se vysílají každou minutu, s výjimkou metriky procentuální hodnoty kreditu, která je vygenerována každých 5 minut.

## <a name="disk-io-throughput-and-queue-depth-metrics"></a>Metrika v/v disku, propustnost a hloubka fronty
K dispozici jsou následující metriky, které vám pomohou získat přehled o výkonu virtuálních počítačů a vstupně-výstupních operací disku a výkonu.

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

## <a name="bursting-metrics"></a>Metriky shlukování
Následující metriky vám pomůžou s pozorovatelskou funkcí [na našich](disk-bursting.md) prémiových disků:

- **Maximální šířka pásma shluku dat pro datový disk**: limit propustnosti, po který mohou datové disky zvýšit zatížení.
- **Maximální šířka pásma shluku disku s operačním systémem**: limit propustnosti, po který se disk s operačním systémem může rozstavit.
- **Maximální počet shlukových vstupně**-výstupních operací datového disku: IOPS omezuje, aby se mohly zvýšit zátěž datových disků.
- **Maximální počet shlukových vstupně-výstupních operací disku pro operační systém**: limit IOPS, na který se může disk s operačním systémem zvýšit
- **Cílová šířka pásma datového disku**: limit propustnosti, který může disk s daty dosáhnout bez roztržení.
- **Šířka pásma cíle disku s operačním systémem**: limit propustnosti, který disk s operačním systémem může dosáhnout bez roztržení.
- **Cílové datové disky**– počet IOPS: limit IOPS, který může datový disk dosáhnout bez roztržení.
- **Cílový disk s operačním systémem**: maximální počet IOPS, které mohou datové disky dosáhnout bez roztržení.
- **Procento využitých kreditů za sekundu datového disku**: akumulované procento shluku propustnosti využitého pro datový disk (y). Vygenerováno v intervalu 5 minut.
- **Procento kreditů využitých v bitech za sekundu na disk s operačním systémem**: akumulované procento shluku propustnosti využitého pro disk s operačním systémem. Vygenerováno v intervalu 5 minut.
- **Procento kreditů využití vstupně-výstupních operací pro datový disk**: akumulované procento shluku IOPS použitého pro datové disky. Vygenerováno v intervalu 5 minut.
- **Procento kreditů využití disku s operačním systémem**: akumulované procento shluku IOPS využitého pro disk s operačním systémem. Vygenerováno v intervalu 5 minut.

## <a name="storage-io-utilization-metrics"></a>Metriky využití v/v úložiště
Následující metriky vám pomůžou diagnostikovat kritická místa v kombinaci virtuálních počítačů a disků. Tyto metriky jsou dostupné jenom při použití virtuálního počítače s povolenou úrovní Premium. Tyto metriky jsou k dispozici pro všechny typy disků s výjimkou Ultra. 

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

## <a name="storage-io-metrics-example"></a>Příklad metrik v/v úložiště

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

Pojďme spustit test srovnávacích testů na tomto virtuálním počítači a kombinaci disků, která vytváří vstupně-výstupní činnost. Informace o tom, jak srovnávací službu Storage v Azure, najdete v tématu [srovnávací testy vaší aplikace na Azure Disk Storage](disks-benchmarks.md). V nástroji pro srovnávací testy vidíte, že kombinace virtuálních počítačů a disků může dosahovat 22 800 IOPS:

![Snímek obrazovky s výstupem f i o zobrazuje zvýrazněný r = 22.8 k.](media/disks-metrics/utilization-metrics-example/fio-output.jpg)



Standard_D8s_v3 může dosáhnout celkem 28 600 IOPS. Pomocí metriky si podívejme na to, co se prochází, a Identifikujte naše kritické body v/v úložiště. V levém podokně vyberte **metriky**:

![Snímek obrazovky znázorňující metriky zvýrazněné v levém podokně](media/disks-metrics/utilization-metrics-example/metrics-menu.jpg)

Nejdřív se podíváme na naši procentuální metriku **využité procentní hodnoty IOPS virtuálního počítače v mezipaměti** :

![Snímek obrazovky zobrazující procento spotřebované V M v/v v mezipaměti](media/disks-metrics/utilization-metrics-example/vm-cached.jpg)

Tato metrika oznamuje, že se používá 61% z 16 000 IOPS přidělených do mezipaměti IOPS na virtuálním počítači. Toto procento znamená, že Nekritická místa v/v úložiště nejsou s disky, které jsou uložené v mezipaměti, protože nejsou v 100%. Teď se podíváme na naše procento metriky **neuloženého počtu vstupně** -výstupních operací IOPS:

![Snímek obrazovky zobrazující procento využité V M neukládání do vyrovnávací paměti V/v](media/disks-metrics/utilization-metrics-example/vm-uncached.jpg)

Tato metrika je 100%. Oznamujeme, že se používají všechny 12 800 vstupně-výstupních operací přidělených pro neuložené IOPS virtuálních počítačů na virtuálním počítači. Jedním ze způsobů, jak tento problém vyřešit, je změnit velikost našeho virtuálního počítače na větší velikost, která může zpracovávat další vstupně-výstupní operace. Než to ale uděláte, Podívejme se na připojený disk, kde zjistíte, kolik IOPS se zobrazuje. Podívejte se na disk s operačním systémem. Prohlédněte si **spotřebované procento IOPS disku s operačním systémem**:

![Snímek obrazovky ukazující procento spotřebovaného disku v/v](media/disks-metrics/utilization-metrics-example/os-disk.jpg)

Tato metrika oznamuje, že se používá přibližně 90% za 5 000 IOPS zřízené pro tento disk s P30 operačním systémem. Toto procento znamená, že na disku s operačním systémem není žádné kritické body. Teď Prohlédněte data, která jsou připojená k virtuálnímu počítači, a Prohlédněte si **spotřebované procento počtu vstupně**-výstupních operací pro datový disk:

![Snímek obrazovky zobrazující procento spotřebovaného datového disku v hodnotě P S](media/disks-metrics/utilization-metrics-example/data-disks-no-splitting.jpg)

Tato metrika nám oznamuje, že procento spotřebovaného počtu vstupně-výstupních operací na všech připojených discích je okolo 42%. Toto procento se počítá na základě IOPS, které disky používají, a není obsluhován z mezipaměti hostitele. Pojďme do této metriky přiblížit použitím *rozdělení* na tyto metriky a rozdělením podle hodnoty logické jednotky (LUN):

![Snímek obrazovky znázorňující spotřebované procento datových disků v hodnotě P s dělením](media/disks-metrics/utilization-metrics-example/data-disks-splitting.jpg)

Tato metrika nám oznamuje, že datové disky připojené k logickým jednotkám 3 a 2 využívají přibližně 85% jejich zřízené IOPS. Tady je diagram toho, co v/v funguje z architektury virtuálních počítačů a disků:

![Příklad metriky úložiště – metriky v systému](media/disks-metrics/utilization-metrics-example/metrics-diagram.jpg)

## <a name="next-steps"></a>Další kroky

- [Přehled metrik Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md)
- [Vysvětlení agregace metrik](../azure-monitor/essentials/metrics-aggregation-explained.md)
- [Vytváření, zobrazení a správa upozornění na metriky pomocí služby Azure Monitor](../azure-monitor/alerts/alerts-metric.md)