---
title: Kopírovat průvodce výkonem a škálovatelností aktivit
description: Přečtěte si o klíčových faktorech, které ovlivňují výkon přesunu dat v Azure Data Factory při použití aktivity kopírování.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/11/2020
ms.openlocfilehash: aedb3df69821d1436b03b2eb1f12873b624d426e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414175"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Kopírovat průvodce výkonem a škálovatelností aktivit

> [!div class="op_single_selector" title1="Vyberte verzi Azure Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-copy-activity-performance.md)
> * [Aktuální verze](copy-activity-performance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ať už chcete provést rozsáhlou migraci dat z datového jezera nebo podnikového datového skladu (EDW) do Azure, nebo chcete ingestovat data ve velkém měřítku do Azure pro analýzu velkých objemů dat, je důležité dosáhnout optimálního výkonu a škálovatelnosti.  Azure Data Factory poskytuje výkonný, odolný a nákladově efektivní mechanismus pro ingestování dat ve velkém měřítku, takže je skvělý pro datové inženýry, kteří chtějí vytvářet vysoce výkonné a škálovatelné kanály pro ingestování dat.

Po přečtení tohoto článku budete moci odpovědět na následující otázky:

- Jaké úrovně výkonu a škálovatelnosti lze dosáhnout pomocí aktivity kopírování adf pro scénáře migrace dat a při jejich ingestování?

- Jaké kroky je třeba provést k vyladění výkonu aktivity kopírování podavačů ADF?
- Jaké optimalizační knoflíky ADF perf mohu využít k optimalizaci výkonu pro spuštění aktivity jedné kopie?
- Jaké další faktory mimo adf vzít v úvahu při optimalizaci výkonu kopírování?

> [!NOTE]
> Pokud nejste obeznámeni s aktivitou kopírování obecně, podívejte se na [přehled aktivity kopírování](copy-activity-overview.md) před přečtením tohoto článku.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>Kopírování výkonu a škálovatelnosti dosažitelné pomocí adf

ADF nabízí architekturu bez serveru, která umožňuje paralelismus na různých úrovních, což umožňuje vývojářům vytvářet kanály pro plné využití šířky pásma sítě, stejně jako úložiště VOPS a šířku pásma pro maximalizaci propustnost pohybu dat pro vaše prostředí.  To znamená, že propustnost, kterou můžete dosáhnout, lze odhadnout měřením minimální propustnosti nabízené zdrojovým úložištěm dat, cílovým úložištěm dat a šířkou pásma sítě mezi zdrojem a cílem.  Níže uvedená tabulka vypočítá dobu trvání kopírování na základě velikosti dat a omezení šířky pásma pro vaše prostředí. 

| Velikost dat / <br/> Šířky pásma | 50 Mb/s    | 100 Mb/s  | 500 Mb/s  | 1 Gb/s   | 5 Gb/s   | 10 Gb/s  | 50 Gb/s   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2,7 min    | 1,4 min   | 0,3 min   | 0,1 min  | 0,03 min | 0,01 min | 0,0 min   |
| **10 GB**                   | 27,3 min   | 13,7 min  | 2,7 min   | 1,3 min  | 0,3 min  | 0,1 min  | 0,03 min  |
| **100 GB**                  | 4,6 hod.    | 2,3 hod.   | 0,5 hod.   | 0,2 hod.  | 0.05 hod. | 0.02 hod. | 0,0 hod.   |
| **1 TB**                    | 46,6 hod.   | 23,3 hod.  | 4,7 hod.   | 2,3 hod.  | 0,5 hod.  | 0,2 hod.  | 0.05 hod.  |
| **10 TB**                   | 19,4 dne  | 9,7 dne  | 1,9 dne  | 0,9 dne | 0,2 dne | 0,1 dne | 0,02 dne |
| **100 TB**                  | 194,2 dne | 97,1 dní | 19,4 dne | 9,7 dne | 1,9 dne | 1 den    | 0,2 dne  |
| **1 PB**                    | 64,7 mo    | 32,4 mo   | 6,5 mo    | 3,2 mo   | 0,6 mo   | 0,3 mo   | 0,06 mo   |
| **10 PB**                   | 647,3 mo   | 323,6 mo  | 64,7 mo   | 31,6 mo  | 6,5 mo   | 3,2 mo   | 0,6 mo    |

Kopie adf je škálovatelná na různých úrovních:

![jak se škáluje kopírování ADF](media/copy-activity-performance/adf-copy-scalability.png)

- Tok řízení ADF může spustit více aktivit kopírování paralelně, například pomocí [pro každou smyčku](control-flow-for-each-activity.md).
- Aktivita jedné kopie může využívat škálovatelné výpočetní prostředky: při použití prostředí Azure Integration Runtime můžete zadat [až 256 DIU](#data-integration-units) pro každou aktivitu kopírování bez serveru. při použití prostředí Integration Runtime s vlastním hostitelem můžete ručně vertikálně vertikálně navýšit kapacitu počítače nebo vertikálně navýšit kapacitu na více počítačů[(až 4 uzly)](create-self-hosted-integration-runtime.md#high-availability-and-scalability)a jedna aktivita kopírování rozdělí jeho soubor nastavený na všechny uzly.
- Jedna kopie aktivity čte z a zapisuje do úložiště dat pomocí více vláken [paralelně](#parallel-copy).

## <a name="performance-tuning-steps"></a>Kroky ladění výkonu

Pomocí těchto kroků můžete optimalizovat výkon služby Azure Data Factory aktivitou kopírování.

1. **Vyzvednout testovací datovou sadu a vytvořit směrný plán.** Během fáze vývoje otestujte kanál pomocí aktivity kopírování proti reprezentativnímu vzorku dat. Vybraná datová sada by měla představovat typické datové vzory (struktura složek, vzorek souboru, schéma dat a tak dále) a je dostatečně velká pro vyhodnocení výkonu kopírování, například trvá 10 minut nebo déle, než se zkopíruje aktivita kopírování. Shromažďovat podrobnosti o spuštění a charakteristiky výkonu po [sledování aktivity kopírování](copy-activity-monitoring.md).

2. **Jak maximalizovat výkon jedné kopie aktivity**:

   Chcete-li začít s, doporučujeme nejprve maximalizovat výkon pomocí jedné aktivity kopírování.

   - **Pokud se aktivita kopírování spouští v prostředí Runtime integrace Azure:** začněte s výchozími hodnotami pro [jednotky integrace dat (DIU)](#data-integration-units) a nastavení [paralelní kopie.](#parallel-copy) 

   - **Pokud se aktivita kopírování spouští v modulu Runtime integrace s vlastním hostitelem:** doporučujeme použít vyhrazený počítač oddělený od serveru hostujícího úložiště dat k hostování modulu runtime integrace. Začněte s výchozími hodnotami pro nastavení [paralelního kopírování](#parallel-copy) a použití jednoho uzlu pro samoobslužnou infračervený přenos.  

   Proveďte spuštění testu výkonu a poznamenejte si dosažený výkon a skutečné hodnoty použité jako DIU a paralelní kopie. Podívejte se na [sledování aktivity kopírování](copy-activity-monitoring.md) o tom, jak shromažďovat výsledky spuštění a nastavení výkonu a přečtěte si, jak [řešit výkon aktivity kopírování](copy-activity-performance-troubleshooting.md) k identifikaci a vyřešení kritického místa. 

   Iterate provést další spuštění testu výkonu po řešení potíží a ladění pokyny. Jakmile spuštění aktivity jedné kopie nemůže dosáhnout lepší propustnosti, zvažte maximalizovat agregační propustnost spuštěním více kopií současně odkazujících na krok 3.


3. **Jak maximalizovat agregační propustnost spuštěním více kopií současně:**

   Nyní, když jste maximalizovali výkon jedné aktivity kopírování, pokud jste ještě nedosáhli horních limitů propustnosti vašeho prostředí – sítě, úložiště zdrojových dat a cílového úložiště dat – můžete spustit více aktivit kopírování paralelně pomocí konstrukcí toku řízení ADF, například [Pro každou smyčku](control-flow-for-each-activity.md). Viz [Kopírovat soubory z více kontejnerů](solution-template-copy-files-multiple-containers.md), [migrovat data z Amazon S3 na ADLS Gen2](solution-template-migration-s3-azure.md)nebo [hromadné kopírování se šablonami](solution-template-bulk-copy-with-control-table.md) řešení ovládací tabulky jako obecný příklad.

5. **Rozbalte konfiguraci na celou datovou sadu.** Když jste spokojeni s výsledky spuštění a výkon, můžete rozšířit definici a kanál pokrýt celou datovou sadu.

## <a name="troubleshoot-copy-activity-performance"></a>Poradce při potížích s výkonem aktivity kopírování

Postupujte podle [kroků optimalizace výkonu](#performance-tuning-steps) a naplánujte a proveďte test výkonu pro váš scénář. A zjistěte, jak řešit problém s výkonem jednotlivých aktivit kopírování v Azure Data Factory z [poradce při potížích s výkonem aktivity kopírování](copy-activity-performance-troubleshooting.md).

## <a name="copy-performance-optimization-features"></a>Kopírování funkcí optimalizace výkonu

Azure Data Factory poskytuje následující funkce optimalizace výkonu:

- [Jednotky integrace dat](#data-integration-units)
- [Škálovatelnost prostředí runtime integrace s vlastním hostitelem](#self-hosted-integration-runtime-scalability)
- [Paralelní kopírování](#parallel-copy)
- [Fázovaná kopie](#staged-copy)

### <a name="data-integration-units"></a>Jednotky integrace dat

Jednotka integrace dat je míra, která představuje výkon (kombinace procesoru, paměti a přidělení síťových prostředků) jedné jednotky v Azure Data Factory. Jednotka integrace dat se vztahuje pouze na [prostředí runtime integrace Azure](concepts-integration-runtime.md#azure-integration-runtime), ale ne na prostředí [runtime integrace s vlastním hostitelem](concepts-integration-runtime.md#self-hosted-integration-runtime). [Další informace](copy-activity-performance-features.md#data-integration-units).

### <a name="self-hosted-integration-runtime-scalability"></a>Škálovatelnost prostředí runtime integrace s vlastním hostitelem

Chcete-li hostovat rostoucí souběžné úlohy nebo dosáhnout vyššího výkonu, můžete vertikálně navýšit kapacitu nebo vertikálně navýšit kapacitu prostředí integrace hostované ho za vlastního nastavení. [Další informace](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability).

### <a name="parallel-copy"></a>Paralelní kopírování

Můžete nastavit paralelní kopii označující paralelismus, který má být aktivita kopírování používána. Tuto vlastnost si můžete myslet jako maximální počet podprocesů v rámci aktivity kopírování, které číst ze zdroje nebo zapisovat do úložiště dat jímky paralelně. [Další informace](copy-activity-performance-features.md#parallel-copy).

### <a name="staged-copy"></a>Fázovaná kopie

Když zkopírujete data ze zdrojového úložiště dat do úložiště dat jímky, můžete použít úložiště objektů blob jako dočasné pracovní úložiště. [Další informace](copy-activity-performance-features.md#staged-copy).

## <a name="next-steps"></a>Další kroky
Podívejte se na další články o aktivitách kopírování:

- [Kopírovat přehled aktivit](copy-activity-overview.md)
- [Poradce při potížích s výkonem aktivity kopírování](copy-activity-performance-troubleshooting.md)
- [Kopírování funkcí optimalizace výkonu aktivity](copy-activity-performance-features.md)
- [Migrace dat z datového jezera nebo datového skladu do Azure pomocí Azure Data Factory](data-migration-guidance-overview.md)
- [Migrace dat z Amazonu S3 do Azure Storage](data-migration-guidance-s3-azure-storage.md)
