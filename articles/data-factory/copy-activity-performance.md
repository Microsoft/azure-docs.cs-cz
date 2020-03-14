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
ms.date: 03/11/2020
ms.openlocfilehash: 231b0d77dc441e70dc0ec8de313291bb6b4f9292
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79261395"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Průvodce škálovatelností a výkonem aktivity kopírování

> [!div class="op_single_selector" title1="Vyberte verzi Azure Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-copy-activity-performance.md)
> * [Aktuální verze](copy-activity-performance.md)

Bez ohledu na to, jestli chcete provést rozsáhlou migraci dat ze služby Data Lake nebo Enterprise Data Warehouse (podnikového) do Azure nebo chcete ingestovat data ve velkém měřítku z různých zdrojů do Azure pro analýzu velkých objemů dat, je důležité dosáhnout optimálního výkonu a škálovatelnost.  Azure Data Factory poskytuje výkonné, odolné a nákladově efektivní mechanismy pro ingestování dat ve velkém měřítku, díky čemuž je vhodné, aby technici pro data dokázali vytvářet vysoce výkonné a škálovatelné kanály příjmu dat.

Po přečtení tohoto článku, budou moci odpovědět na následující otázky:

- Jakou úroveň výkonu a škálovatelnosti je možné dosáhnout použitím aktivity kopírování ADF pro scénáře migrace dat a přijímání dat?

- Jak mám provést optimalizaci výkonu aktivity kopírování ADF?
- Jaké knoflíky optimalizace výkonu ADF mohu využít k optimalizaci výkonu pro jednu spuštění aktivity kopírování?
- Jaké další faktory mimo ADF jsou při optimalizaci výkonu kopírování vhodné?

> [!NOTE]
> Pokud nejste obeznámeni s aktivitou kopírování obecně, přečtěte si článek [Přehled aktivity kopírování](copy-activity-overview.md) .

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>Kopírování výkonu a škálovatelnosti dosažitelné pomocí ADF

ADF nabízí architekturu bez serveru, která umožňuje paralelismus na různých úrovních, což vývojářům umožňuje vytvářet kanály pro plné využití šířky pásma sítě a vstupně-výstupních operací úložiště a šířky pásma pro maximalizaci propustnosti přesunu dat pro vaše prostředí.  To znamená, že propustnost, kterou můžete dosáhnout, lze odhadnout měřením minimální propustnosti nabízených zdrojovým úložištěm dat, cílovým úložištěm dat a šířky pásma sítě mezi zdrojovým a cílovým serverem.  V následující tabulce je vypočítána doba kopírování na základě velikosti dat a limitu šířky pásma pro vaše prostředí. 

| Velikost dat/ <br/> připojení | 50 Mb/s    | 100 Mb/s  | 500 Mb/s  | 1 Gbps   | 5 Gb/s   | 10 Gb/s  | 50 GB/s   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2,7 min.    | 1,4 min.   | 0,3 min.   | 0,1 min.  | 0,03 min. | 0,01 min. | 0,0 min.   |
| **10 GB**                   | 27,3 min.   | 13,7 min.  | 2,7 min.   | 1,3 min.  | 0,3 min.  | 0,1 min.  | 0,03 min.  |
| **100 GB**                  | 4,6 hod.    | 2,3 hod.   | 0,5 hod.   | 0,2 hod.  | 0,05 hod. | 0,02 hod. | 0,0 hod.   |
| **1 TB**                    | 46,6 hod.   | 23,3 hod.  | 4,7 hod.   | 2,3 hod.  | 0,5 hod.  | 0,2 hod.  | 0,05 hod.  |
| **10 TB**                   | 19,4 dní  | 9,7 dní  | 1,9 dní  | 0,9 dní | 0,2 dní | 0,1 dní | 0,02 dní |
| **100 TB**                  | 194,2 dní | 97,1 dní | 19,4 dní | 9,7 dní | 1,9 dní | 1 den    | 0,2 dní  |
| **1 PB**                    | 64,7. mo    | 32,4. mo   | 6,5. mo    | 3,2. mo   | 0,6. mo   | 0,3. mo   | 0,06. mo   |
| **10 PB**                   | 647,3. mo   | 323,6. mo  | 64,7. mo   | 31,6. mo  | 6,5. mo   | 3,2. mo   | 0,6. mo    |

Kopie ADF je škálovatelná na různých úrovních:

![Jak kopíruje ADF škálování](media/copy-activity-performance/adf-copy-scalability.png)

- Tok řízení ADF může spustit více aktivit kopírování paralelně, například pomocí [pro každou smyčku](control-flow-for-each-activity.md).
- Jediná aktivita kopírování může využít výhod škálovatelných výpočetních prostředků: při použití Azure Integration Runtime můžete pro každou aktivitu kopírování v rámci serveru zadat [až 256 DIUs](#data-integration-units) . Při použití Integration Runtime v místním prostředí můžete ručně navýšení kapacity počítače nebo horizontální navýšení kapacity na více počítačů ([až 4 uzly](create-self-hosted-integration-runtime.md#high-availability-and-scalability)) a jedna aktivita kopírování bude rozdělit svou sadu souborů napříč všemi uzly.
- Jedna aktivita kopírování čte z a zapisuje do úložiště dat pomocí [paralelního](#parallel-copy)více vláken.

## <a name="performance-tuning-steps"></a>Postup optimalizace výkonu

Provedením těchto kroků vyoptimalizujete výkon služby Azure Data Factory s aktivitou kopírování.

1. **Vyberte testovací datovou sadu a vytvořte směrný plán.** Během fáze vývoje otestujte svůj kanál pomocí aktivity kopírování v reprezentativní ukázce dat. Datová sada, kterou zvolíte, by měla představovat vaše typické vzorce dat (struktura složek, vzor souborů, schéma dat atd.) a je dostatečně velká pro vyhodnocení výkonu kopírování, například trvá 10 minut nebo i déle, než se aktivita kopírování dokončí. Shromažďování podrobností o spuštění a charakteristik výkonu po [monitorování aktivity kopírování](copy-activity-monitoring.md)

2. **Jak maximalizovat výkon jedné aktivity kopírování**:

   Aby bylo možné začít používat, doporučujeme nejprve maximalizovat výkon pomocí jedné aktivity kopírování.

   - **Pokud se aktivita kopírování provádí na Azure Integration Runtime:** začněte s výchozími hodnotami pro jednotky pro [integraci dat (diú)](#data-integration-units) a nastavení [paralelního kopírování](#parallel-copy) . 

   - **Pokud se aktivita kopírování provádí v Integration runtime v místním prostředí:** doporučujeme použít vyhrazený počítač oddělený od serveru, který hostuje úložiště dat, a hostovat prostředí Integration runtime. Začněte s výchozími hodnotami pro nastavení [paralelního kopírování](#parallel-copy) a použijte jeden uzel pro místní prostředí IR.  

   Proveďte testovací běh a poznamenejte si dosažený výkon a také skutečné hodnoty používané jako DIUs a paralelní kopie. Informace o tom, jak shromažďovat výsledky spuštění a nastavení výkonu, najdete v tématu [monitorování aktivit kopírování](copy-activity-monitoring.md) a Naučte se [řešit problémy s výkonem aktivity kopírování](copy-activity-performance-troubleshooting.md) a vyřešit jejich kritické řešení. 

   Iterace k provedení dalšího testu výkonu se spustí podle pokynů pro řešení potíží a ladění. Po spuštění jedné aktivity kopírování nemůžete dosáhnout lepší propustnosti. zvažte možnost maximalizace agregované propustnosti spuštěním několika kopií současně odkazujících na krok 3.


3. **Jak maximalizovat agregovanou propustnost spuštěním více kopií současně:**

   Teď, když jste dosáhli maximálního výkonu jedné aktivity kopírování, pokud jste ještě nedosáhli horní meze propustnosti vašeho prostředí – síť, zdrojové úložiště dat a cílové úložiště dat – můžete spouštět více aktivit kopírování paralelně pomocí konstrukcí toku řízení ADF, jako je například [pro každou smyčku](control-flow-for-each-activity.md). Přečtěte si téma [kopírování souborů z více kontejnerů](solution-template-copy-files-multiple-containers.md), [migrace dat ze služby Amazon S3 do adls Gen2](solution-template-migration-s3-azure.md)nebo [hromadné kopírování pomocí šablon řešení tabulky ovládacích prvků](solution-template-bulk-copy-with-control-table.md) jako obecné příklad.

5. **Rozšiřte konfiguraci na celou datovou sadu.** Až budete spokojeni s výsledky a výkonem spuštění, můžete rozšířit definici a kanál tak, aby pokryly celou datovou sadu.

## <a name="troubleshoot-copy-activity-performance"></a>Řešení potíží s výkonem aktivity kopírování

Použijte [Postup ladění výkonu](#performance-tuning-steps) k naplánování a provádění testu výkonnosti pro váš scénář. A Naučte se řešit potíže s výkonem všech aktivit kopírování v Azure Data Factory v tématu [řešení potíží s výkonem aktivity kopírování](copy-activity-performance-troubleshooting.md).

## <a name="copy-performance-optimization-features"></a>Kopírovat funkce optimalizace výkonu

Azure Data Factory poskytuje následující funkce optimalizace výkonu:

- [Jednotky integrace dat](#data-integration-units)
- [Škálovatelnost prostředí Integration runtime v místním prostředí](#self-hosted-integration-runtime-scalability)
- [Paralelní kopírování](#parallel-copy)
- [Připravené kopírování](#staged-copy)

### <a name="data-integration-units"></a>Jednotky pro integraci dat

Jednotka Integration data je míra, která představuje napájení (kombinace procesoru, paměti a přidělení síťových prostředků) jedné jednotky v Azure Data Factory. Jednotka pro integraci dat platí jenom pro [prostředí Azure Integration runtime](concepts-integration-runtime.md#azure-integration-runtime), ale ne pro místní [prostředí Integration runtime](concepts-integration-runtime.md#self-hosted-integration-runtime). [Další informace](copy-activity-performance-features.md#data-integration-units).

### <a name="self-hosted-integration-runtime-scalability"></a>Škálovatelnost prostředí Integration runtime v místním prostředí

Chcete-li hostovat rostoucí souběžné zatížení nebo dosáhnout vyššího výkonu, můžete navýšení nebo horizontální navýšení kapacity Integration Runtime v místním prostředí. [Další informace](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability).

### <a name="parallel-copy"></a>Paralelní kopírování

Paralelní kopírování můžete nastavit tak, aby označovala paralelismus, které má aktivita kopírování použít. Tuto vlastnost si můžete představit jako maximální počet vláken v rámci aktivity kopírování, která je načítána ze zdroje nebo zapisuje do úložiště dat jímky paralelně. [Další informace](copy-activity-performance-features.md#parallel-copy).

### <a name="staged-copy"></a>Kopírování dvoufázové instalace

Při kopírování dat ze zdrojového úložiště dat do úložiště dat jímky, můžete zvolit použití Blob storage jako dočasné pracovní úložiště. [Další informace](copy-activity-performance-features.md#staged-copy).

## <a name="next-steps"></a>Další kroky
Další články o aktivitě kopírování najdete v článcích:

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Řešení potíží s výkonem aktivity kopírování](copy-activity-performance-troubleshooting.md)
- [Funkce optimalizace výkonu aktivity kopírování](copy-activity-performance-features.md)
- [Použití Azure Data Factory k migraci dat ze služby Data Lake nebo datového skladu do Azure](data-migration-guidance-overview.md)
- [Migrace dat ze služby Amazon S3 do Azure Storage](data-migration-guidance-s3-azure-storage.md)
