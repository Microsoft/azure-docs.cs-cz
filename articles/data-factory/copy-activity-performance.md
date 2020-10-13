---
title: Průvodce škálovatelností a výkonem aktivity kopírování
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
ms.date: 09/15/2020
ms.openlocfilehash: a6e89883ec0e1e493bad9572876af86f4a0d3853
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324427"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Průvodce škálovatelností a výkonem aktivity kopírování

> [!div class="op_single_selector" title1="Vyberte verzi Azure Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-copy-activity-performance.md)
> * [Aktuální verze](copy-activity-performance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Někdy budete chtít provést rozsáhlou migraci dat ze služby Data Lake nebo Enterprise Data Warehouse (podnikového) do Azure. Další časy, kdy chcete ingestovat velké objemy dat z různých zdrojů do Azure, pro analýzu velkých objemů dat. V každém případě je důležité dosáhnout optimálního výkonu a škálovatelnosti.

Azure Data Factory (ADF) poskytuje mechanismus pro ingestování dat. ADF má následující výhody:

* Zpracovává velké objemy dat.
* Je vysoce výkonná
* Je nákladově efektivní

Tyto výhody usnadňují vytváření ADF pro odborníky na data, kteří chtějí vytvářet škálovatelné kanály příjmu dat, které jsou vysoce výkonné.

Po přečtení tohoto článku budete moci zodpovědět následující otázky:

* Jakou úroveň výkonu a škálovatelnosti je možné dosáhnout použitím aktivity kopírování ADF pro scénáře migrace dat a přijímání dat?
* Jak mám provést optimalizaci výkonu aktivity kopírování ADF?
* Jaké knoflíky optimalizace výkonu ADF mohu využít k optimalizaci výkonu pro jednu spuštění aktivity kopírování?
* Jaké další faktory mimo ADF jsou při optimalizaci výkonu kopírování vhodné?

> [!NOTE]
> Pokud nejste obeznámeni s aktivitou kopírování obecně, přečtěte si článek [Přehled aktivity kopírování](copy-activity-overview.md) .

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>Kopírování výkonu a škálovatelnosti dosažitelné pomocí ADF

ADF nabízí architekturu bez serveru, která umožňuje paralelismus na různých úrovních.

Tato architektura umožňuje vyvíjet kanály, které maximalizují propustnost přesunu dat pro vaše prostředí. Tyto kanály plně využívají tyto prostředky:

* Šířka pásma sítě
* Vstupně-výstupní operace úložiště za sekundu (IOPS) a šířka pásma

Toto úplné využití znamená, že můžete odhadnout celkovou propustnost pomocí měření minimální dostupné propustnosti s následujícími prostředky:

* Zdrojové úložiště dat
* Cílové úložiště dat
* Šířka pásma sítě mezi zdrojovým a cílovým úložištěm dat

Doba kopírování je vypočítána v následující tabulce. Doba trvání vychází z velikosti dat a limitu šířky pásma pro vaše prostředí.

&nbsp;

| Velikost dat/ <br/> připojení | 50 Mb/s    | 100 Mb/s  | 500 Mb/s  | 1 Gb/s   | 5 Gb/s   | 10 Gb/s  | 50 GB/s   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2,7 min.    | 1,4 min.   | 0,3 min.   | 0,1 min.  | 0,03 min. | 0,01 min. | 0,0 min.   |
| **10 GB**                   | 27,3 min.   | 13,7 min.  | 2,7 min.   | 1,3 min.  | 0,3 min.  | 0,1 min.  | 0,03 min.  |
| **100 GB**                  | 4,6 hod.    | 2,3 hod.   | 0,5 hod.   | 0,2 hod.  | 0,05 hod. | 0,02 hod. | 0,0 hod.   |
| **1 TB**                    | 46,6 hod.   | 23,3 hod.  | 4,7 hod.   | 2,3 hod.  | 0,5 hod.  | 0,2 hod.  | 0,05 hod.  |
| **10 TB**                   | 19,4 dní  | 9,7 dní  | 1,9 dní  | 0,9 dní | 0,2 dní | 0,1 dní | 0,02 dní |
| **100 TB**                  | 194,2 dní | 97,1 dní | 19,4 dní | 9,7 dní | 1,9 dní | 1 den    | 0,2 dní  |
| **1 PB**                    | 64,7. mo    | 32,4. mo   | 6,5. mo    | 3,2. mo   | 0,6. mo   | 0,3. mo   | 0,06. mo   |
| **10 PB**                   | 647,3. mo   | 323,6. mo  | 64,7. mo   | 31,6. mo  | 6,5. mo   | 3,2. mo   | 0,6. mo    |
| | |  | | |  | | |

Kopie ADF je škálovatelná na různých úrovních:

![Jak kopíruje ADF škálování](media/copy-activity-performance/adf-copy-scalability.png)

* Tok řízení ADF může spustit více aktivit kopírování paralelně, například pomocí [pro každou smyčku](control-flow-for-each-activity.md).

* Jedna aktivita kopírování může využít výhod škálovatelných výpočetních prostředků.
  * Pokud používáte Azure Integration runtime (IR), můžete pro každou aktivitu kopírování, a to bez serveru, zadat [až 256 jednotek pro integraci dat (DIUs)](#data-integration-units) .
  * Při použití prostředí IR v místním prostředí můžete použít některý z následujících přístupů:
    * Ruční horizontální navýšení kapacity počítače
    * Horizontální navýšení kapacity na více počítačů ([až 4 uzly](create-self-hosted-integration-runtime.md#high-availability-and-scalability)) a jedna aktivita kopírování bude rozdělit svou sadu souborů na všechny uzly.

* Jedna aktivita kopírování čte z a zapisuje do úložiště dat pomocí [paralelního](#parallel-copy)více vláken.

## <a name="performance-tuning-steps"></a>Postup ladění výkonu

Provedením následujících kroků vyoptimalizujete výkon služby Azure Data Factory s aktivitou kopírování:

1. **Vyberte testovací datovou sadu a vytvořte směrný plán.**

    Během vývoje otestujte svůj kanál pomocí aktivity kopírování v reprezentativní ukázce dat. Datová sada, kterou zvolíte, by měla představovat typické vzorce dat spolu s následujícími atributy:

    * Struktura složek
    * Vzor souboru
    * Schéma dat

    A vaše datová sada by měla být dostatečně velká pro vyhodnocení výkonu kopírování. Dobrá velikost trvá pro dokončení aktivity kopírování alespoň 10 minut. Shromažďování podrobností o spuštění a charakteristik výkonu po [monitorování aktivity kopírování](copy-activity-monitoring.md)

2. **Jak maximalizovat výkon jedné aktivity kopírování**:

    Doporučujeme nejprve maximalizovat výkon pomocí jedné aktivity kopírování.

    * **Pokud je aktivita kopírování prováděna v prostředí _Azure_ Integration Runtime:**

        Začněte s výchozími hodnotami pro [jednotky integrace dat (diú)](#data-integration-units) a nastavení [paralelního kopírování](#parallel-copy) .

    * **Pokud je aktivita kopírování prováděna v místním prostředí _Integration_ Runtime:**

        Pro hostování prostředí IR doporučujeme použít vyhrazený počítač. Počítač by měl být oddělený od serveru, který je hostitelem úložiště dat. Začněte s výchozími hodnotami pro nastavení [paralelního kopírování](#parallel-copy) a použijte jeden uzel pro místní prostředí IR.

    Proveďte test testu výkonnosti. Vezměte na vědomí dosažený výkon. Zahrňte skutečné použité hodnoty, například DIUs a paralelní kopie. Informace o tom, jak shromažďovat výsledky spuštění a nastavení výkonu, najdete v tématu [monitorování aktivit kopírování](copy-activity-monitoring.md) . Naučte se [řešit potíže s výkonem aktivity kopírování](copy-activity-performance-troubleshooting.md) a identifikovat a vyřešit jejich kritické potíže.

    Iterace k provedení dalšího testu výkonu se spustí podle pokynů pro řešení potíží a ladění. Po spuštění jedné aktivity kopírování nemůžete dosáhnout lepší propustnosti, zvažte, jestli chcete maximalizovat agregovanou propustnost spuštěním více kopií současně. Tato možnost je popsána v následující číslované odrážce.

3. **Jak maximalizovat agregovanou propustnost spuštěním více kopií současně:**

    Teď jste maximalizovali výkon jedné aktivity kopírování. Pokud jste ještě nedosáhli horní meze propustnosti vašeho prostředí, můžete spustit více aktivit kopírování paralelně. Můžete spustit paralelně pomocí konstrukcí toku řízení ADF. Jedna taková konstrukce je [pro každou smyčku](control-flow-for-each-activity.md). Další informace najdete v následujících článcích o šablonách řešení:

    * [Kopírování souborů z několika kontejnerů](solution-template-copy-files-multiple-containers.md)
    * [Migrace dat ze služby Amazon S3 do ADLS Gen2](solution-template-migration-s3-azure.md)
    * [Hromadné kopírování s řídicí tabulkou](solution-template-bulk-copy-with-control-table.md)

4. **Rozšiřte konfiguraci na celou datovou sadu.**

    Až budete spokojeni s výsledky a výkonem spuštění, můžete rozšířit definici a kanál tak, aby pokryly celou datovou sadu.

## <a name="troubleshoot-copy-activity-performance"></a>Řešení potíží s výkonem aktivity kopírování

Použijte [Postup ladění výkonu](#performance-tuning-steps) k naplánování a provádění testu výkonnosti pro váš scénář. A Naučte se řešit potíže s výkonem všech aktivit kopírování v Azure Data Factory v tématu [řešení potíží s výkonem aktivity kopírování](copy-activity-performance-troubleshooting.md).

## <a name="copy-performance-optimization-features"></a>Kopírovat funkce optimalizace výkonu

Azure Data Factory poskytuje následující funkce optimalizace výkonu:

* [Jednotky integrace dat](#data-integration-units)
* [Škálovatelnost prostředí Integration runtime v místním prostředí](#self-hosted-integration-runtime-scalability)
* [Paralelní kopírování](#parallel-copy)
* [Připravené kopírování](#staged-copy)

### <a name="data-integration-units"></a>Jednotky integrace dat

Jednotka pro integraci dat (DIÚ) je míra, která představuje výkon jedné jednotky v Azure Data Factory. Výkon je kombinací procesoru, paměti a přidělení síťových prostředků. DIÚ se vztahuje pouze na [prostředí Azure Integration runtime](concepts-integration-runtime.md#azure-integration-runtime). DIÚ se nevztahuje na místní [prostředí Integration runtime](concepts-integration-runtime.md#self-hosted-integration-runtime). [Další informace najdete tady](copy-activity-performance-features.md#data-integration-units).

### <a name="self-hosted-integration-runtime-scalability"></a>Škálovatelnost prostředí Integration runtime v místním prostředí

Možná budete chtít hostovat rostoucí souběžné zatížení. Nebo možná budete chtít dosáhnout vyššího výkonu na úrovni vaší stávající úlohy. Škálování zpracování můžete zvýšit pomocí následujících přístupů:

* Můžete navýšit _horizontální prostředí IR na_ dálku tím, že zvýšíte počet [souběžných úloh](create-self-hosted-integration-runtime.md#scale-up) , které se můžou spouštět na uzlu.  
Horizontální navýšení kapacity funguje pouze v případě, že je procesor a paměť uzlu méně, než je plně využito.
* V místním prostředí IR _můžete horizontální_ navýšení kapacity přidáním dalších uzlů (počítačů).

Další informace naleznete v tématech:

* [Funkce optimalizace výkonu aktivity kopírování: škálovatelnost v místním prostředí Integration runtime](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability)
* [Vytvoření a konfigurace prostředí Integration runtime v místním prostředí: požadavky na škálování](create-self-hosted-integration-runtime.md#scale-considerations)

### <a name="parallel-copy"></a>Paralelní kopírování

Vlastnost můžete nastavit `parallelCopies` tak, aby označovala paralelismus, které má aktivita kopírování použít. Tuto vlastnost si můžete představit jako maximální počet vláken v rámci aktivity kopírování. Vlákna pracují paralelně. Vlákna buď čtou ze zdroje, nebo zapisují do úložišť dat jímky. [Další informace](copy-activity-performance-features.md#parallel-copy).

### <a name="staged-copy"></a>Připravené kopírování

Operace kopírování dat může odesílat data _přímo_ do úložiště dat jímky. Alternativně můžete zvolit, že chcete jako _dočasné pracovní_ úložiště používat úložiště objektů BLOB. [Další informace](copy-activity-performance-features.md#staged-copy).

## <a name="next-steps"></a>Další kroky

Další články o aktivitě kopírování najdete v článcích:

* [Přehled aktivit kopírování](copy-activity-overview.md)
* [Řešení potíží s výkonem aktivity kopírování](copy-activity-performance-troubleshooting.md)
* [Funkce optimalizace výkonu aktivity kopírování](copy-activity-performance-features.md)
* [Použití Azure Data Factory k migraci dat ze služby Data Lake nebo datového skladu do Azure](data-migration-guidance-overview.md)
* [Migrace dat z Amazonu S3 do Azure Storage](data-migration-guidance-s3-azure-storage.md)
