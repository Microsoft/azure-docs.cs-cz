---
title: Optimalizace zpracování dat
titleSuffix: Azure Machine Learning
description: Seznamte se s osvědčenými postupy pro optimalizaci rychlostí zpracování dat a o tom, jaké Integrace Azure Machine Learning podporuje pro zpracování dat ve velkém měřítku.
services: machine-learning
ms.service: machine-learning
ms.author: sgilley
author: sdgilley
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 06/26/2020
ms.custom: data4ml
ms.openlocfilehash: 5ab7bac635a0b670087800212727b0d2e2b96934
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103472208"
---
# <a name="optimize-data-processing-with-azure-machine-learning"></a>Optimalizujte zpracování dat pomocí Azure Machine Learning

V tomto článku se seznámíte s osvědčenými postupy, které vám pomůžou optimalizovat rychlost zpracování dat v místním prostředí a ve velkém měřítku.

Azure Machine Learning je integrován s open source balíčky a architekturami pro zpracování dat. Pomocí těchto integrací a použitím doporučení osvědčených postupů v tomto článku můžete zlepšit rychlost zpracování dat místně i ve velkém měřítku.

## <a name="parquet-and-csv-file-formats"></a>Formáty souborů Parquet a CSV

Soubory s hodnotami oddělenými čárkou (CSV) jsou běžné formáty souborů pro zpracování dat. Pro úlohy Machine Learning se ale doporučují formáty souborů Parquet.

[Soubory Parquet](https://parquet.apache.org/) ukládají data v binárním sloupcovém formátu. Tento formát je užitečný, pokud je potřeba rozdělit data do více souborů. Tento formát také umožňuje zaměřit se na příslušná pole experimentů ve službě Machine Learning. Místo toho, abyste museli číst v datovém souboru 20 GB, můžete toto zatížení snížit tak, že vyberete nezbytné sloupce pro vytvoření výuky modelu ML. Soubory Parquet je také možné zkomprimovat pro minimalizaci výkonu zpracování a zabírat méně místa.

Soubory CSV se běžně používají k importu a exportu dat, protože je lze snadno upravovat a číst v aplikaci Excel. Data v CSV se ukládají jako řetězce ve formátu založeném na řádcích a soubory je možné komprimovat, aby se mohly snížit zatížení přenosu dat. Uncompressed CSV může rozšířit o faktor o 2-10 a komprimovaná CSV může ještě víc zvýšit. Takže se 5 GB CSV v paměti rozbalí až do 8 GB paměti RAM, kterou máte v počítači. Toto chování komprese může zvýšit latenci přenosu dat, což není ideální, pokud máte velké množství dat ke zpracování. 

## <a name="pandas-dataframe"></a>PANDAS – datový rámec

[PANDAS](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) se běžně používají k manipulaci s daty a k analýze. `Pandas` funguje dobře pro velikost dat menší než 1 GB, ale doba zpracování pro datové `pandas` rámce se zpomalí, když velikost souboru dosáhne přibližně 1 GB. Tento zpomalení je způsobeno tím, že velikost dat v úložišti není stejná jako velikost dat v datovém rámečku. Například data v souborech CSV můžou v datovém rámci rozšířit až 10krát, takže soubor CSV o velikosti 1 GB se může v dataframe nastát 10 GB.

`Pandas` je jedním vláknem, což znamená, že operace se provádějí postupně na jednom procesoru. Můžete snadno paralelizovat úlohy na více virtuálních procesorů na jednom Azure Machine Learning výpočetní instanci s balíčky, jako je [Modin](https://modin.readthedocs.io/en/latest/) , které se zabalí `Pandas` pomocí distribuovaného back-endu.

Pokud chcete paralelizovat své úkoly pomocí `Modin` a [dAsK](https://dask.org), stačí změnit tento řádek kódu `import pandas as pd` na `import modin.pandas as pd` .

## <a name="dataframe-out-of-memory-error"></a>Datový rámec: Chyba při nedostatek paměti 

K *nedostatku paměti* obvykle dochází v případě, že se váš datový rámec rozšíří nad dostupnou paměť RAM na vašem počítači. Tento koncept platí také pro distribuované rozhraní, například `Modin` nebo `Dask` .  To znamená, že se vaše operace pokusí načíst datový rámec v paměti na každém uzlu v clusteru, ale k tomu není k dispozici dostatek paměti RAM.

Jedním z řešení je zvýšit velikost paměti RAM tak, aby odpovídala velikosti datového rámce v paměti. Doporučujeme, aby výpočet velikosti a výkon zpracování obsahoval dvojnásobek velikosti paměti RAM. Takže pokud je váš datový rámec 10 GB, použijte výpočetní cíl s aspoň 20 GB paměti RAM, abyste zajistili, že se může datový rámec pohodlně vejít do paměti a zpracovat. 

U více virtuálních procesorů vCPU Pamatujte na to, že chcete, aby se jeden oddíl pohodlně vešel do paměti RAM každý vCPU může mít v počítači. To znamená, že pokud máte 16 GB RAM 4 vCPU, budete chtít přibližně 2 GB datasnímků na každou vCPU.

### <a name="local-vs-remote"></a>Místní a vzdálené

Můžete si všimnout, že některé příkazy PANDAS dataframe fungují rychleji při práci na místním počítači a na vzdáleném VIRTUÁLNÍm počítači, který jste zřídili pomocí Azure Machine Learning. Váš místní počítač má obvykle povolený stránkovací soubor, který umožňuje načíst více, než co se vejde do fyzické paměti, což je váš pevný disk se používá jako rozšíření paměti RAM. V současné době se Azure Machine Learning virtuální počítače spouštějí bez stránkovacího souboru, takže můžou načíst jenom tolik dat, kolik je k dispozici fyzické paměti RAM. 

Pro úlohy náročné na výpočetní výkon doporučujeme vybrat větší virtuální počítač pro zlepšení rychlosti zpracování.

Přečtěte si další informace o [dostupných řadách a velikostech virtuálních počítačů](concept-compute-target.md#supported-vm-series-and-sizes) pro Azure Machine Learning. 

Specifikace paměti RAM najdete na odpovídajících stránkách řady virtuálních počítačů, jako jsou [Dv2-Dsv2 Series](../virtual-machines/dv2-dsv2-series-memory.md) nebo [řady NC](../virtual-machines/nc-series.md).

### <a name="minimize-cpu-workloads"></a>Minimalizace zatížení procesoru

Pokud nemůžete přidat k počítači více paměti RAM, můžete použít následující postupy, které vám pomůžou minimalizovat zatížení procesoru a optimalizovat časy zpracování. Tato doporučení se vztahují k jednomu i distribuovaným systémům.

Technika | Description
----|----
Komprese | Pro svá data použijte jiné reprezentace způsobem, který používá méně paměti a významně neovlivní výsledky vašeho výpočtu.<br><br>*Příklad:* Místo uložení položek jako řetězce s přibližně 10 bajtů nebo více za položku uložte je jako logická hodnota, true nebo false, kterou můžete uložit v 1 bajtu.
Vytváření bloků | Načtení dat do paměti v podmnožinách (bloků), zpracování dat v jednom podmnožině v čase nebo paralelní více dílčích sad. Tato metoda funguje nejlépe, pokud potřebujete zpracovat všechna data, ale není nutné načítat všechna data do paměti najednou. <br><br>*Příklad:* Místo zpracování dat v plném roce v jednom okamžiku načtěte a zpracujte data po měsících.
Indexování | Použijte a použijte Rejstřík, který vám ukáže, kde najít data, která vás zajímají. Indexování je užitečné, pokud potřebujete použít jenom podmnožinu dat, ne celou sadu.<br><br>*Příklad:* Pokud máte data o prodeji v plném roce seřazená podle měsíců, index vám pomůže rychle vyhledat požadovaný měsíc, který chcete zpracovat.

## <a name="scale-data-processing"></a>Škálování zpracování dat

Pokud předchozí doporučení nejsou dostatečná a nemůžete získat virtuální počítač, který odpovídá vašim datům, můžete 

* Použijte rozhraní jako `Spark` nebo `Dask` ke zpracování dat z paměti. V této možnosti je datový rámec načten do oddílu RAM podle oddílů a zpracován, přičemž konečný výsledek bude shromážděn na konci.  

* Horizontální navýšení kapacity na cluster pomocí distribuované architektury. V této možnosti jsou zatížení zpracování dat rozdělená a zpracovaná na více procesorech, které pracují paralelně, s konečným výsledkem shromážděným na konci.

### <a name="recommended-distributed-frameworks"></a>Doporučené distribuované architektury

V následující tabulce jsou doporučeny distribuované architektury, které jsou integrovány s Azure Machine Learning na základě předvolby kódu nebo velikosti dat.

Zkušenosti nebo velikost dat | Doporučení
------|------
Pokud jste obeznámeni s `Pandas`| `Modin` nebo datový `Dask` rámec
Pokud dáváte přednost `Spark` | `PySpark`
Pro data menší než 1 GB | `Pandas` místně **nebo** vzdálená instance Azure Machine Learning COMPUTE
Pro data větší než 10 GB| Přejděte ke clusteru pomocí `Ray` , `Dask` nebo `Spark`

## <a name="next-steps"></a>Další kroky

* [Možnosti přijímání dat Azure Machine Learning](concept-data-ingestion.md).
* [Vytvořte a zaregistrujte datové sady](how-to-create-register-datasets.md).
